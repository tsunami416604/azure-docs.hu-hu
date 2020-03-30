---
title: Az Azure Sentinelhez való kapcsolódás ellenőrzése| Microsoft dokumentumok
description: Ellenőrizze a biztonsági megoldás kapcsolatát, és győződjön meg arról, hogy a CEF-üzenetek továbbítása folyamatban van az Azure Sentinel számára.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588433"
---
# <a name="step-3-validate-connectivity"></a>3. LÉPÉS: A kapcsolat ellenőrzése



Miután üzembe helyezte az ügynököt, és konfigurálta a biztonsági megoldást a CEF-üzenetek továbbítására, ebben a cikkben megtudhatja, hogyan ellenőrizheti az Azure Sentinel és a biztonsági megoldás közötti kapcsolatot. 

## <a name="how-to-validate-connectivity"></a>A kapcsolat ellenőrzése

1. Nyissa meg a Log Analytics szolgáltatást, és győződjön meg arról, hogy a naplók a CommonSecurityLog séma használatával érkeznek.<br> A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 

1. A parancsfájl futtatása előtt azt javasoljuk, hogy küldjön üzeneteket a biztonsági megoldás, győződjön meg arról, hogy azok a Syslog proxygép konfigurált. 
1. Emelt szintű engedélyekkel (sudo) kell rendelkeznie a számítógépen. Győződjön meg arról, hogy a Python a számítógépen a következő paranccsal rendelkezik:`python –version`
1. Futtassa a következő parancsfájlt az ügynök, az Azure Sentinel és a biztonsági megoldás közötti kapcsolat ellenőrzéséhez. Ellenőrzi, hogy a démontovábbítás megfelelően van-e konfigurálva, figyeli a megfelelő portokat, és hogy semmi sem akadályozza a démon és a Log Analytics-ügynök közötti kommunikációt. A parancsfájl "TestCommonEventFormat" álüzeneteket is küld a végpontok közötti kapcsolat ellenőrzéséhez. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a CEF-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

