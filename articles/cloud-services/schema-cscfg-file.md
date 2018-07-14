---
title: Azure Cloud Services – definíciós séma (.cscfg fájl) |} A Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 96df87a0d49296280140e392509c0d735f904957
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007022"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services – konfigurációs sémáját (.cscfg fájl)
A szolgáltatás konfigurációs fájlja meghatározza a szerepkör-példányok üzembe helyezéséhez az egyes szerepkörökhöz a szolgáltatásban, minden olyan konfigurációs beállítást, és a szerepkörhöz társított tanúsítványok ujjlenyomatait. Ha a szolgáltatás egy virtuális hálózathoz, a hálózati konfigurációs adatait a konfigurációs fájlban, valamint a virtuális hálózati konfigurációs fájlban kell adni. A szolgáltatás konfigurációs fájlja az alapértelmezett kiterjesztése .cscfg esetében.

A modell le a [Felhőszolgáltatás (klasszikus) – definíciós séma](schema-csdef-file.md).

Alapértelmezés szerint a fájl az Azure Diagnostics konfigurációs van telepítve a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` telepített verziójának a [Azure SDK](https://azure.microsoft.com/downloads/).

Egy szolgáltatásban szerepkörök konfigurálásával kapcsolatos további információkért lásd: [Mi a Cloud Service-modell](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Egyszerű szolgáltatás konfigurációs sémáját
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
Az alábbi témakörök ismertetik a sémát a `ServiceConfiguration` elem:

- [Szerepkörséma](schema-cscfg-role.md)
- [NetworkConfiguration séma](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Szolgáltatás konfigurációs Namespace
A szolgáltatás konfigurációs fájl XML-névtér: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> A szolgáltatáskonfiguráció elem
A `ServiceConfiguration` elem a szolgáltatás konfigurációs fájlja a legfelső szintű elemének.

A következő táblázat ismerteti az attribútumai a `ServiceConfiguration` elemet. Az összes attribútum értékei karakterlánc típusú.

| Attribútum | Leírás |
| --------- | ----------- |
|serviceName|Kötelező. A felhőszolgáltatás neve. Az itt megadott nevének egyeznie kell a szolgáltatásdefiníciós fájlban megadott név.|
|operációsrendszer-család|Választható. Adja meg a vendég operációs rendszer, amely a felhőszolgáltatás szerepkörpéldányain fog futni. További információ a támogatott vendég operációs rendszer kiadásai: [Azure vendég operációs rendszer kiadásai és SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).<br /><br /> Ha nem adja meg egy `osFamily` értéket, és nem állította a `osVersion` az attribútum az adott vendég operációsrendszer-verzió, alapértelmezett értéke 1.|
|osVersion|Választható. Határozza meg, amely a felhőszolgáltatás szerepkörpéldányain fog futni a vendég operációs rendszer verzióját. További információ a Vendég operációsrendszer-verziók: [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md).<br /><br /> Megadhatja, hogy a vendég operációs rendszer automatikusan fel kell frissíthető a legújabb verzióra. Ehhez állítsa az értékét a `osVersion` attribútumot `*`. Ha a beállítása `*`, a szerepkör példányai vannak telepítve a vendég operációs rendszer legújabb verziójának használata a megadott operációsrendszer-család, és automatikusan frissül, ha a vendég operációs rendszer új verzióinak kiadásakor.<br /><br /> Manuálisan adjon meg egy adott verziót, használja a `Configuration String` abból a táblázatból, a **jövőbeli, jelenlegi és átmeneti Vendég operációsrendszer-verziók** szakaszában [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md) .<br /><br /> Az alapértelmezett érték a `osVersion` attribútum `*`.|
|sémaverzióval|Választható. Határozza meg a szolgáltatás konfigurációs sémáját verzióját. A séma verziója lehetővé teszi, hogy válassza ki a megfelelő SDK-eszközök séma érvényesítése használandó, ha az SDK egynél több verziója van telepítve a Visual Studio egymás mellett. Séma- és verzió kompatibilitással kapcsolatos további információkért lásd: [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](cloud-services-guestos-update-matrix.md)|

A szolgáltatás konfigurációs fájlja tartalmaznia kell egy `ServiceConfiguration` elemet. A `ServiceConfiguration` elemet tartalmazhat tetszőleges számú `Role` elemek és nulla vagy 1 `NetworkConfiguration` elemeket.