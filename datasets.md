## Structure of datasets

As of October 2, 2019, each FIBSEM dataset has its own `.n5` container with the following structure (each leaf is an array):
```bash
{dataset_name}.n5
└── volumes
    ├── groundtruth
        └── {version}
            └── Crop{x}
                └── labels
                    └── all
                    └── {ribosomes/nucleolus/centrosome}
                └── {optional: masks}
    ├── masks
        └── foreground
        └── groundtruth
            └── {version}
    └── raw
```
### groundtruth
Ground truth data is produced by human annotation of raw data in small chunks, which we colloquially call "crops". In order to ensure reproducibility of machine learning models, it is convenient to assign version numbers to collections of ground truth data. For each version of the ground truth data, `/volumes/groundtruth/` contains a group with the version number as its name. Inside this group is a collection of groups each representing an annotated crop. For each crop, the annotations are stored as an array of integers in an array at `/{version}/Crop{x}/labels/all/`. Some crops contain organelles (e.g., centrosomes, nucleolus) that require multiple annotations per pixel, and thus these crops will have additional arrays present in the `/Crop{x}/` group. All arrays should all have an `offset` attribute that specifies the location, in world coordinates, of the origin of the crop volume.   

### masks
The `/masks/` group contains binary masks. The array in `/masks/foreground` contains a mask with 1 at every point estimated to be inside the tissue / cell of interest and 0 everywhere else. This allows efficient prediction, since we can use the mask to focus the predictions on regions of interest. The array in `/masks/groundtruth/{version}` contains 1s for every pixel from a ground truth crop, and 0s everywhere else. This mask allows us to avoid running predictions on data that was used to train the model. 

In the future, we may wish to add other masks. Perhaps most useful would be a mask that represents regions of the processed data that have been padded, so that these values can be ignored during histogram-based computations.


### raw
The `/raw` array does not actually contain raw data -- it contains aligned, binned, and inverted data, which can be used for making predictions from the machine learning model. In the future, the truly raw data (i.e., data as it was acquired from the FIBSEM microscope) will live in `/raw`.

## group attributes

`padding`: The amount of padding applied to the data. For some datasets padding is used to to compensate for inconsistent image sizes, and it is also used after image registration. The exact metadata representation of padding is to be determined, because some padding schemes are space-varying.

## array attributes
All array data should ultimately have the following information stored in metadata (although at present we do not completely cover all these cases):

`name`: a name for the dataset.

`axes`: The names of the array axes of the data, e.g., `['z', 'y', 'x']`. 

`units`: The units of the physical coordinates of the data, e.g. `['nm', 'nm', 'nm']`

`scale`: The scaling factors required to map the indices of each axis to positions in real space. E.g., `[4, 4, 4]`

`translate`: The position in real space of the origin of the array data, e.g. `[0, 0, 0]`. 

`contrastLimits`: For data that have not been contrast-adjusted to fill an entire datatype range, the minimum and maximum data value should be computed once and saved as an attribute.

`creationDate`: The time when the array was created.

`program`: The name / identifier of the program that created the array.

## physical interpretation of array dimensions
z: the milling axis
y: the slow axis of EM scanning
x: the fast axis of EM scanning
