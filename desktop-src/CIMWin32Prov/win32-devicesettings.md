---
Description: The Win32\_DeviceSettings abstract, association WMI class relates a logical device and a setting that can be applied to it.
audience: developer
author: REDMOND\\markl
manager: REDMOND\\markl
ms.assetid: 4f6c4c26-8da9-4e2c-8b8c-cec658ac08d4
ms.prod: windows-server-dev
ms.technology:
- cimwin32
- windows-management-instrumentation
ms.tgt_platform: multiple
title: Win32\_DeviceSettings class
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# Win32\_DeviceSettings class

The **Win32\_DeviceSettings** abstract, association [WMI class](https://msdn.microsoft.com/library/aa393244) relates a logical device and a setting that can be applied to it.

The following syntax is simplified from Managed Object Format (MOF) code and includes all of the inherited properties. Properties are listed in alphabetic order, not MOF order.

## Syntax

``` syntax
[Abstract, UUID("{8502C4FD-5FBB-11D2-AAC1-006008C78BC7}"), AMENDMENT]
class Win32_DeviceSettings : CIM_ElementSetting
{
  CIM_Setting       REF Setting;
  CIM_LogicalDevice REF Element;
};
```

## Members

The **Win32\_DeviceSettings** class has these types of members:

-   [Properties](#properties)

### Properties

The **Win32\_DeviceSettings** class has these properties.

<dl> <dt>

**Element**
</dt> <dd> <dl> <dt>

Data type: **CIM\_LogicalDevice**
</dt> <dt>

Access type: Read-only
</dt> <dt>

Qualifiers: [**key**](https://msdn.microsoft.com/library/aa392157), [**Override**](https://msdn.microsoft.com/library/aa393650) ("Element"), [**MappingStrings**](https://msdn.microsoft.com/library/aa393650) ("CIM\|CIM\_LogicalDevice")
</dt> </dl>

A [**CIM\_LogicalDevice**](cim-logicaldevice.md) that represents properties of the logical device on which the settings can be applied.

</dd> <dt>

**Setting**
</dt> <dd> <dl> <dt>

Data type: **CIM\_Setting**
</dt> <dt>

Access type: Read-only
</dt> <dt>

Qualifiers: [**key**](https://msdn.microsoft.com/library/aa392157), [**Override**](https://msdn.microsoft.com/library/aa393650) ("Setting"), [**MappingStrings**](https://msdn.microsoft.com/library/aa393650) ("CIM\|CIM\_Setting")
</dt> </dl>

A [**CIM\_Setting**](cim-setting.md) that represents settings that can be applied to the logical device.

</dd> </dl>

## Remarks

The **Win32\_DeviceSettings** class is derived from [**CIM\_ElementSetting**](cim-elementsetting.md).

## Requirements



|                                     |                                                                                         |
|-------------------------------------|-----------------------------------------------------------------------------------------|
| Minimum supported client<br/> | Windows Vista<br/>                                                                |
| Minimum supported server<br/> | Windows Server 2008<br/>                                                          |
| Namespace<br/>                | Root\\CIMV2<br/>                                                                  |
| MOF<br/>                      | <dl> <dt>CIMWin32.mof</dt> </dl> |
| DLL<br/>                      | <dl> <dt>CIMWin32.dll</dt> </dl> |



## See also

<dl> <dt>

[**CIM\_ElementSetting**](cim-elementsetting.md)
</dt> <dt>

[Computer System Hardware Classes](computer-system-hardware-classes.md)
</dt> </dl>

 

 



