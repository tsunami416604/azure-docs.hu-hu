---
title: Azure Cloud Services – Definition séma (.cscfg fájl) |} Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6347314e7f279356f4f3944f3238deda84f10fc0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358217"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services – Config séma (.cscfg fájl)
A szolgáltatás konfigurációs fájl határozza meg, az egyes szerepkörökhöz, a szolgáltatás telepítéséhez a szerepkörpéldányok számát, a megadott konfigurációs beállításokat, és a szerepkörhöz társított összes tanúsítványok ujjlenyomatait. A szolgáltatás nem kapcsolódik virtuális hálózathoz, ha a hálózati konfigurációs adatait meg kell adni a szolgáltatás konfigurációs fájlja, valamint a virtuális hálózati konfigurációs fájlban. A szolgáltatás konfigurációs fájlja alapértelmezett kiterjesztése .cscfg.

A modell le a [Felhőszolgáltatás (klasszikus) Definition séma](schema-csdef-file.md).

Alapértelmezés szerint a Azure Diagnostics-konfigurációs fájl van telepítve a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` a telepített verzióját az [Azure SDK](https://azure.microsoft.com/downloads/).

Egy szolgáltatást a szerepkörök konfigurálásával kapcsolatos további információkért lásd: [Mi az a Cloud Service modell](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Alapszintű konfigurációs séma
A szolgáltatás konfigurációs fájlja alapvető formátuma a következő.

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
A következő témakörök ismertetik a sémát a `ServiceConfiguration` elem:

- [Szerepkörséma](schema-cscfg-role.md)
- [NetworkConfiguration séma](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Szolgáltatás konfigurációs Namespace
A szolgáltatás konfigurációs fájl XML-névtér: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration elem
A `ServiceConfiguration` elem a szolgáltatás konfigurációs fájlja a legfelső szintű elemének.

A következő táblázat ismerteti az attribútumai a `ServiceConfiguration` elemet. Az összes attribútum értékei karakterlánc típusú.

| Attribútum | Leírás |
| --------- | ----------- |
|serviceName|Kötelező. A felhőszolgáltatás neve. Az itt megadott névvel, meg kell egyeznie a szolgáltatásdefiníciós fájlban megadott név.|
|Család|Választható. Adja meg a szerepkörpéldányok a felhőszolgáltatásban futó vendég operációs rendszer. További információ a támogatott vendég operációs rendszereinek kiadásait: [Azure vendég operációs rendszer kiadja és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).<br /><br /> Ha nem adja meg egy `osFamily` értéket, és nem állította a `osVersion` az attribútum egy adott vendég operációs rendszer verziója, alapértelmezett értéke 1.|
|OsVersion|Választható. Határozza meg a szerepkörpéldányok a felhőszolgáltatásban futó vendég operációs rendszer verzióját. További információ a Vendég operációsrendszer-verziók: [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).<br /><br /> Megadhatja, hogy a vendég operációs rendszer kell automatikusan a legújabb verzióra frissíteni. Ehhez állítsa a `osVersion` attribútumot `*`. Ha beállítása `*`, a szerepkörpéldányok telepítve van a megadott operációsrendszer-család a vendég operációs rendszer legújabb verzióját használja, és automatikusan frissíti a vendég operációs rendszer új verzióinak kiadásakor.<br /><br /> Egy adott verziójához manuális megadásához használja a `Configuration String` a táblából a **jövőbeli, aktuális és átmeneti Vendég operációsrendszer-verziók** szakasza [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md) .<br /><br /> Az alapértelmezett érték a `osVersion` attribútum `*`.|
|sémaverzióval|Választható. Határozza meg a szolgáltatás konfigurációs séma verzióját. A sémaverzió lehetővé teszi, hogy válassza ki a megfelelő SDK eszközöket a sémaérvényesítés használandó, ha az SDK több verziója telepítve van a Visual Studio egymás mellett. Séma- és verzió kompatibilitással kapcsolatos további információkért lásd: [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrix](cloud-services-guestos-update-matrix.md)|

A szolgáltatás konfigurációs fájlja tartalmaznia kell egy `ServiceConfiguration` elemet. A `ServiceConfiguration` elem tartalmazhat tetszőleges számú `Role` elemek és 0 vagy 1 `NetworkConfiguration` elemek.