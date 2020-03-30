---
title: Azure Cloud Services-definíciós séma (.cscfg fájl) | Microsoft dokumentumok
description: A szolgáltatáskonfigurációs (.cscfg) fájl azt határozza meg, hogy az egyes szerepkörökhöz, konfigurációs értékekhez és tanúsítványujjlenyomatokhoz hány szerepkört kell telepíteni.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534745"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services konfigurációs sémája (.cscfg fájl)
A szolgáltatáskonfigurációs fájl határozza meg a szolgáltatás egyes szerepköreihez telepítandó szerepkörpéldányok számát, a konfigurációs beállítások értékeit és a szerepkörhöz társított tanúsítványok ujjlenyomatait. Ha a szolgáltatás egy virtuális hálózat része, a hálózat konfigurációs adatait a szolgáltatás konfigurációs fájljában, valamint a virtuális hálózati konfigurációs fájlban kell megadni. A szolgáltatáskonfigurációs fájl alapértelmezett kiterjesztése .cscfg.

A szolgáltatásmodellt a [felhőszolgáltatás (klasszikus) definíciós séma írja](schema-csdef-file.md)le.

Alapértelmezés szerint az Azure Diagnostics konfigurációs sémafájl telepítve van a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje `<version>` le az [Azure SDK telepített verziójára.](https://azure.microsoft.com/downloads/)

A szerepkörök szolgáltatásban való konfigurálásáról a [Mi a Felhőszolgáltatás-modell](cloud-services-model-and-package.md)című témakörben talál további információt.

## <a name="basic-service-configuration-schema"></a>Alapszintű szolgáltatáskonfigurációs séma
A szolgáltatáskonfigurációs fájl alapformátuma a következő.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Sémadefiníciók
Az alábbi témakörök az `ServiceConfiguration` elem sémáját ismertetik:

- [Szerepkörséma](schema-cscfg-role.md)
- [NetworkConfiguration-séma](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Szolgáltatáskonfigurációs névtér
A szolgáltatás konfigurációs fájljának `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`XML-névtere: .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Szolgáltatáskonfiguráció s.
Az `ServiceConfiguration` elem a szolgáltatáskonfigurációs fájl legfelső szintű eleme.

Az alábbi táblázat az `ServiceConfiguration` elem attribútumait ismerteti. Minden attribútumérték karakterlánctípus.

| Attribútum | Leírás |
| --------- | ----------- |
|szolgáltatásneve|Kötelező. A felhőszolgáltatás neve. Az itt megadott névnek meg kell egyeznie a szolgáltatásdefiníciós fájlban megadott névvel.|
|osFamily között|Választható. Megadja a vendég operációs rendszert, amely a felhőszolgáltatás szerepkörpéldányain fog futni. A támogatott vendég operációsrendszer-kiadásokról az [Azure Vendég operációsrendszer-kiadások és az SDK-kompatibilitási mátrix című](cloud-services-guestos-update-matrix.md)témakörben talál.<br /><br /> Ha nem ad `osFamily` meg értéket, és `osVersion` nem állította be az attribútumot egy adott vendég operációsrendszer-verzióra, a rendszer 1-es alapértelmezett értéket használ.|
|osVersion|Választható. A vendég operációs rendszer azon verzióját adja meg, amely a felhőszolgáltatás szerepkörpéldányain fog futni. A vendég operációsrendszer-verziókról az [Azure Vendég operációsrendszer-kiadások és az SDK-kompatibilitási mátrix című](cloud-services-guestos-update-matrix.md)témakörben talál további információt.<br /><br /> Megadhatja, hogy a vendég operációs rendszer automatikusan frissüljön a legújabb verzióra. Ehhez állítsa az `osVersion` attribútum értékét a `*`értékére. Ha a `*`beállítás, a szerepkörpéldányok a megadott operációsrendszer-család vendég operációs rendszerének legújabb verziójával kerülnek üzembe helyezésre, és a vendég operációs rendszer új verzióinak megjelenésekor automatikusan frissülnek.<br /><br /> Adott verzió manuális megadásához `Configuration String` használja a tábla az [Azure Vendég operációsrendszer-kiadások és az SDK-kompatibilitási mátrix](cloud-services-guestos-update-matrix.md) **jövőbeli, jelenlegi és átmeneti vendég operációsrendszer-verziói** szakaszának.<br /><br /> Az `osVersion` attribútum alapértelmezett értéke `*`a .|
|sémaverzió|Választható. A Szolgáltatáskonfiguráció séma verzióját adja meg. A sémaverzió lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója van telepítve egymás mellett. A séma- és verziókompatibilitásról az [Azure Vendég operációsrendszer-kiadások és az SDK-kompatibilitási mátrix című](cloud-services-guestos-update-matrix.md) témakörben talál további információt.|

A szolgáltatáskonfigurációs fájlnak egy `ServiceConfiguration` elemet kell tartalmaznia. Az `ServiceConfiguration` elem tetszőleges számú `Role` elemet és `NetworkConfiguration` nullát vagy 1 elemet tartalmazhat.