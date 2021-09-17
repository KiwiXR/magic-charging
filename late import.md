# late import

## source: [ProDA](https://github.com/microsoft/ProDA/blob/main/data/__init__.py#L12)

``` python
def find_dataset_using_name(name):
    """Import the module "data/[dataset_name]_dataset.py".

    In the file, the class called DatasetNameDataset() will
    be instantiated. It has to be a subclass of BaseDataset,
    and it is case-insensitive.
    """
    dataset_filename = "data." + name + "_dataset"
    datasetlib = importlib.import_module(dataset_filename)

    dataset = None
    target_dataset_name = name + '_loader'
    for _name, cls in datasetlib.__dict__.items():
        if _name.lower() == target_dataset_name.lower() \
           and issubclass(cls, BaseDataset):
            dataset = cls

    if dataset is None:
        raise NotImplementedError("In %s.py, there should be a subclass of BaseDataset with class name that matches %s in lowercase." % (dataset_filename, target_dataset_name))

    return dataset
```
