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
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: a9421ace7abf1f3d45b1f8cd810067d79faa92ec
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz állapotának ellenőrzése

## <a name="overview-of-the-health-page"></a>A health lap – áttekintés
Az állapot lapon használja az Azure-portál, le is tudja meg, mi történik a felügyelt tartományra naprakész állapotban tarthatja az. Ez a cikk végigvezeti az állapot lapon elemei.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>A felügyelt tartományok állapotának megtekintése
1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure portálon.
2. Kattintson a tartomány meg szeretné tekinteni az állapotát.
3. A bal oldali navigációs ablaktáblán kattintson **állapotfigyelő**.

Az alábbi képen egy minta health oldal bemutatja: ![állapotát a lap](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> A felügyelt tartományok állapota ki lesz értékelve óránként. A felügyelt tartományra módosítások elvégzése után várja meg, amíg a felügyelt tartományok frissített állapotának megtekintéséhez a következő értékelési ciklus. A "Legutóbbi értékelésének" időbélyegzőjéhez viszonyítva a jobb felső sarokban látható, ha a felügyelt tartományok állapotának legutóbbi értékelésének.
>

### <a name="status-of-your-managed-domain"></a>A felügyelt tartományok állapota
Az állapot felső sarkában található a health azt jelzi, a felügyelt tartományok általános állapotát. Az állapot tényezők a meglévő riasztások a tartomány összes. Az Azure AD tartományi szolgáltatások áttekintése lapon a tartomány állapotát is megtekintheti.

| status | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | A felügyelt tartományok zökkenőmentesen fut, és nem rendelkezik minden kritikus vagy figyelmeztetési riasztást. Előfordulhat, hogy a tartomány információs riasztások léptek fel. |
| Figyelmet (figyelmeztetés) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | A felügyelt tartományok kritikus riasztás vannak, de egy vagy több figyelmeztető riasztások léptek fel, amelyek kell figyelembe venni. |
| Figyelmet (kritikus) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | A felügyelt tartományok nincsenek legalább egy kritikus riasztások. Figyelmeztető vagy tájékoztató riasztások is rendelkezhetnek. |
| Központi telepítés | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | A tartomány telepítése folyamatban van. |

## <a name="monitors"></a>Figyelők
Figyelők aspektusainak rendszeresen figyeli az Azure AD tartományi szolgáltatások, a felügyelt tartományban. A legjobb tartsa a figyelő állapota kifogástalan módja a felügyelt tartományok összes aktív riasztás feloldása.

Azure AD tartományi szolgáltatások jelenleg figyeli a következő:
 - Biztonsági mentés
 - Az Azure AD-szinkronizálás

### <a name="the-backup-monitor"></a>A "biztonsági másolat" figyelője
Ez figyeli, hogy a felügyelt tartományok rendszeres biztonsági mentésével történik. A következő táblázat ismerteti, mi történik, a részletek oszlopban, a biztonsági mentési figyelő:

| Részletértéket | Magyarázat |
| --- | --- |
|"Soha nem készített biztonsági" | Ebben az állapotban nem jelent problémát egy újonnan létrehozott felügyelt tartomány. Az első biztonsági mentés általában 24 óra, a felügyelt tartományok kiépítése után jön létre. Ha a felügyelt tartományok nem újonnan létrehozott vagy ebben az állapotban talál egy rendellenes időn [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md). |
| Utolsó biztonsági mentés készült 1-14 napja | Általában a biztonsági mentési figyelő ezt az értéket várt. |
| Utolsó biztonsági mentés készült több mint 14 napja. | Bármely két héttel hosszabb ideje szokatlanul hosszú ideig marad az utolsó biztonsági mentés óta. Aktív kritikus riasztások megakadályozhatja, hogy a felügyelt tartományok rendszeresen biztonsági mentés alatt. Első, minden aktív riasztást a felügyelt tartományok, és ezután Ha a probléma továbbra is fennáll, hárítsa el [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>A "az Azure AD szinkronizálási" figyelője
Microsoft figyeli, hogy milyen gyakran a felügyelt tartományok szinkronizálva az Azure Active Directoryban. Objektumok (felhasználók és csoportok) és az Azure AD-címtárát a módosítások, mert az utolsó szinkronizálás is érinthetik mennyi ideig tarthat egy szinkronizálási időszak száma. Ha a felügyelt tartományok volt utoljára szinkronizálva három nappal ezelőtt, [forduljon a támogatási szolgálathoz](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Riasztások
A felügyelt tartományok, amelyek kell figyelembe venni ahhoz, hogy Azure AD tartományi szolgáltatások futtatásához a problémák vonatkozóan riasztások jönnek létre. Minden riasztás ismerteti a problémát, és biztosítja a megoldás URL-CÍMÉT, hogy a probléma megoldásához adott lépéseit ismerteti. Az összes riasztás és azok megoldásait megtekintéséhez keresse fel a [-riasztások hibaelhárítását végre](active-directory-ds-troubleshoot-alerts.md) cikk.

### <a name="alert-severity"></a>Riasztás fontossága
Riasztások súlyossági három különböző szintjei vannak kategóriákba: kritikus, figyelmeztető és tájékoztató.

 * **A kritikus riasztások** hátrányosan befolyásolhatja a felügyelt tartományok problémákkal. Ezek a riasztások azonnal foglalkozzon, Microsoft nem figyeléséhez, kezeléséhez, javítás, és szinkronizálja a felügyelt tartományok. 
 * **Figyelmeztető riasztások léptek fel** erről értesíteni, amely hatással lehet a jövőben a felügyelt tartományok problémákat. Ezek a riasztások biztonságossá tételéhez a felügyelt tartományok javaslatokat kínál.
 * **Információs riasztások léptek fel** értesítések, amelyek nem negatív mely negatív hatással a tartományban vannak. Mi történik a tartományban és az Azure AD tartományi szolgáltatások ismerő nyomon információs riasztások léptek fel lettek kialakítva.

## <a name="next-steps"></a>További lépések
- [Hárítsa el a felügyelt tartomány riasztást](active-directory-ds-troubleshoot-alerts.md)
- [További tudnivalók az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékért felelős csoport](active-directory-ds-contact-us.md)
