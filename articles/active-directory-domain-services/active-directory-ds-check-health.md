---
title: Az Azure AD tartományi szolgáltatások – a felügyelt tartomány állapotának ellenőrzéséhez |} A Microsoft Docs
description: A health-oldal használatával az Azure Portalon a felügyelt tartomány állapotának ellenőrzéséhez.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: fe5776dad4c4b9ae96484ac266a23e87658cf578
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852831"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD Domain Services felügyelt tartomány állapotának ellenőrzése

## <a name="overview-of-the-health-page"></a>A health-oldal áttekintése
A health-oldal használatával az Azure Portalon, Ön képesek arra, hogy mi történik a felügyelt tartomány naprakész. Ez a cikk végigvezeti a health-oldal elemeit.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>A felügyelt tartomány állapotának megtekintése
1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon.
2. Kattintson a tartomány állapotának megtekintéséhez.
3. Kattintson a bal oldali navigációs ablaktáblában **egészségügyi**.

A következő képen látható egy minta health-oldal mutatja be: ![Példa health-oldal](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> A felügyelt tartomány állapotának abban az esetben minden órában. A felügyelt tartomány módosítások elvégzése után várja meg, amíg a következő értékelési ciklus során frissített a felügyelt tartomány állapotának megtekintéséhez. A jobb felső sarokban lévő "Legutóbbi értékelésének" időbélyeg jeleníti meg, ha a felügyelt tartomány állapotának legutóbbi értékelésének.
>

### <a name="status-of-your-managed-domain"></a>A felügyelt tartomány állapotának
A felső állapota az állapot, a jobb oldalon azt jelzi, hogy a felügyelt tartomány általános állapotát. Az állapot a meglévő riasztásokat a tartományban lévő összes számításba. A tartomány állapotának az Azure AD tartományi szolgáltatások – áttekintés oldalán is megtekintheti.

| status | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | A felügyelt tartomány már simán fut, és nem rendelkezik minden kritikus vagy figyelmeztető riasztást. Ez a tartomány előfordulhat tájékoztatási szintű riasztások. |
| Figyelmet igényel (figyelmeztetés) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Nincsenek kritikus riasztások a felügyelt tartományban lévő, de tartandó egy vagy több figyelmeztető riasztások. |
| Figyelmet igényel (kritikus) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Nincs olyan egy vagy több kritikus riasztást a felügyelt tartományra. Figyelmeztetési és/vagy tájékoztató riasztások is rendelkezhetnek. |
| Központi telepítés | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | A tartomány folyamatban üzembe helyezése folyamatban van. |

## <a name="monitors"></a>Monitorozások
Figyelők a felügyelt tartományhoz, amely rendszeresen figyeli az Azure AD Domain Services aspektusait. A legjobb módja, hogy a figyelő kifogástalan állapotban, a felügyelt tartományhoz tartozó aktív riasztások megoldása érdekében.

Az Azure AD tartományi szolgáltatások jelenleg a következőket figyeli:
 - Backup
 - Szinkronizálás az Azure AD-vel

### <a name="the-backup-monitor"></a>A "backup" figyelő
Ez figyeli, hogy rendszeresen készítsen biztonsági másolatokat a felügyelt tartomány végrehajtása. A következő táblázat ismerteti, hogy milyen hatással vannak a biztonsági mentési figyelő részletei oszlopában:

| Részletek értéke | Magyarázat |
| --- | --- |
|"Soha nem biztonsági mentése" | Ebben az állapotban előfordulhat, hogy egy újonnan létrehozott felügyelt tartományon. Az első biztonsági mentés általában 24 órával a felügyelt tartomány kiépítése után jön létre. Ha a felügyelt tartomány nem újonnan létrehozott vagy rendellenes mennyi idő, jelennek meg ebben az állapotban [forduljon az ügyfélszolgálathoz](active-directory-ds-contact-us.md). |
| Legutóbbi biztonsági mentés készült 1-től 14 nappal ezelőtt | Általánosságban véve a biztonsági mentési figyelő ezt az értéket várt. |
| Legutóbbi biztonsági mentés 14 napnál régebben történt. | Két héttel hosszabb ideje szokatlanul hosszú ideig a legutóbbi biztonsági mentés óta. Aktív kritikus riasztások a felügyelt tartományt megakadályozhatja rendszeres biztonsági mentés alatt álló. First, minden aktív a felügyelt tartományhoz tartozó riasztást, és ezután Ha a probléma továbbra is fennáll, hárítsa el [forduljon az ügyfélszolgálathoz](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>A "az Azure AD szinkronizálási" figyelője
A Microsoft figyeli, hogy milyen gyakran a felügyelt tartományhoz az Azure Active Directory szinkronizálva van. Objektumot (felhasználókat és csoportokat), és mivel a legutóbbi szinkronizálás is érinthetik mennyi ideig is eltarthat a szinkronizálási időszakot az Azure AD-címtárhoz végrehajtott módosítások számát száma. Ha a felügyelt tartomány volt utoljára szinkronizálva több mint három nappal ezelőtt, [forduljon az ügyfélszolgálathoz](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Riasztások
Riasztások jönnek létre a problémákat a tartandó ahhoz, hogy az Azure AD tartományi szolgáltatások futtatásához, a felügyelt tartományban. Minden riasztás ismerteti a problémát, és biztosít egy megoldás URL-címet, amely a probléma megoldásának lépéseit ismerteti. Minden riasztás és azok megoldásait megtekintéséhez keresse fel a [elháríthatja a riasztásokat](active-directory-ds-troubleshoot-alerts.md) cikk.

### <a name="alert-severity"></a>Riasztás fontossága
Riasztások súlyosság három különböző szintjei kategóriákba: kritikus, figyelmeztetés és információs.

 * **Kritikus riasztások** a problémák hátrányosan befolyásolhatja a felügyelt tartományra. Ezek a riasztások, azonnal foglalkozzon, mint a Microsoft nem figyelése, kezelése, javítani és szinkronizálni a felügyelt tartományt. 
 * **Figyelmeztetési szintű riasztások** Önt a problémákat, amelyek hatással lehet a jövőben a felügyelt tartományra. Ezek a riasztások a felügyelt tartomány secure javaslatok kínálnak.
 * **Tájékoztatási szintű riasztások** értesítéseket, amelyek nem negatív negatív hatással vannak a tartományban vannak. Tájékoztatási szintű riasztások úgy tervezték, hogy mi történik a tartomány és az Azure AD tartományi szolgáltatások alapos ismerete.

## <a name="next-steps"></a>További lépések
- [Riasztások a felügyelt tartományon](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)
