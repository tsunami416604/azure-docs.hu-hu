---
title: Fenyegetések észlelése a felhőalapú natív számítástechnika Azure Security Centerban | Microsoft Docs
description: Ez a cikk a Felhőbeli natív számítási riasztásokat ismerteti Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: f2fda893ad84aaf9d11d26d761f5395c7f5650d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666372"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Fenyegetések észlelése a Felhőbeli natív számítástechnika Azure Security Center

Natív megoldásként Azure Security Center egyedi láthatóságot biztosít a belső naplók számára a támadási módszertan azonosítására több cél között. Ez a cikk a következő Azure-szolgáltatásokhoz elérhető riasztásokat ismerteti:

* [Azure App Service](#app-services)
* [Azure-tárolók](#azure-containers) 

> [!NOTE]
> Ezek a szolgáltatások jelenleg nem érhetők el az Azure governmentben és a szuverén Felhőbeli régiókban.

## Azure App Service<a name="app-services"></a>

Security Center a felhő méretezésével azonosítja a App Serviceon futó alkalmazásokat célzó támadásokat. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek egy kis részhalmazát mutatja be, és a hasonló végpontokra való bejárási mintákat jeleníti meg több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.

A Security Center az alapul szolgáló munkapéldányokat és virtuális gépeket is elérheti. A memória-kriminalisztikai szolgáltatással együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.

A Azure App Service riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureappserv)tekintheti meg.

## Azure-tárolók<a name="azure-containers"></a>

Security Center valós idejű veszélyforrások észlelését teszi lehetővé a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A fenyegetéseket különböző szinteken észleljük: 

* A **gazdagép szintjének** Security Center ügynöke (a standard szinten érhető el, a részletek [díjszabása](security-center-pricing.md) ) figyeli a Linuxot a gyanús tevékenységekhez. Az ügynök riasztásokat küld a csomópontból vagy a rajta futó tárolóból származó gyanús tevékenységekről. Ilyen tevékenység például a webrendszerhéj-észlelés és a kapcsolat ismert gyanús IP-címekkel.

    Az ügynök a tároló-specifikus elemzéseket figyeli, így mélyebb betekintést nyújt a tároló-környezet biztonságára. Riasztásokat indít az eseményekhez, például a privilegizált tárolók létrehozásához, az API-kiszolgálókhoz való gyanús hozzáféréshez, valamint a Secure Shell-(SSH-) kiszolgálókhoz, amelyek egy Docker-tárolón belül futnak.

    >[!NOTE]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, csak a veszélyforrások észlelésével járó előnyök és riasztások egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

    A gazda szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-containerhost)tartalmazza.


* **AK-fürt szintjén**a Kubernetes-naplózási elemzések alapján észlelt fenyegetések észlelése. Az **ügynök** nélküli figyelés engedélyezéséhez adja hozzá az előfizetéshez a Kubernetes lehetőséget a **díjszabási & beállítások** lapon (lásd a [díjszabást](security-center-pricing.md)). A riasztások ezen a szinten történő létrehozásához Security Center figyeli az AK által felügyelt szolgáltatásokat az AK által beolvasott naplók használatával. Az ezen a szinten található események közé tartoznak például az elérhető Kubernetes-irányítópultok, a magas jogosultsági szintű szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása.

    >[!NOTE]
    > A Security Center észlelési riasztásokat hoz létre az Azure Kubernetes szolgáltatás műveleteihez és központi telepítések esetén, miután a Kubernetes beállítás engedélyezve van az előfizetési beállításokban. 

    Az AK-beli fürt szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-akscluster)tartalmazza.

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.