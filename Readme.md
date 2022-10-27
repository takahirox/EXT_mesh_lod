# EXT\_mesh\_lod

## Contributors

* Takahiro Aoyagi, Mozilla, [@takahirox](https://github.com/takahirox)

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This `EXT_mesh_lod` extension adds the ability to specify Levels of Detail
(LOD) to glTF `mesh`. An implementation of this extension can use the
LODs for various scenarios like rendering a different LOD based upon the
distance of the object or progressively loading the glTF asset starting with
the lowest LOD.

This extension allows an LOD to be defined in a `node`. (A node having the
extension **MUST** have `mesh`.) The LODs can specify different geometry and
materials across various levels.

The extension is added to the highest LOD level. The extension defines an
`mesh_lod` property which is an array containing the objects including the
`mesh` index as the lower LOD level and the screen coverage number as a hint
to determine the switching threshold for the LOD.

Each `mesh` index in the array points to a LOD level that is lower in quality
than the previous level.

For example, if a `node` has a `mesh` index and the extension, the mesh referred
by the `mesh` index is the highest LOD level. Every `mesh` index specified in the
array of the extension points to the index of `mesh` which should be used as
the lower LOD level.

An implementation of this extension can parse through the array to create the
list of LOD levels available for an asset. If an asset containing this extension
is loaded in a client that doesn't implement the extension, then the highest
LOD level will be loaded and all lower LODs will be ignored.

## Examples

```json
"nodes": [{
    "mesh": 0,
    "extensions": {
        "EXT_mesh_lod": {
            "mesh_lod": [
                {"mesh": 1, "coverage": 0.5},
                {"mesh": 2, "coverage": 0.2}
            ]
        }
    }
}]
 ```

In this node, *mesh[0]* is the highest LOD. The first element in the `mesh_lod`
array, *mesh[1]*, is the next lower LOD (or the Medium_LOD). The second element
in the `mesh_lod` array, *mesh[2]*, is the lowest LOD.

## Screen coverage

The `coverage` property in the `mesh_lod` array specifies a screen coverage
value that can be used as a hint to determine the switching threshold for the
various LODs.

Screen coverage is a ratio between `0.0` and `1.0` representing how big the
mesh's rendered screen surface area is in the total screen surface area.

The highest LOD `coverage` value is implied to `1.0`. The `coverage` value
must be between `0.0` and `1.0`, and must be between the next higher LOD
`coverage` value and the next lower LOD `coverage` value.

The LOD is expected to be selected to be rendered if the current screen
coverage is between the LOD's `coverage` value and the next lower LOD
`coverage` value.

In the example above the screen coverage values can be used as follows:

* LOD0 Rendered from 1.0 to 0.5
* LOD1 Rendered from 0.5 to 0.2
* LOD2 Rendered from 0.2 to 0

## Mesh LOD Array property

| Property | Type | Description | Requires |
|:------|:------|:------|:------|
| `mesh_lod` | `lod [1-*]` | Mesh lower LODs definition | :white_check_mark: Yes |

## Mesh LOD properties

| Property | Type | Description | Requires |
|:------|:------|:------|:------|
| `mesh` | `integer` | LOD mesh index | :white_check_mark: Yes |
| `coverage` | `number` | Screen coverage for switching. The value must be between `0.0` and `1.0`, and must be between the next higher LOD `coverage` value and the next lower LOD `coverage` value. | :white_check_mark: Yes |

## Schema

* [node.EXT_mesh_lod.schema.json](schema/node.EXT_mesh_lod.schema.json)

## Reference

* [glTF `MSFT_lod` extension](https://github.com/KhronosGroup/glTF/tree/main/extensions/2.0/Vendor/MSFT_lod):
A glTF LOD vendor extension proposed by Microsoft. It allows more flexible
LOD configuration. The `EXT_mesh_lod` extension has simpler data structure
than `MSFT_lod` so less flexible but easier to handle for the extension
handlers.
