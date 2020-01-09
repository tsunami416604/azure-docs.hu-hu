---
title: Az ügynök üzembe helyezése a CEF-adatkapcsolat Azure Sentinel Preview-hoz való összekapcsolásához | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe az ügynököt a CEF-adatkapcsolat Azure Sentinelhez való összekapcsolásához.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5451382763195172b48e93ef2ea859552b05d154
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615371"
---
# <a name="step-1-deploy-the-agent"></a>1\. lépés: az ügynök üzembe helyezése


Ebben a lépésben ki kell választania azt a Linux-gépet, amely proxyként fog működni az Azure Sentinel és a biztonsági megoldás között. Futtatnia kell egy parancsfájlt a proxy gépen, amely a következőket teszi:
- Telepíti a Log Analytics ügynököt, és szükség szerint konfigurálja a syslog-üzenetek figyeléséhez.
- Úgy konfigurálja a syslog démont, hogy a 514-es TCP-porton keresztül figyelje a syslog-üzeneteket, majd csak a CEF üzeneteket továbbítja a Log Analytics ügynöknek a 25226-es TCP-porton keresztül.
- Beállítja a syslog-ügynököt az adatok gyűjtésére és biztonságos küldésére az Azure Sentinel számára, ahol a rendszer elemzi és gazdagítja az adatokat.
 
## <a name="deploy-the-agent"></a>Az ügynök üzembe helyezése
 
1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **Common Event Format (CEF)** lehetőséget, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök telepítése és konfigurálása**területen válassza ki a gép típusát, akár az Azure-t, akár a másik felhőt, akár a helyszínen. 
   > [!NOTE]
   > Mivel a következő lépésben a parancsfájl telepíti a Log Analytics ügynököt, és csatlakoztatja a gépet az Azure Sentinel-munkaterülethez, győződjön meg róla, hogy ez a számítógép nincs csatlakoztatva más munkaterülethez.
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`

1. Futtassa a következő szkriptet a proxy gépen.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

Folytassa a [2. lépéssel: a biztonsági megoldás konfigurálása a CEF-üzenetek továbbítására](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

