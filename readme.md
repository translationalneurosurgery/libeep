# Installation

## Linux

```
make clean python
pip install -e .
```

## Windows

from the project root, run in bash

```{bash}
mkdir build
cmake -S . -B build
make -C build
```

You will find a file called `pyeep.so` in `build/python/v3`. This is a python extension to load eego files.

Create and install a python package by running

```{bash}
mkdir libeep
cp build/python/v3/pyeep.so libeep
cp python/__init__.py libeep
pip install -e .
```

# Usage

## Object-oriented-interface for python

After running `pip install -e .`, you will then be able to import eep and use its object oriented interface.

```{python}
import libeep
import matplotlib.pyplot as plt

fname = "example.cnt"
cnt = libeep.cnt_file(fname)
print(f"Within the file {fname} are")
print(f"{cnt.get_channel_count()} channels and ")
print(f"{cnt.get_sample_count()} samples and")
print(f"{cnt.get_trigger_count()} events")

# load 1s before and after the second event
fs = cnt.get_sample_frequency()
marker, tstamp, *info = cnt.get_trigger(1)
data = cnt.get_samples(tstamp-fs, tstamp+fs)
# plot the data
plt.plot(data)
```


## Low-level interface

Because it is a python extension, `pyeep.so` can also be simply imported with `import pyeep`. If the python package is installed, you can use `from libeep import pyeep`. This does only offer a low-level interface. You can find an example use case [here](python/demo_read_cnt.py) or in the following:

```{python}
import pyeep
fname = "example.cnt"
fh = pyeep.read(fname)  # get the file handle

sampling_rate = pyeep.get_sample_frequency(fh)

chan_count = pyeep.get_channel_count(fh)
channel_labels = [pyeep.get_channel_label(fh, chan) for chan in range(chan_count)]

sample_count = pyeep.get_sample_count(fh) # get how many samples are there
data = pyeep.get_samples(fh, 0, sample_count)  # load them all


trigger_count = pyeep.get_trigger_count(fh)
markers = [pyeep.get_trigger(fh, trigger) for trigger in range(trigger_count)]

```