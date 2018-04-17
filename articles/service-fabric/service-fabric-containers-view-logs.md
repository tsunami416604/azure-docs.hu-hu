---
title: Tárolók naplók megtekintése az Azure Service Fabric |} Microsoft Docs
description: Ismerteti a Service Fabric Explorerrel futó Service Fabric tároló Services tároló naplók megtekintéséhez.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>A Service Fabric-tároló szolgáltatás naplók megtekintése
Az Azure Service Fabric egy tároló orchestrator és egyaránt támogat [Linux-és Windows](service-fabric-containers-overview.md).  Ez a cikk ismerteti a tárolóban futó szolgáltatás tároló-naplók megtekintése, hogy diagnosztizálása és elhárítása.

## <a name="access-container-logs"></a>Hozzáférés tároló naplók
Tároló naplók segítségével férhetők el [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Egy webböngészőben nyissa meg a Service Fabric Explorerben talál a fürt felügyeleti végpont a navigáljon [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Tároló naplók az, hogy a tároló szolgáltatáspéldány fut csomóponton találhatók. Tegyük fel, a naplókat web előtér-tárolójának beolvasása a [mintaalkalmazás Linux szavazás](service-fabric-quickstart-containers-linux.md). Bontsa ki a fa nézetben **fürt**>**alkalmazások**>**VotingType**>**fabric: / Voting / azurevotefront**.  Ezután bontsa ki a partíció (d1aa737e-f22a-e347-be16-eec90be24bc1, ebben a példában), és a fürt csomópontján fut-e a tároló *_lnxvm_0*.

A fa nézetben található a kódcsomag a *_lnxvm_0* csomópont kibontásával **csomópontok**>**_lnxvm_0**>**fabric: / szavazás**  > **azurevotfrontPkg**>**kód csomagok**>**kód**.  Válassza ki a **tároló naplók** lehetőséget a tároló naplók megjelenítéséhez.

![Service Fabric platform][Image1]


## <a name="next-steps"></a>További lépések
- Munka – a [hozzon létre egy Linux tároló oktatóanyag](service-fabric-tutorial-create-container-images.md).
- További információ [Service Fabric és a tárolók](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
