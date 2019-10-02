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
                └── {etc}
    ├── masks
        └── foreground
        └── groundtruth
            └── {version}
    └── raw
```
### groundtruth
Ground truth data is produced by human annotation of raw data in small chunks, which we colloquially call "crops". In order to ensure reproducibility of machine learning models, it is convenient to assign version numbers to collections of ground truth data. For each version of the ground truth data, `/volumes/groundtruth/` contains a group with the version number as its name. Inside this group is a collection of groups each representing an annotated crop. For each crop, the annotations are stored as an array of integers in an array at `/{version}/Crop{x}/labels/all/`. Some crops contain organelles (e.g., centrosomes, nucleolus) that require multiple annotations per pixel, and thus these crops will have additional arrays present in the `/Crop{x}/` group. These arrays should all have an `offset` attribute that specifies the location, in world coordinates, of the origin of the crop volume. 

### masks
The `/masks/` group contains binary masks. The array in `/masks/foreground` contains a mask with 1 at every point estimated to be inside the tissue / cell of interest and 0 everywhere else. This allows efficient prediction, since we can use the mask to focus the predictions on regions of interest. The array in `/masks/groundtruth/{version}` contains 1s for every pixel from a ground truth crop, and 0s everywhere else. This mask allows us to avoid running predictions on data that was used to train the model.

### raw
The `/raw` array does not actually contain raw data -- it contains aligned, binned, and inverted data, which can be used for making predictions from the machine learning model. In the future, the truly raw data (i.e., data as it was acquired from the FIBSEM microscope) will live in `/raw`.



