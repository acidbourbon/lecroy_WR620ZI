# lecroy_WR620ZI
python interface for the lecroy WR620ZI

- enables automation of basic waveform capture and built-in measurement functions
- communicates via VXI11 interface over LAN


## Dependencies
- python3
- numpy
- python-vxi11


```
sudo apt-get update
sudo apt-get install python3-pip python3-numpy
sudo pip3 install update
sudo pip3 install setuptools
sudo pip3 install python-vxi11
```

## Example usage


```python
import numpy as np
from matplotlib import pyplot as plt

import WR620ZI as lecroy


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
    "DUT_OUT_T1"   : "p1",
    "DUT_OUT_TOT"  : "p2"
}

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
show(plt)


```

README UNDER CONSTRUCTION
