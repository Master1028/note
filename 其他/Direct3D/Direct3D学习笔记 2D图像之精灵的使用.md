[toc]
# Direct3D学习笔记 2D图像之精灵的使用
## 基础知识
### 创建精灵和D3DXIMAGE_INFO结构体
~~~
//使用该函数创建精灵指针
LPD3DXSPRITE pSprite = nullptr;
//创建结构体用于获取纹理图片的具体信息
D3DXIMAGE_INFO imageInfo;
~~~
### 加载纹理
加载纹理有两个函数
~~~
//一个是简单的加载纹理
D3DXCreateTextureFromFile(Device, "GIF.bmp", &tex)
//一个是复杂的加载纹理，该函数可以把纹理图片的具体信息放到结构体D3DXIMAGE_INFO中
D3DXCreateTextureFromFileEx
	(
		Device,	//设备指针
		TEXT("GIF.bmp"),//路径及文件名
		D3DX_FROM_FILE,	//图片的宽来自于文件本身
		D3DX_FROM_FILE,	//图片的高来自于文件本身
		0,				//多级渐进纹理的等级
		0,				//图片的作用
		D3DFMT_UNKNOWN,	//未知纹理格式
		D3DPOOL_MANAGED,	//受系统管理的存储空间
		D3DX_FILTER_LINEAR,	//线性纹理过滤
		D3DX_FILTER_LINEAR,	//多级渐进纹理过滤，采用线性过滤方式
		D3DCOLOR_XRGB(255, 255, 255),//关键色透明
		&imageInfo,		//图片详细信息存储在这个结构体中
		nullptr,		//调色板信息
		&tex		//返回的纹理指针
	);
~~~
### RECT结构和矩形区域
RECT结构包含四个字段，left、top、right、bottom
下面的函数，能搞方便的实现一些基本操作。
*调用 SetRect 函数可以设定矩形区域:*
~~~
SetRect (&rect, xLeft, yTop, xRight, yBottom) ;
~~~
*将矩形眼X轴和Y轴移动几个单元：*
~~~
offsetRect (&rect,x,y); 
~~~
*增减矩形的尺寸：*
~~~
InflateRect(&rect,x,y);
~~~
*矩形各字段设定为0：*
~~~
SetRectEmpty(&rect);
~~~
*将矩形复制给另一个矩形：*
~~~
CopyRect(&DestRect,&SrcRect);
~~~
*取得两个矩形的交集：*
~~~
IntersectRect(&DestRect,&SrcRect1,&SrcRect2);
~~~
*取得两个矩形的合集：*
~~~
UnionRect(&DestRect,&SrcRect1,&SrcRect2);
~~~
*确定矩形是否为空：*
~~~
bEmpty = IsRectEmpty(&rect);
~~~
*确定点是否在矩形内：*
~~~
bInRect = PtInRect(&rect,point);
~~~
### 绘制精灵
绘制精灵使用的函数如下
~~~
	pSprite->Draw
	(
		tex,						//绘制哪张纹理指针			
		&srcRect,					//绘制图片的哪个矩形区域
		&point,						 //图片的锚点：进行坐标变换的中心点
		&position,					//绘制在客户区的哪个位置
		D3DCOLOR_XRGB(255, 255, 255)//绘制混合色
	);
~~~
绘制精灵前要告诉系统要在3D窗口绘制2D图形
~~~
pSprite->Begin(D3DXSPRITE_ALPHABLEND);//参数告知系统，需要alpha混合的支持
...//绘制精灵
pSpritr->End();	//告诉系统，在3D的窗口中绘制2D的工作结束
~~~
### 2D坐标变换
关于2D坐标变换有个函数需要了解即得到2D坐标转换矩阵函数
~~~
D3DXMATRIX mat;
	D3DXMatrixTransformation2D
	(
		&mat,		//返回的矩阵
		NULL,		//缩放中心
		0.0f,		//影响缩放的因素：缩放比例因子？？
		&aa,		//在XY方向的缩放量
		&bb,		//旋转中心
		0.0f,		//旋转弧度
		&cc			//平移量
	);
	//设置坐标
	pSprite->SetTransform(&mat);
~~~
在渲染前将坐标规范化 **暂时不知道目的** 
	D3DXMatrixIdentity(&mat);

## 绘制流程
1. 定义全局变量 精灵、图片结构体、纹理指针
2. 创建精灵并与设备连接，加载纹理信息，设置纹理过滤器
3. 设置矩阵变量和向量变量
4. 进行绘制
5. 释放相关内存

## 注意事项
