# Use Shiny apps on your Shiny-Server with load-balancing and Docker
The Open-Sourcer Shiny-Server comes with quite some limitations. One is, that only one user at a time can access a single shiny app.
So if two users want to use your app, they have to wait for calculations to be finished of the other person.  
This is anoying in case you have larger/computationally intensive shiny apps.

To circumvent this issue, I started with a really nice tutorial available at http://jaehyeon-kim.github.io/2016/05/Some-Thoughts-On-Shiny-Open-Source-Internal-Load-Balancing.html , in which another shiny application is sued to perform load balancing.

Since I use Docker to wrap the shiny-server and its apps, I implemented this approach into a docker container - without success.
The issue I had was shiny-server not allowing me to have subfolder apps within an app folder.

So I went on and realized that NGINX is able to perform a soft load-balancing.
My problem was, that my shiny application CRISPRAnalyzeR available at http://www.crispr-analyzer.org or http://www.github.com/boutroslab/CRISPRAnalyzeR is also hosted as service behind a proxy, so that different folders are a nogo.

My plan was the following:
Since CRISPRAnalyzeR was coded in a way that there is one main folder with all required files, which can be separated from the ui.R and server.R necessary for the shiny-server to start the app, I could simply have multiple copies running as independent apps - without blowing up the size of the docker container.

1. Make multiple copies of the application
2. Leave shiny-server running as is (directory mode)
3. Add NGINX to handle all requests entering the docker container
4. With NGINX, distribute all requests depending on the usage to different copies of my app

The basics are, that you can sue NGINX as a reverse proxy in combination with soft load-balancing to different ports instead of directories.
