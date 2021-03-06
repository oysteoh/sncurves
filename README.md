# sncurves
`sncurves` is a Python implementation of the S-N curves from DNVGL-RP-C0005
and its predecessor DNV-RP-C203. It requires `numpy` and works in both
Python 2 and 3.

## Usage
Function `get_sn_curve` generates functions which implement various S-N curves,
like so: 

```python
>>> import sncurves
>>> # List valid S-N curve names (HS stands for 'high strength steel')
>>> sncurves.names()
    ['B1', 'B2', 'C', 'C1', 'C2', 'D', 'E', 'F', 'F1', 'F3', 'G', 'HS', 'T', 'W1', 'W2', 'W3']
>>> # Generate an S-N curve function
>>> sn = sncurves.get_sn_curve("C1", seawater=True, cp=False)
>>> sn.params
    SNcurve(name='C1', seawater=True, cp=False, N=1000000.0, m1=3.0,
            loga1=11.972, m2=3.0, loga2=11.972, k=0.15)
```
Here we have generated a function which implements S-N curve C1 in seawater
(as opposed to in air) and without cathodic protection (`cp=False`).
The `sn` function calculates the number of stress cycles to failure for
a given stress range or a sequence of stress ranges (in MPa):
```python
>>> sn(150)
    277796.15020026034
>>> sn([75, 100, 125, 150])
    array([ 2222369.20160208,   937562.00692588,   480031.74754605,
             277796.15020026])
```
The `sn` function also accepts some optional arguments:
```python
>>> help(sn)
```
```
Signature: sn(sigma, t=None, tref=25.0, scf=1.0)

Calculates number of stress cycles to failure for the stress range *sigma*.
S-N curve:  C1 in seawater w/o cathodic protection
Compliance: DNVGL-RP-0005:2014-06

Arguments:
    sigma - stress range [MPa], a number or a sequence of numbers
    t     - thickness through which a crack will most likely grow [mm],
            may be omitted
    tref  - reference thickness [mm], 25.0 by default; ignored if *t*
            is not given
    scf   - stress concentration factor, 1.0 by default;
            stress range will be multiplied by *scf*, it may also affect some
            S-N curve parameters (e.g. parameter k for curve T)

Returns: a number or a numpy array with a shape corresponding to *sigma*.
```

## Compliance with DNVGL standards
Each and every S-N curve implemented in this module is checked for correctness
for low and high number of cycles and for thickness below and above 25 mm
(see benchmark data in directory 'tests'). A specific revision of the
DNVGL-RP-C0005 standard can be selected using the `compliance` function:

```python
>>> sncurves.compliance('2012.10')
    'DNV-RP-C203 October 2012'
>>> sncurves.compliance('2014.06')
    'DNVGL-RP-0005:2014-06'
>>> sncurves.compliance('latest')
    'DNVGL-RP-0005:2014-06'
```
The S-N curves generated by `get_sn_curve` will be different, depending on the
selected standard revision.
