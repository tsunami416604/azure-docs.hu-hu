---
title: Tárolók az Azure Monitor szolgáltatás áttekintése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor-tárolókhoz, amely figyeli az AKS Container Insights megoldás és az érték azt tesz lehetővé, hogy az AKS-fürt és az Azure Container Instances állapotának figyelése.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521836"
---
# <a name="azure-monitor-for-containers-overview"></a>Az Azure Monitor tárolók áttekintése

A tárolók az Azure Monitor egy olyan szolgáltatás, vagy az Azure Container Instances szolgáltatásban, vagy a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök üzembe tárolóalapú számítási feladatok teljesítményének figyelésére tervezett. A tárolók alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelése, metrikák és naplók is összegyűjti az Ön számára a Linuxhoz készült Log Analytics-ügynököt egy tárolóalapú verziója keresztül. A metrikák tároló írt metrikák és a rendszer naplóadatokat ír a naplókat tároló társított a [Log Analytics](../log-query/log-query-overview.md) munkaterületen. 

![Az Azure Monitor tárolók architektúra](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Az Azure Monitor-tárolók biztosítják a funkciója?

A tárolók az Azure Monitor tesz lehetővé egy átfogó figyelési környezetet biztosít az Azure Monitor különböző funkcióit engedélyezése, hogy jobban megismerhesse a teljesítmény és a Kubernetes-fürt és a tárolók számítási feladatainak állapotát. A tárolók az Azure Monitor segítségével:

* AKS-tárolók, a csomópont és a processzor és memória átlagos kihasználtság futó azonosításához. A Tudásbázis segítségével erőforrás szűk keresztmetszetek azonosítása.
* Azonosítsa a tárolócsoportok és a tárolók az Azure Container Instances szolgáltatásban üzemeltetett processzor és memória felhasználását.  
* Azonosítsa, amelyben a tároló található a vezérlő vagy podot. A Tudásbázis segítségével megtekintheti a vezérlő vagy a pod általános teljesítménye.
* Tekintse át az erőforrás-használat, a gazdagépen futó számítási feladatok, amely a szabványos folyamatokat, amelyek támogatják a pod kapcsolódnak.
* Ismerje meg a fürt átlagos és a leggyakrabban használt terhelések viselkedését. Ennek segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést, amely képes elviselni a fürt. 
* Proaktív módon Önt, és jegyezze fel, amikor a CPU és memória kihasználtságáról, a csomópontok és a tárolók túllépi a küszöbértékek értesítéseinek beállítása.  

## <a name="how-do-i-access-this-feature"></a>Hogyan érhetem el ezt a szolgáltatást?
Az Azure Monitor-tárolókhoz az Azure Monitor vagy közvetlenül a kijelölt AKS-fürt két módon érheti el. Az Azure Monitor, az összes, a tárolók üzembe helyezett, amely monitorozza és melyek nem, amely lehetővé teszi a Keresés és szűrés az előfizetések és erőforráscsoportok, és a tárolók az Azure Monitor ezután részletes egy globális perspektíva rendelkezik a kiválasztott tároló.  Ellenkező esetben a funkció eléréséhez közvetlenül a kijelölt AKS tároló az AKS oldalról.  

![Hozzáférés az Azure Monitor-tárolók módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha érdekli, figyelése és kezelése a Docker és a Windows a tárológazdagép konfiguráció megtekintése, naplózási és erőforrás-használatot, tekintse meg a [Tárolómonitorozási megoldás](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>További lépések
Az AKS-fürt a figyelés megkezdése előtt tekintse át a [engedélyezése az Azure Monitor-tárolókhoz](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek.  
