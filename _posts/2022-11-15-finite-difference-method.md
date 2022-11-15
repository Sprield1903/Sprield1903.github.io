---
title: 热传导方程有限差分数值解法的实现。
date: 2022-11-15 14:23:00 +0800
categories: [代码]
tags: [MATLAB]
pin: true
author: Sprield1903

toc: true
comments: true
typora-root-url: ../../Sprield1903.github.io
math: false
mermaid: true



---

# 1. 基本理论

本次实验的线性方程：

![截屏2022-11-15 下午2.25.45](/assets/blog_res/2022-11-15-finite-difference-method.assets/%E6%88%AA%E5%B1%8F2022-11-15%20%E4%B8%8B%E5%8D%882.25.45.png)

时间上一阶向前差分，空间上二阶中心差分：

![截屏2022-11-15 下午2.27.39](/assets/blog_res/2022-11-15-finite-difference-method.assets/%E6%88%AA%E5%B1%8F2022-11-15%20%E4%B8%8B%E5%8D%882.27.39.png)

时间上一阶向后差分，空间上二阶中心差分：

![截屏2022-11-15 下午2.28.54](/assets/blog_res/2022-11-15-finite-difference-method.assets/%E6%88%AA%E5%B1%8F2022-11-15%20%E4%B8%8B%E5%8D%882.28.54.png)

<font color=red>（以后记得学怎么用LaTeX，不能每次都这么懒）</font>

# 2. 代码

## 2.1 时间上一阶向前差分

```matlab
% 数值模拟与机器学习基础 实验三
% Created by Sprield1903 on 2022/10/11
clear all;
close all
clc;

dt = 0.006;
% 时间步长
dh = 0.1;
% 空间步长

Nt = 1/dt + 1;
Nx = 1/dh + 1;

for i = 1:Nx
    x = (i-1)*dh;
    X(i) = x;
    u(1,i) = x*(1-x);
end
% 初始条件

for n = 2:Nt
    u(n,1) = 0;
    u(n,Nx) = 0;
    for i = 2:Nx-1
        u(n,i) = u(n-1,i) + dt/dh^2*(u(n-1,i+1)-2*u(n-1,i)+u(n-1,i-1));
    end
end

for n = 1:Nt
    plot(X(:),u(n,:));
    axis([0 1 0 0.3]);
    t = (n-1)*dt;
    str = ['Time=',num2str(t),'s'];
    title(str);
    pause(dt);
    
end
```

## 2.2 时间上一阶向后差分<font color = red>（解离散线性方程组）</font>

```matlab
% 数值模拟与机器学习基础 实验三(附加)
% Created by Sprield1903 on 2022/10/11
clear all;
close all
clc;

dt = 0.006;
% 时间步长
dh = 0.1;
% 空间步长

Nt = 1/dt + 1;
Nx = 1/dh + 1;

for i = 1:Nx
    x = (i-1)*dh;
    axisX(i) = x;
    u(1,i) = x*(1-x);
end
% 初始条件

icount = 0;

mu = dt/dh^2;

for i = 1:Nx
    iele = i;
    
    %(j-1)
    if(i >= 2)
        icount = icount + 1;
        val(icount) = -mu;
        irow(icount) = iele;
        jcol(icount) = iele - 1;
    end

    %(j+1)
    if(i <= Nx-1)
        icount = icount + 1;
        val(icount) = -mu;
        irow(icount) = iele;
        jcol(icount) = iele + 1;
    end

    %(j)
    icount = icount + 1;
    val(icount) = 1 + 2*mu;
    irow(icount) = iele;
    jcol(icount) = iele;
end

Number = iele;

for n = 2:Nt

    % 完全体
    K = zeros(Number,Number);
    for i = 1:icount
        K(irow(i),jcol(i)) = val(i);
    end

    f(:) = u(n-1,:);

    for i = 1:Nx
        if(i==1|i==Nx)
            iele = i;
            % 代入边界条件
            K(iele,:) = 0;
            K(:,iele) = 0;
            K(iele,iele) = 1;
            g = 0;
            f(:) = f(:) - g*K(:,iele);
            f(iele) = g;
        end
    end

    X = f/K;
    u(n,:) = X(:);
end



for n = 1:Nt
    plot(axisX(:),u(n,:));
    axis([0 1 0 0.3]);
    t = (n-1)*dt;
    str = ['Time=',num2str(t),'s'];
    title(str);
    pause(dt);    
end
```

# 3. 运行结果

$\frac{\Delta t}  {\Delta h^2}>\frac12$向前差分的稳定性出现问题，而向后差分的稳定性不受影响。

## 3.1 时间上一阶向前差分

![大于-向前-终](/assets/blog_res/2022-11-15-finite-difference-method.assets/%E5%A4%A7%E4%BA%8E-%E5%90%91%E5%89%8D-%E7%BB%88.jpg)

## 3.2 时间上一阶向后差分

![大于-向后-终](/assets/blog_res/2022-11-15-finite-difference-method.assets/%E5%A4%A7%E4%BA%8E-%E5%90%91%E5%90%8E-%E7%BB%88.jpg)
