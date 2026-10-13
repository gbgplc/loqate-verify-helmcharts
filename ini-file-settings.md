# INI File Settings

*Installmanager* chart versions greater than *0.2.6* along with image versions greater than *0.1.108104* introduce new functionality that allows setting options in the `loqate.ini` when installing data.

Setting values:

```yaml
app:
  ini:
    referenceDatasetCacheSize: 7
    referencePageCacheSize: 12
    cassMultithreadedSettings: "yes"
```

Will overwrite or append that option in `loqate.ini`, like this:

```ini
ReferenceDatasetCacheSize=7
ReferencePageCacheSize=12
CassMultithreadedSettings=yes
```

The options above are the defaults for the chart.  You can override or add any option, but only those that are valid within the ini file will have an effect.  Some options are only valid in the ini file but many are only valid in requests.

### How options are matched

Each key has its first letter upper-cased to give the option name, for example `referencePageCacheSize` becomes `ReferencePageCacheSize`.  The name must match an option at the start of a line exactly, including case, and the line is replaced.  If there is no such line the option is appended to the end of the file.

Only whole option names are matched.  Setting `pageCacheSize` no longer changes `ReferencePageCacheSize`, and a commented out line such as `;ReferencePageCacheSize=12` is left alone and the option is appended instead.  This exact matching applies to *InstallManager* image versions *0.1.1252792* and later.  Earlier versions matched part of a name or a commented out line, so an option that appeared to be set may not have been; check the setting is applied by looking at `loqate.ini` after the install.

## Spatial-API

*Spatial-api* chart versions 0.8.0 and later, with image version *0.1.1260013* or later, can set options for a single deployment, using the same `app.ini` format.  The shared `loqate.ini` on the data volume is not changed: an init container applies the options to a copy that only that deployment uses.

```yaml
app:
  ini:
    referencePageCacheSize: 4
```

Options are matched as described in [How options are matched](#how-options-are-matched).  If `loqate.ini` is missing from the data volume the init container fails and the pod does not start.
