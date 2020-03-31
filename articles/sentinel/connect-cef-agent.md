---
title: Az ügynök üzembe helyezése a CEF-adatok Azure Sentinel Előzetes verzióhoz való csatlakoztatásához| Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az ügynököt a CEF-adatok Azure Sentinelhez való csatlakoztatásához.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588467"
---
# <a name="step-1-deploy-the-agent"></a>1. lépés: Az ügynök telepítése


Ebben a lépésben ki kell választania azt a Linux-gépet, amely proxyként fog működni az Azure Sentinel és a biztonsági megoldás között. Meg kell futtatni a parancsfájlt a proxy gépen, amely:
- Telepíti a Log Analytics-ügynököt, és szükség szerint konfigurálja a Syslog-üzenetek figyeléséhez.
- Úgy állítja be a Syslog démont, hogy az 514-es TCP-porton keresztül meghallgassa a Syslog-üzeneteket, majd csak a CEF-üzeneteket továbbítja a Log Analytics-ügynöknek a 25226-os TCP-port használatával.
- Beállítja a Syslog-ügynököt az adatok gyűjtésére és biztonságos elküldésére az Azure Sentinelbe, ahol elemzik és gazdagítják.
 
## <a name="deploy-the-agent"></a>Az ügynök telepítése
 
1. Az Azure Sentinel portálon kattintson az **Adatösszekötők** elemre, és válassza a **Közös eseményformátum (CEF)** lehetőséget, majd **nyissa meg az összekötő lapot.** 

1. A **Syslog ügynök telepítése és konfigurálása**csoportban válassza ki a géptípusát, az Azure-t, a más felhőt vagy a helyszíni beállításokat. 
   > [!NOTE]
   > Mivel a parancsfájl a következő lépésben telepíti a Log Analytics-ügynököt, és csatlakoztatja a gépet az Azure Sentinel-munkaterülethez, győződjön meg arról, hogy a gép nem csatlakozik más munkaterülethez.
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a számítógépen. Győződjön meg arról, hogy a Python a számítógépen a következő paranccsal rendelkezik:`python –version`

1. Futtassa a következő parancsfájlt a proxyszámítógépen.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. A parancsfájl futása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

Tovább a [2.](connect-cef-solution-config.md)


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a CEF-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)

