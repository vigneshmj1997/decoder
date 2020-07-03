# decoder
this repo contains the foler to use only the decoder part of wav2letter


# Dependencies
## 1. Kenlm
Example build commands on Ubuntu:
```
sudo apt-get install liblzma-dev libbz2-dev libzstd-dev
git clone https://github.com/kpu/kenlm.git
cd kenlm
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DKENLM_MAX_ORDER=20 -DCMAKE_POSITION_INDEPENDENT_CODE=ON
make -j16* Any CBLAS library, i.e. at least one of these: 

# don't forget to export KENLM_ROOT_DIR
```
# 3. Additional Dependencies
* Any CBLAS library, i.e. at least one of these: 
  * [ATLAS](http://math-atlas.sourceforge.net/)
  * [openBLAS](https://www.openblas.net/)
* [FFTW3](http://www.fftw.org/)
```
apt-get install libsndfile1-dev libopenblas-dev libfftw3-dev libgflags-dev
```
```
# in your wav2letter++ directory
mkdir -p build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j4 # (or any number of threads)
```

# Installation of  python binding
```export KENLM_ROOT_DIR=/root/kenlm
   cd bindings/python
   pip install -e .
```
