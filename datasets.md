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

## array attributes
All array data should ultimately have the following attributes:
`resolution`: e.g. `[4, 4, 4]` or `[z : 4, y : 4, x : 3]`. The pixel spacing of the data in real units, e.g. nanometers  

`offset`: The position in real space of the origin of the array data. Parent data should always have the offset `[.5, .5, .5]`, (the center coordinate of a single pixel in 3D), and all ROI taken from from the parent data will have an offset relative to `[.5, .5, .5]`  

`contrast_limits`: For data that have not been contrast-adjusted to fill an entire datatype range, the minimum and maximum data value should be computed once and saved as an attribute.

## array dimensions
Z: The milling axis
Y: The axis normal to the coverslip
X: The remaining axis
