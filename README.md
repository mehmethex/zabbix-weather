# zabbix-weather
Collect weather conditions from openweathermap.org using host coordinates and Zabbix built-in HTTP agent + some JS preprocessing (Zabbix 4.2), HTTP agent only ( Zabbix 4.0 ) or perl script(Zabbix < 4.0).  


## Get key from openweathermap  
http://openweathermap.org/appid  

## Template  
1. Import sample template for Zabbix 2.4-3.4 or for Zabbix 4.2, 4.0.  

Currently supported items:  

| Item       |       key |        via script(Zabbix 2.4+) |     via HTTP agent(Zabbix 4.0) |  via HTTP agent+JS* (Zabbix 4.2+) |
|------------|-----------|------------|------------|-----|  
| Temperature | temp | Y | Y | Y|  
|Humidity|humidity|Y|Y|Y|  
|Visibility|visibility|Y|Y|Y|  
|Is dark|is_dark|Y|N|Y*|  
|Wind speed|wind.speed|Y|Y|Y|  
|Weather status|weather|Y|Y|Y|  
|Weather description|weather.description|Y|Y|Y|  
|Weather condition id |weather.condition.id|Y|Y|Y|  



2.	Add global/template level Macro  
`{$WEATHER_APIKEY} = YOURKEY`  
add another global macro as prefered language for weather descriptions:  
`{$WEATHER_LANG} = en`  


## Setup hosts    
Add macros {$LAT} and {$LON} for each host that requires weather monitoring:  
![image](https://cloud.githubusercontent.com/assets/14870891/21159303/c87f61a2-c191-11e6-8f49-638d877b06a6.png)

## Install the script(only for Zabbix < 4.0)
For Zabbix 2.4-3.4, please read below how to setup the collector script.  
0. Dependencies  
The script is written in Perl and you will need common modules in order to run it:  
```
LWP
JSON::XS
```
There are numerous ways to install them:  

| in Debian  | In Centos | using CPAN | using cpanm|  
|------------|-----------|------------|------------|  
|  `apt-get install libwww-perl libjson-xs-perl` | `yum install perl-JSON-XS perl-libwww-perl perl-LWP-Protocol-https` | `PERL_MM_USE_DEFAULT=1 perl -MCPAN -e 'install Bundle::LWP'` and  `PERL_MM_USE_DEFAULT=1 perl -MCPAN -e 'install JSON::XS'`| `cpanm install LWP` and `cpanm install JSON::XS`| 
1. place get_weather.pl into externalscripts folder  
then:  
`chmod +x get_weather.pl`  
`chown zabbix:zabbix get_weather.pl`  
2. Check that it is working  
`./get_weather.pl -lat 55.672944 -lon 38.478944 --hostname TEST --api_key YOURKEY`  
You should receive `OK` as script output.  

2.1. If you need to use http proxy:  
Make sure that you have environment variables `http_proxy` and `https_proxy` defined under user `zabbix`  
For example:  
```
export http_proxy=http://proxy_ip:3129/
export https_proxy=$http_proxy  
./get_weather.pl -lat 55.672944 -lon 38.478944 --hostname TEST --api_key YOURKEY  
```
## Demo  
And that is it:  
![screencapture--1481663617632](https://cloud.githubusercontent.com/assets/14870891/21159567/c8e270de-c192-11e6-9452-cf1ed5251f60.png)

![image](https://user-images.githubusercontent.com/14870891/54673763-0067bc80-4b0c-11e9-9a90-7bfcaea8beeb.png)


## More  
Note: It would more logical to use Latitue and Longitude Inventory fields and not user macros, but {INVENTORY.*} values cannot be used in items keys.  
A feature request to change that is here: https://support.zabbix.com/browse/ZBXNEXT-2546.

