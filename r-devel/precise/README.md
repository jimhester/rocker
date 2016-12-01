To build the container and run it
```shell
docker build -t r-devel-precise .
docker run -it r-devel-precise /bin/bash
```

Within the container you can query the CXX1X variables to see they are unset.
```shell
R CMD config CXX1X
#
R CMD config CXX1XSTD
#
```

Compare with a container using the current release

```shell
docker run -it rocker/r-apt:precise /bin/bash
```

```shell
R CMD config CXX1X
# g++
R CMD config CXX1XSTD
# -std=c++0x
```
