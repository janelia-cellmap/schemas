# schemas
Documentation of how data, metadata, and derived data are are organized for the COSEM project

## Structure of datasets

Each dataset (image sample) has its own `.n5` container with the following structure (each leaf is an array):
```bash
{dataset_name}.n5
└── fibsem
    ├── {version}
        └── s0
        └── s1
        └── ...
        └── s(N-1)
├── predictions
    └── {class_0}_seg
        └── s0
        └── s1
        └── ...
        └── s(N-1)
    └── {class_1}_seg
        └── s0
        └── ...
        └── s(N-1)
    └── ...
    └── {class_(M-1)}_seg
        └── s0
        └── ...
        └── s(N-1)
```

### FIBSEM

FIBSEM and derived image data are stored in a folder (`fibsem`) in the base n5 container. Different versions of
the data, potentially having undergone different processing, are stored in the `fibsem` folder. For example,
`/fibsem/aligned_uint16_v0` would contain fibsem data that was aligned, and is stored as unsigned 16-bit integers.
Each version is stored as a multiscale image, as described [here](multiscale.md).

### Predictions

Organelle predictions are stored in a folder (`predictions`) in the base n5 container. Each distinct organelle
type ("class") is stored in its own folder inside `predictions.` For example, `/predictions/golgi_seg` might
contain the network predictions for Golgi. Each organelle prediction is stored as a multiscale image, as
described [here](multiscale.md).

