---
title: "Az Azure AD Connect Synchronization Service Managert műveletek |} Microsoft Docs"
description: "Ismerje meg a műveletek fülre a Synchronization Service Managert, az Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1475e4fcd11eb008badba49665f4af6029a1697
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-sync-service-manager-operations-tab"></a>A Service Manager szinkronizálási műveletek lapján

![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

A műveletek lapon a legutóbbi művelet eredményei láthatók. Ezen a lapon az kulcs megértéséhez, valamint a problémák elhárításához.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>A műveletek lapon látható információk ismertetése
Felső részén látható minden futtatásakor időrendi sorrendben. Alapértelmezés szerint a műveletek az elmúlt hét napban tartja információinak naplózásához, de ez a beállítás használatával módosítható a [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md). Bármely futtatása, amelyek nem szerepelnek egy sikeres állapotnak keresni szeretne. Rendezés a fejlécek kattintva módosíthatja.

A **állapot** oszlop a legfontosabb adatokat, és a legsúlyosabb károkat okozó problémát futtató jeleníti meg. A leggyakoribb állapotokról a prioritásuk szerinti sorrendben kell vizsgálni gyors összegzése (ahol * több lehetséges hiba karakterláncok jelzi).

| status | Megjegyzés |
| --- | --- |
| leállított-* |A Futtatás nem sikerült befejezni. Ha például a távoli rendszer nem működik, és nem érhető el. |
| leállt hiba-korlát |Több mint 5000 hibák vannak. A Futtatás automatikusan hibák nagy száma miatt le lett állítva. |
| Befejezett -\*-hibák |A Futtatás befejeződött, de hibák (kevesebb mint 5000) meg kell vizsgálni. |
| Befejezett -\*-figyelmeztetések |A Futtatás befejeződött, de bizonyos adatok nem várt állapotban van. Ha hibákat, majd ezt az üzenetet az általában csak az egyik oka. Mindaddig, amíg meg kell oldani hibák, figyelmeztetések nem ki kell vizsgálni. |
| sikeres |Nincs probléma. |

Amikor kiválaszt egy sorra, alsó frissítései futtató részleteinek megjelenítése. A bal alsó a, lehetséges, hogy egy lista bármelyiket **lépés #**. Ez a lista csak akkor jelenik meg, ha több tartomány az erdőben, ahol minden egyes tartományhoz egy lépés képviseli. A tartománynév címszó alatt található **partíció**. A **szinkronizálási statisztika**, található további információ a feldolgozott módosítások számát. Kattintson az alábbi hivatkozásokat a módosított objektumok listájának beolvasása. Ha hibákkal objektummal rendelkezik, azokat a hibákat az Eseménynaplón **szinkronizálási hibák**.

További információkért lásd: [olyan objektum, amely nem szinkronizál hibaelhárítása](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Következő lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
