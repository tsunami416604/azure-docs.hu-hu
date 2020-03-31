---
title: Cloud Services szerepkör config XPath cheat sheet | Microsoft dokumentumok
description: A különböző XPath-beállításokat használhatja a felhőalapú szolgáltatási szerepkör config a beállítások környezeti változóként való elérhetővé.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386085"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Szerepkör-konfigurációs beállítások felfedése környezeti változóként az XPath segítségével
A felhőszolgáltatás-feldolgozó vagy a webes szerepkör-szolgáltatás definíciós fájljában a futásidejű konfigurációs értékek környezeti változókként is elérhetővé tehető. A következő XPath-értékek támogatottak (amelyek api-értékeknek felelnek meg).

Ezek az XPath-értékek a [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) függvénytáron keresztül is elérhetők. 

## <a name="app-running-in-emulator"></a>Emulátorban futó alkalmazás
Azt jelzi, hogy az alkalmazás fut az emulátorban.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Központi telepítési azonosító
Lekéri a példány központi telepítési azonosítóját.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Szerepkör azonosítója
A példány aktuális szerepkör-azonosítójának beolvasása.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kód |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Frissítési tartomány
A példány frissítési tartományát olvassa be.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Tartalék tartomány
A példány tartalék tartományát olvassa be.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Szerepkörnév
A példányok szerepkörnevének beolvasása.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurációs beállítás
A megadott konfigurációs beállítás értékének beolvasása.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kód |var beállítás = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Helyi tárolási útvonal
A példány helyi tárolási útvonalát olvassa be.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Helyi tárhely mérete
Lekéri a példány helyi tárolójának méretét.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabájt; |

## <a name="endpoint-protocol"></a>Végpont protokoll
A példány végpontprotokolljának beolvasása.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Kód |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. jegyzőkönyv; |

## <a name="endpoint-ip"></a>Végpont IP-címe
A megadott végpont IP-címének beszerzése.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Kód |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Cím |

## <a name="endpoint-port"></a>Végpontport
A példány végpontportjának beolvasása.

| Típus | Példa |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Példa
Íme egy példa egy feldolgozói szerepkörre, amely létrehoz `TestIsEmulated` egy indítási feladatot, amelynek az [ @emulated xpath értékre](#app-running-in-emulator)megnevezett környezeti változója van. 

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

## <a name="next-steps"></a>További lépések
További információ a [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlról.

Hozzon létre egy [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) csomagot.

[Távoli asztal](cloud-services-role-enable-remote-desktop-new-portal.md) engedélyezése szerepkörhöz.




