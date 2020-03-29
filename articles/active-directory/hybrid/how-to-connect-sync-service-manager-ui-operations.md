---
title: Az Azure AD Connect szinkronizálási szolgáltatáskezelő műveletei | Microsoft dokumentumok
description: Ismerje meg az Azure AD Connect szinkronizálási szolgáltatáskezelőjének Műveletek lapját.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381407"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>A Szolgáltatáskezelő szinkronizálása műveletek lap használata

![Szolgáltatáskezelő szinkronizálása](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

A műveletek lapon a legutóbbi műveletek eredményei láthatók. Ez a lap kulcsfontosságú a problémák megértéséhez és elhárításához.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>A Műveletek lapon látható információk ismertetése
A felső fele időrendi sorrendben mutatja az összes futást. Alapértelmezés szerint a műveleti napló információkat tartalmaz az elmúlt hét napról, de ez a beállítás módosítható az [ütemezővel.](how-to-connect-sync-feature-scheduler.md) Olyan futtatást szeretne keresni, amely nem mutat sikeres állapotot. A rendezést a fejlécek kattintásával módosíthatja.

Az **Állapot** oszlop a legfontosabb információ, és a futtatás legsúlyosabb problémáját mutatja. Itt van egy gyors összefoglaló a leggyakoribb állapotok prioritási sorrendben, hogy vizsgálja meg (ahol * jelzi több lehetséges hiba karakterláncok).

| status | Megjegyzés |
| --- | --- |
| megállt-\* |A futtatás nem fejeződött be. Ha például a távoli rendszer nem áll meg, és nem lehet kapcsolatba lépni vele. |
| leállított-hiba-limit |Több mint 5000 hiba van. A futtatás a nagy számú hiba miatt automatikusan leállt. |
| befejezett-\*-hibák |A futtatás befejeződött, de vannak hibák (kevesebb, mint 5000), amelyeket meg kell vizsgálni. |
| befejeződött-\*-figyelmeztetések |A futtatás befejeződött, de néhány adat nem a várt állapotban van. Ha hibákat észlel, akkor ez az üzenet általában csak egy tünet. Amíg nem hárította el a hibákat, ne vizsgálja meg a figyelmeztetéseket. |
| sikeres |Semmi probléma. |

Amikor kijelöl egy sort, az alsó frissítés az adott futtatás részleteinek megjelenítéséhez frissül. Az alsó sarokban, lehet, hogy van egy lista mondván **Lépés #**. Ez a lista csak akkor jelenik meg, ha az erdőben több olyan tartomány van, ahol minden tartományt egy lépés képvisel. A tartománynév a **Partition**cím alatt található. A **Szinkronizálási statisztika csoportban**további információt talál a feldolgozott módosítások számáról. A hivatkozásokra kattintva lehívhatja a módosított objektumok listáját. Ha hibás objektumokat észlel, ezek a hibák a **Szinkronizálási hibák csoportban**jelennek meg.

További információt a [nem szinkronizált objektumok hibaelhárítása című](tshoot-connect-object-not-syncing.md) témakörben talál.

## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
