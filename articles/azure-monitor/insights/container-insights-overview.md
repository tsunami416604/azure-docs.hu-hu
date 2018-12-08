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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: f46d6f9a8ed17d9ce7b6dd3ff1e797306dd48bf9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101925"
---
# <a name="azure-monitor-for-containers-overview"></a>Az Azure Monitor tárolók áttekintése

A tárolók az Azure Monitor egy olyan szolgáltatás, vagy az Azure Container Instances szolgáltatásban, vagy a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök üzembe tárolóalapú számítási feladatok teljesítményének figyelésére tervezett. A tárolók alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelése, ezek a metrikák és naplók automatikusan az Ön számára a Linuxhoz készült Log Analytics-ügynököt egy tárolóalapú verziója gyűjtött és tárolt a [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) munkaterületen. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Az Azure Monitor-tárolók biztosítják a funkciója?

A tárolók az Azure Monitor számos előre meghatározott nézeteket, amelyek bemutatják az adataihoz, tárolókhoz kapcsolódó számítási feladatot, és milyen hatással van a figyelt Kubernetes-fürt teljesítménybeli állapotát, így is tartalmazza:  

* AKS-tárolók, a csomópont és a processzor és memória átlagos kihasználtság futó azonosításához. A Tudásbázis segítségével erőforrás szűk keresztmetszetek azonosítása.
* Azonosítsa a tárolócsoportok és a tárolók az Azure Container Instances szolgáltatásban üzemeltetett processzor és memória felhasználását.  * Azonosítása, ahol a tároló található a vezérlő vagy podot. A Tudásbázis segítségével megtekintheti a vezérlő vagy a pod általános teljesítménye.
* Tekintse át az erőforrás-használat, a gazdagépen futó számítási feladatok, amely a szabványos folyamatokat, amelyek támogatják a pod kapcsolódnak.
* Ismerje meg a fürt átlagos és a leggyakrabban használt terhelések viselkedését. Ennek segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést, amely képes elviselni a fürt. 

## <a name="how-do-i-access-this-feature"></a>Hogyan érhetem el ezt a szolgáltatást?
Az Azure Monitor-tárolókhoz az Azure Monitor vagy közvetlenül a kijelölt AKS-fürt két módon érheti el. Az Azure Monitor rendelkezik az összes tárolót egy globális szempontjából telepített, amely monitorozza, és amelyek nincsenek, amely lehetővé teszi a Keresés és szűrés az előfizetések és erőforráscsoportok, és a kiválasztott tárolók az Azure Monitor ezután részletes a tároló.  Ellenkező esetben egyszerűen a funkció eléréséhez közvetlenül a kijelölt AKS tároló az AKS oldalról.  

![Hozzáférés az Azure Monitor-tárolók módszereinek áttekintése](./media/container-insights-overview/azmon-containers-views-1812.png)

Ha érdekli, figyelése és kezelése a Docker és a Windows a tárológazdagép konfiguráció megtekintése, naplózási és erőforrás-használatot, tekintse meg a [Tárolómonitorozási megoldás](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>További lépések
Az AKS-fürt a figyelés megkezdése előtt tekintse át a [hogyan üzembe helyezni az Azure figyeli, hogy tárolók](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek.  
