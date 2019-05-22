# common-utils
整理平时使用的工具函数
```javascript

/*
* author:w候人兮猗(ahwgs)
* url:https://www.ahwgs.cn
* github:https://github.com/ahwgs/common-utils/blob/master/utils
* */

/**
 * 是否是数组
 * @param value
 * @returns {boolean}
 */
export const isArray = (value) => {
    return toString.call(value) === '[object Array]';
}

/**
 * 是否是ArrayBuffer
 * @param val
 * @returns {boolean}
 */
export const isArrayBuffer = (val) => {
    return toString.call(val) === '[object ArrayBuffer]';
}

/**
 * 是否是FormData
 * @param val
 * @returns {boolean}
 */
export const isFormData = (val) => {
    return (typeof FormData !== 'undefined') && (val instanceof FormData);
}

/**
 * 确定值是否为ArrayBuffer上的视图
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a view on an ArrayBuffer, otherwise false
 */
export const isArrayBufferView = (val) => {
    let result;
    if ((typeof ArrayBuffer !== 'undefined') && (ArrayBuffer.isView)) {
        result = ArrayBuffer.isView(val);
    } else {
        result = (val) && (val.buffer) && (val.buffer instanceof ArrayBuffer);
    }
    return result;
}

/**
 * 是否是字符串
 * @param val
 * @returns {boolean}
 */
export const isString = (val) => {
    return typeof val === 'string';
}

/**
 * 是否是数字
 * @param val
 * @returns {boolean}
 */
export const isNumber = (val) => {
    return typeof val === 'number';
}

/**
 *  是否是undefined
 * @param val
 * @returns {boolean}
 */
export const isUndefined = (val) => {
    return typeof val === 'undefined';
}

/**
 * 是否是对象
 * @param val
 * @returns {boolean}
 */
export const isObject = (val) => {
    return val !== null && typeof val === 'object';
}

/**
 * 是否是时间对象
 * @param val
 * @returns {boolean}
 */
export const isDate = (val) => {
    return toString.call(val) === '[object Date]';
}

/**
 * 是否是文件对象
 * @param val
 * @returns {boolean}
 */
export const isFile = (val) => {
    return toString.call(val) === '[object File]';
}

/**
 * 是否是Blob对象
 * @param val
 * @returns {boolean}
 */
export const isBlob = (val) => {
    return toString.call(val) === '[object Blob]';
}

/**
 * 是否是方法
 * @param val
 * @returns {boolean}
 */
export const isFunction = (val) => {
    return toString.call(val) === '[object Function]';
}

/**
 * 是否是流
 * @param val
 * @returns {boolean}
 */
export const isStream = (val) => {
    return isObject(val) && isFunction(val.pipe);
}

/**
 * 是否是搜索参数
 * @param val
 * @returns {boolean}
 */
export const isURLSearchParams = (val) => {
    return typeof URLSearchParams !== 'undefined' && val instanceof URLSearchParams;
}

/**
 * trim 方法
 * @param str
 * @returns {string}
 */
export const trim = (str) => {
    return str.replace(/^\s*/, '').replace(/\s*$/, '');
}

/**
 * Determine if we're running in a standard browser environment
 *
 * This allows axios to run in a web worker, and react-native.
 * Both environments support XMLHttpRequest, but not fully standard globals.
 *
 * web workers:
 *  typeof window -> undefined
 *  typeof document -> undefined
 *
 * react-native:
 *  navigator.product -> 'ReactNative'
 */
export const isStandardBrowserEnv = () => {
    if (typeof navigator !== 'undefined' && navigator.product === 'ReactNative') {
        return false;
    }
    return (
        typeof window !== 'undefined' &&
        typeof document !== 'undefined'
    );
}

/**
 * Iterate over an Array or an Object invoking a function for each item.
 *
 * If `obj` is an Array callback will be called passing
 * the value, index, and complete array for each item.
 *
 * If 'obj' is an Object callback will be called passing
 * the value, key, and complete object for each property.
 *
 * @param {Object|Array} obj The object to iterate
 * @param {Function} fn The callback to invoke for each item
 */
export const forEach = (obj, fn) => {
    // Don't bother if no value provided
    if (obj === null || typeof obj === 'undefined') {
        return;
    }

    // Force an array if not already something iterable
    if (typeof obj !== 'object') {
        /*eslint no-param-reassign:0*/
        obj = [obj];
    }

    if (isArray(obj)) {
        // Iterate over array values
        for (var i = 0, l = obj.length; i < l; i++) {
            fn.call(null, obj[i], i, obj);
        }
    } else {
        // Iterate over object keys
        for (var key in obj) {
            if (Object.prototype.hasOwnProperty.call(obj, key)) {
                fn.call(null, obj[key], key, obj);
            }
        }
    }
}

/**
 * Accepts varargs expecting each argument to be an object, then
 * immutably merges the properties of each object and returns result.
 *
 * When multiple objects contain the same key the later object in
 * the arguments list will take precedence.
 *
 * Example:
 *
 * ```js
 * var result = merge({foo: 123}, {foo: 456});
 * console.log(result.foo); // outputs 456
 * ```
 *
 * @param {Object} obj1 Object to merge
 * @returns {Object} Result of all merge properties
 */
export const merge = (/* obj1, obj2, obj3, ... */) => {
    var result = {};

    function assignValue(val, key) {
        if (typeof result[key] === 'object' && typeof val === 'object') {
            result[key] = merge(result[key], val);
        } else {
            result[key] = val;
        }
    }

    for (var i = 0, l = arguments.length; i < l; i++) {
        forEach(arguments[i], assignValue);
    }
    return result;
}

/**
 * Extends object a by mutably adding to it the properties of object b.
 *
 * @param {Object} a The object to be extended
 * @param {Object} b The object to copy properties from
 * @param {Object} thisArg The object to bind function to
 * @return {Object} The resulting value of object a
 */
export const extend = (a, b, thisArg) => {
    forEach(b, function assignValue(val, key) {
        if (thisArg && typeof val === 'function') {
            a[key] = bind(val, thisArg);
        } else {
            a[key] = val;
        }
    });
    return a;
}

/**
 * 是否是绝对路径
 * @param url
 * @returns {boolean}
 */
export const isAbsoluteURL = (url) => {
    // A URL is considered absolute if it begins with "<scheme>://" or "//" (protocol-relative URL).
    // RFC 3986 defines scheme name as a sequence of characters beginning with a letter and followed
    // by any combination of letters, digits, plus, period, or hyphen.
    return /^([a-z][a-z\d\+\-\.]*:)?\/\//i.test(url);
};

/**
 * 防抖函数
 * @param method 事件触发的操作
 * @param delay 多少毫秒内连续触发事件，不会执行
 * @returns {Function}
 */
export const debounce = (method, delay) => {
    let timer = null;
    return function () {
        let self = this,
            args = arguments;
        timer && clearTimeout(timer);
        timer = setTimeout(function () {
            method.apply(self, args);
        }, delay);
    }
}

/**
 * 节流函数
 * @param method 事件触发的操作
 * @param mustRunDelay 间隔多少毫秒需要触发一次事件
 */
export const throttle = (method, mustRunDelay) => {
    let timer,
        args = arguments,
        start;
    return function loop() {
        let self = this;
        let now = Date.now();
        if (!start) {
            start = now;
        }
        if (timer) {
            clearTimeout(timer);
        }
        if (now - start >= mustRunDelay) {
            method.apply(self, args);
            start = now;
        } else {
            timer = setTimeout(function () {
                loop.apply(self, args);
            }, 50);
        }
    }
}

/**
 * 添加收藏功能
 * @param url
 * @param title
 */
export const addFavorite = (url, title) => {
    var ua = navigator.userAgent.toLowerCase();
    if (ua.indexOf("360se") > -1) {
        alert("由于360浏览器功能限制，请按手动收藏！");
    } else if (document.all) {
        // window.external.AddToFavoritesBar(url, title); //IE8
        window.external.addFavorite(url, title);
    } else if (document.all) {
        try {
            window.external.addFavorite(url, title);
        } catch (e) {
            alert('您的浏览器不支持自动添加收藏, 请使用浏览器菜单手动设置!');
        }
    } else if (window.sidebar) {
        window.sidebar.addPanel(title, url, "");
    } else if (window.opera && window.print) {
        var e = document.createElement('a');
        e.setAttribute('href', location.href);
        e.setAttribute('title', document.title);
        e.setAttribute('rel', 'sidebar');
        e.click();
    } else {
        alert('您的浏览器不支持自动添加收藏, 请使用浏览器菜单手动设置!');
    }
}

/**
 * 判断host是否是ip地址
 * @param host
 * @returns {boolean}
 */
export const isIp = (host) => {
    const ipv4Regex = /^(25[0-5]|2[0-4]\d|[0-1]?\d?\d)(\.(25[0-5]|2[0-4]\d|[0-1]?\d?\d)){3}$/;
    const ipv6Regex = /^\s*((([0-9A-Fa-f]{1,4}:){7}([0-9A-Fa-f]{1,4}|:))|(([0-9A-Fa-f]{1,4}:){6}(:[0-9A-Fa-f]{1,4}|((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3})|:))|(([0-9A-Fa-f]{1,4}:){5}(((:[0-9A-Fa-f]{1,4}){1,2})|:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3})|:))|(([0-9A-Fa-f]{1,4}:){4}(((:[0-9A-Fa-f]{1,4}){1,3})|((:[0-9A-Fa-f]{1,4})?:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){3}(((:[0-9A-Fa-f]{1,4}){1,4})|((:[0-9A-Fa-f]{1,4}){0,2}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){2}(((:[0-9A-Fa-f]{1,4}){1,5})|((:[0-9A-Fa-f]{1,4}){0,3}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){1}(((:[0-9A-Fa-f]{1,4}){1,6})|((:[0-9A-Fa-f]{1,4}){0,4}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(:(((:[0-9A-Fa-f]{1,4}){1,7})|((:[0-9A-Fa-f]{1,4}){0,5}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:)))(%.+)?\s*$/;
    const isIP = ipv4Regex.test(host) || ipv6Regex.test(host);
    return isIP;
}

/**
 * 个位数字补零
 * @param val
 * @returns {string}
 */
export function fixedZero(val) {
    return val * 1 < 10 ? `0${val}` : val;
}


/**
 * 金钱转大写
 * @param n
 * @returns {string}
 */
export function digitUppercase(n) {
    const fraction = ['角', '分'];
    const digit = ['零', '壹', '贰', '叁', '肆', '伍', '陆', '柒', '捌', '玖'];
    const unit = [['元', '万', '亿'], ['', '拾', '佰', '仟', '万']];
    let num = Math.abs(n);
    let s = '';
    fraction.forEach((item, index) => {
        s += (digit[Math.floor(accMul(num, 10 * 10 ** index)) % 10] + item).replace(/零./, '');
    });
    s = s || '整';
    num = Math.floor(num);
    for (let i = 0; i < unit[0].length && num > 0; i += 1) {
        let p = '';
        for (let j = 0; j < unit[1].length && num > 0; j += 1) {
            p = digit[num % 10] + unit[1][j] + p;
            num = Math.floor(num / 10);
        }
        s = p.replace(/(零.)*零$/, '').replace(/^$/, '零') + unit[0][i] + s;
    }

    return s
        .replace(/(零.)*零元/, '元')
        .replace(/(零.)+/g, '零')
        .replace(/^整$/, '零元整');
}

/**
 * 生成UUId
 * @returns {string}
 */
export const createUUID = () => {
    let s = [];
    let hexDigits = '0123456789abcdefghijklmnopqrstuvwxyz';
    for (let i = 0; i < 36; i++) {
        s[i] = hexDigits.substr(Math.floor(Math.random() * 0x10), 1);
    }
    s[14] = '4'; // bits 12-15 of the time_hi_and_version field to 0010
    s[19] = hexDigits.substr((s[19] & 0x3) | 0x8, 1); // bits 6-7 of the clock_seq_hi_and_reserved to 01
    s[8] = s[13] = s[18] = s[23] = '-';

    return s.join('');
}
/**
 * 寻找数组下标
 * @param array
 * @param predicate （model => model.namespace === namespace）
 * @returns {number}
 */
export const findIndex = (array, predicate) => {
    for (let i = 0, length = array.length; i < length; i++) {
        if (predicate(array[i], i)) return i;
    }

    return -1;
};

/**
 * 设备判断
 * @type {string}
 */
const ua = window.navigator.userAgent.toLowerCase();

const isAndroid = /(Android);?[\s/]+([\d.]+)?/.test(ua)
const isIpad = /(iPad).*OS\s([\d_]+)/.test(ua)
const isIpod = /(iPod)(.*OS\s([\d_]+))?/.test(ua)
const isIphone = !isIpad && /(iPhone\sOS)\s([\d_]+)/.test(ua)
const isWechat = /micromessenger/i.test(ua)
const isAlipay = /alipayclient/i.test(ua)


/**
 * 是否是移动端
 * @returns {boolean}
 * @constructor
 */
export const IsMobile = () => {
    const sUserAgent = navigator.userAgent.toLowerCase();
    if ((sUserAgent.match(/(iphone|ipad|ipod|android|windows ce|windows mobile)/i))) {
        return true;
    }
    return false;
}

/**
 * 是否是Ie10以下
 * @returns {boolean}
 * @constructor
 */
export const LTIE10 = () => {
    const userAgent = navigator.userAgent;
    //判断是否IE浏览器
    const isIE = userAgent.indexOf("compatible") > -1
        && userAgent.indexOf("MSIE") > -1
        && userAgent.indexOf("Opera") == -1;

    if (isIE) {
        const reIE = new RegExp("MSIE (\\d+\\.\\d+);");
        reIE.test(userAgent);         //检测一个字符串是否匹配某个模式
        const fIEVersion = parseFloat(RegExp["$1"]);
        if (fIEVersion < 10)
            return true;
    }
    return false;
}

/**
 * 判断两个域名是否相等
 * @param mobileDomain
 * @param host
 * @returns {number}
 * @constructor
 */
export const IsSubDomain = (mobileDomain, host) => {
    this.getdomain = function (f) {
        var e = f.indexOf("://");
        if (e > 0) {
            var h = f.substr(e + 3)
        } else {
            var h = f
        }
        var g = /^www\./;
        if (g.test(h)) {
            h = h.substr(4)
        }
        return h
    };
    if (mobileDomain == host) {
        return 1 //相同
    } else {
        var mobileDomain = this.getdomain(mobileDomain);
        var b = this.getdomain(host);
        if (mobileDomain == b) {
            return 1 //相同
        } else {
            mobileDomain = mobileDomain.replace(".", "\\.");
            var a = new RegExp("\\." + mobileDomain + "$");
            if (b.match(a)) {
                return 2 //当前host包含mobileDomain
            } else {
                return 0 //不同
            }
        }
    }
}


/**
 * 将移动端自动重定向到pc
 * @param mobileDomain
 * @constructor
 */
export const RedirectMobile=(mobileDomain)=> {
    try {
        if (!IsMobile()) {  //先判断是否是手机端
            if (LTIE10())
                window.location.href = "/upgradebrowser"; //这里进行非ie10以上版本处理
            return;
        }

        var IsJump = false;
        var host = window.location.host;
        var vey = IsSubDomain(mobileDomain, host); //判断两个域名是否相同
        if (vey == 1 || vey == 2) { //相同或者包含
            IsJump = false
        } else {
            //取缓存中的值
            if (sessionStorage.IsJumpMobile == undefined)
            {
                if (confirm("检测到您使用的是移动设备，是否跳转到移动版？")) {
                    var path = window.location.pathname + window.location.search;
                    if (mobileDomain.indexOf("http://") == -1)
                        mobileDomain = "http://" + mobileDomain;
                    mobileDomain = mobileDomain + path;
                    IsJump = true;
                }
                else
                {
                    sessionStorage.IsJumpMobile = false;
                }
            }
        }

        if (IsJump) {
            location.replace(mobileDomain);
        }
    } catch (err) { }
}





  /**
 * 将一级的数据结构处理成树状数据结构
 * @param {Object} obj {key, pKey, data}
 *  @param obj.key  字段名称 比如id
 *  @param obj.pKey 父字段名称 比如 pid
 *  @param obj.rootPValue 根节点的父字段的值
 *  @param obj.data 需要处理的数据
 *  @param obj.jsonData 是否深复制数据（默认是true）
 * @return {Array} arr
   */
   
   // 使用示例代码：
    list: [{id: 1, pid: 0, name: 11}, {id: 2, pid: 1, name: 2}]
    getTreeArr({ key: 'id', pKey: 'pid', data: list })
    result: [
        {id: 1, pid: 0, name: 11, children: [
            {id: 2, pid: 1, name: 2}
        ]}
    ]   

  getTreeArr: (obj) => {
    if (!Array.isArray(obj.data)) {
      console.log('getTreeArr=>请传入数组')
      return []
    }
    obj.jsonData = obj.jsonData === false ? obj.jsonData : true
    const arr = obj.jsonData ? JSON.parse(JSON.stringify(obj.data)) : obj.data
    const arr1 = []
    // 将数据处理成数状结构
    arr.forEach(item => {
      let index = 0
      item.children = []
      arr.forEach(item1 => {
        // 得到树结构关系
        if (item[obj.key] === item1[obj.pKey]) {
          item.children.push(item1)
        }
        // 判断根节点
        if (item1[obj.key] !== item[obj.pKey]) {
          index++
        }
      })
      // 没传入根节点，根据当前数据结构得到根节点
      if (!('rootPValue' in obj) && index === arr.length) {
        arr1.push(item)
      }
    })
    // 传入根节点，根据传入的根节点组成树结构
    if ('rootPValue' in obj) {
      arr.forEach(item => {
        if (item[obj.pKey] === obj.rootPValue) {
          arr1.push(item)
        }
      })
    }
    return arr1
  }

//数组去重
//根据对象的属性不同去重
  handleRepeatArr ({ data, key }) {
    if (!Array.isArray(data)) {
      console.log('请传入数组')
      return
    }
    const arr = []; const obj = {}
    data.forEach((item, index) => {
      const attr = key ? item[key] : item
      if (!obj[attr]) {
        obj[attr] = index + 1
        arr.push(item)
      }
    })
    return arr
  }

//递归去重  会将数据默认排序
  handleRepeatArr ({ data, key }) {
    if (!Array.isArray(data)) {
      console.log('请传入数组')
      return
    }
    /** 1.递归去重，缺点，会将数据默认排序 */
    // 先对数据做排序处理
    data = data.sort((item, item1) => {
      if (key) {
        return item[key] - item1[key]
      }
      return item - item1
    })
    // 递归去重
    function getData (index) {
      if (index >= 1) {
        // 判断当前数据和下一条数据是否相等
        let result = key ? data[index][key] === data[index - 1][key] : data[index] === data[index - 1]
        if (result) {
          data.splice(index, 1)
        }
        getData(index - 1)
      }
    }
    getData(data.length - 1)
    return data
  }
  
  //利用indexOf以及forEach
  //适合处理数组，不适合处理对象数组
  handleRepeatArr ({ data, key }) {
    if (!Array.isArray(data)) {
      console.log('请传入数组')
      return
    }
    let arr = []
    data.forEach((item, index) => {
      // 如果当前元素在之后没有出现过(后面出现的数据会保留)
      // let result = data.indexOf(item, index + 1)
      // 如果当前元素在之前没有出现过(前面出现的数据会保留)
      let result = index === 0 ? -1 : data.lastIndexOf(item, index - 1)
      if (result === -1) {
        arr.push(item)
      }
    })
    return arr
  }
//双层循环去重
//占用内存高
  handleRepeatArr ({ data, key }) {
    if (!Array.isArray(data)) {
      console.log('请传入数组')
      return
    }
    for (let i = 0, len = data.length; i < len; i++) {
      for (let j = i + 1; j < len; j++) {
        let result = key ? data[i][key] === data[j][key] : data[i] === data[j]
        if (result) {
          data.splice(j, 1)
          len--
          j--
        }
      }
    }
    return data
  }


//复制内容
  /**
   * 复制
   * @param {String} value 要复制的值
   */
  copyData (value) {
    const inputDom = document.createElement('input')
    inputDom.value = value
    document.body.appendChild(inputDom)
    inputDom.select() // 选择对象
    document.execCommand('Copy') // 执行浏览器复制命令
    document.body.removeChild(inputDom) // 删除DOM
  }
 //a模拟window.open打开窗口
 /*
    因为有些浏览器会默认拦截window.open，当需要函数中打开窗口，可以使用a标签模拟window.open
 */
   /**
   * a模拟window.open，不会被浏览器拦截
   * @param {String} url        a标签打开的地址
   * @param {String} id         a标签的ID
   * @param {String} targetType a标签点击打开的方式（当前页面打开还是新窗口打开）
   */
  openWindow: (url, targetType = '_blank', id = 'open', download = false) => {
    // 如果存在则删除
    if (document.getElementById(id)) {
      document.body.removeChild(document.getElementById(id))
    }
    const a = document.createElement('a')
    a.setAttribute('href', url)
    if (download) {
      a.setAttribute('download', url)
    }
    a.setAttribute('target', targetType)
    a.setAttribute('id', id)
    document.body.appendChild(a)
    a.click()
  }

//文件大小显示转换
/*
// 使用示例代码：
bytesToSize(12) // 12.0 B
bytesToSize(683468) // 667 KB
bytesToSize(4544) // 4.44 KB
bytesToSize(98223445) // 93.7 MB
bytesToSize(9822344566) // 9.15 GB

*/
  bytesToSize (bytes) {
    if (bytes === 0) return '0 B'
    var k = 1024 // or 1024
    var sizes = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']
    var i = Math.floor(Math.log(bytes) / Math.log(k))
    return (bytes / Math.pow(k, i)).toPrecision(3) + ' ' + sizes[i]
  }

```
