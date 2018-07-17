---
title: Tárolási lehetőségek az Azure Service Fabric-háló állapot |} A Microsoft Docs
description: Ismerje meg az állapot megbízhatóan tárolja az Azure Service Fabric-háló futó Service Fabric-háló alkalmazások.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076460"
---
# <a name="state-management-with-service-fabric"></a>A Service Fabric állapotkezelés
A Service Fabric számos különböző lehetőség támogatja a állapot tárolásához. Az állapot minták és a Service Fabric fogalmi áttekintése: [Service Fabric fogalmak: állapot](/azure/service-fabric/service-fabric-concepts-state). Az összes ezek ugyanezek a fogalmak alkalmazhatók-e a szolgáltatások futnak, belül vagy kívül a Service Fabric-háló. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Az Azure Service Fabric-háló állapot tárolási lehetőségek
Service Fabric tervezhetők is egyszerűen üzembe helyezni egy új alkalmazást és csatlakoztathatja azt egy Azure-ban üzemeltetett meglévő adattár. Amellett, hogy minden olyan távoli adatbázis használata esetén többféle módon, attól függően, hogy a szolgáltatás kívánja-e a helyi vagy távoli tároló adatainak tárolásához. 

* Helyileg tárolt replikált adatok
  * A Reliable Collections (nem érhető el előzetes verzióban érhető el)
    * Egy könyvtár, amely datové struktury,-üzenetsorok és -kulcs-érték párok használatához hasonlóan a szolgáltatásban
    * Ez biztosítja, hogy a legkönnyebb és leggyorsabb módja az adatok, miközben egyszerű partíción útválasztás együtt az intelligens útválasztási a Service Fabric-háló kezeléséhez
  * A Service Fabric kötet illesztőprogram (nem érhető el előzetes verzióban érhető el)
    * A docker kötet illesztőprogram egy tárolóba helyi kötet csatlakoztatása
    * Ez a végső rugalmasságot biztosít az adattárolási helyileg, bármilyen API-val, amely támogatja a file storage.

* Távtároló
  * Az Azure Files kötet illesztőprogram
    * A docker kötet illesztőprogram csatlakoztatása egy Azure-fájlmegosztási egy tárolóba
    * A kevesebb nagy teljesítményű, de olcsóbb teljes is rugalmas és megbízható beállítás minden API-val, amely támogatja a file storage biztosítja.
    * [Útmutató: alkalmazás üzembe helyezése az Azure Files-kötet](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>További lépések

Az alkalmazásmodell kapcsolatos tudnivalókat lásd: [Service Fabric-erőforrások](service-fabric-mesh-service-fabric-resources.md)
