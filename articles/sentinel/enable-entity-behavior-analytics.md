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
ms.date: 10/28/2020
ms.author: yelevin
ms.openlocfilehash: 140228a65be166bc172e81267c4449b49621e02c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425779"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>A felhasználó és az entitás viselkedés-elemzésének (UEBA) engedélyezése az Azure Sentinelben 

> [!IMPORTANT]
>
> - A UEBA-és entitás-lapok szolgáltatásai mostantól **általánosan elérhetők** a következő Azure Sentinel földrajzi területeken és régiókban:
>    - Földrajz Egyesült Államok
>    - Nyugat-Európa régiója
>    - Ausztráliai földrajzi hely
>
> - Az összes többi földrajzi és régió esetében ezek a funkciók a jelenleg **előzetes** verzióban maradnak. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="prerequisites"></a>Előfeltételek

A funkció engedélyezéséhez vagy letiltásához (ezek az előfeltételek nem szükségesek a funkció használatához):

- A felhasználónak a szervezet Azure Active Directory tagjának kell lennie, és nem vendég felhasználó.

- A felhasználónak hozzá kell rendelnie a **globális rendszergazdai** vagy **biztonsági rendszergazdai** szerepköröket az Azure ad-ben.

- A felhasználónak a következő **Azure-szerepkörök** közül legalább egyet hozzá kell rendelnie (További információ az [Azure RBAC-ről](roles.md)):
    - Az **Azure Sentinel közreműködő** a munkaterület vagy az erőforráscsoport szintjén.
    - **Log Analytics közreműködő** az erőforráscsoport vagy az előfizetés szintjén.

- A munkaterület nem rendelkezhet az Azure-beli erőforrás-zárolások alkalmazásával. [További információ az Azure-erőforrások zárolásáról](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>A felhasználó és az entitás viselkedés-elemzésének engedélyezése

1. Az Azure Sentinel navigációs menüjében válassza az **entitás viselkedése** elemet.

1. A címsor alatt **kapcsolja** be a kapcsolót a **be** értékre.

1. Kattintson az **adatforrások kiválasztása** gombra.

1. Az **adatforrás kiválasztása** ablaktáblán jelölje be azon adatforrások melletti jelölőnégyzeteket, amelyeken engedélyezni szeretné a UEBA, majd válassza az **alkalmaz** lehetőséget.

    > [!NOTE]
    >
    > Az **adatforrás kiválasztása** ablaktábla alsó felében megjelenik azon UEBA által támogatott adatforrások listája, amelyek még nincsenek engedélyezve. 
    >
    > Ha engedélyezte a UEBA, az új adatforrások csatlakoztatásakor lehetősége lesz arra, hogy a UEBA közvetlenül az adatösszekötő ablaktábláról engedélyezze őket, ha a UEBA-kompatibilisek.

1. Válassza **az Ugrás az entitás keresése** lehetőséget. Ekkor megjelenik az entitás keresési panelje, amelyről mostantól az látható, ha az **entitás viselkedését** választja a fő Azure Sentinel menüben.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan engedélyezheti és konfigurálhatja a felhasználói és entitások viselkedésének elemzését (UEBA) az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
