# xboxapi-tools
Tools for interacting with the unofficial [Xbox API](https://xboxapi.com).

## API

Instantiate an API object:

```python
from xboxapitools.api import XboxApi
api = XboxApi(api_key="{api_key}", language="{language}")
```

* `api_key` argument can be omitted if an `XBOXAPIKEY` environment variable exists.
* `language` will default to your current language, override with a string like `de-DE`.
* `base_url` can also be passed if you wish to hit a different base endpoint other than `https://xboxapi.com/v2/`

Make an API call:

```python
>>> major = api.get_xuid_by_gamertag("Major Nelson")
```

An `XboxApiResponse` object will be returned. The heat of the meat is in its `data` member, but there is other metadata such as `rate_remaining`, informing you of how many API calls you have remaining.

```python
>>> print major.data
2584878536129841
>>> print major.rate_remaining
83
>>> print major.continuation_token
None
```

The API will only return up to 100 records per request. Iterate the response object to return the next page of records.

```python
>>> major_clips = api.get_user_gameclips(major.data)
>>> print len(major_clips.data)
100
>>> major_clips2 = major_clips.next()
>>> print len(major_clips2)
99
```

The `fill_data()` method of an `XboxApiResponse` object will automatically make additional API calls and aggregate the returned records. The following example gets the latest 300 game clips for **The Elder Scrolls V: Skyrim Special Edition**:

```python
>>> skyrim_title_id = 252034287
>>> skyrim_clips = api.get_saved_gameclips(skyrim_title_id)
>>> print len(skyrim_clips.data)
97
>>> skyrim_clips.fill_data(300)
>>> print len(skyrim_clips.data)
300
```