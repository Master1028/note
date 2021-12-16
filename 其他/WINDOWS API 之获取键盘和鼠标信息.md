[toc]
## WINDOWS API 之获取键盘和鼠标信息

### 1.基础概念
[参考链接](https://blog.csdn.net/lin_angle/article/details/1792142?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)
1.扫描码：键盘上每一个有意义的键都对应着唯一的一个标识值，成为扫描码
2.虚拟码：虚拟码是WINDOWS系统内部定义的与硬件设备无关的键盘编码，常用虚拟码在windows.h中已经定义。**eg:**VK_ESCAPE;
3.键盘事件的消息：**键盘消息**分为按键消息和字符消息；**按键消息**分为系统按键消息和非系统按键消息。**系统按键消息**就是WM_SYSKEYUP，**非系统按键消息**就是WM_KEYUP，两者的区别是前者的SYS代表着system，表示该按键对Windows比对Windows应用程序更加重要，一般windows用户程序会忽略系统按键消息并将它们传送到DefWindowProc。**字符消息**是由按键消息转换而来的.**eg:**WM_KEYDOWN，在WinMain函数中的消息循环中有一个TranslateMessage()函数，它的主要功能是把按键消息转化为字符消息，但只有当键盘驱动程序把键盘字符映射成ASCII码后才能产生WM_CHAR消息。
4.对于键盘消息，**wParam**是虚拟键代码，表示按下或释放的键，而**lParam**则包含属于按键的其它数据。wParam 通常是一个与消息有关的常量值，也可能是窗口或控件的句柄。 
lParam 通常是一个指向内存中数据的指针。 两种解释接近。

### 2.键盘及鼠标键码及wParam和lParam
[虚拟键码链接](https://blog.csdn.net/bnb45/article/details/8035291?spm=1001.2014.3001.5502)
[字符消息链接](https://blog.csdn.net/liubing8609/article/details/82079510)
### 3.窗口处理函数理解
~~~C++
LRESULT CALLBACK d3d::WndProc
(HWND hwnd, UINT msg, WPARAM wParam, LPARAM lParam)
{
	switch (msg)
	{
	case WM_DESTROY:
		::PostQuitMessage(0);
		break;

	case WM_KEYDOWN:
		if (wParam == VK_ESCAPE)
			::DestroyWindow(hwnd);
		break;
	}
	return ::DefWindowProc(hwnd, msg, wParam, lParam);
}
~~~
LResult:长的整数型，窗口程序或者回调函数返回的32位值。
callback:回调函数，CALLBACK表示这个函数是给系统调用的，实际上CALLBACK就是__stdcall

窗口处理函数总共有四个参数，含义分别为
1.窗口句柄，表示是哪一个窗口。
2.消息，表示WINDOWS定义的UINT类型的消息如WM_KEYUP等。
3.WORD类型的32位整型变量，在原来的WIN16API中是16位，现在和lParam一样也是32位，通常是一个与消息有关的常量值，也可能是窗口或控件的句柄。
4.LONG类型的32位整型变量，一般包含属于按键的其它数据。
后两个参数的具体含义根据msg的不同也有不同的含义。
[不同字符消息的wParam和lParam含义](https://blog.csdn.net/chdhust/article/details/7252155?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161838847816780264050691%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161838847816780264050691&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-7252155.pc_search_result_hbase_insert&utm_term=windows%E7%BC%96%E7%A8%8B%E4%B8%ADwParam%E5%92%8ClParam%E6%B6%88%E6%81%AF)
### 4.获取信息相关函数
~~~
a.GetCursorPos(POINT* point); 			  //获得鼠标在桌面的坐标：
b.GetWindowRect(HWND hwnd,LPRECT* lprect);//获得指定窗口的坐标信息
c.GET_WHEEL_DELTA_WPARAM(wParam)；		 //获得鼠标滑轮滚动值
/*
当信息是WM_MOUSEWHEEL时，lParam将获得鼠标的屏幕位置（坐标），wParam参数的低位表明鼠标键和Shift与Ctrl键的状态。wParam高位有一个 “delta”值，该值可正可负，指出了滚轮导致屏幕滚动几行，120表示向上3行。
*/ 
d.GetAsyncKeyState(int vKey);				//判断指定按键是否被按下
/*
一般情况下这个函数需要这样使用GetAsyncKeyState('W') & 0x8000f；之所以要’与上0x8000f‘是为了屏蔽掉其他可能的状态
类似的函数还有
SHORT GetKeyState(int nVirtKey); 
SHORT GetAsyncKeyState(int vKey); 
BOOL GetKeyboardState(PBYTE lpKeyState); 
区别是
1.从Windows的消息队列中返回按键值
2.从硬件设备的键盘返回按键值，这个函数可能返回不了模拟键盘事件的按键值（待验证）
3.当从windows的消息队列中移除键盘消息时才返回按键值
*/
~~~
### 5
0x8000 & GetKeyState(VK_SHIFT); 这句是判断是否有按下shift键 
 
为什么GetAsyncKeyState（）& 
 
首先说明，有好多程序或书上是0x8000f,这个f不是十六进制的f而是代表浮点数。其实& 8000才是本质。小鱼我整理后自己写了点东西，总结一下 
 
 
首先介绍一下几个概念： 
按位与运算符"&"：是双目运算符，其功能是参与运算的两数各对应的二进位相与。只有对应的两个二进位均为1时，结果位才为1 ，否则为0。参与运算的数以补码方式出现。例如：0x11 & 0x12（即0001 0001 & 0001 0010）的结果是0x10（0001 0000）；（关于vs取反参考附） 
虚键：指的是非字母可以明确表示的键.（例如ESC BS TAB NumLock 等,虚键列表见附）； 
物理键状态：在操作系统的控制面板中设置鼠标左右键的映射（实际的鼠标左键可以映射成右键点击事件），或者通过程序也可以这样设置，这样就产生了（实际的）物理键状态； 
逻辑键状态：使用 GetKeyState,GetKeyboardState,等函数得到的逻辑键状态，模拟按下按键； 
GetAsyncKeyState函数功能：读取的是物理键状态，也是就是不管你怎么鼠标键盘映射，它只读取实际的按键状态。MSDN上给出了例子很恰当For example, the call GetAsyncKeyState(VK_LBUTTON) always returns the state of the left physical mouse button, regardless of whether it is mapped to the left or right logical mouse button.也就是说如果你重新设置了映射，GetAsyncKeyState还是只读取物理状态； 
GetAsyncKeyState的返回值：表示两个内容，一个是最高位bit的值，代表这个键是否被按下，按下为1，抬起为0；一个是最低位bit的值，在windowsCE下要忽略（参考自MSDNIf the most significant bit is set, the key is down. The least significant bit is not valid in Windows CE, and should be ignored.） 
Asynchronous：英文意思是异步的 
 
实际当中GetAsyncKeyState的返回值是什么呢？小鱼我写了个程序来获取返回值： 
#include <Windows.h> 
#include <stdio.h> 
 
void main() 
{ 
while(1) 
{ 
short a = ::GetAsyncKeyState(VK_LSHIFT) 
printf( "0x%x",a); 
sleep(10); 
} 
} 
当然，用MessageBox可以这样写： 
if(short a = ::GetAsyncKeyState(VK_LSHIFT)) 
{ 
char buffer[30]; 
sprintf(buffer, "0x%x",a); 
MessageBox(0, buffer, "a的值", MB_OK); 
} 
 
GetAsyncKeyState按键不按或抬起后不按的返回值0x0        即0000 0000 0000 0000 0000 0000 0000 0000
GetAsyncKeyState按键被按下后的返回值    返回0xffff8001 即1111 1111 1111 1111 1000 0000 0000 0001   (这里并不是返回4字节，而是%x打印出32位，前十六位补f) 
0x8000 即0000 0000 0000 0000 1000 0000 0000 0000 
GetAsyncKeyState(VK_LSHIFT) & 0x8000    返回0x1          即0000 0000 0000 0000 1000 0000 0000 0000 
 
那么为什么GetAsyncKeyState要 ‘与’上 0x8000这个常数呢? 
答案是：获取按键状态，屏蔽掉其他的可能状态,按照MSDN上说低位should ignore。 
网上有人这样写，意思很明确： 
#define KEYDOWN(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 1 : 0) 
#define KEYUP(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 0 : 1) 
 
程序应该是： 
if(GetAsyncKeyState(VK_LSHIFT)&&0x8000) 
对于虚键而言下面这样写逻辑是不对的，虽然结果一样： 
if(GetAsyncKeyState(VK_LSHIFT)) 
 
所以让键盘的"上下左右"出发事件可以这样写： 
if( ::GetAsyncKeyState(VK_LEFT) & 0x8000 ) 
code... 
if( ::GetAsyncKeyState(VK_RIGHT)& 0x8000 ) 
code... 
if( ::GetAsyncKeyState(VK_UP) & 0x8000 ) 
code... 
if( ::GetAsyncKeyState(VK_DOWN) & 0x8000 ) 
code... 
 
关于GetAsyncKeyState与GetKeyState区别： 
GetAsyncKeyState上面已经讲差不多了，关于GetAsyncKeyState与GetKeyState二者最大区别:GetAsyncKeyState在按键不按的情况下为0，而GetKeyState在按键不按的情况下开始为0，当一次‘按下抬起’后变为1，依次循环。 
 
SHORT GetKeyState(int nVirtKey   // virtual-key code); 
作用：返回键的状态，按下、释放或锁定（down、up or toggled） 
参数：虚拟键代码（VK_)。如果是字母a-z、A-Z 或数字0-9， 则为其对应的ASCII码（比如字母O的ASCII码为十六进制的0x4F） 
返回值：返回码的高位显示当前是否有键被按下，低位（0位）则显示NumLock、CapsLock、ScrollLock的状态（ON或OFF，为ON时键盘指示灯亮）。即高位为1，返回值小于0，说明有键按下；最低位为1表示处于锁定（ON）状态(参考MSDN：If the high-order bit is 1, the key is down; otherwise, it is up. 
If the low-order bit is 1, the key is toggled. A key, such as the CAPS LOCK key, is toggled if it is turned on. The key is off and untoggled if the low-order bit is 0. A toggle key's indicator light (if any) on the keyboard will be on when the key is toggled, and off when the key is untoggled. ) 
注：此函数不应该在键盘消息处理程序以外使用，因为它返回的信息只有在键盘消息从消息队列中被检索到之后才有效。若确实需要，请使用GetAsyncKeyState 
 
---------------------------------------- 
网上还找到了一些资料： 
 
关于和其他的几个函数的区别： 
SHORT GetKeyState(int nVirtKey); 
SHORT GetAsyncKeyState(int vKey); 
BOOL GetKeyboardState(PBYTE lpKeyState); 
 
三个取key status的函数的最大区别是： 
第一个：是从windows消息队列中取得键盘消息，返回key status. 
第二个：是直接侦测键盘的硬件中断，返回key status. 
第三个：是当从windows消息队列中移除键盘消息时，才返回key status. 
 
keybd_event函数，是模拟键盘击键，一次完整的击键模拟事件，是"按下"和"弹起"两个消息，所以 keybd_event(VK_F12,0,0,0);keybd_event(VK_F12,0,KEYEVENTF_KEYUP,0); 完成了一次完整的点击 F12 的事件。 
 
GetAsyncKeyState()函数，是直接侦测键盘的硬件中断。（有些人说，是一种“实时性”的侦测，这种说法，感觉不对，比如你调用 Sleep(),就算是中断一年的时间，只要在这期间程序还在运行，它都可以把那个键的状态侦测出来）。自上一次调用GetAsyncKeyState()函数以来（在某些循环中，N次调用GetAsyncKeyState(),它每次检查的，都是自上次调用之后，键的状态），若键已被按过，则返回1，否则，返回0；有些资料显示：倘若输入焦点从属于与调用函数的输入线程不同的另一个线程，则返回零（例如，在另一个程序拥有输入焦点时，应该返回零）。实验证明，这种说法并不完全，函数实际是在大部份范围内工作的，只有少数是另外）。 
 
 
--------------- 
附： 
VC++编译器，计算～10，得出的结果是－11。为什么不是5呢 
 
10的二进制表示为1010，按位取反应该为0101，也就是十进制的5，为什么会得出－11？ 
 
 
VC是32位编译器，所以 
 
10 = 00000000 00000000 00000000   00001010 
 
~10 = 11111111 11111111   11111111   11110101 =   -11 
 
可以通过掩码（位与） 与15位与 
 
15 = 00000000 00000000 00000000   00001111 
 
~10 = 00000000 00000000 00000000   00000101   =   -11 
附： 
VK_LBUTTON             鼠标左键                      0x01 
VK_RBUTTON             鼠标右键                      0x02 
VK_CANCEL              Ctrl + Break                  0x03 
VK_MBUTTON             鼠标中键                      0x04 
 
VK_BACK                Backspace 键       0x08 
VK_TAB                 Tab 键                        0x09 
 
VK_RETURN              回车键                        0x0D 
 
 
VK_SHIFT               Shift 键                      0x10 
VK_CONTROL             Ctrl 键                       0x11 
VK_MENU                Alt 键                 0x12 
VK_PAUSE               Pause 键                      0x13 
VK_CAPITAL             Caps Lock 键                  0x14 
 
VK_ESCAPE              Esc 键                        0x1B 
 
VK_SPACE               空格键         0x20 
VK_PRIOR               Page Up 键                    0x21 
VK_NEXT                Page Down 键                  0x22 
VK_END                 End 键                        0x23 
VK_HOME                Home 键                       0x24 
VK_LEFT                左箭头键                      0x25 
VK_UP                  上箭头键                      0x26 
VK_RIGHT               右箭头键                      0x27 
VK_DOWN                下箭头键                      0x28 
VK_SNAPSHOT            Print Screen 键               0x2C 
VK_Insert              Insert 键                     0x2D 
VK_Delete              Delete 键                     0x2E 
 
'0' – '9'             数字 0 - 9                    0x30 - 0x39 
'A' – 'Z'             字母 A - Z                    0x41 - 0x5A 
 
VK_LWIN                左WinKey(104键盘才有)         0x5B 
VK_RWIN                右WinKey(104键盘才有)         0x5C 
VK_APPS                AppsKey(104键盘才有)          0x5D 
 
VK_NUMPAD0            小键盘 0 键                    0x60 
VK_NUMPAD1            小键盘 1 键                    0x61 
VK_NUMPAD2            小键盘 2 键                    0x62 
VK_NUMPAD3            小键盘 3 键                    0x63 
VK_NUMPAD4            小键盘 4 键                    0x64 
VK_NUMPAD5            小键盘 5 键                    0x65 
VK_NUMPAD6            小键盘 6 键                    0x66 
VK_NUMPAD7            小键盘 7 键                    0x67 
VK_NUMPAD8            小键盘 8 键                    0x68 
VK_NUMPAD9            小键盘 9 键                    0x69 
 
VK_F1 - VK_F24        功能键F1 – F24               0x70 - 0x87 
 
VK_NUMLOCK            Num Lock 键                   0x90 
VK_SCROLL             Scroll Lock 键                0x91 