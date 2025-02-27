# Install Shiny Server on Raspberry Pi 4
${\textsf{\color{blue}System information:}}$
```
8GB RAM
128GB SD card
Update from 21 Feb 2025 
Updated by thinh
```

## Install dependencies and reboot
```sudo apt-get install -y gfortran libreadline6-dev libx11-dev libxt-dev libcairo2-dev```

## Install R version 4.0.2
${\textsf{\color{blue}First update all your sources:}}$  
```sudo apt update```  

${\textsf{\color{blue}Next, install any updates that are available:}}$    
```sudo apt upgrade```  

${\textsf{\color{blue}Finally, install the r4pi build of R:}}$  

```sudo apt-get install -y gfortran libreadline6-dev libx11-dev libxt-dev \
                               libpng-dev libjpeg-dev libcairo2-dev xvfb \
                               libbz2-dev libzstd-dev liblzma-dev \
                               libcurl4-openssl-dev \
                               texinfo texlive texlive-fonts-extra \
                               screen wget libpcre2-dev```  
```cd /usr/local/src```  
```sudo wget https://cran.rstudio.com/src/base/R-4/R-4.0.2.tar.gz```  
```sudo su```  
```tar zxvf R-4.0.2.tar.gz```  
```cd R-4.0.2```  
```./configure --enable-R-shlib #--with-blas --with-lapack #optional```  
```make```  
```make install```  
```cd ..```  
```rm -rf R-4.0.2*```  
```exit```  
```cd # return to home directory```  

${\textsf{\color{blue}You can start R by running:}}$  
```R```

## Install shiny package
```
sudo su - -c "R -e \"install.packages('later', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('fs', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('Rcpp', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('httpuv', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('mime', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('jsonlite', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('digest', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('htmltools', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('xtable', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('R6', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('Cairo', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('sourcetools', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""
```
## Install libssl-dev for cmake
```sudo apt-get install libssl-dev```

## Install cmake 4.0
```
wget https://github.com/Kitware/CMake/releases/download/v4.0.0-rc1/cmake-4.0.0-rc1.tar.gz  
tar xzf cmake-4.0.0-rc1.tar.gz  
cd cmake-4.0.0-rc1  
./configure  
make  
sudo make install
```
## Install NVM, NodeJS
```
sudo apt install nodejs npm  
export PATH=$PATH:/path/to/node  

```
## Install Shiny-server
```
git clone https://github.com/rstudio/shiny-server.git  
cd shiny-server  
DIR=`pwd`  
PATH=$DIR/bin:$PATH  
mkdir tmp
cd tmp  
PYTHON=`which python`  
sudo cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DPYTHON="$PYTHON" ../  
sudo make
mkdir ../build  
(cd .. && ./bin/npm --python="$PYTHON" rebuild)  
(cd .. && ./bin/node ./ext/node/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js --python="$PYTHON" rebuild)  
sudo make install  
```

## Prepare a system for Shiny Server’s default configuration
```
sudo ln -s /usr/local/shiny-server/bin/shiny-server /usr/bin/shiny-server  
sudo useradd -r -m shiny  
sudo mkdir -p /var/log/shiny-server  
sudo mkdir -p /srv/shiny-server  
sudo mkdir -p /var/lib/shiny-server  
sudo chown shiny /var/log/shiny-server  
sudo mkdir -p /etc/shiny-server
```

## Create the Shiny Server configure file
```
cd /etc/shiny-server/  
sudo wget http://withr.me/misc/shiny-server.conf  
```
${\textsf{\color{blue}Or create an empty file and fill it the content of default.conf}}$  
```
sudo nano default.conf
```
${\textsf{\color{blue}Fill it the content of default.config}}$  
```
# Instruct Shiny Server to run applications as the user "shiny"
run_as shiny;

# Define a server that listens on port 3838
server {
  listen 3838;

  # Define a location at the base URL
  location / {

    # Host the directory of Shiny Apps stored in this directory
    site_dir /srv/shiny-server;

    # Log all Shiny output to files in this directory
    log_dir /var/log/shiny-server;

    # When a user visits the base URL rather than a particular application,
    # an index of the applications available in this directory will be shown.
    directory_index on;
  }
}
```
## Add your shiny applications
${\textsf{\color{blue}Create a demo shiny-app in /srv/shiny-server,}}$ [like this](https://shiny.posit.co/r/gallery/start-simple/kmeans-example/)
```
cd /srv/shiny-server
sudo mkdir kmeans
cd kmeans
sudo wget http://withr.me/misc/kmeans/ui.R
sudo wget http://withr.me/misc/kmeans/server.R
```
##### Or create server.R, ui.R and copy these code:
${\textsf{\color{blue}These code for server.R}}$
```
function(input, output, session) {

  # Combine the selected variables into a new data frame
  selectedData <- reactive({
    iris[, c(input$xcol, input$ycol)]
  })

  clusters <- reactive({
    kmeans(selectedData(), input$clusters)
  })

  output$plot1 <- renderPlot({
    palette(c("#E41A1C", "#377EB8", "#4DAF4A", "#984EA3",
      "#FF7F00", "#FFFF33", "#A65628", "#F781BF", "#999999"))

    par(mar = c(5.1, 4.1, 0, 1))
    plot(selectedData(),
         col = clusters()$cluster,
         pch = 20, cex = 3)
    points(clusters()$centers, pch = 4, cex = 4, lwd = 4)
  })

}
```
${\textsf{\color{blue}These code for ui.R}}$
```
# k-means only works with numerical variables,
# so don't give the user the option to select
# a categorical variable
vars <- setdiff(names(iris), "Species")

pageWithSidebar(
  headerPanel('Iris k-means clustering'),
  sidebarPanel(
    selectInput('xcol', 'X Variable', vars),
    selectInput('ycol', 'Y Variable', vars, selected = vars[[2]]),
    numericInput('clusters', 'Cluster count', 3, min = 1, max = 9)
  ),
  mainPanel(
    plotOutput('plot1')
  )
)
```
## Change file permissions to let Shiny Server get access
```
sudo chmod 777 -R /srv
sudo shiny-server
```

# Now open your web browser and type: RPi-IP:3838 in its address input
${\textsf{\color{blue}type: ifconfig to know your Raspberry IP}}$
```
ifconfig
```
