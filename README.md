# Install Shiny Server on Raspberry Pi
## Install dependencies and reboot
sudo apt-get install -y gfortran libreadline6-dev libx11-dev libxt-dev libcairo2-dev

## Install R
First update all your sources: sudo apt update

Next, install any updates that are available: 

sudo apt upgrade

Finally, install the r4pi build of R: 

sudo apt install r4pi

You can start R by running: 

R

## Install shiny package

install.packages("Rcpp")

install.packages("httpuv")

install.packages("mime")

install.packages("jsonlite")

install.packages("digest")

install.packages("htmltools")

install.packages("xtable")

install.packages("R6")

install.packages("Cairo")

install.packages("shiny")

## Install cmake 4.0

wget https://github.com/Kitware/CMake/releases/download/v4.0.0-rc1/cmake-4.0.0-rc1.tar.gz

tar xzf cmake-4.0.0-rc1.tar.gz

cd cmake-4.0.0-rc1

./configure

make

sudo make install

## Install Shiny-server

git clone https://github.com/rstudio/shiny-server.git

cd shiny-server

DIR=`pwd`

PATH=$DIR/bin:$PATH

mkdir tmp

cd tmp

PYTHON=`which python`

sudo cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DPYTHON="$PYTHON" ../

make
