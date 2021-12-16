### D3D坐标变换函数

#### 平移矩阵
~~~
D3DXMATRIX * D3DXMatrixTranslation
(
D3DXMATRIX * pOut,
FLOAT x,
FLOAT y,
FLOAT z
);
~~~
#### 创建单位矩阵
~~~
D3DXMATRIX * D3DXMatrixIdentity
(
  D3DXMATRIX * pOut
);
~~~
#### 创建沿某个轴旋转的矩阵
~~~
D3DXMATRIX * D3DXMatrixRotationX
(
  D3DXMATRIX *pOut,
  FLOAT Angle
);
~~~
#### 缩放矩阵
~~~
D3DXMATRIX * D3DXMatrixScaling(
  D3DXMATRIX *pOut,
  FLOAT sx,
  FLOAT sy,
  FLOAT sz
);
~~~
