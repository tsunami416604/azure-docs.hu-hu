---
title: Az Azure Sentinel kapcsolatának ellenőrzése | Microsoft Docs
description: Ellenőrizze a biztonsági megoldás kapcsolatát, és győződjön meg arról, hogy a CEF üzeneteket továbbítják az Azure Sentinelnek.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588433"
---
# <a name="step-3-validate-connectivity"></a>3\. lépés: a kapcsolat ellenőrzése



Miután telepítette az ügynököt, és konfigurálta a biztonsági megoldást a CEF üzenetek továbbítására, ebből a cikkből megtudhatja, hogyan ellenőrizheti az Azure Sentinel és a biztonsági megoldás közötti kapcsolatot. 

## <a name="how-to-validate-connectivity"></a>A kapcsolat érvényesítése

1. A Log Analytics megnyitásával győződjön meg arról, hogy a naplók a CommonSecurityLog séma használatával érkeznek.<br> Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

1. A szkript futtatása előtt javasoljuk, hogy küldjön üzeneteket a biztonsági megoldásból, hogy azok továbbítva legyenek a konfigurált syslog-proxy gépre. 
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. A következő paranccsal ellenőrizze, hogy rendelkezik-e a Python használatával a gépen: `python –version`
1. Futtassa a következő szkriptet az ügynök, az Azure Sentinel és a biztonsági megoldás közötti kapcsolat vizsgálatához. Ellenőrzi, hogy a démon továbbítása megfelelően van-e konfigurálva, figyeli a megfelelő portokat, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. A parancsfájl a "TestCommonEventFormat" nevű, a végpontok közötti kapcsolat vizsgálatához is elküldi a "" üzenetet. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

