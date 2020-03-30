---
title: Feltételes hozzáférési házirendek áttelepítése – Azure Active Directory
description: Ismerje meg, mit kell tudnia a klasszikus szabályzatok áttelepítéséhez az Azure Portalon.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185911"
---
# <a name="conditional-access-classic-policy-migration"></a>Feltételes hozzáférés – klasszikus házirend-áttelepítés

A feltételes hozzáférés az Azure Active Directory által használt eszköz a jelek egyesítéséhez, a döntések meghozatalához és a szervezeti szabályzatok kényszerítéséhez. A feltételes hozzáférés az új identitásvezérelt vezérlősík középpontjában áll. Bár a cél továbbra is ugyanaz, az új Azure Portal kiadása jelentős fejlesztéseket vezetett be a feltételes hozzáférés működéséhez.

Fontolja meg az Azure Portalon nem létrehozott szabályzatok áttelepítését, mert:

- Most már foglalkozik forgatókönyvek nem tudta kezelni korábban.
- A kezelni kívánt házirendek számát a konszolidálásukkal csökkentheti.
- Az összes feltételes hozzáférési szabályzatot egy központi helyen kezelheti.
- A klasszikus Azure-portál megszűnik.

Ez a cikk ismerteti, mit kell tudni a meglévő feltételes hozzáférési szabályzatok áttelepítése az új keretrendszerbe.

## <a name="classic-policies"></a>Klasszikus házirendek

Az [Azure Portalon](https://portal.azure.com)a feltételes hozzáférési szabályzatok az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférése**területen találhatók. Előfordulhat, hogy a szervezet régebbi feltételes hozzáférési házirendekkel is rendelkezik, amelyeket nem ezen a lapon hoztak létre. Ezeket a házirendeket *klasszikus házirendeknek*nevezzük. A klasszikus házirendek feltételes hozzáférési házirendek, amelyekben létrehozott:

- Klasszikus Azure-portál
- Az Intune klasszikus portálja
- Az Intune Alkalmazásvédelmi portálja

A **feltételes hozzáférés** lapon a klasszikus szabályzatok eléréséhez kattintson a [**Klasszikus házirendek**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) a **Kezelés** szakaszban. 

![Feltételes hozzáférés az Azure AD-ben a klasszikus szabályzatok nézetét megjelenítő](./media/policy-migration/71.png)

A **Klasszikus házirendek** nézet lehetőséget nyújt a következőkre:

- A klasszikus házirendek szűrése.
- Tiltsa le a klasszikus házirendeket.
- Tekintse át a klasszikus házirend beállításait, és tiltsa le.

   ![Klasszikus házirend-részletek, beleértve a meglévő házirend-konfigurációt](./media/policy-migration/74.png)

> [!WARNING]
> Miután letiltotta a klasszikus szabályzatnem engedélyezhető újra.

A klasszikus házirend részletes nézete lehetővé teszi a beállítások dokumentálását, a belefoglalt vagy kizárt csoportok módosítását, valamint a házirend letiltását.

![Házirend részletei - A felvehető vagy kizárandó csoportok](./media/policy-migration/75.png)

A kijelölt csoportok módosításával vagy adott csoportok kizárásával tesztelheti a letiltott klasszikus házirend hatását néhány tesztfelhasználó számára, mielőtt letiltaná a házirendet az összes belefoglalt felhasználóra és csoportra vonatkozóan.
 
## <a name="migration-considerations"></a>Migrálási szempontok

Ebben a cikkben az Azure AD feltételes hozzáférési szabályzatok is nevezik *az új szabályzatok.*
A klasszikus szabályzatok továbbra is együtt dolgoznak az új házirendekkel, amíg le nem tiltja vagy nem törli őket. 

A következő szempontok fontosak a szakpolitikai konszolidáció összefüggésében:

- Bár a klasszikus szabályzatok egy adott felhőalkalmazáshoz vannak kötve, annyi felhőalkalmazást választhat ki, amennyit csak kell egy új szabályzatban.
- A klasszikus szabályzat és a felhőalapú alkalmazások új szabályzatának vezérléséhez az összes vezérlő (*ÉS*) teljesülnie kell. 
- Egy új házirendben a következőket teheti:
   - Több feltétel kombinálása, ha a forgatókönyv megköveteli. 
   - Jelöljön ki több támogatási követelményt hozzáférés-vezérlésként, és kombinálja azokat egy logikai *VAGY* (a kijelölt vezérlők valamelyikének megkövetelése) vagy egy logikai *ÉS* (az összes kijelölt vezérlőelem szükséges).

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Ha az **Office 365 Exchange online** klasszikus szabályzatait szeretné áttelepíteni, amelyek ügyfélalkalmazásként tartalmazzák az Exchange Active **Sync-et,** előfordulhat, hogy nem tudja azokat egyetlen új házirendbe egyesíteni. 

Ez például akkor áll fenn, ha az összes ügyfélalkalmazás-típust támogatni szeretné. Az Exchange Active Sync ügyfélalkalmazások feltételként való **használatára** vonatkozó új szabályzatban nem választhat más ügyfélalkalmazásokat.

![Feltételes hozzáférés az ügyfélalkalmazások kijelölése](./media/policy-migration/64.png)

Egy új házirendbe való bevonás akkor sem lehetséges, ha a klasszikus házirendek több feltételt tartalmaznak. Az **Exchange Active Sync** ügyfélalkalmazások feltételként konfigurált új házirendje nem támogat más feltételeket:   

![Az Exchange ActiveSync nem támogatja a kijelölt feltételeket](./media/policy-migration/08.png)

Ha olyan új házirenddel rendelkezik, amelyhez az **Exchange Active Sync** ügyfélalkalmazások feltétele konfigurálva van, meg kell győződnie arról, hogy az összes többi feltétel nincs konfigurálva. 

![Feltételes hozzáférési feltételek](./media/policy-migration/16.png)
 
Az Office 365 Exchange Online alkalmazásalapú klasszikus szabályzatai, amelyek tartalmazzák az **Exchange Active Sync-et,** mivel az ügyfélalkalmazások feltétele **lehetővé** teszi a támogatott és **nem támogatott** eszközplatformokat. Bár nem konfigurálhat egyes eszközplatformokat egy kapcsolódó új házirendben, a támogatást csak a [támogatott eszközplatformokra korlátozhatja.](concept-conditional-access-conditions.md#device-platforms) 

![Feltételes hozzáférés az Exchange ActiveSync kiválasztása](./media/policy-migration/65.png)

Több klasszikus szabályzatot is konszolidálhat, amelyek az **Exchange Active Sync-et** ügyfélalkalmazásokként tartalmazzák, ha rendelkeznek:

- Csak **az Exchange aktív szinkronizálása** feltételként 
- A hozzáférés megadására vonatkozó számos követelmény konfigurálva

Az egyik gyakori forgatókönyv a következők konszolidálása:

- Az Azure klasszikus portáleszköz-alapú klasszikus szabályzata 
- Alkalmazásalapú klasszikus szabályzat az Intune alkalmazásvédelmi portálján 
 
Ebben az esetben a klasszikus szabályzatokat egyetlen új szabályzatba konszolidálhatja, amely mindkét követelményt kiválasztotta.

![Feltételes hozzáférés-támogatási vezérlők](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Eszközplatformok

Az alkalmazásalapú vezérlőkkel rendelkező klasszikus szabályzatok előre konfigurálva vannak az iOS és az Android rendszerrel, mint az eszköz platformfeltételével. 

Egy új szabályzatban ki kell választania az egyénileg támogatni kívánt [eszközplatformokat.](concept-conditional-access-conditions.md#device-platforms)

![Feltételes hozzáférésű eszközplatformok kiválasztása](./media/policy-migration/41.png)

## <a name="next-steps"></a>További lépések

- [Az új házirend-döntések hatásának meghatározásához használja a csak jelentésmódot a feltételes hozzáféréshez.](concept-conditional-access-report-only.md)
- Ha tudni szeretné, hogyan konfigurálhat feltételes hozzáférési házirendet, olvassa el a Feltételes hozzáférés közös házirendjei című [témakört.](concept-conditional-access-policy-common.md)
- Ha készen áll a feltételes hozzáférési házirendek konfigurálására a környezetéhez, olvassa el a [Hogyan tervezze meg a feltételes hozzáférés központi telepítését az Azure Active Directoryban című témakört.](plan-conditional-access.md) 
