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
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 96a312630c92048f36f79e3bec18f83ed5a445ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414130"
---
# <a name="azure-monitor-for-containers-overview"></a>Az Azure Monitor tárolók áttekintése

A tárolók az Azure Monitor egy olyan szolgáltatás, vagy az Azure Container Instances szolgáltatásban, vagy a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök üzembe tárolóalapú számítási feladatok teljesítményének figyelésére tervezett. A tárolók alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti a metrikákat és a naplókat a linuxos Log Analytics-ügynök egy tárolós verziójában. A metrikák a metrikák tárolójába íródnak, és a rendszer a napló adatait a [log Analytics](../log-query/log-query-overview.md) munkaterülethez társított naplók tárolójába írja. 

![Azure Monitor a tárolók architektúrához](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Az Azure Monitor-tárolók biztosítják a funkciója?

A Azure Monitor a tárolók számára átfogó figyelési élményt nyújt Azure Monitor különböző funkciói segítségével, így megismerheti a Kubernetes-fürt teljesítményét és állapotát, valamint a tároló munkaterheléseit. A tárolók Azure Monitor a következőket teheti:

* AKS-tárolók, a csomópont és a processzor és memória átlagos kihasználtság futó azonosításához. A Tudásbázis segítségével erőforrás szűk keresztmetszetek azonosítása.
* Azonosítsa a tárolócsoportok és a tárolók az Azure Container Instances szolgáltatásban üzemeltetett processzor és memória felhasználását.  
* Annak meghatározása, hogy a tároló hol található egy vezérlőben vagy egy Pod-ban. A Tudásbázis segítségével megtekintheti a vezérlő vagy a pod általános teljesítménye.
* Tekintse át az erőforrás-használat, a gazdagépen futó számítási feladatok, amely a szabványos folyamatokat, amelyek támogatják a pod kapcsolódnak.
* Ismerje meg a fürt átlagos és a leggyakrabban használt terhelések viselkedését. Ennek segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést, amely képes elviselni a fürt. 
* Riasztásokat állíthat be, amelyekkel proaktívan értesítheti Önt, vagy rögzítheti azt, ha a csomópontokon vagy tárolókban a CPU és a memória kihasználtsága túllépi a küszöbértéket.
* A [Prometheus](https://prometheus.io/docs/introduction/overview/) -nal integrálva megtekintheti a csomópontokból és Kubernetes származó alkalmazások és munkaterhelések mérőszámait, és [lekérdezésekkel](container-insights-log-search.md) egyéni riasztásokat, irányítópultokat és részletes elemzéseket készíthet.

>[!NOTE]
>A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
>

Tekintse meg az alábbi videót, amely egy köztes szintű részletes bemutatót biztosít, amely segít megismerni az AK-fürt Azure Monitor for containers szolgáltatással való figyelését.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hogyan érhetem el ezt a szolgáltatást?

Az Azure Monitor-tárolókhoz az Azure Monitor vagy közvetlenül a kijelölt AKS-fürt két módon érheti el. A Azure Monitor globális perspektívát biztosít az összes üzembe helyezett tárolóról, amelyek figyelése és nem, így lehetővé teszi az előfizetések és az erőforráscsoportok keresését és szűrését, majd a tárolók Azure Monitor való részletezését kiválasztott tároló.  Ellenkező esetben közvetlenül egy kiválasztott AK-tárolóból is elérheti a szolgáltatást az AK-ból származó lapról.  

![Hozzáférés az Azure Monitor-tárolók módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha az AK-n kívül futó Docker-és Windows-tároló gazdagépek figyelését és felügyeletét érdekli, tekintse meg a [tároló-figyelési megoldást](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>További lépések

Az AK-fürt figyelésének megkezdéséhez tekintse át az [Azure monitor a tárolók](container-insights-onboard.md) számára című témakört, és Ismerje meg a figyelés engedélyezésének követelményeit és rendelkezésre álló módszereit.  
