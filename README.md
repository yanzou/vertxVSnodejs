vertx VS nodejs
=============

vertx
-----------------
```javascript

var vertx = require('vertx');
var Server = function(port, host){
    var server = vertx.createHttpServer();
    this.use = function(handler){
        server.requestHandler(handler);
    };
    this.start = function() {
        server.listen(port, host);
    };
};

var handler = function(req) {
  req.response.end("<html><body><h1>Hello from vert.x using CommonJS!</h1></body></html>");
}

server = new Server(9000, '127.0.0.1');
server.use(handler);
server.start();

```

nodejs (using cluster)
--------------------------
```javascript
var http = require('http');
var cluster = require('cluster');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
    cluster.on('listening', function(worker, address){
    });
    cluster.on('exit', function(worker, code, signal) {
        console.log('worker ' + worker.process.pid + ' died');
    });
} else {
    http.createServer(function(req, res) {
        res.writeHead(200);
        res.end("<html><body><h1>Hello from vert.x using CommonJS!</h1></body></html>");
    }).listen(9001);
}
```


Benchmark
-------------------------------------
```shell
 ab -n 100000 -c 1000 http://127.0.0.1:9000/
 ab -n 100000 -c 1000 http://127.0.0.1:9001/
```

Vertx:
```
Document Path:          /
Document Length:        68 bytes

Concurrency Level:      1000
Time taken for tests:   18.491 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      10700000 bytes
HTML transferred:       6800000 bytes
Requests per second:    5407.97 [#/sec] (mean)
Time per request:       184.912 [ms] (mean)
Time per request:       0.185 [ms] (mean, across all concurrent requests)
Transfer rate:          565.09 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0  132 421.8     34    7052
Processing:    14   48  13.5     48     469
Waiting:        9   38  12.8     38     456
Total:         26  180 424.8     82    7112

Percentage of the requests served within a certain time (ms)
  50%     82
  66%     86
  75%     89
  80%     92
  90%    108
  95%   1084
  98%   1104
  99%   1293
 100%   7112 (longest request)
```

Nodejs
```
Document Path:          /
Document Length:        68 bytes

Concurrency Level:      1000
Time taken for tests:   23.513 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      14300000 bytes
HTML transferred:       6800000 bytes
Requests per second:    4252.96 [#/sec] (mean)
Time per request:       235.131 [ms] (mean)
Time per request:       0.235 [ms] (mean, across all concurrent requests)
Transfer rate:          593.92 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       25  100  32.6    100    1091
Processing:    37  134  25.7    131     382
Waiting:       20  105  28.0     96     361
Total:         73  234  37.4    233    1262

Percentage of the requests served within a certain time (ms)
  50%    233
  66%    245
  75%    251
  80%    253
  90%    259
  95%    264
  98%    273
  99%    284
 100%   1262 (longest request)
```

comparation
-------------------
<table>
    <tr>
        <td></td><td>vertx</td><td>node</td>
    </tr>
    <tr>
        <td>
          Time taken for tests:<br>   
          Complete requests:<br>      
          Failed requests:<br>        
          Total transferred:<br>      
          HTML transferred: <br>      
          Requests per second:<br>    
          Time per request: <br>      
          Time per request:<br>       
          Transfer rate: <br>         
        </td>
        <td>
          23.513 seconds<br>
          100000<br>
          0<br>
          14300000 bytes<br>
          6800000 bytes<br>
          4252.96 [#/sec] (mean)<br>
          235.131 [ms] (mean)<br>
          0.235 [ms] (mean, across all concurrent requests)<br>
          593.92 [Kbytes/sec] received<br>
        </td>
        <td>
          18.491 seconds<br>
          100000<br>
          0<br>
          10700000 bytes<br>
          6800000 bytes<br>
          5407.97 [#/sec] (mean)<br>
          184.912 [ms] (mean)<br>
          0.185 [ms] (mean, across all concurrent requests)<br>
          565.09 [Kbytes/sec] received<br>
        </td>
    </tr>
</table>




