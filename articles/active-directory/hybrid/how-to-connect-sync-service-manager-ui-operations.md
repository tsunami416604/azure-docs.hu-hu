---
title: Az Azure AD Connect szinkronizálási szolgáltatáskezelő műveleti |} A Microsoft Docs
description: Ismerje meg a művelet lapon a Synchronization Service Managert, az Azure AD Connect.
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
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 57d5dd17a180c946043c307e31e1c89e91f1219e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467689"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>A szinkronizálási szolgáltatáskezelő műveleti lapon

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

A műveletek lapon láthatók a legutóbbi operations eredményeinek. Ezen a lapon az kulcs megértéséhez, valamint a hibaelhárítás.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>A művelet lapon látható információk magyarázata
Felső részén látható minden Futtatás időrendi sorrendben. Alapértelmezés szerint a műveletek naplófájlformátumának tartja az elmúlt hét nap, de ez a beállítás is módosítható a [scheduler](how-to-connect-sync-feature-scheduler.md). Kívánt összes futtatás, amelyek nem szerepelnek a sikeres állapotú. A rendezés a fejlécek kattintva módosíthatja.

A **állapot** oszlop a legfontosabb információt, és bemutatja a legsúlyosabb problémát egy Futtatás esetében. Íme a leggyakoribb állapotok a prioritásuk szerinti sorrendben kell vizsgálatára gyors összefoglalását (ahol * több lehetséges hiba karakterláncok jelzi).

| status | Megjegyzés |
| --- | --- |
| leállított-\* |A Futtatás nem sikerült végrehajtani. Ha például a távoli rendszer nem működik, és nem érhető el. |
| stopped-error-limit |Több mint 5000 hibák vannak. A Futtatás hibák nagy száma miatt automatikusan le lett állítva. |
| Befejezett -\*-hibák |A Futtatás befejeződött, de hibák (kevesebb mint 5000) meg kell vizsgálni. |
| Befejezett -\*– figyelmeztetés |A Futtatás befejeződött, de egyes adatokat nem szerepel a várt állapota. Ha hibákat észlel, majd ezt az üzenetet általában a csak a hiba. Hibák elhárítása, amíg nem kell vizsgálni – figyelmeztetések. |
| sikeres |Nincs probléma. |

Amikor kiválaszt egy sort, az alsó frissíti az adott Futtatás részleteinek megjelenítéséhez. A bal szélén az alsó, lehetséges, hogy egy lista üzenettel **lépés #**. Ez a lista csak akkor jelenik meg, ha több tartományt az erdőben, ahol minden egyes tartományhoz egy lépés képviseli. A tartomány nevét a cím alatt található **partíció**. A **szinkronizálási statisztika**, talál további információt a feldolgozott módosítások számát. Kattinthat a hivatkozásokra kattintva a módosított objektumok listája. Ha hibákkal objektummal rendelkezik, ezek a hibák jelennek meg **szinkronizálási hibák**.

További információkért lásd: [nem szinkronizálódó objektumok hibaelhárítása](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
