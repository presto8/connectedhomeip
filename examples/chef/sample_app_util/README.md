# Chef Build Conventions

## Overview

---

It is convenient to follow some naming and build conventions for Chef tool due
to the large volume of sample apps that may be created and the ambiguity that
may result from arbitrary names.

There are three components to the convention proposed here:

1. The naming convention for the sample matter device and clusters (referred to
   here as the `sample app`).
2. The naming convention to use for the build files which will be flashed on the
   devices.
3. The usage of metadata files that shall accompany build files to provide more
   detailed information about builds.

The convention proposed here should be adopted by the zap files provided in
`examples/chef/devices` and the builds generated from Chef tool in CI.

## Limitations

---

The largest filename that can be used on MacOS and Linux is 255 characters. If a
sample app name would exceed this limit by following this convention, then the
sample app should be given an arbitrary name.

This limitation is called out, but, with the given naming conventions, this
should rarely happen.

## Convention

---

### Sample App Naming Convention

Sample apps should be named by concatenating the name of all endpoints in the
order of their index. Endpoint names are separated by underscores (`_`) and a 10
character hash of the sample app metadata is appended to the end.

Valid sample app names conform to the following format:

```
<endpoint_0>_<endpoint_1>_<hash>
```

For example, here are some valid names:

```
rootnode_extendedcolorlight_H1l9gnQDYl
rootnode_speaker_8qRQaEj0Hy
rootnode_lightsensor_L6dEbmVDah
rootnode_dimmablelight_rWsDiwzw2t
rootnode_pressuresensor_03quf7tPOL
rootnode_flowsensor_ixbAboycie
rootnode_windowcovering_b9QoiScjOq
rootnode_doorlock_d5wtU7sjFR
rootnode_thermostat_KuQYArmwl7
rootnode_dimmablelight_7pNE3GVarn
rootnode_temperaturesensor_i0wGnDVUAc
rootnode_occupancysensor_wyGeQSokNp
rootnode_humiditysensor_pv0comNKyT
bridgednode_temperaturesensor_onofflight_onoffpluginunit_MI9DSdkH8H
```

### Sample App Build Naming Convention

The sample app builds formats will be named by pre-pending the zap file name
(described above) with the platform and appending connectivity info.

Valid build names conform to the following format:

```
<platform>_<zap_file_name>_<wifi/thread>
```

Note that `<zap_file_name>` follows the convention:
`<endpoint_0>_<endpoint_1>_<hash>`.

Together that is:

```
<platform>_<endpoint_0>_<endpoint_1>_<hash>_<wifi/thread>
```

The list of platforms supported here (as of writing this) are:

```
m5stack
nrf52840dk
linux
```

For example, here are some valid names:

```
m5stack_rootnode_humiditysensor_pv0comNKyT_wifi
nrf52840dk_rootnode_humiditysensor_pv0comNKyT_thread
linux_rootnode_humiditysensor_pv0comNKyT_wifi
```

### Metadata file convention

Metadata files are `yaml` files that should accompany build files.

The metadata files have a structure as follows:

```
    [
      {
        <endpoint_0_name>: {
          "server_clusters": {
            <cluster_name>: {
              "name": <name>,
              "commands": [
                <command_name>
              ],
              "attributes": [
                {
                  "name": <attribute_name>,
                  "value": <attribute_value>
                }
              ]
            }
          }
          "client_clusters": {
            <clusters>  # See above
          }
        }
      }
    ]
```

Note that it is more readable in `yaml` format. Since hashes are generated from
the metadata info, additional conventions are needed to ensure consistency for
the metadata structure.

The following conventions are used:

-   All lists are sorted alphabetically.
-   If a list contains dictionaries, it will be sorted by the "name" key. If it
    does not contain "name" key, it will be sorted by the first key common to
    all dictionaries that comes first alphabetically.
-   The list of endpoints is excluded from the above conventions. Endpoints are
    ordered according to their endpoint number; here, the endpoint number is the
    same as the order they are read from the zap file.

As an example, take a look at
[sample_zap_file.yaml](test_files/sample_zap_file.yaml)

## Utility Usage

---

There are a few primary usage cases for the utility
[sample_app_util.py](sample_app_util.py). Details are provided by using
`python sample_app_util.py zap --help`. Below is a summary.

| Command                                                                      | Description                                                                                                                                                                  |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `python sample_app_util.py zap <zap_file> --generate-name`                   | Generates the name for a zap file per the specified convention                                                                                                               |
| `python sample_app_util.py zap <zap_file> --rename-file`                     | Renames the zap file per specified convention                                                                                                                                |
| `python sample_app_util.py zap <zap_file> --generate-metadata [output_path]` | Generates the metadata file adjacent to the zap file with `.yaml` extension. If `[output_path]` is provided then the metadata file will be stored at the location specified. |

## Running Tests

---

Navigate to the base directory of this README.

```
cd <project_root>/examples/chef/sample_app_util
```

Run unit tests.

```
python -m unittest
```
