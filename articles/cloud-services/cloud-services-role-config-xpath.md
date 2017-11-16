---
title: "Cloud Services szerepkör konfigurációs XPath Adatlap |} Microsoft Docs"
description: "A különböző XPath-beállítások segítségével a felhőalapú szolgáltatás szerepkör config teszi közzé a beállítások, egy környezeti változó."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e71adbca34390bda3a7d4067742ffb3a28201449
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>XPath környezeti változó, teszi közzé a szerepkör konfigurációs beállításai
A felhőalapú szolgáltatás munkavégző vagy a webes szerepkör szolgáltatásdefiníciós fájl teszi környezeti változóként futásidejű konfigurációs értékeket. A következő XPath értékek (amely API értékek) használata támogatott.

Ezek az értékek XPath keresztül is érhetők el a [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) könyvtárban. 

## <a name="app-running-in-emulator"></a>Emulátoron futó alkalmazáshoz
Azt jelzi, hogy az alkalmazás futtatása az emulátorban.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Központi telepítési azonosítója
Lekéri a telepítési azonosító, a példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Szerepkör-azonosítója
Lekérdezi az aktuális szerepkör-Azonosítót a példányt.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Kód |var azonosítója = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Tartomány frissítése
Lekéri a frissítési tartomány, a példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Hibatartomány
Lekéri a tartalék tartomány-példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Szerepkör neve
A példányok szerepkör nevét kéri le.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurációs beállítás
A megadott konfigurációs beállítás értékét kérdezi le.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et vagy a CurrentInstance/ConfigurationSettings/ConfigurationSetting [@name= 'Setting1']/@value" |
| Kód |var beállítás = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Tárolási helyi elérési útja
Lekéri a helyi tárterület elérési példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et vagy a CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Helyi tárterület mérete
Lekéri a helyi tárterület-példány méretét.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et vagy a CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Végpont protokoll
Lekéri a végpont protokoll-példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et/CurrentInstance/végpont vagy végpont [@name= 'Endpoint1']/@protocol" |
| Kód |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokoll; |

## <a name="endpoint-ip"></a>Végpont IP
Lekérdezi a megadott végpont IP-címe.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et/CurrentInstance/végpont vagy végpont [@name= 'Endpoint1']/@address" |
| Kód |var cím = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Végpont portja
Lekéri a végponti port-példány.

| Típus | Példa |
| --- | --- |
| XPath |XPath = "/ roleenvironment-et/CurrentInstance/végpont vagy végpont [@name= 'Endpoint1']/@port" |
| Kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Példa
A feldolgozói szerepkör, amely egy indítási tevékenységhez hoz létre egy környezeti változó neve, például `TestIsEmulated` beállítása a [ @emulated xpath értékét](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Következő lépések
További információ a [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlt.

Hozzon létre egy [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) csomag.

Engedélyezése [távoli asztal](cloud-services-role-enable-remote-desktop-new-portal.md) szerepkör.

