# ScreenshotOne Module for PlatformOS

This repository contains a standalone PlatformOS / Insites module that creates website thumbnails using [ScreenshotOne](https://screenshotone.com/).

## Overview

The module exposes a partial that triggers the ScreenshotOne API and uploads the resulting image to a record in your PlatformOS database. A valid `screenshot_one_access_key` constant must be configured in your instance.

The API call is defined in `modules/screenshotone/public/api_calls/shoot.liquid`:

```
{% capture path %}
    https://api.screenshotone.com/take?access_key={{ context.constants.screenshot_one_access_key }}&{{- data.params -}}
{% endcapture %}
```

## Usage

Include the `shoot` partial to capture a screenshot and store it on a record.
Pass the current image URL (if any) via the `image` parameter so the partial can
skip records that already have an image:

```
{% include 'modules/screenshotone/public/views/partials/shoot',
  id: record_id,
  table: 'table_name',
  url: 'https://example.com',
  name: 'some_name',
  image: existing_image_property %}
```

If `image` is already present, the partial immediately returns. Passing the
current value allows you to avoid unnecessary API calls. Here is an example that
generates a thumbnail for an Offer record only when the record has no image:

```
{% liquid
  function generate_image = "modules/screenshotone/shoot",
    table: "modules/ins_databases/offers",
    id: offer.id,
    image: offer.image.url,
    url: offer.url,
    name: offer.title
%}
```

Parameters accepted by the partial are documented at the top of the file:

```
{% comment %}
    params:
        table - name of table to upload image to
        id - id of record to update
        url - url of website to take picture of
        name - name property to use (will be slugified)
        image - value of the record's image property; the partial exits if this is present
{% endcomment %}
```

## Testing

A demo page is provided in `modules/screenshotone/public/views/pages/test.liquid`. Update the `id`, `table`, `name` and `url` variables with values from your environment and visit `/shoot_test` to trigger a screenshot:

```
{% liquid
    comment
        update these variables to point to a database record and url to test
    endcomment
    assign id = 1234
    assign table = "modules/example/table"
    assign name = "example_name"
    assign url: "ttps://www.example.com"
%}
```

## Dependencies

This module has no code dependencies other than a ScreenshotOne account and the `screenshot_one_access_key` constant.
