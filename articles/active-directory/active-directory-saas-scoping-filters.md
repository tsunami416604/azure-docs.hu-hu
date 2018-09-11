---
title: Szűrők hatókörének beállítása az alkalmazások üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan hatókörszűrők használata alkalmazásokkal, amelyek támogatják az automatikus felhasználóátadást készíthet elő, ha egy objektum nem elégíti ki az üzleti követelményeinek kiépítése folyamatban lévő objektumok elkerülése érdekében.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da742f54a9a415c6078a6185a9605db700dffd5
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44324954"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attribútum-alapú alkalmazások üzembe helyezése és hatókörének beállítása szűrőket
Ez a cikk célja, hogy azt ismertetik, hogyan hatókörszűrők használata, amelyek meghatározzák, hogy mely felhasználók lesznek kiépítve, egy alkalmazás Attribútumalapú szabályok meghatározásához.

## <a name="scoping-filter-use-cases"></a>Hatókörkezeléshez kapcsolódó szűrő alkalmazási helyzetek

Egy hatókörszűrőt lehetővé teszi, hogy az Azure Active Directory (Azure AD) regisztrációs szolgáltatási vagy bármely olyan attribútum, amely megfelel egy adott értékkel rendelkező felhasználók kizárja. Ha például egy értékesítési csapat által használt SaaS-alkalmazásokban az Azure AD-ből származó felhasználók kiépítésekor megadhatja, hogy a kiépítés hatókörébe legyen "Értékesítés", "Részleg" attribútuma csak felhasználói.

Hatókörkezeléshez kapcsolódó szűrők használható eltérően kiépítési összekötő típusától függően:

* **Kimenő kiépítése az Azure ad-ből a SaaS-alkalmazásokban**. Ha az Azure AD a forrásrendszerben [felhasználó és csoport-hozzárendelések](manage-apps/assign-user-or-group-access-portal.md) vannak a leggyakrabban használt módszer meghatározásához, hogy mely felhasználók tartoznak a kiépítés hatókörébe. Ezeket a hozzárendeléseket is használhatók az egyszeri bejelentkezés engedélyezése és kezelése, hozzáférés és üzembe helyezését egyetlen módot. Hatókörének beállítása szűrőket segítségével igény szerint hozzárendelések mellett vagy helyett, szűrhetők a felhasználók attribútum értékei alapján.

    >[!TIP]
    > Kiépítés alapján a vállalati alkalmazás-hozzárendeléseit a beállítások módosításával letilthatja a [hatókör](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) az üzembe helyezési beállítások menüt **minden felhasználó és csoport szinkronizálása**. Ez opció és attribútum alapján Hatókörszűrő használatával teljesítményt nyújt, gyorsabb, mint biztonságicsoport-alapú hozzárendelések használatával.  

* **Bejövő HCM alkalmazásokat az Azure AD-ből és az Active Directory**. Ha egy [HCM-alkalmazást például a Workday](saas-apps/workday-tutorial.md) van a forrásrendszerben Hatókörszűrő az elsődleges módszer, mely felhasználókat kell létrehozni az Active Directory vagy az Azure AD HCM-alkalmazás meghatározására.

Alapértelmezés szerint az Azure AD-kiépítési összekötők nem rendelkezik konfigurált hatókörének meghatározásához Attribútumalapú szűrők. 

## <a name="scoping-filter-construction"></a>Hatókörkezeléshez kapcsolódó szűrő építése

Egy vagy több áll egy hatókörszűrőt *záradékok*. Záradékok határozza meg, hogy mely felhasználók jogosultak kiértékelése a minden egyes felhasználói attribútumok a Hatókörszűrő továbbítása. Előfordulhat például, hogy egy záradék szükséges, hogy a felhasználó "State" attribútum megegyezik-e a "Győr", így csak a New York-i felhasználók érvénybe lépnek az alkalmazásba. 

Egyetlen záradék egyetlen feltétel egy egyetlen attribútum értéke határozza meg. Több záradékot egy egyetlen hatókörszűrőt jönnek létre, ha Ön értékeli ki őket együtt "És" logic használatával. Ez azt jelenti, hogy az összes záradékok kell értékelnie, ki kell építeni egy felhasználó sorrendben "true".

Végül több Hatókörszűrő egyetlen alkalmazásra vonatkozó hozható létre. Ha több Hatókörszűrő megadva, azok van együtt lett kiértékelve "Vagy" logic használatával. Ez azt jelenti, hogy a konfigurált Hatókörszűrő bármelyikében a záradékok kiértékelni a "true", ha a felhasználó van kiépítve.

Minden felhasználó vagy csoport az Azure AD létesítési szolgáltatás által feldolgozott mindig képest értékeli ki külön-külön mindegyik hatókörszűrőt.

Tegyük fel fontolja meg a következő Hatókörszűrő:

![Hatókör-beállítási szűrője](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

A Hatókörszűrő megfelelően felhasználók ki kell építeni a következő feltételeknek kell megfelelnie:

* A New York-i kell lenniük.
* A műszaki részleg kell működniük.
* A vállalat alkalmazott azonosítója 1 000 000 és 2,000,000 között kell lennie.
* A feladat címe nem lehet null értékű vagy üres.

## <a name="create-scoping-filters"></a>Hatókörszűrő létrehozása
Hatókörszűrő vannak konfigurálva, az egyes Azure AD-felhasználó kiépítésére összekötő attribútumleképezések részeként. A következő eljárás azt feltételezi, hogy már állított be az Automatikus kiépítés [a támogatott alkalmazások egyik](saas-apps/tutorial-list.md) és ad hozzá egy hatókörszűrőt rá.

### <a name="create-a-scoping-filter"></a>Hozzon létre egy hatókörszűrőt
1. A a [az Azure portal](https://portal.azure.com), nyissa meg a **Azure Active Directory** > **vállalati alkalmazások** > **mindenalkalmazás** szakaszban.

2. Válassza ki az alkalmazást, amelynek már konfigurálta az Automatikus kiépítés: például: "ServiceNow".

3. Válassza ki a **kiépítési** fülre.

4. Az a **leképezések** területen válassza ki a leképezést, amely egy hatókörszűrőt a konfigurálni kívánt: például "szinkronizálása az Azure Active Directory felhasználók a ServiceNow".

5. Válassza ki a **Forrásobjektum hatóköre forrás** menü.

6. Válassza ki **Hatókörszűrő hozzáadása**.

7. Adja meg a záradékot egy forrás kiválasztásával **attribútumnév**, egy **operátor**, és a egy **attribútumérték** az egyeztetéshez. A következő operátor használata támogatott:

   a. **EGYENLŐ**. Záradék visszaadja az "true", ha a kiértékelt attribútum a bemeneti karakterlánc pontosan megegyezik (megkülönbözteti a kis-és nagybetűket).

   b. **NEM EGYENLŐ**. Záradék "true", ha a kiértékelt attribútum nem egyezik a bemeneti karakterlánc (megkülönbözteti a kis-és nagybetűket) értéket ad vissza.

   c. **IGAZ**. Záradék "true", ha a kiértékelt attribútum IGAZ logikai értéket adja vissza.

   d. **FALSE (HAMIS)**. Záradék "true", ha a kiértékelt attribútum false logikai értéket adja vissza.

   e. **NULL ÉRTÉKŰ**. Záradék "true", ha a kiértékelt attribútum üres adja vissza.

   f. **NEM NULL**. Záradék adja vissza "true", ha a kiértékelt attribútum nem számít üresnek.

   g. **REGULÁRIS KIFEJEZÉS EGYEZTETÉSE**. Záradék "true", ha a kiértékelt attribútuma megfelel a reguláris kifejezési mintája adja vissza. Például: ([1-9][0-9]) megegyezik bármilyen szám 10-es és 99 között.

   h. **REGULÁRIS KIFEJEZÉS EGYEZTETÉSE NEM**. Záradék "true", ha a kiértékelt attribútum nem felel meg a reguláris kifejezési mintája adja vissza.

8. Válassza ki **hozzáadása új hatókörkezelési záradék**.

9. Szükség esetén ismételje meg a lépéseket 7-8 több hatókörzáradék hozzáadása.

10. A **felmerülő címe**, adja hozzá a Hatókörszűrő nevét.

11. Kattintson az **OK** gombra.

12. Válassza ki **OK** újra a **hatókörének beállítása szűrőket** képernyő. Szükség esetén ismételje meg a 6 – 11 egy másik Hatókörszűrő hozzáadása.

13. Válassza ki **mentése** a a **attribútumleképzés** képernyő. 

>[!IMPORTANT] 
> Egy új hatókörkezelési szűrő eseményindítók új teljes szinkronizálás az alkalmazáshoz, ahol a forrásrendszerben lévő összes felhasználó értékeli ki újra az új Hatókörszűrő elleni mentése. Ha egy felhasználó az alkalmazást a korábban hatókörébe tartozó kiépítés, de a hatókörén kívül esik, a fiók le van tiltva vagy – figyelmeztetés megszüntetésről az alkalmazásban.


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Alkalmazások kezelése az Azure Active Directoryban vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Felhasználói kiépítésének és megszüntetésének biztosítása az SaaS-alkalmazások automatizálása](active-directory-saas-app-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](active-directory-saas-customizing-attribute-mappings.md)
* [Kifejezések írása attribútumleképezésekhez](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Alkalmazáskiépítési értesítések](active-directory-saas-app-provisioning.md)
* [SCIM használata a felhasználók és csoportok Azure Active Directoryból származó alkalmazások automatikus kiépítésének engedélyezése](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](saas-apps/tutorial-list.md)

