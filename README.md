## MightyWatt powered by Python

This is a Python package with a web interface for the [MightyWatt][],
a great programmable electronic load for the Arduino Uno/Due.
Designed as an Arduino shield it is made to be operated in
as a constant-current or constant-voltage mode.
In addition, a constant-power and constant-resistance mode are
implemented in software on the Arduino.
It uses a FET in linear mode to dissipate the power.

### Installation

Installing this Python package is as simple as:

    pip install --upgrade https://github.com/pklaus/MightyWatt_Python/archive/master.zip

### Starting the web server

Here is how you start the web server, just adapt
to the name of the serial port of your MightyWatt hardware:

    mw_web_server /dev/ttyACM0

The web server will then be available at <http://localhost:8001>
and the web page will look like this:

![screenshot of the web interface](./docs/web-screenshot.png)

You can stop the web server by pressing `[Ctrl]-[C]`. (This might take a few seconds.)

The web server comes with the user interface shown above but it **also provides
an API** to control the MightyWatt with simple HTTP requests:

To set constant current mode with 1.0 Amps:

    curl http://localhost:8001/api/mode/cc/1.0

To set remote voltage sensing:

    curl http://localhost:8001/api/voltage-sensing/remote

To stop the load:

    curl http://localhost:8001/api/stop

### Controlling the load with the web client

You can use the web server API to script a program for the load.
This software already comes with a a script to do simple tasks such as ramping current etc.:

    mw_web_client \
      --watchdog 'V>0.5' \
      --remote \
      http://localhost:8001 \
      ramp CC 0 1.3 \
      --duration 600

This will ramp the current from 0 to 1.3 Amps in 600 seconds.
It will use the remote sensing feature and stop if the voltage drops below 0.5 Volts.
The output of the script to stdout looks like the log files of the MightyWatt Windows software:

    # MightyWatt Log File
    # Started on	06/04/2015	11:51:39 AM
    # Current [A]	Voltage [V]	Time since start [s]	Temperature [deg C]	Local[l]/Remote[r]
    0.000	3.153	0.000	24	r
    0.000	3.159	0.107	24	r
    0.000	3.155	0.209	24	r
    0.000	3.157	0.303	24	r
    0.000	3.159	0.410	24	r
    0.000	3.160	0.508	24	r
    [...]

### Using the Python module without the web server

If you want to create scripts using the MightyWatt and don't want to
use the web server for some reason, you can also simply use the
MightyWatt object to control the load programmatically:

```python
from mightywatt import MightyWatt
import time

mw = MightyWatt('/dev/tty.usbmodemfd121')
mw.print_status()
mw.set_cc(1.00)
mw.print_status()
mw.set_update_rate(100)
time.sleep(0.5)
mw.print_status()
time.sleep(0.5)
mw.print_status()
#mw.set_remote()
mw.print_status()
mw.set_cp(2.5)
mw.print_status()
mw.stop()
mw.close()
```

### Requirements

This software requires [Python](https://www.python.org/) to run.
It is written for Python 3.4 but should also run on 2.7 (not tested as much though!).

In addition it requires these external dependencies:

* [pySerial](http://pyserial.sourceforge.net/)
* [Bottle](http://bottlepy.org)

Both should be automatically fetched and installed from
[the Python Package Index](https://pypi.python.org/pypi)
during the installation of this package.

### Author

* The MightyWatt is an open hardware/open software product
  developed by Jakub Polonský, available for sale
  at [Tindie](https://www.tindie.com/products/Kaktus/mightywatt-kit-70-watt-electronic-load-for-arduino/).
* This software was written by Philipp Klaus.

[MightyWatt]: http://kaktuscircuits.blogspot.cz/2014/07/mightywatt-revison-2-now-50-mightier.html

