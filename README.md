# lecroy_WR620ZI
python3 interface for the Lecroy WaveSurfer WR620ZI digital storage oscilloscope

- enables automation of basic waveform capture and built-in measurement functions
- communicates via VXI11 interface over LAN
- captured data is compatible with numpy and matplotlib
- tested on Linux ... *might* work on Windows, too
- works great in combination with jupyter notebooks!!!


## Dependencies
- python3
- numpy
- python-vxi11
- matplotlib


```
sudo apt-get update
sudo apt-get install python3-pip python3-numpy python3-matplotlib
sudo pip3 install update
sudo pip3 install setuptools
sudo pip3 install python-vxi11
```

Warning: in the current state, the IP of the scope is hardcoded in WR620ZI.py.
Fill fix that asap ...

## Example usage


```python
import numpy as np
from matplotlib import pyplot as plt

import WR620ZI as lecroy

# change IP to your scope LAN address
# make sure that LXI interface is enabled in the scope remote settings!
lecroy.init("192.168.43.20")

##################################################
##           configure LeCroy Scope             ##
##################################################

#lecroy.clear_all()

# set horizontal scaling, time/div
tdiv=50e-9
lecroy.set_tdiv(tdiv)

lecroy.set_trigger_delay(-4*tdiv) # t0 = 10% of screen

# access scope channels by labels,
# for easy re-mapping of scope channels without
# touching your below measurement automation
s = {
    "DUT_IN"   :"C4", 
    "DUT_OUT"  :"C2"  
}

# access scope measurements by labels
m = {
    "DUT_OUT_RISE"   : "p1",
    "DUT_OUT_FALL"   : "p2",
    "DUT_OUT_WIDTH"   : "p3"
}

# set up measurements for rise time, fall time and pulse width
lecroy.setup_measurement(m["DUT_OUT_FALL"],  s["DUT_OUT"], "rise")
lecroy.setup_measurement(m["DUT_OUT_RISE"],  s["DUT_OUT"], "fall")
lecroy.setup_measurement(m["DUT_OUT_WIDTH"], s["DUT_OUT"], "width")

##################################################
##              capture waveforms               ##
##################################################

time, wfm = lecroy.capture_waveforms([
    s["DUT_IN"],
    s["DUT_OUT"]
  ],
    average=10
)

plt.plot(time, wfm[s["DUT_IN"]], "r" ,label="DUT_IN")
plt.plot(time, wfm[s["DUT_OUT"]],"g" ,label="DUT_OUT")
plt.legend()
plt.xlabel("time (s)")
plt.ylabel("voltage (V)")
plt.show()

```

![Photo](https://github.com/acidbourbon/lecroy_WR620ZI/blob/master/pics/waveforms.png)


```python
##################################################
##          use built-in measurements           ##
##################################################

n_samples = 100

vals = lecroy.measure_statistics([m["DUT_OUT_RISE"],m["DUT_OUT_FALL"]],n_samples)

## calculate some simple statistics
rise_mean = np.mean(vals[m["DUT_OUT_RISE"]])
rise_std  = np.std(vals[m["DUT_OUT_RISE"]])

## plot recorded data as histogram
plt.hist(vals[m["DUT_OUT_RISE"]], label="mean = {:3.2e}s\nstd = {:3.2e}s".format(rise_mean,rise_std)) 
#plt.hist(vals[m["DUT_OUT_RISE"]], bins = np.arange(0,5,0.05))
plt.title("rise time distribution") 
plt.xlabel("time (s)")
plt.ylabel("counts")
plt.legend()
plt.show()

```

<<<<<<< HEAD
=======
![Photo](https://github.com/acidbourbon/lecroy_WR620ZI/blob/master/pics/meas_hist.png)
>>>>>>> a823da164e9301fa2ca0cc5d95a9a2aac3cd93e4
