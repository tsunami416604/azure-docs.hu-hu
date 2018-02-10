---
title: "Azure AD tartományi szolgáltatások - ellenőrizze, a felügyelt tartományok állapotát |} Microsoft Docs"
description: "Ellenőrizze, a felügyelt tartományra az állapot lapon az Azure portálon állapotát."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: ergreenl
ms.openlocfilehash: 4adbce0305bdc1a9b261f5cf644fad876d101bc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-ad-domain-services---check-the-health-of-your-managed-domain"></a>Azure AD tartományi szolgáltatások - ellenőrizze, a felügyelt tartományok állapotát

## <a name="your-domains-health"></a>A tartomány állapota

Az állapot lapon használja az Azure-portál, le is tudja meg, mi történik a felügyelt tartományra naprakész állapotban tarthatja az. Ez a cikk lépéseit a rendszerállapot az elemek lapon, és győződjön meg arról, hogy a tartományhoz való tip-top alakzat van.

### <a name="view-your-health-page"></a>A health lap megtekintése

1. Az Azure AD tartományi szolgáltatások oldalra léphet a [Azure-portálon](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)
2. Kattintson a tartomány meg szeretné tekinteni az állapotát.
3. Kattintson a bal oldali navigációs "Állapot".

Az alábbi képen egy példa health lap.

![Példa health lap](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> A tartomány állapota ki lesz értékelve óránként körül. A felügyelt tartományra módosítások elvégzése után meg kell várnia, amíg a tartomány megtekintéséhez a következő értékelési ciklus frissítették állapotát. Ha a tartomány legutóbbi értékelésének jobb felső sarokban található "Utolsó kiértékelt" időbélyegének használatával ellenőrizheti.
>

### <a name="status-of-your-managed-domain"></a>A felügyelt tartományok állapota

Az állapot felső sarkában található a health azt jelzi, a tartomány általános állapotát. Az állapot tényezők a meglévő riasztások a tartomány összes. Az Azure AD tartományi szolgáltatások áttekintése lapon a tartomány állapotát is megtekintheti.

Egy felügyelt tartományba állapotok:

| status | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | A felügyelt tartományok zökkenőmentesen fut, és nem rendelkezik minden kritikus vagy figyelmeztetési riasztást. Előfordulhat, hogy a tartomány információs riasztások léptek fel. |
| Figyelmet (figyelmeztetés) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | A felügyelt tartományok kritikus riasztás vannak, de egy vagy több figyelmeztető riasztások léptek fel, amelyek kell figyelembe venni. |
| Figyelmet (kritikus) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | A felügyelt tartományok nincsenek legalább egy kritikus riasztások. Figyelmeztető vagy tájékoztató riasztások is rendelkezhetnek. |
| Központi telepítés | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | A tartomány telepítése folyamatban van. |

## <a name="monitors"></a>Figyelők

Figyelők bizonyos elemeinek Azure AD tartományi szolgáltatások felügyeli, a felügyelt tartományban kapcsolatos adatok találhatók. A legjobb tartsa a figyelő állapota kifogástalan módja a felügyelt tartomány bármely riasztás feloldása.

A rendelkezésre álló figyelők vannak:
 - Biztonsági mentés
 - Az AAD-szinkronizálás

### <a name="backup"></a>Biztonsági mentés

Ez figyeli, hogy milyen gyakran vesszük a felügyelt tartományok biztonsági másolatait. Az alábbiakban látható egy táblát, amely azt ismerteti, a részletek oszlopban, a biztonsági mentési figyelő, és milyen értékeket kell számítani.

| Részletértéket | Magyarázat |
| --- | --- |
|"Soha nem készített biztonsági" | Ebben az állapotban nem jelent problémát egy újonnan létrehozott tartomány. Az első biztonsági mentés általában 24 óra múlva jön létre. Ha a felügyelt tartományok nem újonnan létrehozott vagy rendellenes mennyi idő, ebben az állapotban vannak [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md). |
| Utolsó biztonsági mentés készült 1-14 napja | Ez általában a biztonsági mentési figyelő a várt értékkel. |
| Utolsó biztonsági mentés készült több mint 14 napja. | Bármely két héttel hosszabb ideje szokatlanul hosszú ideig marad az utolsó biztonsági mentés óta. Első, javítsa ki az riasztások jelennek meg a felügyelt tartományra, majd ha a probléma továbbra is fennáll, [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md). |


### <a name="synchronization-with-azure-ad"></a>Az Azure AD-szinkronizálás

Microsoft nyomon követi, hogy milyen gyakran a felügyelt tartományok szinkronizálva az Azure Active Directoryban. A felhasználók a felügyelt tartományok, valamint a legutóbbi szinkronizálás is érinthetik mennyi ideig tarthat egy szinkronizálási időszak óta végrehajtott módosítások mennyisége száma. Általánosságban elmondható, ha a legutóbbi szinkronizálás óta három napnál hosszabb lett, javasoljuk, hogy [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Riasztások

A riasztások jelezhetik a felügyelt tartományok, amelyek kell figyelembe venni ahhoz, hogy Azure AD tartományi szolgáltatások futtatásához a problémák. Minden riasztás bemutatja a problémát, valamint egy URL-címet, a probléma megoldásának lépéseit ismerteti. Az összes riasztás és azok megoldásait megtekintéséhez keresse fel a [-riasztások hibaelhárítását végre](active-directory-ds-troubleshoot-alerts.md) cikk.

### <a name="severity"></a>Súlyosság
Riasztások súlyossági három különböző szintjei vannak kategóriákba: kritikus, figyelmeztető és tájékoztató.

 * **A kritikus riasztások** hátrányosan befolyásolhatja a felügyelt tartományok problémákkal. Ezek a riasztások azonnal foglalkozzon, Microsoft nem figyeléséhez, kezeléséhez, javítás, és szinkronizálja a felügyelt tartományok.
 * **Figyelmeztető riasztások léptek fel** problémákat, amelyek hatással lehet a jövőben a tartományhoz, de nem feltétlenül "bontja" a szolgáltatás lehet. Ezek a riasztások szerkezeti ajánlott eljárásokat, és adjon a felügyelt tartományok védelmére vonatkozó javaslatokat.
 * **Információs riasztások léptek fel** értesítések, amelyek nem negatív mely negatív hatással a tartományban vannak. Mi történik a tartományban és az Azure AD tartományi szolgáltatások ismerő nyomon információs riasztások léptek fel lettek kialakítva.

## <a name="next-steps"></a>További lépések
- [Hárítsa el a felügyelt tartomány riasztást](active-directory-ds-troubleshoot-alerts.md)
- [További tudnivalók az Azure AD tartományi szolgáltatások](active-directory-ds-features.md)
- [Kapcsolatfelvétel](active-directory-ds-contact-us.md)
