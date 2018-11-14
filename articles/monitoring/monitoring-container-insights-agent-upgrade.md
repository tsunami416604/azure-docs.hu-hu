---
title: Tárolók (előzetes verzió) ügynök frissítése a Azure Monitor |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használja az Azure Monitor tárolók Log Analytics-ügynököket frissít.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629058"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Az Azure Monitor tárolók (előzetes verzió) ügynök frissítése
Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Az ügynök egy új verziója jelent meg, ha az ügynök van nem automatikusan frissítve a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes fürtökön.

Ez a cikk azt ismerteti, hogyan frissíteni az ügynököt.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Felügyelt Kubernetes-fürtön az ügynök frissítése
Az ügynök frissítési folyamata két egyszerű lépésből áll. Az első lépés, hogy tiltsa le az Azure CLI használatával az Azure Monitor szolgáltatással figyelést.  Az ismertetett lépéseket követve a [letiltani a figyelést](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) cikk. Azure CLI használatával lehetővé teszi számunkra, hogy az ügynök eltávolítása a fürt csomópontjainak a megoldás és a vonatkozó adatok a munkaterületen tárolt befolyásolása nélkül. 

>[!NOTE]
>Ezt a karbantartási tevékenységet hajt végre, gyűjtött adatokat nem továbbítja a fürt csomópontjainak, majd azt teljesítménynézetet nem jelenik meg az adatok közötti idő eltávolíthatja az ügynököt, és az új verzió telepítése. 
>

Az ügynök az új verzió telepítéséhez kövesse a leírt lépéseket követve a [előkészítése figyelési](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) című cikket, a folyamat befejezéséhez az Azure CLI használatával.  

Miután a figyelés újbóli engedélyezését, körülbelül 15 percet, mielőtt a fürt frissített mérőszámok megtekintéséhez vehet igénybe. 

## <a name="next-steps"></a>További lépések
Ha az ügynök frissítése közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](monitoring-container-insights-troubleshoot.md) támogatást.