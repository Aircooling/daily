var upload = layui.upload;
var element = layui.element;
var uploadInst = upload.render({
        elem: '#uploadF'
        , url: '/api/uplodeFile'
        , accept: 'file'
        , size:10240
        , field: 'TemplateFile'
        , before: function (obj) { //obj参数包含的信息，跟 choose回调完全一致，可参见上文。
            layer.load(); //上传loading
            let str = '<div class="pull-left" style="width: 80%"><div class="layui-progress" lay-showPercent="yes" style="margin-top: 10px" lay-filter="demo">'
                + '<div class="layui-progress-bar layui-bg-blue"></div>'
                + '</div></div><div class="pull-left" id="showType"></div>';
            $("td .laytable-cell-1-0-2").html(str);
            $('#uploadF').addClass('layui-hide');
        }
        , progress: function (n, elem) {
            var percent = n + '%'; //获取进度百分比
            element.progress('demo', percent); //可配合 layui 进度条元素使用
            $("#showType").html('上传中 '+percent);
        }
        , done: function (res) {
            layer.closeAll('loading');
            //上传完毕回调
            if (res.code != 0) {
                layer.msg(res.data.msg);
                $("td .laytable-cell-1-0-2").html('');
                $('#uploadF').removeClass('layui-hide');
                return
            }
            $('.layui-progress-bar').removeClass('layui-bg-blue').addClass('layui-bg-green');
            $("#showType").html('上传完成');

            //缓存数据方便下个页面取出
            localStorage.OriginaPath = res.data.file.OriginaPath;
            localStorage.pageNumber = res.data.file.pageNumber;
            localStorage.ExtendName = res.data.file.ExtendName;
            localStorage.CoverState = res.data.file.CoverState;
            localStorage.FileName = res.data.file.FileName;

            let str = '<a class="redC del">删除</a>'
                + '<a class="look" data-url="' + res.data.file.OriginaPath
                + '">查看</a>';
            $("td .laytable-cell-1-0-3").html(str);
            $("td .laytable-cell-1-0-1").html(res.data.file.pageNumber);
            $("td .laytable-cell-1-0-0").html(res.data.file.FileName);

        }
        , error: function () {
            $("#showType").html('上传失败');

            //请求异常回调
            let str = '<a class="redC del">删除</a>' +
                '<a class="again">重新上传</a>';
            $("td .laytable-cell-1-0-3").html(str);
            layer.closeAll('loading');
        }
    });
