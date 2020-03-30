---
title: Alkalmazások kiépítése hatókörszűrőkkel | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a hatókörszűrőket az automatikus felhasználói kiépítést támogató alkalmazások objektumainak kiépítése megakadályozásához, ha egy objektum nem felel meg az üzleti követelményeknek.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138535"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel
Ez a cikk célja, hogy elmagyarázza, hogyan használhat hatókörszűrőket attribútumalapú szabályok meghatározására, amelyek meghatározzák, hogy mely felhasználók vannak kiépítve egy alkalmazáshoz.

## <a name="scoping-filter-use-cases"></a>Hatókör-szűrő használati esetei

A hatókör-szűrő lehetővé teszi, hogy az Azure Active Directory (Azure AD) létesítési szolgáltatás tartalmazza vagy kizárja azokat a felhasználókat, akik egy adott értéknek megfelelő attribútummal rendelkeznek. Ha például az Azure AD-ből egy értékesítési csoport által használt SaaS-alkalmazásba épít ki felhasználókat, megadhatja, hogy csak az "Értékesítés" "Részleg" attribútummal rendelkező felhasználók legyenek a kiépítés hatókörében.

A hatókörszűrők a létesítési összekötő típusától függően eltérő módon használhatók:

* **Kimenő kiépítés az Azure AD-ből saas-alkalmazásokba.** Ha az Azure AD a forrásrendszer, [a felhasználói és csoport-hozzárendelések](../manage-apps/assign-user-or-group-access-portal.md) a leggyakoribb módszer annak meghatározására, hogy mely felhasználók vannak hatókörben a kiépítés. Ezek a hozzárendelések is használják egyszeri bejelentkezés engedélyezéséhez, és egyetlen módszert biztosít a hozzáférés és a kiépítés kezelésére. A hatókörszűrők a hozzárendelések mellett vagy helyettük opcionálisan használhatók a felhasználók attribútumértékek alapján történő szűrésére.

    >[!TIP]
    > A vállalati alkalmazások hozzárendelései alapján letilthatja a kiépítést, ha módosítja a [Beállításokat](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) a Hatókör menüben az **Összes felhasználó és csoport szinkronizálása**parancshoz. Ezzel a beállítással, valamint attribútumalapú hatókörszűrőkkel gyorsabb teljesítményt nyújt, mint a csoportalapú hozzárendelések használata.  

* **Bejövő kiépítés a HCM-alkalmazásokból az Azure AD-be és az Active Directoryba.** Ha egy [HCM-alkalmazás, például a Workday](../saas-apps/workday-tutorial.md) a forrásrendszer, a hatókör-szűrők az elsődleges módszer annak meghatározására, hogy mely felhasználókat kell kiépíteni a HCM-alkalmazásból az Active Directoryba vagy az Azure AD-be.

Alapértelmezés szerint az Azure AD-kiépítési összekötők nem rendelkeznek attribútumalapú hatókör-szűrők konfigurálva. 

## <a name="scoping-filter-construction"></a>Hatókörszűrő építése

A hatókörszűrő egy vagy több *záradékból*áll. A záradékok az egyes felhasználók attribútumainak kiértékelésével határozzák meg, hogy mely felhasználók léphetnek át a hatókörszűrőn. Előfordulhat például, hogy van egy záradéka, amely megköveteli, hogy a felhasználó "State" attribútuma "New York" legyen, így csak a New York-i felhasználók kerülnek kiépítésre az alkalmazásba. 

Egyetlen záradék egyetlen feltételt határoz meg egyetlen attribútumértékhez. Ha egyetlen hatókörszűrőben több záradék jön létre, a rendszer együtt értékeli ki őket az "AND" logika használatával. Ez azt jelenti, hogy minden záradéknak "true"-nak kell lennie ahhoz, hogy a felhasználó kilegyen építve.

Végül egyetlen alkalmazáshoz több hatókörszűrő hozható létre. Ha több hatókörszűrő van jelen, a rendszer együtt értékeli ki őket "OR" logika használatával. Ez azt jelenti, hogy ha a konfigurált hatókörszűrők bármelyikének összes záradéka "true" értéket ad ki, a felhasználó ki van építve.

Az Azure AD-kiépítési szolgáltatás által feldolgozott minden egyes felhasználó vagy csoport minden egyes hatókörszűrővel szemben minden egyes külön-külön kiértékelése történik.

Vegyük például a következő hatókörszűrőt:

![Hatókörszűrő](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

A hatókörszűrő szerint a felhasználóknak a következő feltételeknek kell megfelelniük a kiépítéshez:

* Biztos New Yorkban vannak.
* A gépészeti részlegen kell dolgozniuk.
* A vállalat alkalmazottjának azonosítójának 1 000 000 és 2 000 000 között kell lennie.
* A beosztásuk nem lehet null vagy üres.

## <a name="create-scoping-filters"></a>Hatókörszűrők létrehozása
Hatókör-szűrők az egyes Azure AD-felhasználók létesítési összekötőattribútum-leképezések részeként konfigurálva vannak konfigurálva. A következő eljárás feltételezi, hogy már beállította az automatikus kiépítést [az egyik támogatott alkalmazáshoz,](../saas-apps/tutorial-list.md) és hatókörszűrőt ad hozzá.

### <a name="create-a-scoping-filter"></a>Hatókörszűrő létrehozása
1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** szakaszban.

2. Válassza ki azt az alkalmazást, amelyhez konfigurálta az automatikus kiépítést: például "ServiceNow".

3. Válassza a **Kiépítés** lapot.

4. A **leképezések** szakaszban válassza ki a hatókörszűrőt konfigurálni kívánt leképezést: például "Az Azure Active Directory felhasználóinak szinkronizálása a ServiceNow-val".

5. Válassza a **Forrásobjektum hatóköre** menüt.

6. Válassza **a Hatókörszűrő hozzáadása lehetőséget.**

7. Definiáljon egy záradékot egy forrás **attribútumnév**, egy **operátor**és egy **attribútumérték** kiválasztásával, amelynek egyeznie kell. A következő operátorok támogatottak:

   a. **Egyenlő .** A záradék "true" értéket ad vissza, ha a kiértékelt attribútum pontosan megegyezik a bemeneti karakterlánc értékével (a kis- és nagybetűk megkülönböztetése).

   b. **Nem egyenlő**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum nem egyezik meg a bemeneti karakterlánc értékével (kis- és nagybetűk megkülönböztetése).

   c. **EZ IGAZ**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum true logikai értéket tartalmaz.

   d. **HAMIS**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum hamis logikai értéket tartalmaz.

   e. **NULL .** A záradék "true" értéket ad vissza, ha a kiértékelt attribútum üres.

   f. **NEM NULL**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum nem üres.

   g. **REGEX EGYEZÉS**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum megegyezik egy reguláris kifejezési mintával. Például: ([1-9][0-9]) tetszőleges 10 és 99 közötti számnak felel meg.

   h. **NEM REGEX MÉRKŐZÉS**. A záradék "true" értéket ad vissza, ha a kiértékelt attribútum nem egyezik meg a reguláris kifejezésmintával.
   
   i. **Greater_Than.** A záradék "true" értéket ad vissza, ha a kiértékelt attribútum nagyobb, mint az érték. A hatókörszűrőben megadott értéknek egész számnak kell lennie, és a felhasználó attribútumának egész számnak kell lennie [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** A záradék "true" értéket ad vissza, ha az értékelt attribútum nagyobb vagy egyenlő az értékkel. A hatókörszűrőben megadott értéknek egész számnak kell lennie, és a felhasználó attribútumának egész számnak kell lennie [0,1,2,...]. 
   
   k. **Tartalmazza.** A záradék "true" értéket ad vissza, ha az értékelt attribútum az [itt](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8)leírtak szerint tartalmazza a karakterlánc értékét (kis- és nagybetűk megkülönböztetése). 


>[!IMPORTANT] 
> - Az IsMemberOf szűrő jelenleg nem támogatott.
> - Az EQUALS és a NOT EQUALS nem támogatott a többértékű attribútumok esetében

9. Ha szükséges, ismételje meg a 7-8.

10. A **Hatókörszűrő címe mezőben**adja meg a hatókörszűrő nevét.

11. Válassza **az OK gombot.**

12. A Hatókörszűrők képernyőn ismét válassza az **OK** **gombot.** Szükség esetén ismételje meg a 6-11.

13. Válassza a **Mentés** gombot az **Attribútumleképezés** képernyőn. 

>[!IMPORTANT] 
> Új hatókörszűrő mentése új teljes szinkronizálást eredményez az alkalmazás számára, ahol a forrásrendszer összes felhasználóját újra kiértékeli a rendszer az új hatókörszűrővel szemben. Ha az alkalmazás egyik felhasználója korábban a kiépítés hatókörében volt, de nem terjed ki, a fiók le van tiltva vagy megszüntetve van az alkalmazásban. Az alapértelmezett viselkedés felülbírálásához olvassa el a [Hatókörön kívül eső felhasználói fiókok törlésének kihagyása hivatkozást.](../app-provisioning/skip-out-of-scope-deletions.md)


## <a name="common-scoping-filters"></a>Gyakori hatókörszűrők
| Célattribútum| Művelet | Érték | Leírás|
|----|----|----|----|
|userPrincipalName|REGEX EGYEZÉS|.\*@domain.com |A tartománysal @domain.com rendelkező userPrincipal felhasználó minden felhasználója kiépíti a tartományt|
|userPrincipalName|NEM REGEX EGYEZÉS|.\*@domain.com|A tartománysal @domain.com rendelkező userPrincipal felhasználó minden felhasználója nem lesz kiépítve.|
|Részleg|Egyenlő|Értékesítési|Az értékesítési osztály minden felhasználója kiépítésre van lehetősége|
|workerID|REGEX EGYEZÉS|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Az 1000000 és 2000000 közötti munkavállalói azonosítóval rendelkező alkalmazottak kiépítésre várnak.|

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználók kiépítésének és a SaaS-alkalmazásokhoz való kiépítésének automatizálása](../app-provisioning/user-provisioning.md)
* [Attribútumleképezések testreszabása a felhasználók kiépítéséhez](../app-provisioning/customize-application-attributes.md)
* [Kifejezések írása attribútumleképezésekhez](functions-for-customizing-application-data.md)
* [Fiókkiépítési értesítések](../app-provisioning/user-provisioning.md)
* [Az SCIM használatával engedélyezheti a felhasználók és csoportok automatikus kiépítését az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Az SaaS-alkalmazások integrálására vonatkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)

