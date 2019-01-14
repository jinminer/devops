### docker install failed



![docker-install-failed](D:\advance\jinm\learn\devops\docker\docker-install-failed.png)



> [**solution**](https://blog.csdn.net/zengmingen/article/details/78318349)  
>
> 需要更新下epel第三方软件库
>
> yum没有找到docker包，更新epel第三方软件库
>
>





http://60.205.189.50:4000



### container virtual-machine 



![container-vm](D:\advance\jinm\learn\devops\docker\container-vm.png)





* `docker command` 

  * `get test` 

    * jkljlk

    ```shell
    1882  sudo tee /etc/docker/daemon.json <<-'EOF'\n{\n  "registry-mirrors": ["https://lu0mvbm9.mirror.aliyuncs.com"]\n}\nEOF
     1883  sudo systemctl daemon-reload 
     1884  sudo systemctl restart docker
     1885  more /etc/docker/daemon.json  
     1886  exit
     1887  more /etc/docker/daemon.json  
     1888  docker run hello-world  
     1889  docker info 
     1890  docker --help 
     1891  docker images 
     1892  docker images -a  
     1893  docker images -q  
     1894  docker images 
     1895  docker images -a 
     1896  docker image --digests
     1897  docker image --help 
     1898  docker images --help  
     1899  docker images --digests 
     1900  docker images --no-trunc 
     1901  docker images -a  
     1902  docker image --help  
     1903  docker image rm 4b09c85e4c5b
     1904  docker images --no-trunc  
     1905  docker images -qa
     1906  docker search tomcat
     1907  docker search -s 30 tomcat
     1908  docker search --filter=stars=30 tomcat
     1909  docker search --filter=stars=100 tomcat
     1910  docker search nginx
     1911  docker pull nginx 
     1912  docker images 
     1913  docker rmi helloworld 
     1914  docker rmi hello-world 
     1915  docker rmi -f nginx  
     1916  docker images 
     1917  docker rmi 07695c7a038d 
     1918  docker rmi -f 07695c7a038d 
     1919  docker images 
     1920  docker rmi -f 315798907716   
     1921  docker images 
     1922  docker rmi -f entrypoint:v0.1   
     1923  docker images 
     1924  docker rmi -f keanemer/entrypoint:  
     1925  docker rmi -f keanemer/entrypoint:v0.1 
     1926  docker imags
     1927  docker images 
     1928  docker rmi -f f090c78858fa   
     1929  docker rmi -f d650dd0c5864     
     1930  docker images 
     1931  docker rmi f090c78858fa  
     1932  docker images 
     1933  docker ps -a  
     1934  docker container rm  eb549dbea6c2 
     1935  docker container rm $(docker ps -a)  
     1936  docker rm $(docker ps -a) 
     1937  docker rm $(docker ps -aq) 
     1938  docker images 
     1939  docker ps -a  
     1940  docker rmi -f docker.io/hello-world:latest  
     1941  docker images 
     1942  df 
     1943  docker images 
     1944  docker image -a
     1945  docker images --help  
     1946  docker rmi $(docker images -aq)  
     1947  docker images 
     1948  docker ps -a  
     1949  docker pull tomcat 
     1950  more /etc/docker/daemon.json 
     1951  docker images 
     1952  docker run -ti docker.io/tomcat:latest 
     1953  curl http://localhost
     1954  docker ps -a  
     1955  docker container start 2e0acefab3a6 
     1956  docker ps -a  
     1957  curl http://localhost
     1958  ll 
     1959  curl http://localhost:8080
     1960  docker images 
     1961  docker ps -l  
     1962  docker ps -3 
     1963  docker ps -1
     1964  docker ps -n 3
     1965  docker pull hello-world 
     1966  docker images 
     1967  docker ps 
     1968  docker kill 2e0acefab3a6 
     1969  docker ps 
     1970  docker run -it --name myhello hello-world
     1971  docker ps 
     1972  docker images 
     1973  docker ps -a  
     1974  docker run -it tomcat:latest
     1975  ll 
     1976  docker ps -a  
     1977  docker run -it 51d7a41f9e8c
     1978  docker run -it 2e0acefab3a6
     1979  docker images 
     1980  docker run -it 1a51cb5e3006
     1981  curl http://localhost:8080 
     1982  curl http://localhost
     1983  curl http://localhost:8848
     1984  docker ps -a  
     1985  docker images  
     1986  docker run -it -p8848:8080 1a51cb5e3006
     1987  curl http://localhost 
     1988  curl http://localhost:8848
     1989  telnet localhost 8848
     1990  docker images 
     1991  docker run -d 1a51cb5e3006  
     1992  docker ps -a 
     1993  curl http://localhost:8848 
     1994  docker ps -a  
     1995  docker stop d687cd99f9e3 
     1996  docker ps -a  
     1997  docker images 
     1998  docker run -d -p8848:8080 1a51cb5e3006  
     1999  curl http://localhost:8848
     2000  telnet localhost 8848 
     2001  cd /home/jinm/workspace/docker/demo/
     2002  ll 
     2003* cd /home/jinm/workspace/docker/demo/
     2004* ll 
     2005* docker ps -a  
     2006* docker exec --help 
     2007* docker ps  
     2008* docker exec -t 1a51cb5e3006 
     2009* docker exec -t 0555bc0e4355  
     2010* docker exec -t 0555bc0e4355 bash
     2011* docker ps -a  
     2012* docker stop  0555bc0e4355 
     2013* docker ps -a  
     2014* docker run -d -p 8848:8080 --name=mytomcat tomcat  
     2015* docker ps -a  
     2016* curl http://localhost:8848 
     2017* wget http://localhost:8848 
     2018* docker ps -a  
     2019* docker exec -t 06b2ae01204b bash 
     2020* docker exec -ti 06b2ae01204b bash 
     2021  docker ps -a  
     2022  docker commit -m="create image from current container" -a="keanemer" 06b2ae01204b  "tomcat2:v2.0"
     2023  docker images 
     2024  docker run -d -p 80:8080 --name tomcat-docs2 tomcat2:v2.0 
     2025  docker ps -a  
     2026  history
     2027  docker exec -it tomcat-docs2 bash
     2028  docker ps -a  
     2029  docker exec tomcat-docs2 bash
     2030  docker exec -ti  tomcat-docs2 bash
     2031  docker ps -a  
     2032  docker cp --help  
     2033* docker ps -a 
     2034* docker exec -ti tomcat-docs2 bash
     2035  docker cp -a 283184fcc243:/usr/local/tomcat/BUILDING.txt . 
     2036  ll 
     2037  docker images
    ```


