```js
var ajax = function (opts) {
    function format(data) {
        var arr = []
        for (var name in data) {
            arr.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]))
        }
        return arr.join('&')
    }

    if (opts.dataType == 'jsonp') {
        var callback = ('jsonp_' + new Date().getTime())
        opts.data.callback = callback
        var script = document.createElement('script')
        script.src = opts.url + '?' + format(opts.data)
        script.onerror = function () {
            opts.error && opts.error()
        }
        window[callback] = function (res) {
            document.body.removeChild(script)
            window[callback] = null
            opts.success && opts.success(res)
        }
        document.body.appendChild(script)
    } else {
        var xhr = new XMLHttpRequest()
        var data = format(opts.data)
        xhr.open(opts.method.toUpperCase(), opts.url + ((type == 'GET') ? ('?' + data) : ''))
        xhr.onreadystatechange = function () {
            if (xhr.readyState == 4) {
                var _res = {}
                try {
                    _res = JSON.parse(xhr.responseText)
                } catch (e) {}
                if (xhr.status == 200) {
                    opts.success && opts.success(_res)
                } else {
                    opts.error && opts.error(_res)
                }
            }
        }
        xhr.send(data)
    }
}
```