---
title: Alkalmazások kiépítése a hatóköri szűrőkkel | Microsoft Docs
description: Ismerje meg, hogyan használhatók a hatóköri szűrők, hogy megakadályozza az olyan alkalmazásokban lévő objektumok használatát, amelyek támogatják az automatikus felhasználó-kiépítés kiépítési módját, ha egy objektum nem felel meg az üzleti követelményeinek.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb1ed48d501ca3166e0b906c622507b59ef059a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "70812684"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attribútum-alapú alkalmazás-kiépítés hatóköri szűrőkkel
Ennek a cikknek a célja annak ismertetése, hogyan használhatók a hatóköri szűrők olyan attribútum-alapú szabályok definiálásához, amelyek meghatározzák, hogy mely felhasználók legyenek kiépítve egy alkalmazáshoz.

## <a name="scoping-filter-use-cases"></a>Hatókör-szűrő használati esetei

A hatóköri szűrők lehetővé teszik, hogy az Azure Active Directory (Azure AD) kiépítési szolgáltatás belefoglaljon vagy kizárjon egy adott értékkel egyező attribútummal rendelkező felhasználókat. Ha például a felhasználók az Azure AD-ből egy értékesítési csapat által használt SaaS-alkalmazásba vannak kiépítve, akkor megadhatja, hogy csak a "Sales" osztály "részleg" attribútummal rendelkező felhasználók legyenek kiépítve.

A hatóköri szűrők a kiépítési összekötő típusától függően eltérő módon is használhatók:

* **Kimenő kiépítés az Azure ad-ből az SaaS-alkalmazásokba**. Ha az Azure AD a forrásoldali rendszer, a felhasználók [és csoportok hozzárendelései](assign-user-or-group-access-portal.md) a leggyakoribb módszer annak meghatározására, hogy mely felhasználók tartoznak a kiépítés hatóköréhez. Ezek a hozzárendelések az egyszeri bejelentkezés engedélyezésére is használhatók, és egyetlen módszert biztosítanak a hozzáférés és a kiépítés kezeléséhez. A hatókörhöz tartozó szűrők opcionálisan, a hozzárendelések mellett vagy ahelyett is használhatók, hogy az attribútumok alapján szűrhetik a felhasználókat.

    >[!TIP]
    > Egy vállalati alkalmazás hozzárendelései alapján letilthatja az üzembe helyezést, ha a [hatókör](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menü Beállítások elemére módosítja a kiépítési beállítások lehetőséget az **összes felhasználó és csoport szinkronizálásához**. Ha ezt a lehetőséget választja, és az attribútum-alapú hatóköri szűrők gyorsabb teljesítményt biztosítanak, mint a csoport alapú hozzárendelések használata.  

* **Bejövő kiépítés a HCM-alkalmazásokból az Azure ad-be és a Active Directoryba**. Ha egy [HCM-alkalmazás, például a munkanap](../saas-apps/workday-tutorial.md) a forrásrendszer, a hatóköri szűrők az elsődleges módszer annak meghatározására, hogy mely felhasználókat kell kiépíteni a HCM-alkalmazásból Active Directory vagy az Azure ad-be.

Alapértelmezés szerint az Azure AD kiépítési összekötői nem rendelkeznek konfigurált attribútum-alapú hatóköri szűrőkkel. 

## <a name="scoping-filter-construction"></a>Hatókör-szűrő kialakítása

Egy hatóköri szűrő egy vagy több *záradékból*áll. A záradékok határozzák meg, hogy mely felhasználók számára engedélyezett a hatókör-szűrő átadása az egyes felhasználók attribútumainak kiértékelésével. Előfordulhat például, hogy egy olyan záradékkal rendelkezik, amely megköveteli, hogy a felhasználó "State" attribútuma "New York" legyen, így csak a New York-i felhasználók lettek kiépítve az alkalmazásba. 

Egyetlen záradék egyetlen feltételt határoz meg egyetlen attribútum értékének megadásához. Ha egy hatókör-szűrőben több záradék jön létre, a rendszer a "és a" logikával együtt értékeli ki őket. Ez azt jelenti, hogy az összes záradéknak az "igaz" értéket kell kiértékelnie ahhoz, hogy egy felhasználó kiépíthető legyen.

Végül több hatókör-szűrő is létrehozható egyetlen alkalmazáshoz. Ha több hatóköri szűrő van jelen, a rendszer a "vagy" logikával együtt értékeli ki őket. Ez azt jelenti, hogy ha a beállított hatóköri szűrők bármelyikének összes záradéka "true" értékre van kiértékelve, a felhasználó kiépítve.

Az Azure AD-létesítési szolgáltatás által feldolgozott minden egyes felhasználót vagy csoportot a rendszer minden egyes hatóköri szűrőnél egyedileg értékel ki.

Példaként vegye figyelembe a következő hatókör-szűrőt:

![Hatókör-szűrő](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

A hatókör-szűrőnek megfelelően a felhasználóknak meg kell felelniük a következő feltételeknek:

* A szolgáltatásnak New Yorkban kell lennie.
* A mérnöki részlegnek kell dolgozniuk.
* A vállalat alkalmazotti AZONOSÍTÓjának 1 000 000 és 2 000 000 között kell lennie.
* A beosztás értéke nem lehet null vagy üres.

## <a name="create-scoping-filters"></a>Hatóköri szűrők létrehozása
A hatóköri szűrők az egyes Azure AD-beli felhasználói létesítési összekötők attribútum-hozzárendeléseinek részeként vannak konfigurálva. Az alábbi eljárás azt feltételezi, hogy már beállította az automatikus kiépítési műveletet az [egyik támogatott alkalmazáshoz](../saas-apps/tutorial-list.md) , és egy hatókör-szűrőt ad hozzá.

### <a name="create-a-scoping-filter"></a>Hatókör-szűrő létrehozása
1. A [Azure Portal](https://portal.azure.com)lépjen a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** szakaszra.

2. Válassza ki azt az alkalmazást, amelyhez az automatikus kiépítés konfigurálva van: például "ServiceNow".

3. Válassza ki a **kiépítés** lapot.

4. A **leképezések** szakaszban válassza ki azt a leképezést, amelyhez hatókör-szűrőt szeretne konfigurálni: például "Azure Active Directory felhasználók szinkronizálása a ServiceNow".

5. Válassza ki a **forrás objektum hatóköre** menüt.

6. Válassza a **hatókör hozzáadása szűrő**elemet.

7. Definiáljon egy záradékot úgy, hogy kiválasztja a forrás **attribútum nevét**, egy **operátort**és egy **attribútum-értéket** , amely megfelel a következőnek:. A következő operátorok támogatottak:

   a. **Egyenlő**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum pontosan egyezik a bemeneti karakterlánc értékével (kis-és nagybetűk megkülönböztetése).

   b. **nem egyenlő**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem egyezik a bemeneti karakterlánc értékével (kis-és nagybetűk megkülönböztetése).

   c. **igaz**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum értéke TRUE (igaz) logikai érték.

   d. **hamis**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum a hamis logikai értéket tartalmazza.

   e. **NULL értékű**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum üres.

   f. **nem null**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem üres.

   g. **REGEX egyezés**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum megfelel egy reguláris kifejezési mintának. Például: ([1-9] [0-9]) a 10 és 99 közötti számra illeszkedik.

   h. **nem a REGEX egyezése**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem felel meg a reguláris kifejezési mintának.

8. Válassza az **Új hatókör hozzáadása záradékot**.

9. Szükség esetén ismételje meg a 7-8. lépést további hatókör-záradékok hozzáadásához.

10. A **hatóköri szűrő címe**területen adja meg a hatókör-szűrő nevét.

11. Kattintson az **OK** gombra.

12. Kattintson ismét az **OK gombra** a **hatókör szűrők** képernyőjén. Szükség esetén ismételje meg a 6-11-es lépést egy másik hatókör-szűrő hozzáadásához.

13. Válassza a **Mentés** lehetőséget az **attribútum-leképezési** képernyőn. 

>[!IMPORTANT] 
> Egy új hatókör-szűrő mentése új teljes szinkronizálást indít az alkalmazáshoz, ahol a rendszer az új hatókör-szűrővel újra kiértékeli a forrásrendszer összes felhasználóját. Ha az alkalmazás egyik felhasználója korábban a kiépítés hatókörében volt, de a hatókörön kívül esik, a fiókja le van tiltva, vagy az alkalmazás nem lesz kiépítve. Az alapértelmezett viselkedés felülbírálásához tekintse meg a [Hatókörön kívüli felhasználói fiókok törlésének kihagyását](skip-out-of-scope-deletions.md)ismertető témakört.


## <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások megszüntetése](user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
* [Kifejezések írása attribútumleképezésekhez](functions-for-customizing-application-data.md)
* [Fiók kiépítési értesítései](user-provisioning.md)
* [A SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez Azure Active Directoryról alkalmazások számára](use-scim-to-provision-users-and-groups.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)

