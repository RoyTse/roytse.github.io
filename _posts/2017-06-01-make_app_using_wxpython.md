---
layout: post
title: 使用 wxPython 创建自己的OSX应用
date: 2017-06-01 
tags: Python学习  
---

## 介绍

　　学习Python的同学，肯定经常有写一些脚本，但是对Python的GUI却不一定熟悉，我正好最近因为找不到osx上面的一些小工具，决定自己用Python写一个，所以接触了`wxPython`这个图形库。`wxPython`是Python语言的一套优秀的GUI图形库。允许Python程序员很方便的创建完整的、功能键全的GUI用户界面。`wxPython`是作为优秀的跨平台GUI库`wxWidgets`的Python封装和Python模块的方式提供给用户的。

　　在本文中，我将介绍 `wxPython` , 帮你了解 这个`GUI`的基本使用方法，并使用`py2app`将你的应用转变为osx应用程序。 读这篇文章前，需要知道Python的基础知识和一些熟悉编程理念.                
                           

<div align="center">
	<img src="/images/posts/wxpython/wxpython.jpeg" height="300" width="500">  
</div> 


### 目录

* [如何安装wxpython](#How-to-install-wxpython)
* [如何安装py2app](#How-to-install-py2app)
* [如何使用wxpython](#How-to-use-wxPython)
* [如何设置事件](#How-to-set-Events)
* [使用py2app创建应用](#use-py2app-create-app)
* [结束语](#The-End)


### <a name="How-to-install-wxpython"></a>如何安装wxpython？

　　安装`wxpython`只需要去其[官方网站](https://www.wxpython.org/download.php)的下载界面，选择自己相应系统的相应`python`版本下载即可。目前支持系统的有windows、osx、linux。支持的`python`版本为2.6或2.7。你可以直接点击这里的链接下载osx-py2.7的`wxpython`。

　　[wxPython3.0-osx-carbon-py2.7](http://downloads.sourceforge.net/wxpython/wxPython3.0-osx-3.0.2.0-carbon-py2.7.dmg) 
　　[wxPython3.0-osx-cocoa-py2.7](http://downloads.sourceforge.net/wxpython/wxPython3.0-osx-3.0.2.0-cocoa-py2.7.dmg)

版本号中的`cocoa`-`carbon`的区别在于内置函数的命名不同。选择自己喜欢的版本即可。

### <a name="How-to-install-py2app"></a>如何安装py2app

　　`py2app`是`python`的一个第三方模块。安装只需要输入下面的代码：

    sudo pip install py2app

　　在终端执行后`pip`会自动安装它。如果没有`pip`可以使用`easy_install`或者`homebrew`安装`pip`。

### <a name="How-to-use-wxPython"></a>如何使用wxPython ？

　　如果想深入理解wxPython可以参考[wxPython教程](http://www.yiibai.com/wxpython/)。我使用的`wxFormBuilder`来快速创建一个应用模型。'wxFormBuilder'与'mfc'的使用差不多。将自己需要的模块扔在框架中即可。因为应用会自动创建一个工程，所以直接选择Forms里面的窗体，我这里选的是Frame。然后在Layout里面选择布局管理器。我选择的是wxFlexGridSizer下再包含三个wxGridBagSizer。每个wxGridBagSizer下包含若干个控件，即可完成对界面的编辑。选中某个空间，可在右边的Properties里面给空间命名，控件信息、大小、风格等修改。还可以更改布局权重，由gbsizeritem的row\column\rowspan\colspan四个参数控制。通过这些方式，可以自定义自己的程序界面。
<div align="center">
    <img src="/images/posts/wxpython/wxmain.jpg" height="400" width="880">  
</div> 
　　如果想给控件增加`事件响应`，选中控件后单击Properties旁边的Events。`双击`该控件可用事件即可增加该事件的构造函数。将自己的程序调整好后，在下方有个Python按钮，点击即可看见界面对应的Python代码。此处附上我的界面代码。可以直接粘贴在`wxFormBuilder`中即可看到界面效果。

    # -*- coding: utf-8 -*- 

    ###########################################################################
    ## Python code generated with wxFormBuilder (version Sep 12 2010)
    ## http://www.wxformbuilder.org/
    ##
    ## PLEASE DO "NOT" EDIT THIS FILE!
    ###########################################################################

    import wx

    ###########################################################################
    ## Class RoysTools
    ###########################################################################

    class RoysTools ( wx.Frame ):
    
        def __init__( self, parent ):
            wx.Frame.__init__ ( self, parent, id = wx.ID_ANY, title = u"RoysTools", pos = wx.DefaultPosition, size = wx.Size( 640,235 ), style = wx.DEFAULT_FRAME_STYLE|wx.TAB_TRAVERSAL )
            
            self.SetSizeHintsSz( wx.DefaultSize, wx.DefaultSize )
            
            fgSizer1 = wx.FlexGridSizer( 4, 4, 0, 0 )
            fgSizer1.SetFlexibleDirection( wx.BOTH )
            fgSizer1.SetNonFlexibleGrowMode( wx.FLEX_GROWMODE_SPECIFIED )
            
            gbSizer2 = wx.GridBagSizer( 0, 0 )
            gbSizer2.SetFlexibleDirection( wx.BOTH )
            gbSizer2.SetNonFlexibleGrowMode( wx.FLEX_GROWMODE_SPECIFIED )
            
            self.m_staticText1 = wx.StaticText( self, wx.ID_ANY, u"待转换进制：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText1.Wrap( -1 )
            gbSizer2.Add( self.m_staticText1, wx.GBPosition( 1, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText13 = wx.StaticText( self, wx.ID_ANY, u"进制转换", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText13.Wrap( -1 )
            self.m_staticText13.SetFont( wx.Font( 20, 70, 90, 90, False, wx.EmptyString ) )
            
            gbSizer2.Add( self.m_staticText13, wx.GBPosition( 0, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.wait_hex = wx.TextCtrl( self, wx.ID_ANY, u"2", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.wait_hex.SetMaxLength( 2 ) 
            gbSizer2.Add( self.wait_hex, wx.GBPosition( 1, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText2 = wx.StaticText( self, wx.ID_ANY, u"待转换数字：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText2.Wrap( -1 )
            gbSizer2.Add( self.m_staticText2, wx.GBPosition( 2, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.wait_hex_num = wx.TextCtrl( self, wx.ID_ANY, u"1", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer2.Add( self.wait_hex_num, wx.GBPosition( 2, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText7 = wx.StaticText( self, wx.ID_ANY, u"目标进制：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText7.Wrap( -1 )
            gbSizer2.Add( self.m_staticText7, wx.GBPosition( 3, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.to_hex = wx.TextCtrl( self, wx.ID_ANY, u"10", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.to_hex.SetMaxLength( 2 ) 
            gbSizer2.Add( self.to_hex, wx.GBPosition( 3, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText3 = wx.StaticText( self, wx.ID_ANY, u"转换后数字：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText3.Wrap( -1 )
            gbSizer2.Add( self.m_staticText3, wx.GBPosition( 4, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.hex_num = wx.TextCtrl( self, wx.ID_ANY, u"1", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer2.Add( self.hex_num, wx.GBPosition( 4, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.hex_btn = wx.Button( self, wx.ID_ANY, u"确定", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer2.Add( self.hex_btn, wx.GBPosition( 5, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.hex_cp_btn = wx.Button( self, wx.ID_ANY, u"复制", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer2.Add( self.hex_cp_btn, wx.GBPosition( 5, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            fgSizer1.Add( gbSizer2, 1, wx.EXPAND, 5 )
            
            gbSizer21 = wx.GridBagSizer( 0, 0 )
            gbSizer21.SetFlexibleDirection( wx.BOTH )
            gbSizer21.SetNonFlexibleGrowMode( wx.FLEX_GROWMODE_SPECIFIED )
            
            self.m_staticText11 = wx.StaticText( self, wx.ID_ANY, u"待转换字符：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText11.Wrap( -1 )
            gbSizer21.Add( self.m_staticText11, wx.GBPosition( 1, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText131 = wx.StaticText( self, wx.ID_ANY, u"ASCII转换", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText131.Wrap( -1 )
            self.m_staticText131.SetFont( wx.Font( 20, 70, 90, 90, False, wx.EmptyString ) )
            
            gbSizer21.Add( self.m_staticText131, wx.GBPosition( 0, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText12 = wx.StaticText( self, wx.ID_ANY, u"进制与asc可\n同时转换多\n个字符，用\n空格隔开", wx.DefaultPosition, wx.Size( -1,-1 ), 0 )
            self.m_staticText12.Wrap( -1 )
            gbSizer21.Add( self.m_staticText12, wx.GBPosition( 4, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.wait_asc_char = wx.TextCtrl( self, wx.ID_ANY, u"48", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer21.Add( self.wait_asc_char, wx.GBPosition( 1, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText21 = wx.StaticText( self, wx.ID_ANY, u"转换后字符：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText21.Wrap( -1 )
            gbSizer21.Add( self.m_staticText21, wx.GBPosition( 2, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.asc_char = wx.TextCtrl( self, wx.ID_ANY, u"0", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer21.Add( self.asc_char, wx.GBPosition( 2, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.asc_btn = wx.Button( self, wx.ID_ANY, u"确定", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer21.Add( self.asc_btn, wx.GBPosition( 3, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.asc_cp_btn = wx.Button( self, wx.ID_ANY, u"复制", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer21.Add( self.asc_cp_btn, wx.GBPosition( 3, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            fgSizer1.Add( gbSizer21, 1, wx.EXPAND, 5 )
            
            gbSizer211 = wx.GridBagSizer( 0, 0 )
            gbSizer211.SetFlexibleDirection( wx.BOTH )
            gbSizer211.SetNonFlexibleGrowMode( wx.FLEX_GROWMODE_SPECIFIED )
            
            self.m_staticText111 = wx.StaticText( self, wx.ID_ANY, u"待转换字符：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText111.Wrap( -1 )
            gbSizer211.Add( self.m_staticText111, wx.GBPosition( 1, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText1311 = wx.StaticText( self, wx.ID_ANY, u"base64", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText1311.Wrap( -1 )
            self.m_staticText1311.SetFont( wx.Font( 20, 70, 90, 90, False, wx.EmptyString ) )
            
            gbSizer211.Add( self.m_staticText1311, wx.GBPosition( 0, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.wait_base_char = wx.TextCtrl( self, wx.ID_ANY, wx.EmptyString, wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer211.Add( self.wait_base_char, wx.GBPosition( 1, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.m_staticText211 = wx.StaticText( self, wx.ID_ANY, u"转换后字符：", wx.DefaultPosition, wx.DefaultSize, 0 )
            self.m_staticText211.Wrap( -1 )
            gbSizer211.Add( self.m_staticText211, wx.GBPosition( 2, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.base_char = wx.TextCtrl( self, wx.ID_ANY, wx.EmptyString, wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer211.Add( self.base_char, wx.GBPosition( 2, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.base_btn = wx.Button( self, wx.ID_ANY, u"确定", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer211.Add( self.base_btn, wx.GBPosition( 4, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            m_choice1Choices = [ u"Encode", u"Decode" ]
            self.m_choice1 = wx.Choice( self, wx.ID_ANY, wx.DefaultPosition, wx.DefaultSize, m_choice1Choices, 0 )
            self.m_choice1.SetSelection( 0 )
            gbSizer211.Add( self.m_choice1, wx.GBPosition( 3, 0 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            self.base_cp_btn = wx.Button( self, wx.ID_ANY, u"复制", wx.DefaultPosition, wx.DefaultSize, 0 )
            gbSizer211.Add( self.base_cp_btn, wx.GBPosition( 4, 1 ), wx.GBSpan( 1, 1 ), wx.ALL, 5 )
            
            fgSizer1.Add( gbSizer211, 1, wx.EXPAND, 5 )
            
            self.SetSizer( fgSizer1 )
            self.Layout()
            
            self.Centre( wx.BOTH )
            
            # Connect Events
            self.hex_btn.Bind( wx.EVT_BUTTON, self.hex_btnOnButtonClick )
            self.hex_cp_btn.Bind( wx.EVT_BUTTON, self.hex_cp_btnOnButtonClick )
            self.asc_btn.Bind( wx.EVT_BUTTON, self.asc_btnOnButtonClick )
            self.asc_cp_btn.Bind( wx.EVT_BUTTON, self.asc_cp_btnOnButtonClick )
            self.base_btn.Bind( wx.EVT_BUTTON, self.base_btnOnButtonClick )
            self.base_cp_btn.Bind( wx.EVT_BUTTON, self.base_cp_btnOnButtonClick )
        
        def __del__( self ):
            pass
        
        
        # Virtual event handlers, overide them in your derived class
        def hex_btnOnButtonClick( self, event ):
            event.Skip()
        
        def hex_cp_btnOnButtonClick( self, event ):
            event.Skip()
        
        def asc_btnOnButtonClick( self, event ):
            event.Skip()
        
        def asc_cp_btnOnButtonClick( self, event ):
            event.Skip()
        
        def base_btnOnButtonClick( self, event ):
            event.Skip()
        
        def base_cp_btnOnButtonClick( self, event ):
            event.Skip()
    
　　至此，对应用的界面编辑就完成了。

### <a name="How-to-set-Events"></a>如何设置事件

　　如前面所说，创建好事件的构造函数后，可以直接将控件相应的代码写在构造函数中，也可以通过继承重写的方式实现。为了以后好修改，我选择的继承重写。所以将布局代码保存为basechangefirend.py。然后新建一个mainchangefirend.py文件，作为运行文件，**将窗体继承下来后。重写其事件函数。**主函数再初始化执行主窗体就好。我将我的代码附上以做参考：

    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    import wx
    import basechangefirend
    import deci
    import base64
    # 首先，从源文件中将主窗体继承下来.就是修改过name属性的主窗体。

    class MianWindow(basechangefirend.RoysTools):

        def init_main_window(self):
            pass

        def hex_btnOnButtonClick(self, event):
            try:
                Wait_hex = int(self.wait_hex.GetValue())
                Wait_hex_num = int(self.wait_hex_num.GetValue())
                To_hex = int(self.to_hex.GetValue())
            except Exception as e:
                dlg = wx.MessageDialog(
                    None, u"输入有误", u"警告信息", wx.YES_NO | wx.ICON_QUESTION)
                if dlg.ShowModal() == wx.ID_YES:
                    dlg.Destroy()
            else:
                ten_hex = int(deci.N_2_decimal(Wait_hex_num, Wait_hex))
                list_Hex_num = deci.decimal_2_N(ten_hex, To_hex)
                list1 = []
                for x in reversed(list_Hex_num):
                    x = str(x)
                    list1.append(x)
                Hex_num = "".join(list1)
                self.hex_num.SetValue(Hex_num)

            finally:
                event.Skip()

        def hex_cp_btnOnButtonClick(self, event):
            # 取得剪贴板并确保其为打开状态
            text_obj = wx.TextDataObject()
            text_obj.SetText(self.hex_num.GetValue())
            if wx.TheClipboard.IsOpened() or wx.TheClipboard.Open():
                wx.TheClipboard.SetData(text_obj)
                wx.TheClipboard.Close()

        def asc_btnOnButtonClick(self, event):
            self.asc_char.Clear()
            Wait_asc_char = self.wait_asc_char.GetValue()
            wac = Wait_asc_char.split()
            for x in wac:
                int_asc_char = int(x)
                try:
                    asc_char = chr(int_asc_char)
                except Exception as e:
                    dlg = wx.MessageDialog(
                        None, u"输入有误", u"警告信息", wx.YES_NO | wx.ICON_QUESTION)
                    if dlg.ShowModal() == wx.ID_YES:
                        dlg.Destroy()
                else:
                    self.asc_char.AppendText(asc_char)
                finally:
                    pass
            event.Skip()

        def asc_cp_btnOnButtonClick(self, event):
            text_obj = wx.TextDataObject()
            text_obj.SetText(self.asc_char.GetValue())
            if wx.TheClipboard.IsOpened() or wx.TheClipboard.Open():
                wx.TheClipboard.SetData(text_obj)
                wx.TheClipboard.Close()

        def base_btnOnButtonClick(self, event):
            if self.m_choice1.GetStringSelection() == 'Encode':
                try:
                    Base_char = base64.b64encode(self.wait_base_char.GetValue())
                except Exception as e:
                    dlg = wx.MessageDialog(
                        None, u"输入有误", u"警告信息", wx.YES_NO | wx.ICON_QUESTION)
                    if dlg.ShowModal() == wx.ID_YES:
                        dlg.Destroy()
                else:
                    self.base_char.SetValue(Base_char)
                finally:
                    pass
            elif self.m_choice1.GetStringSelection() == 'Decode':
                try:
                    Base_char = base64.b64decode(self.wait_base_char.GetValue())
                except Exception as e:
                    dlg = wx.MessageDialog(
                        None, u"输入有误", u"警告信息", wx.YES_NO | wx.ICON_ERROR)
                    if dlg.ShowModal() == wx.ID_YES:
                        dlg.Destroy()
                else:
                    self.base_char.SetValue(Base_char)
                finally:
                    pass
            event.Skip()

        def base_cp_btnOnButtonClick(self, event):
            text_obj = wx.TextDataObject()
            text_obj.SetText(self.base_char.GetValue())
            if wx.TheClipboard.IsOpened() or wx.TheClipboard.Open():
                wx.TheClipboard.SetData(text_obj)
                wx.TheClipboard.Close()

    if __name__ == '__main__':
        app = wx.App()
        # None表示的是此窗口没有上级父窗体。如果有，就直接在父窗体代码调用的时候填入‘self’就好了。
        main_win = MianWindow(None)
        main_win.init_main_window()
        main_win.Show()
        app.MainLoop()#进入窗口循环

这样我们就完成了程序的编写。

### <a name="use-py2app-create-app"></a>使用py2app创建应用

####创建setup.py

　　因为py2app有一个`py2applet`的帮助程序，所以我们只需要在终端中输入以下命令：

    $ py2applet --make-setup mainchangefirend.py

　　便会自动创建一个`setup.py`里面有应用程序的基本定义。我的修改后如下：

    """
    This is a setup.py script generated by py2applet

    Usage:
        python setup.py py2app
    """
    #coding: utf-8

    from setuptools import setup

    APP = ['mainchangefirend.py']
    APP_NAME="Roy'sTools"
    DATA_FILES = ['app.icns']
    OPTIONS = {
        'argv_emulation':True,
        'iconfile':'app.icns',
        'plist': {
                            'CFBundleName': APP_NAME,
                            'CFBundleDisplayName': APP_NAME,
                            'CFBundleGetInfoString': "Change something",
                            'CFBundleIdentifier': "com.farewell.osx.roy'stools",
                            'CFBundleVersion': "1.0.0",
                            'CFBundleShortVersionString': "1.0",
                            'NSHumanReadableCopyright': u"Copyright 2017,Roy,Rights Reserved"
                        }
                    }

    setup(
        app=APP,
        data_files=DATA_FILES,
        options={'py2app': OPTIONS},
        setup_requires=['py2app'],
    )

　　如果应用还包括一些json、图片，将其包含在DATA_FILES中即可。例如：

    DATA_FILES = ['testdata.json', 'picture.png']

####构建应用程序

　　如果要测试应用程序输入以下代码即可构建“别名模式”下的程序，这种程序通过软连接你的代码所以只能在你的电脑中使用。命令如下：

    $ python setup.py py2app -A

　　别名模式下的应用程序只是引用你的代码文件，所以你对代码文件的每一个修改，都可以在下次启动应用程序时自动反应出来。无须再次生成新的应用程序。如果你准备发布你的应用程序，去掉命令中的-A即可。但是在构建之前，要删除掉任何旧的`build`和`dist`目录。例如:

    $ rm -rf build dist
    $ python setup.py py2app

### <a name="The-End"></a>结束语

　　以上你看到了如何用 wxPython 以及py2app 构建一个简单的osx应用程序，这段代码是为了让刚接触Python以及想写一个自己的小工具的人了解如何开始实现。 要解决更复杂的现实生活中的问题，要开发真正的项目，还需要深入学习更多。

　　应用已经传在github上面。地址如下：[Roy'sTools](https://github.com/RoyTse/roytse.github.io)

<br>
转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [使用 wxPython 创建自己的OSX应用](http://RoyTse.github.io/2017/06/make_app_using_wxpython/)  
