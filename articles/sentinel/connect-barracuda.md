---
title: Barracuda-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Barracuda-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588484"
---
# <a name="connect-your-barracuda-appliance"></a>Csatlakoztassa a Barracuda készüléket 



A Barracuda webalkalmazás-tűzfal (WAF) összekötő lehetővé teszi a Barracuda-naplók és az Azure Sentinel egyszerű csatlakoztatását az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. Ez több betekintést nyújt a szervezet hálózatába, és javítja a biztonsági műveleti képességeket. Az Azure Sentinel kihasználja a **Barracuda** és a Log Analytics-ügynök közötti natív integráció előnyeit a zökkenőmentes integráció érdekében. 


> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-barracuda-waf"></a>A Barracuda WAF konfigurálása és csatlakoztatása
A Barracuda webalkalmazás-tűzfal közvetlenül integrálhatja és exportálhatja a naplókat az Azure Sentinelbe a Log Analytics-ügynökön keresztül.
1. Lépjen a [Barracuda WAF konfigurációs folyamatra,](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)és kövesse a kapcsolat beállításához az alábbi paramétereket:
    - **Munkaterület-azonosító:** másolja a munkaterület-azonosító értékét az Azure Sentinel Barracuda összekötő lapról.
    - **Elsődleges kulcs:** másolja az elsődleges kulcs értékét az Azure Sentinel Barracuda összekötő lapról.
1. Ha a Megfelelő sémát szeretné használni a Log Analytics szolgáltatásban a Barracuda-eseményekhez, keresse meg a **CommonSecurityLog** és **a barracuda_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Barracuda-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


