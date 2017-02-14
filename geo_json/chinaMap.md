# 利用matplotlib绘制中国大陆地图

## 准备工作

* 安装好Basemap、pyshp等
* 中国行政区划经纬数据可从<http://www.gadm.org/>获得，要绘制完整的中国地图，需要china、hongkong、macao、taiwan四个文件。
* 由于gadm数据未能更新，部分区划数据与现实不符，可利用DataV+团队的地理工具<http://datav.aliyun.com/static/tools/atlas>获取。

## 准备绘图

1. 绘制背景图

```
fig = plt.figure(figsize=(11.7, 8.3))
plt.subplots_adjust(left=0.05,right=0.95,top=0.90,bottom=0.05,wspace=0.15,hspace=0.05)
ax = plt.subplot(111)
# m = Basemap(projection='merc', llcrnrlon=70, llcrnrlat=15, urcrnrlon=140, urcrnrlat=55, lat_0=15, lon_0=95, resolution='l')
# m = Basemap(projection='merc', llcrnrlon=115, llcrnrlat=29, urcrnrlon=123, urcrnrlat=36, lat_0=15, lon_0=95, resolution='l')
m = Basemap(projection='merc', llcrnrlon=118, llcrnrlat=31, urcrnrlon=120, urcrnrlat=33, lat_0=15, lon_0=95, resolution='l')
m.drawcoastlines()
m.fillcontinents(color='#333333',lake_color='#333333')
m.drawmapboundary(fill_color='#333333')
```

2. 绘制需要的行政区划图

```
r = shapefile.Reader('./shapefile/path')
shapes = r.shapes()
records = r.records()

for record, shape in zip(records, shapes):
    lons,lats = zip(*shape.points)
    data = np.array(m(lons, lats)).T

    if len(shape.parts) == 1:
        segs = [data,]
    else:
        segs = []
        for i in range(1,len(shape.parts)):
            index = shape.parts[i-1]
            index2 = shape.parts[i]
            segs.append(data[index:index2])
        segs.append(data[index2:])

    lines = LineCollection(segs,antialiaseds=(1,))
    lines.set_facecolors(cm.jet(np.random.rand(1)))
    lines.set_edgecolors('k')
    lines.set_linewidth(0.1)
    ax.add_collection(lines)
```

## 参考链接

[1] http://www.geophysique.be/2013/02/12/matplotlib-basemap-tutorial-10-shapefiles-unleached-continued/

[2] https://doomzhou.github.io/coder/2015/07/04/Basemap-Pyplot.html

[3] http://seisman.info/china-administrative-areas-data.html
