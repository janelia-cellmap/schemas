# schemas
Documentation of how data, metadata, and derived data are are organized for the COSEM project

## Structure of datasets

Each dataset (image sample) has its own `.n5` container with the following structure (each leaf is an array):
```bash
{dataset_name}.n5
└── em
    ├── {version}
        └── s0
        └── s1
        └── ...
        └── s(N-1)
├── labels
    └── {class_0}_seg # a segmentation dataset
        └── s0
        └── s1
        └── ...
        └── s(N-1)
    └── {class_0}_pred # a prediction volume
        └── s0
        └── ...
        └── s(N-1)
    └── ...
    └── {class_0}_{class_1}_contacts # a segmentation volume representing
        └── s0                       # a relationship between class_0 and class_1
        └── ...
        └── s(N-1)
```

### EM

FIB-SEM and derived image data are stored in a folder (`em`) in the base n5 container. Different versions of the data, potentially having undergone different processing, are stored in this group. For example,
`/em/fibsem-uint16` contains preprocessed fib-sem data stored as unsigned 16-bit integers.
Each version is stored as a multiscale image, as described [here](multiscale.md).

### Labels

Organelle predictions and segmentations are stored in a folder (`labels`) in the base n5 container. Each distinct organelle type ("class") is stored in its own folder inside `labels.` For example, `/labels/golgi_seg` might contain the network predictions for Golgi. Each organelle prediction is stored as a multiscale image, as described [here](multiscale.md).

`labels` also contains various analysis volumes, like segmentations the represent regions where two organelle classes interact.