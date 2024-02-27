# EChartsPlugin
[Introduction](#Introduction)  
[Installation Instructions](#Installation Instructions)  
[Packing Instructions](#Packing Instructions)  
[How to use](#How to use)  
[Special blueprint node description](#Special blueprint node description)  
[Example](#Example)  
[Tips & Tricks](#Tips & Tricks)  
[Known Issues](#Known Issues)  
[ECharts Option object and plugin Option object correspondence table](#echarts-option object and plugin option object correspondence table)  

# Introduction
This plugin integrates ECharts into Unreal Engine, based on UMG controls, ECharts version 5.4.4, which can realize most of the charting effects of ECharts, and currently tested the Windows platform, which realizes the following functions:  
1, support the use of blueprint nodes to dynamically create ECharts Option  
2, support the use of Json strings directly ECharts Option  
3, support for some of the ECharts Extension ( such as: Baidu map extension )  
4, support for ECharts click, dbclick, mouseover events , events can be received in the blueprint when the event Json string to get the event type and data ( more event support added )  
5, support DispatchAction, you can use the blueprint to control ECharts.  
6. Support ECharts background transparency control  
7, support for EChats theme (Theme)  

# Installation instructions
## 1, copy the plug-in to the project Plugins directory  
! [image](img/1.jpg)  
## 2, if you need to refer to other Javascript files (for example: Baidu map extension), copy the js file to the plugin's Content\ECharts directory  
! [image](img/2.jpg)  

# Packaging instructions
All the files in the Content\ECharts directory of the plugin will be copied to the output directory when the project is packaged.
! [image](img/23.jpg)  
  
If the program flashes after packaging, see the Known Issues section at the end of this document.  

# Usage  
## 1. Add the ECharts Widget control to the UMG.  
! [image](img/3.jpg)  
## 2. Set the properties of the ECharts Widget control.  
! [image](img/4.jpg)  
Enable Transparent: background transparency  
Extension Script Files: extra js script files loaded on ECharts page (can be the name of js file in Content\ECharts directory or online url address, see example: 6_BaiduMap)  
Theme Json String: Json string of the Theme used by ECharts.  
Events: ECharts events to be received, after checking the blueprint node will receive the corresponding event Json string.  

## 3. Setting Option for ECharts Widget in Blueprint  
There are two ways to set Option for ECharts Widget.  
Method 1: Construct the Json string of the Option directly, and set the string to the ECharts Widget, see the example: 1_RawJsonString  
! [image](img/5.jpg)  
Method 2: Create and set Option object by blueprint, set the object to ECharts Widget, see example: 2_BlueprintOption  
! [image](img/6.jpg)  
Note: When setting Option for an ECharts Widget, the two methods can be used in conjunction with each other to realize flexible and complex Option settings, see example: 8_KLine  
## 4. Receive ECharts events in blueprints  
ECharts Widget's properties of the event checked, when the event occurs in ECharts you can receive the event in the blueprint Json string, contains the event type and related data, see example: 3_Event  
! [image](img/7.jpg)  
## 5, through the DispatchAction control ECharts, see example: 4_Action  
Call DispatchAction on ECharts Widget to pass in the Json string of the action.  
! [image](img/8.jpg)  
## 6. Dynamic Creation of ECharts Option Objects via Blueprint Node Description
This plugin in the UE ported the vast majority of the objects in the ECharts Option, you can set the object on demand, through the blueprint to construct a complex Option structure.  
! [image](img/9.jpg)  
In the image above, a tooltip object is first created, with the trigger property set, then an axis pointer is created, with the type property set, then the axis pointer object is set to the tooltip, and finally the tooltip object is set to the Option, and the entire Option can be used in this way for almost all Option created, example: 8_KLine.  

When an attribute of an object is set, only then will the corresponding attribute string be generated in the final Option, unset attributes will not appear in the Option, for example, in the above figure the axisPointer object will generate a Json object string in the tooltip: axisPointer: {type:' cross'}, while other properties of axisPointer will not appear. This enables the merge operation of options in ECharts, so that the object that sets the option and the Json string can be used at the same time.  

Plugin ported Option in the structure of the object's attribute names and js to maintain consistency, the object's related properties and input values please refer to the [ECharts configuration items official documentation](https://echarts.apache.org/zh/option.html) (except for a particularly flexible way to write the attributes, such as: series) markPoint data, the object is not refined, you need to use the full string settings, example: 8_Kline, does not exclude the individual object porting problems, such as found to affect the use of please leave a message or e-mail feedback).  

In order to realize the flexible writing of attribute values in js, all non-object attributes in the plugin are of string type (example: the left attribute of legend can be entered as 20, '20%', 'left', 'center', 'right', etc.), so if the value of the attribute is of string type, you need to bring single quotes when setting it.  
Example:  
! [image](img/10.jpg) 
! [image](img/11.jpg) ! 
! [image](img/12.jpg) 
! [image](img/13.jpg) 
! [image](img/14.jpg)  

Since there are a lot of objects with the same name in the Option of ECharts, for example: many of them contain data, some of them have the same data structure (xAxis and yAxis), while others are different. In order to reduce the number of objects in the plug-in, the transplantation of objects with the same name is summarized, a structure in the plug-in to generate a corresponding object, a variety of structural objects with the same name, the transplantation of the object name with an index number to distinguish between the unique object name without a serial number, such as Echarts Option there are a total of 25 different structure of the data object, so after the transplantation plug-in code For example, there are 25 different data objects in Echarts Option, so the plugin code after porting has 25 data objects from EChartsOption_data0 to EChartsOption_data24, and the data objects in xAxis and yAxis use UEChartsOption_data1, while the data objects in the pie in the series use UEChartsOption_data5, refer to [EChartOption object and plugin Option object correspondence table](MapTable.md) for the complete correspondence.  

You can also see in the following figure by creating the Set data node of yAxis that data is an array of type EChartsOption_data1  
! [image](img/15.jpg)  
## Special blueprint node description  
## 1, SetOption and SetOptionJsonString nodes  
! [image](img/16.jpg)  
! [image](img/17.jpg)  
AdditionalScript in the node can set additional js script, the script position is before the ECharts setOption, you can add the data and function definition of js and call it in Option. The added data and function definitions are global variables that can be accessed subsequently if SetOption is called again, see examples: 6_BaiduMap and 8_KLine.  

Where the SetOption node returns the Json string of the final generated Option (with AddtionalScript)  
## Description of the example  
## Run the example
Open the Content\Example\ExampleMap level in the plugin, run the level, and click the corresponding example button in the ui to view the example  
! [image](img/22.jpg)  
  
## Example functions (more examples added)  
1_RawJsonString  
Set EChartsWidget directly using Option's Json string.  
  
2_BlueprintOption  
Dynamically create an Option object using a blueprint, then use the Option object to set the EChartsWidget.  
  
3_Event  
Checks the ECharts event of interest and receives the data of the event in the blueprint.  
  
4_Action  
Controls the ECharts via the DispatchAction blueprint node of the EChartsWidget using the action's Json string.  
  
5_Transparent  
Set the background of ECharts to transparent  
  
6_BaiduMap  
The official ECharts example: Air Quality in Major Cities - BaiduMap, loads the ECharts BaiduMap extension js file and uses additionalScript to set additional required js data and code when setting the Option of the EChartsWidget.  
  
This example directly uses the AK of the Baidu map api in the ECharts example (https://api.map.baidu.com/api?v=3.0&ak=), please change it to your own AK.  
  
7_Theme  
Set ECharts Theme Json string for EChartsWidget.  
  
8_KLine  
The official example of ECharts: SSE index, using both Json string and Option object to set the Option of EChartsWidget.  
