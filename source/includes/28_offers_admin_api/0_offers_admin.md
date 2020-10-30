# <a name="offers-admin"></a>  Offers Admin API

<aside class="warning">
This API is in development. Therefore, it may not be ready for use and is a subject to change at any time.
</aside>

This section describes endpoints destined for offers management. 
Navigate to [Offers](#offers) section to see docs for member-related endpoints.

## Common models

### <a name="admin-offer-model"></a> Offer

> Offer example:

```json
{
  "id": 1000115,
  "name": "Buy two shoes, get one jacket",
  "type": "REGULAR",
  "description": null,
  "external_url": "https://example.com/foo",
  "read_more": { "button_title": "Button title", "url": null, "title": "Title", "text": "Text" },
  "usable_since": "2019-03-13T17:32:00.000Z",
  "usable_until": null,
  "collection_ids": [1000016, 1000017],
  "tags": ["2 + 1"],
  "stores": ["M&H"],
  "files": [
    {
      "url": "https://offers-api.s3.eu-central-1.amazonaws.com/offer-1000115-offer_default-original.jpg",
      "width": 600,
      "height": 800,
      "kind": "offer_default",
      "size_type": "base"
    }
  ],
  "extras": {},
  "image_template": {
    "rectangles": [] // List of rectangles - see "ImageTemplate Rectangle"
  } 
}
```

Key | Type | Optional? | Description
--------- | --------- | -------- | ---------
id | integer | no |  
name | string | yes |
type | enum: ['REGULAR', 'EXCLUSIVE', 'BIRTHDAY', 'WELCOME'] | no |
description | string| yes | 
internal_name | string| yes | 
internal_description | string| yes | 
external_url | string| yes | The URL the offer may be linked with 
read_more | Object | no | Additional offer display data
read_more.button_title | string | yes |  
read_more.url | string | yes |  
read_more.title | string | yes |
read_more.text | string | yes |
status | enum: `['draft', 'published']` | no | Offer is visible only when status is `'published'` 
usable_since | Date | yes | Time since the offer may be used. If null, there is no restriction. 
usable_until | Date | yes | Time until the offer may be used. If null, there is no restriction.
visible_since | Date | yes | Time since the offer is visible to members. If null, there is no restriction. 
visible_until | Date | yes | Time until the offer is visible to members. If null, there is no restriction.
collection_ids | integer[] | no (may be empty) | List of collections ids the offer belongs to
tags | string[] | no (may be empty) | List of tags identifiers associated with the offer. Information from [Translations &bull; List](#list-translations) should be used to display the tags. 
stores | string[] | no (may be empty) | List of stores names associated with the offer. See [Stores &bull; List](#store-list).
audience_id | Integer | yes | DMP's audience ID. If it is set, the offer is available only to members belonging to this audience
campaign_id | Integer | yes | MPC's campaign ID
files | File[] | no | A list of offer files - see [File model](#file-model)
extras | Object | no (may be empty) | Extendable container for any potential extra data
display_schemas | Object | no | Offer displays schemas - see [Offer display schemas](#offers-display-schemas)
maximum_uses_per_user | Integer | yes | Limit of of uses individual member may perform. When empty, there's no limit.
stock | Integer | yes | Global limit of member uses. When empty, there's no limit.
created_at | Date | no | When the offer has been created 
updated_at | Date | no | Last time when the offer has been updated  
archived_at | Date | yes | Time when the offer has been archived
uses_count | Date | no | Total number of times the offer has been used by members   
image_template | Object | yes | See [image_template](#offer-image-template-attr) below
image_template.rectangles | ImageTemplateRectangle[] | yes | See [ImageTemplate Rectangle](#offer-image-template-rectangle)

#### <a name="admin-offer-model-image-template"></a> `image_template` attribute

With this object, you can determine that offer's image will be rendered according to the given definition.

It reflects the [ImageTemplate model](#offer-image-template-model). 
However, it **is not** a reference to existing [ImageTemplate record](#offer-image-template-record). 

If you want to apply record's model to the offer, you must use some of its attributes (copy them) as the offer's `image_template` attribute.

### <a name="admin-offer-payload"></a> OfferPayload

Following Offer attributes (described above) are available to set when creating or updating offers.
     
* name
* description
* internal_name
* internal_description
* external_url
* read_more
* status
* usable_since
* usable_until
* visible_since
* visible_until
* collection_ids
* tags
* stores
* audience_id
* campaign_id
* extras
* display_schemas
* maximum_uses_per_user
* stock
* image_template

### <a name="offer-image-template"></a> ImageTemplate


#### <a name="offer-image-template-model"></a> ImageTemplate Model

> ImageTemplate model:

```json
{
    "rectangles": [], // List of rectangles - see "ImageTemplate Rectangle"
}
```

Defines how offer image should be rendered. 

It's an interface used both by [Offer's `image_template` attribute](#admin-offer-model-image-template) and 
[ImageTemplate record](#offer-image-template-record).


Attribute      | Type                | Description
-------------- | ------------------- | ---------------------------------------------------
rectangles     | TemplateRectangle[] | See [ImageTemplate Rectangle](#offer-image-template-rectangle)

##### Rendering

The output image is rendered from template after offer creation/update request, asynchronously. 

Rendering process draws (up to 4) [rectangles](#offer-image-template-rectangle) over a background image (not yet implemented).

When rendering is done, the image is stored as a regular file of the subject offer. 

#### <a name="offer-image-template-record"></a> ImageTemplate Record

> ImageTemplate record example:

```json
{
    "id": 2,
    "name": "Majadada3",
    "default": false,
    "rectangles": [], // List of rectangles - see "ImageTemplate Rectangle"
    "created_at": "2020-09-04T14:55:44.385Z",
    "updated_at": "2020-09-04T14:55:44.385Z"
}
```

Stores `ImageTemplate` model (see above) for re-usage. Managed with [ImageTemplates API](#image-templates).

Attribute      | Type                | Description
-------------- | ------------------- | ---------------------------------------------------
id             | integer             | 
name           | string              | 
default        | boolean             | See below
rectangles     | TemplateRectangle[] | See [ImageTemplate Rectangle](#offer-image-template-rectangle)
created_at     | Date                | When template has been created
updated_at     | Date                | Last time the template has been updated

##### `default` attribute

It's possible to mark template as default for LC (doing this will un-mark previous default offer, if such exists). 

It's up to API client how to utilize this feature.

#### <a name="offer-image-template-record-payload"></a> ImageTemplate Record payload

> ImageTemplate record payload example:

```json
{
    "id": 2,
    "name": "Majadada3",
    "default": false,
    "rectangles": [] // List of rectangles - see "ImageTemplate Rectangle"
}
```

Used for creating and updating ImageTemplate

Attribute      | Type                | Required? | Description
-------------- | ------------------- | --------- | ---------------------------------------------------
name           | string              | yes       |
default        | boolean             | no        | See below
rectangles     | TemplateRectangle[] | yes       | See [ImageTemplate Rectangle](#offer-image-template-rectangle)

### <a name="offer-image-template-rectangle"></a> ImageTemplate Rectangle

> ImageTemplateRectangle example:

```json
{
    "name": "Title",
    "width": 100,
    "height": 200,
    "offset_top": 5,
    "offset_left": 15,
    "text_align": "center",
    "color": "#FF00FF",
    "font_size": 15,
    "font_family": "'Playfair Display', serif",
    "font_url": "https://fonts.googleapis.com/css?family=Raleway:400,700&display=swap",
    "content": "{{stores}}"
}
```

Key         | Type                                | Required? | Description
----------- | ----------------------------------- | --------- | ---------------------------------------------------
name        | string                              | yes       | Identifies rectangle in the template
width       | integer                             | yes       | Width of rendered rectangle
height      | integer                             | yes       | Height of rendered rectangle
offset_left | integer                             | yes       | x position relative to the top left corner of image
offset_top  | integer                             | yes       | y position relative to the top left corner of image
text_align  | enum: `['left', 'center', 'right']` | yes       | Text alignment inside the rectangle
color       | hex (e.g. `'#FF00EE'`)              | yes       | Color of the rectangle text 
font_size   | integer                             | yes       | Font size of the rectangle text
font_family | string                              | yes       | Family definition, related to included `font_url`
font_url    | URL                                 | yes       | URL of font face
content     | string                              | no        | Content to display, may contain [merge fields](#image-template-mergefields)  

### <a name="image-template-mergefields"></a> ImageTemplate Merge-fields

It is possible to use following merge-fields in order to inject offer attributes into the image generated from template.

Merge field       | Description 
----------------- | ---------------------------------------------------
`{{name}}`        | Injects `name` of offer
`{{description}}` | Injects `description` of offer
`{{collection}}`  | Injects `name` of first collection assigned to offer
`{{stores}}`      | When only one store is assigned to offer, injects it as it is. When more, it injects stores joined with comma