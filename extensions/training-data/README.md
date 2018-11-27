# Training Data Extension Specification (`td`)

**Extension [Maturity Classification](../README.md#extension-maturity): Proposal**

This document explains the fields of the STAC Training Data (TD) Extension to a STAC Item. It is used to describe labeled Areas of Interest (AOIs) that are used with earth observation imagery. The AOI is usually a polygon, but in some cases could be a line or point such as is the case with a road. This extension is meant to support using labeled AOIs with Machine Learning models, but could be used in any application where labeled AOIs are needed.

- [Example Item](example-roads.json)
- [Example Asset (labels)](example-labels.json)
- [Example Source Imagery](example-source.json)
- [JSON Schema](schema.json)

A Training Data Item represents a set of polygons with labels and, like other STAC Items should be part of a Collection. It is up to the data provider how to group their catalog, but a typical use might have a Collection of a series of training data sets (Items) that are related. For example a "Building" collection might have 50 Items, each one was a set of building AOIs for a single country. The Collection holds details on the data providers and the license.

## Item fields
Like other content extensions, the TD extension adds additional fields to a STAC Item, which are detailed after some additional clarification on what the core fields mean with respect to a TD Item.

### Core Item fields
Some additional notes are given here for some of the core STAC Item fields and what they represent for training data.

- **bbox** and **geometry**: The bounding box and the geometry of a TD Item represents the region for which the training data is valid. This could be the extent of all the AOIs in the dataset, or could be the region the provider believes the training data is representative.
- **properties.datetime**: The datetime of a TD Item is the nominal datetime for which the training data applies, typically this is the datetime of the source imagery used to generat the labels. If the training data applies over a range of datetimes (e.g., generated from multiple source images) then use the datetime-range (dtr) extension to indicate start and end datetimes.
- **properties.assets**: Unlike other extensions, the TD extension requires a single asset with the key "labels". This is a GeoJSON FeatureCollection asset containing the actual label features. As with the core STAC Item a thumbnail asset is also strongly encouraged.

### New Item properties
| element         | type info       | name                       | description       | 
|-----------------|-----------------|----------------------------|--------------------------------------------------------------------------------------------------| 
| td:title | string | Title | A human readable title of the dataset for display |
| td:description | string | Description | **REQUIRED.** A description of the training data, how it was created, and what it is recommended for |
| td:objects | [string] | Objects | **REQUIRED.** a list of keywords representing the nature of the labels. (e.g., trees, buildings, cars, hippos)
| td:source_type      | string (enum)     |  Method                | **REQUIRED.** The method of gathering the training data, either "ground" or "remote" |
| td:precision  | string (enum) | Task             |  **REQUIRED.** One of 'classification', 'detection', or 'segmentation' |

#### Required Asset: labels
The TD Extension aads the requirement of at least one asset that uses the key "labels". The asset will contain a link to the actual label data as a GeoJSON FeatureCollection.

Each Feature in the GeoJSON is a single AOI, either Polygon, Line, or Point and must contain the following properties.

| element         | type info       | name                       | description       | 
|-----------------|-----------------|----------------------------|--------------------------------------------------------------------------------------------------| 
| td:label | string | label | The name of the feature (e.g., road, building, face) |

#### Links: source imagery
A Training Data Item links to any source imagery that the AOIs apply to by linking to the STAC Item representing the imagery. Source imagery is indicated by using a `rel` type of "source" and providing the link to the STAC Item.

In addition the link has a new Training Data specific field:

| element         | type info       | name                       | description       | 
|-----------------|-----------------|----------------------------|--------------------------------------------------------------------------------------------------| 
| td:assets | [string] | Assets | The keys for the assets to which the training data applies |


## Implementations
This is an initial proposal, there are no current implementations.

## Extensions
Training data Items may often use the datetime-range extension if the training dataset applies over a range of dates. While the EO extension doesn't make sense within a TD Item itself, most TD Items will link to source data which will frequently use the EO Extension. The [extensions page](../README.md) gives an overview about these and other extensions.