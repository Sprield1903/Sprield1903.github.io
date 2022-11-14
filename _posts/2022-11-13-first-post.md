---
title: 通过MATLAB画GIF图并储存。
date: 2022-11-13 10:34:00 +0800
categories: [随笔]
tags: [生活]
pin: true
author: 湾区书记汤姆

toc: true
comments: true
typora-root-url: ../../Sprield1903.github.io
math: false
mermaid: true



---

# 1. 简介

这个是我在自己目前的学习生涯中写过的一个比较满意的生成GIF的代码，可以在做其他需要生成GIF图的作业中使用。

# 2. 代码

```matlab
% Function:For water mass
% Created by Lunwei Fan on 2022/3/26
clear all;
close all;
clc;

FName = 'DataSource.nc';
Lon = ncread(FName, 'longitude');
Lat = ncread(FName, 'latitude');
Depth = ncread(FName, 'depth');
Time = ncread(FName,'time');
Temp = ncread(FName,'thetao');
% Load data

for i = 1:848
    Time(i) = datenum('2020-10-01')+(i-1)/4;
end
Time = datestr(Time,'yyyymmdd');
Time = string(Time);
% Process the time

Depth(:) = -Depth(:);
% Process the depth

%--------------------------------------------------
LonGrid = repmat(Lon, 1, size(Depth, 1));
LatGrid = repmat(Lat, 1, size(Depth, 1));
DepthGrid1 = repmat(Depth', size(Lon, 1), 1);
DepthGrid2 = repmat(Depth', size(Lat, 1), 1);
% Data grid

pic_num = 1;
for i = 1:848
    Temp1(:,:) = Temp(:,37,:,i);
    Temp2(:,:) = Temp(37,:,:,i);

    subplot(1,2,1);
    pcolor(LonGrid,DepthGrid1,Temp1)
    shading interp;
    title(Time(i));
    CB = colorbar;
    set(get(CB,'title'),'string','Temperature/°C');
    colormap("jet");
    caxis([0 20]);
    xlabel('Longitude/°E');
    ylabel('Depth/m');

    subplot(1,2,2);
    pcolor(LatGrid,DepthGrid2,Temp2)
    shading interp;
    title(Time(i));
    CB = colorbar;
    set(get(CB,'title'),'string','Temperature/°C');
    colormap("jet");
    caxis([0 20]);
    xlabel('Latitude/°N');
    ylabel('Depth/m');

    F = getframe(gcf);
    I = frame2im(F);
    [I,map] = rgb2ind(I,256);
    if pic_num == 1
        imwrite(I,map,'with.gif','gif','LoopCount',inf,'DelayTime',0.001);
    else
        imwrite(I,map,'with.gif','gif','WriteMode','append','DelayTime',0.001);
    end
    pic_num = pic_num + 1;
end
%--------------------------------------------------
```

# 3. 运行结果

![with](/assets/blog_res/2022-11-13-first-post.assets/with.gif)

