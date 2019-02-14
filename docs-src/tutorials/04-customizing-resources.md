AdminBro works quite well with a default scaffolding, but what if you want to modify how the Resources look like?
You can use {@link ResourceOptions}

## Resource options

{@link ResourceOptions} are passed to the AdminBro along with other {@link AdminBroOptions configuration options}.

```javascript
...
const Article = require('./models/article')

const adminBroOptions = {
  resources: [
    { resource: Article, options: '...your options go here' },
  ],
  branding: {
    companyName: 'Amazing c.o.',
  },
  ...
}
...
```

When not passed - AdminBro will use defaults.

## How a __Resource__ can be modified?

You have lots of options. You can modify basic appearance of a resource as well as more complicate aspects, such as
how a particular field should be rendered.

In the next sections, I will point out a couple of options.

### { __parent__ } In the sidebar

By default, AdminBro groups resources by the database they belong to, but you can change that and group them
in a different way. It can be done by using a `parent` option.

Lets say you want to group all text'ish resources into a __content__ category in the sidebar.
You can do this by passing the __parent__ as an option:

```javascript
const contentParent = {
  name: 'Content',
  icon: 'fa fa-file-text',
}
const adminBroOptions = {
  resources: [
    { resource: Article, options: { parent: contentParent } },
    { resource: BlogPost, options: { parent: contentParent } },
    { resource: Comment, options: { parent: contentParent } },
  ],
}
```

This will group all Resources together in a "Content" category in a sidebar. It'll aslo automatically add
__file-text__ icon from {@link https://fontawesome.com/}



### { __name__ } of a __Resource__

By default, the name of a Resource is taken from the database _collection/table_. You can change that
by setting a __name__ option.

```javascript
const adminBroOptions = {
  resources: [
    { resource: Article, options: { name: 'Your custom name' } },
  ],
}
```

AdminBro has 2 fonts included by default:
* {@link http://fizzed.com/oss/font-mfizz Mfizz}
* {@link https://fontawesome.com/ Font Awesome}

You can add your own fonts by using: { __assets.styles__ } setting in {@link AdminBroOptions}.

### { __xxxProperties__ } - visibility of properties

It defines which properties should be visible in a __list__, __edit__, __show__ and __filter__ views.

For example, lets say that you have a resource city with 20 fields like: _name, lat, lng, population, polution, ..._.
By default, AdminBro will render first DEFAULT_MAX_ITEMS_IN_LIST which is __8__. What if you want to present only 3 of them, but in a different order.

You can do this by using __listProperties__ option:

```javascript
const adminBroOptions = {
  resources: [
    { resource: City, options: { listProperties: ['name', 'population', 'polution'] } },
  ],
}
```

The same goes with __showProperties__, __editProperties__ and __filterProperties__.

## { __properties.propertyName__ } - custom property options

AdminBro allows you to:
- fully customize how each property should be presented
- add custom properties

Everything thanks to {@link PropertyOptions}.

### Visibility of properties { __...propertyName.position__ } and { __...propertyName.isVisible__ }

Using __xxxProperties__ is not the only way of handling, which property should be seen on a "list", "edit", "filter"
and "show" views. You can achieve a similar result by using __position__ and __isVisible__ options.

It makes more sense to use them if you want to disable one particular field, so instead of modifying entire __xxxProperties__ array, you can setup just one filed.

In the following example,
I will hide __name__ field in the __list__, __filter__ and the __edit__, but will leave it in a __show__ view.

```javascript
const adminBroOptions = {
  resources: [
    { resource: City, options: { properties: {
      name: {
        isVisible: { list: false, filter: false, show: true, edit: false },
      }
    }},
  ],
}
```

You can hide the entire field from all views by simply setting __isVisible__ to false.

Also, you can simply change position of a field by using __position__ option. By default, all fields has position __100__, except the __title__ field, which gets position __-1__ - meaning it will be in the beginning of a list.

__Important notice about overriding xxxProperties__: both _{ propertyName.position }_ and _{ propertyName.isVisible }_ will be overriden by _xxxProperties_ if you set it.

### { __propertyName.type__ } of a property

By default, types of properties are computed by adapters, see {@tutorial 03-passing-resources} tutorial.

When you have a __DATE__ field, it will be rendered as a __date__ with __datepicker__ and a custom __from__ - __to__ filter.

You can change this behaviour by modyfying its type. So, for instance, you can add a richtext editor to a  __content__ like that:

```javascript
const adminBroOptions = {
  resources: [
    { resource: City, options: { properties: {
      content: { type: 'richtext' },
    }},
  ],
}
```

And you will see {@link https://quilljs.com} editor in place of a regular text field.

There are multiple fields supported (see PropertyTypes in the sidebar)

### { __propertyName.render__ } property appearance

You can also change the way property is rendered. The only thing you have to do is to change its __render__ property.

Render has all the fields defined by {@link PropertyType}. You can override just one of the fields or all of them.

Lets say, we want to change the way how __content__ property is rendered on the list:

```javascript
const adminBroOptions = {
  resources: [
    { resource: City, options: { properties: {
      content: { render: {
        list: (property, record, h) => {
          return record.param(property.name()) + ' - this is custom addition'
        }
      } },
    }},
  ],
}
```

Take a look at how those functions were defined in different types: <a href='./admin-bro_src_backend_property-types_richtext.js.html'>richtext.js</a>

For a detailed information about function arguments, check {@link PropertyType}.

## Adding new properties

You can add a new properties to the Resource by using a { __properties.propertyName__ }. You simply define all renderers and that's it. For example, if you want to group 'lat' and 'lng' fields on the list and display them as
a _Google Map_ in a __show__ view, you can use something like this:

```javascript
const adminBroOptions = {
  resources: [
    { resource: City, options: { properties: {
      lat: { isVisible: { list: false, show: false, edit: true, filter: true } },
      lng: { isVisible: { list: false, show: false, edit: true, filter: true } },
      position: { render: {
        list: (property, record, h) => {
          return [
            'lat:',
            record.param('lat'),
            'lng: ',
            record.param('lng'),
          ].join(' ')
        },
        show: (property, record, h) => {
          return '<div id="map">...</div><script....></script>'
        },
        head: {
          scripts: ['https://goooglemapsscript.js']
        }
      } },
    }},
  ],
}
```

Above, we also use __head__ parameter having all __scripts__ and __styles__, which should be added to a HEAD of the page.

## What's next?

If you want to see an in-depth explanation on how resources can be modified, you can study how {@link PropertyDecorator} and {@link ResourceDecorator} look like.

