# DataStream DSWS

[![Downloads](https://pepy.tech/badge/datastreamdsws)](https://pepy.tech/project/datastreamdsws) [![Downloads](https://pepy.tech/badge/datastreamdsws/month)](https://pepy.tech/project/datastreamdsws) [![Downloads](https://pepy.tech/badge/datastreamdsws/week)](https://pepy.tech/project/datastreamdsws)

A Python Wrapper for Refinitiv's Datastream Web Services (DSWS) Product.

To connect to the Refinitiv DataStream database via Datastream Web Services, a DataStream subscription and username / password combination is required.

**Please Note that this is an Official Beta Package**

For support on the package, pleae contact the Refinitv support team.

The package includes all functionalities required to get data from DataStream, in the form of Pandas DataFrames.

*The package refers to [this product](https://product.datastream.com). If the user needs to change this to another url or direct deliver IP, the config file can be used, with the path provided explicitly in the code. Please refer to [**Appendix 2**](#Appendix-2) for config details.*

## Requirements

This package requires Python 3.X to run.

```python
pip install pandas
pip install requests
pip install Urllib3
pip install DateTime
pip install pytz
```

## Usage

This library interacts with the [Refinitiv's](https://developers.refinitiv.com/en/api-catalog) API to:
- [Make time series requests](#Time-series-requests).
- [Get bundle data](#Using-get_bundle_data).
- [Retrieving data for a list.](#Retrieving-data-for-a-List)
- [Retrieving data for Expressions.](#Retrieving-data-for-Expressions)
- [Symbol substitution.](#Symbol-Substitution)
- [Retrieve data for NDOR.](#Retrieving-data-for-an-NDOR)
- [Retrieve data for a point in time.](#Retrieving-data-for-a-point-in-time)
- [Collect usage stats.](#Usage-Stats)
- [Return the instrument and Datatype names.](#Using-get_bundle_data-function)


In order to install the package:

```python
pip install DatastreamDSWS
```

### To import the package:

```python
import DatastreamDSWS as dsws
```

Configuring the url path:

```python
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>, config = <<<your config path>>>)
```

### Authenticate and use ```get_data``` to request data:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='VOD', fields=['P'], start='2017-01-01', end='-5D')
print(df.head())
```

If any network issues persist when running the above code, please refer to [Appendix 1](#Appendix-1).

### Time series requests:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='VOD', fields=['P', 'MV', 'VO'], start='-10D', end='-0D', freq='D')
print(df.head())
```

### Using ```get_bundle_data```:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
reqs = []
reqs.append(ds.post_user_request(tickers='VOD', fields=['VO', 'P'], start='2017-01-01', kind=0))
reqs.append(ds.post_user_request(tickers='U:BAC', fields=['P'], start='1975-01-01', end='0D', freq= 'Y'))

df = ds.get_bundle_data(bundleRequest=reqs)
print(df.head())
```

### Retrieving data for a List:

**Note**: The ```|L``` operator should be used for tickers, if passing in a list datatype.
**Note**: The ```kind``` parameter is set to 0 for static requests. By default, 'kind' is set to 1 for a TimeSeries request.

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers="LS&PCOMP|L", fields=["NAME"], kind=0)
print(df.head())
```

### Retrieving data for Expressions:

**Note**: The ```|E``` operator should be used for tickers, if passing in a Expressions.

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='PCH#(VOD(P), 3M|E', start="2018-01-01", end="-1M", freq="M")
print(df.head())
```

### Symbol Substitution:


```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='VOD, U:JPM', fields=['PCH#(X(P), -3M'], freq="M")
print(df.head())
```

### Retrieving data for an NDOR:

**Note**: The ```|D``` operator should be used for tickers, if passing in a NDOR.

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='USGDPâ€|D', fields=['DS.NDOR1'])
print(df.head())
```

### Retrieving data for a point in time:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='CNCONPRCF(DREL1)', fields=['(X)'], start='-2Y', end='0D', freq='M')
print(df.head())
```

### Usage Stats:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='STATS', fields=['DS.USERSTATS'], kind=0)
print(df.head())
```

### Returning Instrument and Datatype names:

This can be achieved using two methods:

**Note**: The ```retName``` parameter should explicitly be set as True for both the ```post_user_request``` and ```get_bundle_data``` functions.

#### Using ```get_data``` function:
```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)
df = ds.get_data(tickers='VOD, BARC', fields= ['PH', 'PL'], start= '-2D', retName=True)
print(df.head())
```

#### Using ```get_bundle_data``` function:
```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>)

b_reqs = []
b_reqs.append(ds.post_user_request(tickers='VOD, BARC', fields=['PH', 'PL'], start = '-2D', retName=True))
b_reqs.append(ds.post_user_request(tickers='R, ABGJ', fields=['WC05350'], kind=1, start = '2015-12-31', end='2016-12-31', freq='Y', retName=True))

df = ds.get_bundle_data(b_reqs, retName=True
print(df.head())
```


## Appendix-1

**Network Issues**

As we have implemented a 'HTTPS' connection, some of the clients may experience issues while using the package. Some of the common issues and possible resolutions are listed below:

#### Issue 1

```
ConnectionError(MaxRetryError("HTTPSConnectionPool(host='product.datastream.com', port=443): Max retries exceeded with url: /DSWSClient/V1/DSService.svc/rest/GetToken (Caused by NewConnectionError('<requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x00000000159514A8>: Failed to establish a new connection: [Errno 11004] getaddrinfo failed',))",),), <traceback object at 0x0000000015A60DC8>)
Please contact the network Admin team to provide the Proxy details.
```

Provide the proxy details in the code as shown below:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>, proxy='https://user:password@proxyip:port')
```

#### Issue 2

```
MaxRetryError: HTTPSConnectionPool(host='product.datastream.com', port=443): Max retries exceeded with url: /DSWSClient/V1/DSService.svc/rest/Token?username=xxxxx2&password=xxxxxx (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')])")))
```

Please find the SSL certificate path on your local machine and provide the same in the code as shown below:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>, sslCer='path\certfile')
```


## Appendix-2

The config file should contain the url that you need to connect to. A config.ini file needs to be created and the contents of the file should be as shown below:
```
[url] path=https://example.com
```

The ```config.ini``` file is provided on Github for your reference. The full path to the relavent config file should be provided, as demonstrated below:

```python
import DatastreamDSWS as dsws
ds = dsws.Datastream(username= <<<insert username>>>, password= <<<insert password>>>, config='<path>/config.ini')
```






