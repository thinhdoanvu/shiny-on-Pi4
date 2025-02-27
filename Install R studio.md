## Install Rust and Cargo
```
sudo curl https://sh.rustup.rs -sSf | sh  
source $HOME/.cargo/env  
```
Output: 
Current installation options:  

   default host triple: aarch64-unknown-linux-gnu  
     default toolchain: stable (default)  
               profile: default  
  modify PATH variable: yes  
1) Proceed with standard installation (default - just press enter)  
2) Customize installation  
3) Cancel installation  

Please, press 1

## Install sentry-cli
```
sudo git clone https://github.com/getsentry/sentry-cli.git
sudo chown -R pi ~/sentry-cli
cd sentry-cli
cargo build
cd
sudo rm -rf sentry-cli
```

## Clone RStudio repository
sudo git clone https://github.com/rstudio/rstudio.git

## Avoid installing crashpad
```
sudo nano /home/pi/rstudio/dependencies/common/install-common
```
```
    # Comment these lines
        # ./install-crashpad
        # sudo ./install-crashpad
```
