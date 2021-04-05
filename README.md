# stocks-1
How to retrieve pcap, down sample, load stock archive data into NoSQL db

Pull historical archive T-N days in PCAP format - from IEX at:
 https://iextrading.com/trading/market-data/#hist-download
 https://iextrading.com/iex-historical-data-terms/ - data use and IEX citation for providing complete record
 https://iextrading.com/trading/market-data/ - pcap format specifications are found here
 
 https://iexcloud.io/docs/api/ - the official IEX Api useful to pull individual stocks
 https://github.com/timpalpant/go-iex - a GOlang wrapper around IEX Api to read the archived pcap data
 Go-IEX has a utility called pcap2csv with default configuration that creates one second "bar" data.
 One second "bar" data downsamples original data from milliseconds realm to 1 second between stock samples.
 Individual bar data timestamps are now 
 e.g. 
      go run pcap2csv/main.go < 21-04-04.pcap.gz > 21-04-04.csv
      more 21-04-04.csv
symbol,time,open,high,low,close,volume
ABNB,2021-04-01T12:00:00Z,191.1800,191.1800,191.1800,191.1800,2
ACY,2021-04-01T12:00:00Z,4.3400,4.3400,4.3400,4.3400,1
AGFY,2021-04-01T12:00:00Z,13.2800,13.2800,13.2800,13.2800,10
AMZN,2021-04-01T12:00:00Z,3113.1300,3113.1300,3113.1300,3113.1300,6
APPS,2021-04-01T12:00:00Z,83.5000,83.5000,83.5000,83.5000,25
BA,2021-04-01T12:00:00Z,255.9700,255.9700,255.9700,255.9700,34
BIDU,2021-04-01T12:00:00Z,223.3100,223.6000,223.3100,223.6000,73
BLNK,2021-04-01T12:00:00Z,43.4200,43.4200,43.4200,43.4200,71
CHPT,2021-04-01T12:00:00Z,30.5200,30.5200,30.3600,30.3600,60
CLSK,2021-04-01T12:00:00Z,24.3500,24.3500,24.3200,24.3200,200
CTRM,2021-04-01T12:00:00Z,0.7540,0.7540,0.7540,0.7540,60
DOCU,2021-04-01T12:00:00Z,205.7800,205.7800,205.7800,205.7800,10
EH,2021-04-01T12:00:00Z,38.8200,38.8200,38.8200,38.8200,25
FB,2021-04-01T12:00:00Z,298.2400,298.2400,297.9500,298.1000,142
FCEL,2021-04-01T12:00:00Z,15.3200,15.3200,15.3200,15.3200,200
FNKO,2021-04-01T12:00:00Z,22.4600,22.5000,22.4600,22.5000,105
FTFT,2021-04-01T12:00:00Z,7.7800,7.7800,7.7800,7.7800,600
GILD,2021-04-01T12:00:00Z,65.7000,65.7000,65.6900,65.6900,60

Pulling one stock out yields 1 second interval using grep (general regular expression processor) 
grep TSLA 21-04-04.csvTSLA,2021-04-01T19:37:00Z,662.3500,662.4000,661.4500,661.6700,3662
.....
TSLA,2021-04-01T19:38:00Z,661.7800,661.9900,660.7200,660.7300,1908
TSLA,2021-04-01T19:39:00Z,660.7400,661.0100,660.3700,660.4400,4079
TSLA,2021-04-01T19:40:00Z,660.4000,661.1400,660.4000,661.0000,2990
TSLA,2021-04-01T19:41:00Z,660.9800,661.2800,660.9800,661.1300,1266
TSLA,2021-04-01T19:42:00Z,661.6000,661.6000,660.8200,660.8200,1049
TSLA,2021-04-01T19:43:00Z,660.8000,660.8400,659.5200,660.4900,3347
TSLA,2021-04-01T19:44:00Z,660.4800,660.5500,659.8500,659.9700,1832
TSLA,2021-04-01T19:45:00Z,659.9300,659.9800,659.5200,659.8000,1914
......

The CSV file is measured in ~50 MB, the original pcap file is around 2.5 Gb - a 50x reduction
Several NoSQL DB exist that can quickly ingest CSV files. 
e.g. ELK stack,Aerospike, InfluxDB

In ELK's Kibana Node/React subsystem a Drag-n-Drop utility can ingest CSV files with less than 100M.
ELK is not as fast as InfluxDB nor Aerospike, since the latter are written in Go-lang and C respectively.
Many analytic processes utilize ELT - Extract, Transform, Load.
From a traceability standpoint LET - Load, Extract/Transform - if possible keeps original data sets intact.
Downstream processes can then Extract/Transform elements. This enables team to execute on parallel analyses and visualization.
On a laptop with hard disk the 50M file contained 735K records with fields - symbol,time,open,high,low,close,volume and took roughly 2 minutes to import into the Elasticsearch index and be available for visualization and other actions.

Transposition of above format enables side by side comparison of stocks at same time stamp; useful for applying 1st order statistics. Header in file is all the stock symbols on one line that appear to be several lines in this readme file.
Followed by same second numerical lines starting on line 2.
TBD
1) I will post the bash shell file script that does this transformation from the original file.
2) ? 
3) 
e.g.
AA,AAL,AAPL,ABBV,ABT,A,ADM,ADP,AEO,AIG,ALEC,ALLY,ALXN,AMAT,AM,AMD,AMZN,ANTM,APA,APO,APTV,ARNC,ATUS,ATVI,AVTR,AXTA,AZPN,BABA,BAC,BBD,BEN,BIDU,BMY,BP,BSX,BWA
,BX,CCL,C,CFG,CHD,CHK,CI,CL,CLF,CLR,CMCSA,CNC,CNQ,CNX,COG,COMM,COP,CREE,CRM,CSCO,CSX,CTSH,CTVA,CVX,DAL,DAR,D,DD,DE,DELL,DIS,DLTR,DOW,DVN,DXC,EA,EBAY,EEM,EF
A,ELAN,EOG,EPD,EQH,EQR,ET,ETRN,EWZ,EXC,EXPE,FANG,FAST,FB,FBHS,F,FCX,FDX,FE,FIS,FITB,FLEX,FTCH,FTI,FXI,GD,GDX,GE,GFI,GILD,GLPI,GLW,GM,GNTX,GO,GPK,GS,GT,HAL,
HBAN,HD,HES,HOG,HOLX,HP,HPE,HPQ,HST,IBM,ILMN,INFY,INTC,IP,IVZ,IWM,JBLU,JCI,JD,JHG,JNJ,K,KEX,KEY,KEYS,KHC,KIM,KKR,KMI,KO,LB,LITE,LLY,LRCX,LSCC,LSTR,LVS,MA,M
CD,M,MDLZ,MDRX,MDT,MGM,MIK,MLCO,MMC,MNRO,MO,MOS,MPC,MRK,MRO,MRVL,MS,MSFT,MTOR,MU,MUR,MXIM,NCLH,NIO,NKE,NOW,NTAP,NUAN,NVST,NXPI,NYCB,O,OLN,OMC,ON,ORCL,OXY,P
AA,PAGP,PDD,PENN,PFE,PG,PGR,PINS,PKG,PM,PSX,PTEN,PYPL,QCOM,QQQ,QRTEA,QRVO,RCL,RDS.A,RF,RIG,ROK,RRC,RUN,SBUX,SCHW,SHW,SKT,SKX,SLB,SLM,SNAP,SO,SPR,SPY,STLD,S
TX,SYF,SYK,SYY,TAL,T,TEVA,TGT,TME,TMHC,TMO,TPR,TSCO,TSM,TWTR,TXN,TXT,UAL,UBER,UNH,UPS,URI,USB,VALE,VAR,V,VIAV,VIPS,VLO,VST,VTR,VXX,VZ,WCC,WDC,WFC,WMB,WMT,W
U,WW,WY,X,XEL,XLE,XLF,XLI,XLK,XLNX,XLV,XOM,YUMC,ZNGA,TSLA
32.5300,24.0000,123.4000,108.8900,120.5600,128.0700,57.1600,190.3500,29.6300,46.1200,20.4000,45.8000,153.0200,136.5000,9.0900,79.9500,3113.1300,358.9600,18
.2400,47.7400,140.2300,25.5500,32.6700,93.9300,29.2200,29.7600,147.8650,230.0000,39.0600,4.6500,29.8800,223.6000,63.3100,24.3700,38.6500,46.7100,75.4600,26
.8500,72.4400,44.1900,87.2000,44.2700,242.8000,78.6600,20.1600,26.2700,54.3300,64.2300,31.0100,14.7800,18.9800,15.4500,53.1000,111.3800,213.8200,51.8800,97
.1600,79.1200,46.9400,105.1400,48.3000,74.6400,75.6700,77.8000,373.4900,88.4600,185.2600,114.9100,64.2400,22.2100,31.3500,137.2100,61.7400,54.1750,76.2650,
29.7500,72.7700,22.2700,32.6800,72.0300,7.7100,8.1700,33.3050,43.6600,173.5300,74.4900,50.6900,298.2400,96.7500,12.3200,33.0800,283.8100,34.6300,141.5500,3
7.4150,18.4700,53.5900,7.7300,47.7150,181.8700,32.9600,13.2650,9.9600,65.7000,42.6700,43.9000,57.6800,35.9600,37.0000,18.1350,324.8900,17.6300,21.6650,15.7
700,306.3400,71.7900,40.2050,74.8100,27.3000,15.7800,32.0700,16.9200,133.6900,393.4600,18.9300,64.5600,54.0200,25.3900,222.4100,20.4900,59.9300,86.2600,31.
3300,162.5300,63.2400,60.9700,19.9800,143.9900,40.0200,18.9600,49.6100,16.6800,52.8400,62.1900,92.3900,187.4000,606.7900,46.2800,165.4200,61.2300,357.6850,
224.2400,16.3000,58.5100,15.3400,118.7500,38.2400,21.9250,20.2500,121.9300,65.4400,51.1500,32.1900,53.7950,77.4600,10.9400,50.1400,77.6200,238.5800,29.8800
,92.3400,16.2400,92.9600,27.9000,41.1300,133.8000,508.7500,73.6100,44.6500,40.8050,205.4500,12.6600,63.7600,38.8100,74.6000,42.8200,70.6150,26.8200,9.1000,
9.3900,140.3200,106.9300,36.3000,135.6100,95.4500,75.6200,134.1150,88.8100,81.6600,7.2800,246.8300,134.4800,322.6100,12.0900,186.2100,86.7400,39.4700,20.68
00,3.6000,266.9100,10.5450,62.8400,109.6400,65.3800,252.7100,15.7200,42.0400,27.5100,18.0700,53.1800,62.2100,49.5000,397.9100,50.4800,76.8600,41.0300,244.1
500,78.6800,56.0300,30.3600,11.6550,198.6400,20.9400,31.4100,461.5000,41.7400,177.8600,121.3000,64.8000,192.1400,56.0600,57.8000,55.4800,372.6750,170.3700,
332.2100,55.2800,17.4300,176.5800,213.1100,15.9200,30.8800,72.2800,17.6900,53.8500,11.2900,58.0700,88.0700,70.0000,39.0400,23.7000,136.2800,24.8300,31.5300
,35.9700,25.7400,66.4300,49.0700,34.0900,98.7600,134.3800,126.8800,116.9200,56.0800,59.8000,10.3500,682.9700
....

In this case I kept the first 300 seconds worth - since it was Friday before Easter Sunday many stocks held at a steady price.

--------------------------
Now use influxdb to push same CSV; setup influx with a configuration:
influx setup --org example-org --bucket example-bucket --username example-user --password ExAmPl3PA55W0rD --force --name new-name

Add a new line onto header of original file to declare datatypes; kibana Drag-n-Drop CSV file guesses data type and if wrong a user can override the guess.

File lines look like this now:time influx write -b example-bucket -f $PWD/21-04-04.csv
real	0m31.247s
user	0m15.840s
sys	0m2.414s

Approx. 31 seconds for 735K records








      
