---
title: Cloud Services szerepkör-konfiguráció XPath-lapja | Microsoft Docs
description: A Cloud Service szerepkör-konfigurációban használható különböző XPath-beállítások a beállítások környezeti változóként való megjelenítéséhez.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: cd2bdc4fc4b2a135907851ca4d3034430618e0cd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359004"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Szerepkör-konfigurációs beállítások közzététele környezeti változóként XPath-ként
A Cloud Service Worker vagy a web role szolgáltatás definíciós fájljában a futásidejű konfigurációs értékeket környezeti változókként teheti elérhetővé. A következő XPath-értékek támogatottak (amelyek az API-értékeknek felelnek meg).

Ezek az XPath-értékek a [Microsoft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) könyvtáron keresztül is elérhetők. 

## <a name="app-running-in-emulator"></a>Emulátorban futó alkalmazás
Azt jelzi, hogy az alkalmazás az emulátorban fut.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment. IsEmulated; |

## <a name="deployment-id"></a>Üzemelő példány azonosítója
A példány központi telepítési AZONOSÍTÓjának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment. DeploymentId; |

## <a name="role-id"></a>Szerepkör-azonosító
A példány aktuális szerepkör-AZONOSÍTÓjának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kód |var azonosító = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Tartomány frissítése
A példány frissítési tartományának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Tartalék tartomány
A példány tartalék tartományának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Szerepkörnév
Lekéri a példányok szerepkörének nevét.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurációs beállítás
A megadott konfigurációs beállítás értékének beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kód |var-beállítás = RoleEnvironment. GetConfigurationSettingValue ("Setting1"); |

## <a name="local-storage-path"></a>Helyi tár elérési útja
A példány helyi tárolási útvonalának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment. GetLocalResource ("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Helyi tárterület mérete
Lekéri a példány helyi tárterületének méretét.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment. GetLocalResource ("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Végponti protokoll
A példány végponti protokolljának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Kód |var Prot = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. Protokoll |

## <a name="endpoint-ip"></a>Végpont IP-címe
Lekéri a megadott végpont IP-címét.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Kód |var címe = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. címe |

## <a name="endpoint-port"></a>Végpont portja
A példány végpont-portjának beolvasása.

| Type | Példa |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Kód |var port = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. port; |

## <a name="example"></a>Példa
Itt látható egy példa arra a feldolgozói szerepkörre, amely indítási feladatot hoz létre egy `TestIsEmulated` beállított nevű környezeti változóval az [ @emulated XPath értékhez](#app-running-in-emulator). 

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
További információ a [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlról.

Hozzon létre egy [szervizcsomagot. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) csomagot.

Engedélyezze a [Távoli asztal](cloud-services-role-enable-remote-desktop-new-portal.md) szerepkört.

