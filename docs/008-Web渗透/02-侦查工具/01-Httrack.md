# Httrack
> 一个爬网工具, 可以镜像网站. 可以跨平台, Linux下为命令行模式, Windows有图形化.
> 官网: https://www.httrack.com/

## 0x01 使用
> Kali 下

```
httrack -h

HTTrack version 3.49-2
	usage: httrack <URLs> [-option] [+<URL_FILTER>] [-<URL_FILTER>] [+<mime:MIME_FILTER>] [-<mime:MIME_FILTER>]
	with options listed below: (* is the default value)

General options:
  O  path for mirror/logfiles+cache (-O path_mirror[,path_cache_and_logfiles]) (--path <param>)

Action options:
  w *mirror web sites (--mirror)
  W  mirror web sites, semi-automatic (asks questions) (--mirror-wizard)
  g  just get files (saved in the current directory) (--get-files)
  i  continue an interrupted mirror using the cache (--continue)
  Y   mirror ALL links located in the first level pages (mirror links) (--mirrorlinks)

```
