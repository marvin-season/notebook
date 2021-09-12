## 异步请求

### ajax

```js
<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>

$(function () {
    $("#btn").click(function () {
        $.ajax({
            url: "http://localhost:8888/marvin_blog/async/hello",
            data: {
                name: '小马',
                pass: '123456'
            },
            type: "post",
            success: function (data) {
                console.table(data)
                console.log("==============================")
            },
            error: function (data) {
                console.error(data)
                console.log("==============================")
            }
        });
    })
})

```

### axios

http://www.axios-js.com/zh-cn/docs/#axios-get-url-config

```js
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
$(function () {
    function create() {
            return axios.post(
                'http://localhost:8888/marvin_blog/async/create',
                {
                    name: "createUser",
                    pass: "123456"
                }
            )
        }

        function find() {
            return axios.get(
                'http://localhost:8888/marvin_blog/async/find'
            )
        }

        $("#btn_get").click(async function () {
            let res = await find();
            console.log(res)
        })
        $("#btn_post").click(async function () {
            let res = await create();
            console.log(res)
        })
        // all([args...])处理多个并行请求 
        // axios.spread(function (res1, res2)  统一处理并行返回结果`

        $("#btn").click(function () {
            axios.all([find(), create()]).then(
                axios.spread(function (res1, res2) {
                    console.log('res1 = ', res1)
                    console.log('res2 = ', res2)
                })
            )
        })
})
```

