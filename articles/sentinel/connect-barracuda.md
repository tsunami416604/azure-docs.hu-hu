---
title: Csatlakozás Azure-on Előzetesben Sentinel-Barracuda adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Barracuda adatokat az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8e92fd3918230b48449926dcbb7528d919fd96f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492838"
---
# <a name="connect-your-barracuda-appliance"></a>A Barracuda berendezés csatlakoztatása 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Barracuda webalkalmazási tűzfal (WAF) összekötő segítségével kapcsolódhat egyszerűen azokhoz a Barracuda naplók az Azure Sentinel, megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat, és a vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket. Az Azure Sentinel közötti natív integrációnak kihasználja **Barracuda** és a Microsoft Azure OMS való zökkenőmentes integrációt biztosítanak. 


> [!NOTE]
> Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="configure-and-connect-barracuda-waf"></a>Beállítása és csatlakozása a Barracuda WAF
Barracuda Web Application Firewall integráció és naplók exportálása közvetlenül az Azure Sentinel-Azure OMS-kiszolgálón keresztül.
1. Lépjen a [Barracuda WAF a konfigurációs folyamat](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), és kövesse az utasításokat a kapcsolatot, ezek a paraméterek használatával:
    - **Munkaterület-Azonosítót**: másolja ki a munkaterület-azonosító értékét az Azure-Sentinel-Barracuda összekötő oldalról.
    - **Elsődleges kulcs**: másolja az elsődleges kulcs értékét az Azure-Sentinel-Barracuda összekötő oldalról.
2. Sentinel-az Azure Portalon nyissa meg a, amelyen üzembe helyezett Azure Sentinel-munkaterületet, és válassza ki a végén a sort, majd válassza a három pontra (...) **speciális beállítások**. 
1. Válassza ki **adatok** , majd **Syslog**.
1. Győződjön meg arról, hogy a létesítmény Barracuda beállított létezik, és állítsa be a súlyosság, majd kattintson az **mentése**.
6. A Barracuda események Log Analytics használja a megfelelő sémát, keresse meg **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Barracuda berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

