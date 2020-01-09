---
title: Azure Cloud Services definíciós séma (. cscfg fájl) | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: 71c0bb1b09d480a05a9e5a54b269d0da8fde5bc3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449099"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services konfigurációs séma (. cscfg fájl)
A szolgáltatás konfigurációs fájlja meghatározza a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek. Ha a szolgáltatás egy Virtual Network része, a hálózat konfigurációs adatait meg kell adni a szolgáltatás konfigurációs fájljában, valamint a virtuális hálózat konfigurációs fájljában. A szolgáltatás konfigurációs fájljának alapértelmezett kiterjesztése a. cscfg.

A szolgáltatási modellt a [Cloud Service (klasszikus) definíciós sémája](schema-csdef-file.md)írja le.

Alapértelmezés szerint a Azure Diagnostics konfigurációs sémafájl telepítve van a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje le a `<version>`t az [Azure SDK](https://azure.microsoft.com/downloads/)telepített verziójára.

A szerepkörök szolgáltatásban való konfigurálásával kapcsolatos további információkért lásd: [Mi a Cloud Service-modell](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Alapszintű szolgáltatás konfigurációs sémája
A szolgáltatás konfigurációs fájljának alapszintű formátuma a következő.

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

## <a name="schema-definitions"></a>Séma-definíciók
A következő témakörök ismertetik a `ServiceConfiguration` elem sémáját:

- [Szerepkörséma](schema-cscfg-role.md)
- [NetworkConfiguration séma](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Szolgáltatás konfigurációs névtere
A szolgáltatás konfigurációs fájljának XML-névtere a következő: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a>ServiceConfiguration elem
A `ServiceConfiguration` elem a szolgáltatás konfigurációs fájljának legfelső szintű eleme.

Az alábbi táblázat a `ServiceConfiguration` elem attribútumait ismerteti. Minden attribútum értéke karakterlánc típusú.

| Attribútum | Leírás |
| --------- | ----------- |
|serviceName|Kötelező. A felhőalapú szolgáltatás neve. Az itt megadott névnek egyeznie kell a szolgáltatás definíciós fájljában megadott névvel.|
|osFamily|Választható. Meghatározza azt a vendég operációs rendszert, amely a felhőalapú szolgáltatásban a szerepkör példányain fog futni. A támogatott vendég operációs rendszer kiadásaival kapcsolatos információkért lásd: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md).<br /><br /> Ha nem tartalmaz `osFamily` értéket, és nem állította be a `osVersion` attribútumot egy adott vendég operációsrendszer-verzióra, a rendszer az alapértelmezett 1 értéket használja.|
|osVersion|Választható. Megadja a vendég operációs rendszernek azt a verzióját, amely a felhőalapú szolgáltatásban a szerepkör példányain fog futni. A vendég operációs rendszer verziójával kapcsolatos további információkért lásd: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md).<br /><br /> Megadhatja, hogy a vendég operációs rendszer automatikusan a legújabb verzióra legyen frissítve. Ehhez állítsa `*`értékre a `osVersion` attribútum értékét. Ha `*`értékre van állítva, a rendszer telepíti a szerepkör példányait a vendég operációs rendszer legújabb verziójára a megadott operációsrendszer-családra vonatkozóan, és a rendszer automatikusan frissíti a vendég operációs rendszer új verzióinak kiadásakor.<br /><br /> Egy adott verzió manuális megadásához használja az [Azure vendég operációs rendszer-kiadások és az SDK-kompatibilitási mátrix](cloud-services-guestos-update-matrix.md) **jövőbeli, jelenlegi és átmeneti vendég operációs rendszer verziók** szakaszának `Configuration String` a táblázatból.<br /><br /> A `osVersion` attribútum alapértelmezett értéke `*`.|
|sémaverzióval|Választható. Megadja a szolgáltatás konfigurációs sémájának verzióját. A séma verziója lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója is telepítve van egymás mellett. További információ a séma és a verziók kompatibilitásáról: az [Azure vendég operációs rendszer kiadásai és az SDK kompatibilitási mátrixa](cloud-services-guestos-update-matrix.md)|

A szolgáltatás konfigurációs fájljának tartalmaznia kell egy `ServiceConfiguration` elemet. A `ServiceConfiguration` elem tetszőleges számú `Role` elemet és nulla vagy 1 `NetworkConfiguration` elemet tartalmazhat.