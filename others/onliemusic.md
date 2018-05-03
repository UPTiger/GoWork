百度音乐
1). 获取歌曲列表：

http://tingapi.ting.baidu.com/v1/restserver/ting?from=android&version=5.6.5.0&method=baidu.ting.artist.getSongList&format=json&order=2&tinguid=7994&artistid=7994&offset=0&limits=50

2). 获取专辑列表：

http://tingapi.ting.baidu.com/v1/restserver/ting?from=android&version=5.6.5.0&method=baidu.ting.artist.getAlbumList&format=json&order=1&tinguid=7994&offset=0&limits=30

3). 获取歌手信息：

http://tingapi.ting.baidu.com/v1/restserver/ting?from=android&version=5.6.5.0&method=baidu.ting.artist.getinfo&format=json&tinguid=7994&artistid=7994

4). 获取歌词以及图片：

http://tingapi.ting.baidu.com/v1/restserver/ting?from=android&version=5.6.5.0&method=baidu.ting.search.lrcpic&format=json&query=Apollo%27s%2BTriumph%2B%28Paul%2BDinletir%2BRemix%29$$Audio%2BMachine&ts=1444316027469&e=6Wwvzqnijq08Nrv0qI%2BN3Thp9GuKdV82ZxAS3UrvifMc%2FoVWLyZ8dSolFUF5r4W3SB2tm4z5TWT95sihhOG7qeqvjhThJWnh6h745kRGSTI%3D&type=2
-------------------------------------------------------
API 地址

根地址为 http://api.lostg.com

虾米歌词 /music/xiami/lyrics/{id}
虾米单曲 /music/xiami/songs/{id}
虾米专辑 /music/xiami/albums/{id}
虾米歌手 /music/xiami/artists/{id}
虾米精选集 /music/xiami/collections/{id}
网易歌词 /music/163/lyrics/{id}
网易单曲 /music/163/songs/{id}
网易专辑 /music/163/albums/{id}
网易歌手 /music/163/artists/{id}
网易歌单 /music/163/collections/{id}
省略网站名称，默认调用虾米音乐
例如 虾米歌词 /music/lyrics/{id}

省略接口类别，默认调用单曲音乐
例如 虾米单曲 /music/xiamis/{id}

全部省略，默认调用虾米单曲音乐
即 虾米单曲 /musics/{id}

在获取歌手，歌单，专辑时，由于歌曲数量可能出现过多的情况，返回值会比较大，因此建议通过以下方式仅获取歌曲 ID，然后通过 ID 再获取具体的歌曲信息。

虾米专辑歌曲 ID /music/xiami/albums/ids/{id}
虾米歌手歌曲 ID /music/xiami/artists/ids/{id}
虾米精选集歌曲 ID /music/xiami/collections/ids/{id}
网易专辑歌曲 ID /music/163/albums/ids/{id}
网易歌手歌曲 ID /music/163/artists/ids/{id}
网易歌单歌曲 ID /music/163/collections/ids/{id}
参数

id: 必选参数，值为单曲/专辑/歌手/精选集的 ID，歌词接口中的参数 id 为单曲 ID
lyric: 可选参数，值可为任意值，若包含该参数，则返回值中将包含歌词信息
建议：当歌手/专辑/精选集中包含的歌曲数目较多时，请关闭歌词信息的获取，改用歌词接口获取歌词，可节约获取时间。

返回值

返回 json 格式数据，包含以下几个字段：

id: 歌曲 ID
title: 歌曲名
singer: 歌手
album: 专辑名
album_pic: 专辑图片(一般尺寸)
album_pic_m: 专辑图片(小尺寸)
album_pic_l: 专辑图片(原始尺寸)
lyric: 歌词
location: 歌曲链接
网易云音乐的专辑图片仅有一种大小，三个字段的值均相同。
歌词接口返回值中仅包含歌词信息。

使用方式

建议使用 AJAX 跨域请求

例如获取虾米音乐《Mockingbird》的信息


$.ajax({
	type: "get",
	dataType: "jsonp",
	jsonp: "callback",
	url: "https://api.lostg.com/music/2088114", //默认接口为虾米单曲
	data: {
		lyric: 1
	},
	async: !1,
	success: function(b) {
				console.log(b)
			}
	});
 

演示：https://api.lostg.com/music/2088114?callback=jQuery110106158943774644285_1438935727679&&lyric=1&_=1438935727681



网易云音乐常用API浅析 | Moonlib
 

话不多说
PC客户端抓包而来

0.说明
关于头部信息

12Cookie: os=pc; deviceId=B55AC773505E5606F9D355A1A15553CE78B89FC7D8CB8A157B84; osver=Microsoft-Windows-8-Professional-build-9200-64bit; appver=1.5.0.75771; usertrack=ezq0alR0yqJMJC0dr9tEAg==; MUSIC_A=088a57b553bd8cef58487f9d01aeUser-Agent: Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.138 Safari/537.36\r\n

上面是抓到的信息，其中必要的只有cookie中的appver。而且如果要调用api，必须加上Referer，只要是music.163.com的就可以

12Cookie: appver=1.5.0.75771;Referer: http://music.163.com/

以上两条即可

返回的格式均为json

 

1.搜索
抓取到的信息如下

123456789101112131415Full request URI：http://music.163.com/api/search/pcKey: hlpretagValue: Key: hlposttagValue: Key: sValue: \345\226\234\346\254\242\344\275\240Key: offsetValue: 0Key: totalValue: trueKey: limitValue: 100Key: typeValue: 1

URL：

POST http://music.163.com/api/search/pc

必要参数：

s：搜索的内容

offset：偏移量（分页用）

limit：获取的数量

type：搜索的类型

歌曲 1

专辑 10

歌手 100

歌单 1000

用户 1002

mv 1004

歌词 1006

主播电台 1009

 

2.歌曲信息
1Full request URI: http://music.163.com/api/song/detail/?id=28377211&ids=%5B28377211%5D

URL：

GET  http://music.163.com/api/song/detail/

必要参数：

id：歌曲ID

ids：不知道干什么用的，用[]括起来的歌曲ID

 

3.歌手专辑
1Full request URI: http://music.163.com/api/artist/albums/166009?id=166009&offset=0&total=true&limit=5

URL：

GET http://music.163.com/api/artist/albums/歌手ID

必要参数：

limit：获取的数量(不知道为什么这个必须加上）

 

4.专辑信息
1Full request URI: http://music.163.com/api/album/2457012?ext=true&id=2457012&offset=0&total=true&limit=10

URL：

GET http://music.163.com/api/album/专辑ID

 

5.歌单
1Full request URI: http://music.163.com/api/playlist/detail?id=37880978&updateTime=-1

URL：

GET http://music.163.com/api/playlist/detail

必要参数：

id：歌单ID

 

6.歌词
1Full request URI: http://music.163.com/api/song/lyric?os=pc&id=93920&lv=-1&kv=-1&tv=-1

URL：

GET http://music.163.com/api/song/lyric

必要参数：

id：歌曲ID

lv：值为-1，我猜测应该是判断是否搜索lyric格式

kv：值为-1，这个值貌似并不影响结果，意义不明

tv：值为-1，是否搜索tlyric格式

 

7.MV
1Full request URI: http://music.163.com/api/mv/detail?id=319104&type=mp4

URL：

GET http://music.163.com/api/mv/detail

必要参数：

id：mvid

type：值为mp4，视频格式，不清楚还有没有别的格式


