---
title: Az Entity Behavior Analytics használata a speciális fenyegetések észleléséhez | Microsoft Docs
description: A felhasználó és az entitás viselkedés-elemzésének engedélyezése az Azure Sentinelben és az adatforrások konfigurálása
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995492"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>A felhasználó és az entitás viselkedés-elemzésének (UEBA) engedélyezése az Azure Sentinelben 



## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak hozzá kell rendelnie a **globális rendszergazdai** vagy **biztonsági rendszergazdai** szerepköröket az Azure ad-ben ahhoz, hogy engedélyezze vagy tiltsa le a UEBA, de ne futtassa.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>A felhasználó és az entitás viselkedés-elemzésének engedélyezése

1. Az Azure Sentinel navigációs menüjében válassza az **entitás viselkedése (előzetes verzió)** lehetőséget.

1. A címsor alatt **kapcsolja**be a kapcsolót a **be**értékre.

1. Kattintson az **adatforrások kiválasztása** gombra.

1. Az **adatforrás kiválasztása** ablaktáblán jelölje be azon adatforrások melletti jelölőnégyzeteket, amelyeken engedélyezni szeretné a UEBA, majd válassza az **alkalmaz**lehetőséget.

    > [!NOTE]
    >
    > Az **adatforrás kiválasztása** ablaktábla alsó felében megjelenik azon UEBA által támogatott adatforrások listája, amelyek még nincsenek engedélyezve. 
    >
    > Ha engedélyezte a UEBA, az új adatforrások csatlakoztatásakor lehetősége lesz arra, hogy a UEBA közvetlenül az adatösszekötő ablaktábláról engedélyezze őket, ha a UEBA-kompatibilisek.

1. Válassza **az Ugrás az entitás keresése**lehetőséget. Ekkor megjelenik az entitás keresési panelje, amelyről mostantól az látható, ha az **entitás viselkedését** választja a fő Azure Sentinel menüben.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan engedélyezheti és konfigurálhatja a felhasználói és entitások viselkedésének elemzését (UEBA) az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
