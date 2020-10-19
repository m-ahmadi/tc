[![NPM](https://nodei.co/npm/tse-client.png)](https://nodei.co/npm/tse-client/)  
[![GitHub tag](https://img.shields.io/github/tag/m-ahmadi/tse-client.svg)](https://GitHub.com/m-ahmadi/tse-client/tags/)
[![GitHub issues](https://img.shields.io/github/issues/m-ahmadi/tse-client.svg)](https://GitHub.com/m-ahmadi/tse-client/issues/)
# TSE Client
A client for receiving stock data from the Tehran Stock Exchange (TSE).  
Works in Browser, Node, and as CLI.  
The previous versions `0.x` and `1.x` were a direct port of the [official Windows app](http://cdn.tsetmc.com/Site.aspx?ParTree=111A11).  

# CLI

#### Install:
```shell
npm install tse-client -g
```
#### Basic:
```shell
tse ذوب
tse ذوب فولاد خساپا شپنا
tse "شاخص کل6"
tse "شاخص کل فرابورس6" "شاخص کل (هم وزن)6"
```
#### Adjust prices:
```shell
tse آپ -j 1 # افزایش سرمایه + سود نقدی
tse آپ -j 2 # افزایش سرمایه
```
#### Select columns:
```shell
tse فملی -c "6,7,8,10"
tse فملی -c "6:OPEN 7:HIGH 8:LOW 10:CLOSE"
tse فملی -c "4:date 10:close 13:count 12:volume"
tse ls -A
tse ls -D
# defaults: "4:DATE 6:OPEN 7:HIGH 8:LOW 9:LAST 10:CLOSE 12:VOL"
```
#### History depth:
```shell
tse ذوب -b 3m       # سه ماه گذشته (پیشفرض)
tse ذوب -b 40d      # چهل روز گذشته
tse ذوب -b 2y       # دو سال گذشته
tse ذوب -b 13920101 # تاریخ شمسی
tse ذوب -b 13800101 # کمترین تاریخ ممکن
```
#### File generation:
```shell
tse کگل -o /mydir # output directory
tse کگل -n 3      # file name
tse کگل -x txt    # file extension
tse کگل -e ascii  # file encoding
tse کگل -l @      # file delimiter char
tse کگل -H        # file without headers
```
#### Select symbols from file:
```shell
tse ls -F "i=34" > car.txt
tse ls -F "i=27" > iron.txt
tse -i car.txt -o ./car-group
tse -i iron.txt -o ./iron-group
```
#### Select symbols by filter:
```shell
# tse -f "t= نوع نماد i= گروه صنعت m= نوع بازار"
tse -f "i=27"                 # گروه صنعت: فلزات اساسی
tse -f "i=27 t=300"           # گروه صنعت: فلزات اساسی & نوع نماد: سهم بورس
tse -f "m=4 i=27,53,38 t=404" # نوع بازار: پایه & گروه صنعت: فلزات و سیمان و قند & نوع نماد: حق تقدم
tse ls -T
tse ls -I
tse ls -M
tse ls -F "i=27 t=300" # گروه فلزات بازار بورس
tse ls -F "i=27 t=303" # گروه فلزات بازار فرابورس
tse ls -F "i=27 t=309" # گروه فلزات بازار پایه
tse ls -F "i=34 t=303" # گروه خودرو بازار بورس
tse ls -F "i=34 t=309" # گروه خودرو بازار پایه
```
#### Save settings:
```
tse ذوب فولاد -o ./mytse --save
tse
tse -x txt
tse -n 3 --save
tse -o ./myother --save
tse
```
#### View saved settings and more:
```
tse ls -S
tse ls -D
tse ls -F "i=34"
tse ls -F "i=27 t=300"
tse ls -M -T -I
tse ls -T -O 1  # order by count (descending)
tse ls -T -O 1_ # order by count (ascending)
tse ls -h
```


# Node
#### Install:
```shell
npm install tse-client
```
#### Usage:
```javascript
const tse = require('tse');

(async () => {
  let { prices, error } = await tse.getPrices(['ذوب', 'فولاد']);
  if (!error) console.log(prices);

  let { prices } = await tse.getPrices(['خساپا'], {adjustPrices: 1});

  let { prices } = await tse.getPrices(['شپنا'], {columns: [4,7,8]});

  let { prices } = await tse.getPrices(['شپنا'], {columns: [[4,'DATE'],[7,'MAX'],[8,'MIN']]}); 
  
  console.table(tse.columnList);
})();
```

# Browser
#### Using standalone bundle:
*(bundled with the 4 dependencies)*
```html
<script src="https://cdn.jsdelivr.net/npm/tse-client/dist/tse.bundle.min.js"></script>
<script>
  (async () => {
    const { prices: data, error } = await tse.getPrices(['ذوب', 'فولاد']);
    if (!error) console.log(data);
    
    const { prices: adjustedData } = await tse.getPrices(['خساپا'], {adjustPrices: 1});
  
    const { prices: customCols1 } = await tse.getPrices(['شپنا'], {columns: [4,7,8]}); // default names
    const { prices: customCols2 } = await tse.getPrices(['شپنا'], {columns: [[4,'DATE'],[7,'MAX'],[8,'MIN']]}); // custom names
    
    console.table(tse.columnList); // view column indexes and their names
  })();
</script>
```

#### Using the module itself:
*(dependencies must be loaded before)*
```html
<script src="https://cdn.jsdelivr.net/npm/big.js"></script>
<script src="https://cdn.jsdelivr.net/npm/localforage"></script>
<script src="https://cdn.jsdelivr.net/npm/jalaali-js/dist/jalaali.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/pako/dist/pako.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/tse-client/dist/tse.min.js"></script>
<script>
  tse.getPrices(['فولاد']).then(([prices]) => console.log(prices));
</script>
```

#### Using behind a simple `PHP` proxy:
```php
// proxy.php
<?php
$t  = isset($_GET['t'])  ? $_GET['t']  : '';
$a  = isset($_GET['a'])  ? $_GET['a']  : '';
$a2 = isset($_GET['a2']) ? $_GET['a2'] : '';

echo file_get_contents("http://service.tsetmc.com/tsev2/data/TseClient2.aspx?t=$t&a=$a&a2=$a2");
?>
```
```javascript
tse.API_URL = 'http://path/to/proxy.php';
(async function () {
  const data = await tse.getPrices(['فملی']);
})();
```
#### Some Info:
| file | desc
|------------------------|-----------------------|
|`dist/tse.js`           | Unminified code. |
|`dist/tse.min.js`       | Minified code. |
|`dist/tse.bundle.min.js`| Minified dependencies + minified code. |

dependency | desc
-------|-------------
`big.js`      | `Required`. For price adjustment calculations.
`localforage` | `Required`. For storing in `indexedDB`. 
`jalaali-js`  | `Optional`. Only needed for `ShamsiDate` column. *(Recommanded to not exclude this, though you can)*
`pako`        | `Optional`. For compression of stored data. *(Highly recommanded, total data: 300 MB, compressed: 30 MB)*


# API

### `tse.API_URL`
The API URL to use for HTTP requests.  
Only string and valid URL.  
Default: `http://service.tsetmc.com/tsev2/data/TseClient2.aspx`
### `tse.UPDATE_INTERVAL`
Update data only if these many days have passed since the last update.  
Only integers.  
Default: `1`
### `tse.PRICES_UPDATE_CHUNK`
Amount of instruments per request.  
Only integers.  
Min: `1`  
Max: `60`  
Default: `10` in *Browser*. `50` in *Node*.
### `tse.PRICES_UPDATE_CHUNK_DELAY`
Amount of delay (in ms) to wait before requesting another chunk of instruments.  
Default: `500` in *Browser*. `3000` in *Node*.
### `tse.PRICES_UPDATE_RETRY_COUNT`
Amount of retry attempts before giving up.  
Only integers.  
Default: `3`
### `tse.PRICES_UPDATE_RETRY_DELAY`
Amount of delay (in ms) to wait before making another retry.  
Only integers.  
Default: `5000`
### `tse.getInstruments(struct=true, arr=true, structKey='InsCode')`
Update (if needed) and return list of instruments.
### `tse.getPrices(symbols=['','',...], ?settings={...})`
Update (if needed) and return prices of instruments.
```javascript
const defaultSettings = {
  columns: [
    [4, 'date'],
    [6, 'open'],
    [7, 'high'],
    [8, 'low'],
    [9, 'last'],
    [10, 'close'],
    [12, 'vol']
  ],
  adjustPrices: 0,
  daysWithoutTrade: false,
  startDate: '20010321'
};

const prices = await tse.getPrices(symbols=['sym1', 'sym2', ...], defaultSettings);
/*
  prices: [
    // sym1 prices
    {
      open:  [0, 0, ...],
      high:  [0, 0, ...],
      low:   [0, 0, ...],
      last:  [0, 0, ...]
      close: [0, 0, ...],
      vol:   [0, 0, ...]
    },
    
    // sym2 prices
    {
      open: [],
      high: [],
      ...
    },
    
    ...
  ]
*/
```
adjustPrices | desc | desc fa
-------------|------|---------
0 | none | بدون تعدیل
1 | capital increase + dividends | افزایش سرمایه + سود
2 | capital increase | افزایش سرمایه

### `tse.columnList`
A list of all possible columns.
index | name | fname
------|------|------------------
0  | CompanyCode    | کد شرکت
1  | LatinName      | نام لاتین
2  | Symbol         | نماد
3  | Name           | نام
4  | Date           | تاریخ میلادی
5  | ShamsiDate     | تاریخ شمسی
6  | PriceFirst     | اولین قیمت
7  | PriceMax       | بیشترین قیمت
8  | PriceMin       | کمترین قیمت
9  | LastPrice      | آخرین قیمت
10 | ClosingPrice   | قیمت پایانی
11 | Price          | ارزش
12 | Volume         | حجم
13 | Count          | تعداد معاملات
14 | PriceYesterday | قیمت دیروز

# Some Notes
- `Instrument.Symbol` characters are cleaned from `zero-width` characters, `ك` and  `ي`.  
- The price adjustment algorithm is still a direct port of the [official Windows app](http://cdn.tsetmc.com/Site.aspx?ParTree=111A11).
- In Browser, the `InstrumentAndShare` data is stored in `localStorage`.
- In Browser, the `ClosingPrices` data is stored in `indexedDB`.