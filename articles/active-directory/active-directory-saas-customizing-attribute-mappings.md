---
title: Az Azure AD-attribútum-leképezésekhez testreszabása |} Microsoft Docs
description: Ismerje meg, milyen attribútum-leképezésekhez SaaS-alkalmazásokhoz az Azure Active Directoryban, hogyan módosíthatja azokat az üzleti igények kielégítéséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7dad9f3e688c43de3eabd430bf5618ad4632ca3d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036097"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Attribútum-hozzárendelések kiépítés az SaaS-alkalmazásokhoz az Azure Active Directory felhasználói testreszabása
Microsoft Azure AD támogatást nyújt a felhasználók átadása, például a Salesforce, a Google Apps és a többi külső SaaS-alkalmazásokban. Ha egy külső SaaS-alkalmazáshoz engedélyezve van a felhasználók átadása, az Azure-portálon szabályozza az attribútumértékek attribútum-megfeleltetéseket formában.

Nincs olyan előre konfigurált és a attribútum-hozzárendelések az Azure AD felhasználói és minden SaaS-alkalmazás felhasználói objektumok között. Egyes alkalmazások más típusú objektumokat, felhasználókon, például a csoportok kezelése. <br> 
 Az alapértelmezett attribútum-hozzárendelések igényeinek megfelelően testre. Ez azt jelenti, módosítsa vagy törölje a meglévő attribútum-hozzárendelések, vagy új attribútum-hozzárendelések létrehozása.
 
## <a name="editing-user-attribute-mappings"></a>Attribútum-felhasználóleképezéseket szerkesztése

Az Azure AD portálon érhető el ez a szolgáltatás kattint egy **hozzárendelések** konfigurációja **kiépítési** a a **kezelése** szakasza egy  **A vállalati alkalmazás**.


![Salesforce][5] 

Kattintson egy **hozzárendelések** konfigurációs, megnyílik a kapcsolódó **attribútum-leképezés** képernyő. Nincsenek attribútum-hozzárendelések egy SaaS-alkalmazáshoz megfelelő működéséhez szükséges. A szükséges attribútumokat a **törlése** szolgáltatás nem érhető el.


![Salesforce][6]  

A fenti példában láthatja, hogy a **felhasználónév** attribútum egy felügyelt objektum a Salesforce-ban a telepítéskor a **userPrincipalName** érték a csatolt Azure Active Directory objektum.

Testre szabhat meglévő **attribútum-hozzárendelések** leképezéseket kattintva. Ekkor megnyílik a **attribútum szerkesztése** képernyő.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>Attribútum-leképezés típusainak ismertetése
Az attribútum-hozzárendelések meghatározhatja, hogyan attribútumok megjelenik a külső SaaS-alkalmazáshoz. Számos négy különböző hozzárendelési típusokhoz támogatott.

* **Közvetlen** – a target attribútummal fel van töltve a csatolt objektum attribútum értékét az Azure ad-ben.
* **Állandó** – a target attribútum a telepítéskor megadott egy adott karakterláncot.
* **Kifejezés** -a target attribútum egy parancsfájl-szerű kifejezés eredménye alapján van feltöltve. 
  További információkért lásd: [írása attribútum-hozzárendelések az Azure Active Directoryban a kifejezéseket](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nincs** -marad a cél attribútumának változatlan. Azonban a célattribútum valaha is üres, ha a telepítéskor, amely az alapértelmezett érték.

Négy alapvető típusaival mellett egyéni attribútum-hozzárendelések támogatja a nem kötelező fogalma **alapértelmezett** érték-hozzárendelés. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a target attribútummal megadni értéket, ha nincs sem értéket, és a célobjektum, nem az Azure ad-ben. A leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.


### <a name="understanding-attribute-mapping-properties"></a>Attribútum-leképezés tulajdonságok ismertetése

Az előző szakaszban akkor már bevezetett attribútum-leképezés típusa tulajdonság.
Mellett ez a tulajdonság a attribútum-hozzárendelések is támogatja a következő attribútumokat:

- **Adatforrás-attribútum** -a felhasználói attribútum a forrásrendszerből (Példa: Azure Active Directory).
- **Cél attribútumának** – a felhasználói attribútum a célrendszeren (Példa: a ServiceNow).
- **Ezzel az attribútummal objektumok megfelelő** – függetlenül attól, ez a leképezés kell használni a forrás és cél közötti felhasználók egyedi azonosítására. Ez általában be van állítva a userPrincipalName vagy mail attribútum az Azure AD, amely általában egy felhasználónév mező a célalkalmazásban van hozzárendelve.
- **Megfelelő sorrend** – több egyező attribútumok állítható be. Ha több, kiértékelésük, ez a mező által megadott sorrendben. Amint a program egyezést talál, további megfelelő attribútumok kiértékelése.
- **Ez a leképezés alkalmazása**
    - **Mindig** – Ez a leképezés alkalmazni mind a felhasználó létrehozása és a műveletek frissítése
    - **Csak létrehozásakor** – Ez a leképezés csak a felhasználó létrehozási műveletek alkalmazása


## <a name="editing-group-attribute-mappings"></a>Csoport attribútum-hozzárendelések szerkesztése

Alkalmazások, például a ServiceNow, jelölje be, és a Google Apps, kijelölt száma is támogatja a felhasználói objektumok mellett csoportházirend-objektumok létrehozásához. Objektumok is tartalmaz a csoport tulajdonságai, például a megjelenített nevek és e-mail-aliasok csoporttagok mellett.

![ServiceNow][8]  

Csoport kiépítés is opcionálisan engedélyezhető vagy letiltható az eszközcsoport-leképezéssel kiválasztásával **hozzárendelések**, és a beállítás **engedélyezve** kívánt beállításnak a **attribútum-leképezés** képernyő.

Az objektumok részeként kiépített attribútumok testre szabható a felhasználói objektumok, a fentiekben ismertetett azonos módon. 

>[!TIP]
>Objektumok (tulajdonságait és tagok) egy különböző fogalom a [csoportok hozzárendelése](manage-apps/assign-user-or-group-access-portal.md) egy alkalmazáshoz. Egy csoport hozzárendelése egy alkalmazás, de csak kiépíteni a felhasználói objektumok a csoportban lévő lehetőség. A teljes csoport objektumok kiépítése nem használatához szükséges csoportok hozzárendelésekben.


## <a name="editing-the-list-of-supported-attributes"></a>Támogatott attribútumok listáját szerkesztése

A felhasználó egy adott alkalmazáshoz támogatott attribútumok előre konfigurált. A legtöbb alkalmazás felhasználói felügyeleti API-k nem támogatják a séma felderítési, ezért az Azure AD szolgáltatás kiépítését nem képes dinamikusan generálni a hívások az alkalmazás által támogatott attribútumok listáját. 

Azonban az egyes alkalmazások támogatják az egyéni attribútumok. Ahhoz, hogy az Azure AD szolgáltatás kiépítését lehet olvasni és írni az egyéni attribútumok, a definícióikat meg kell adni az Azure portál használatával a **speciális beállítások megjelenítése** alján található jelölőnégyzetet az  **Attribútum-leképezés** képernyő.

Alkalmazások és a rendszer támogatja az attribútumlista testreszabása a következők:

* Salesforce
* ServiceNow
* Munkanapok
* Az Azure Active Directory ([Azure AD Graph API alapértelmezett attribútumok](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) és egyéni címtárbővítmények támogatott)
* Támogató alkalmazások [SCIM 2.0](https://tools.ietf.org/html/rfc7643), ahol az attribútumok meghatározott a [core séma](https://tools.ietf.org/html/rfc7643) hozzá kell adni

>[!NOTE]
>Támogatott attribútumok listáját szerkesztése csak ajánlott a rendszergazdák, akik az alkalmazásaikat és rendszerek sémája testreszabott, és az egyéni attribútumok meghatározott hogyan első aktuális ismerete. Egyes esetekben ehhez a egy alkalmazás vagy a rendszer által biztosított API-k és fejlesztői eszközök ismeretét. 

![Szerkesztő][9]  

Támogatott attribútumok listáját szerkesztésekor találhatók a következő tulajdonságokkal:

* **Név** – a rendszer annak a nevét, a célobjektum sémában meghatározott elemnévvel. 
* **Típus** – az attribútum tárolja, a célobjektum sémában meghatározott elemnévvel adatok típusát. Ez a következők egyike lehet:
   * *Bináris* -attribútum bináris adatokat tartalmaz.
   * *Logikai* -attribútum igaz vagy hamis értéket tartalmaz.
   * *Dátum és idő* -attribútum dátum karakterláncot tartalmaz.
   * *Egész* -attribútum egy egész számot tartalmaz.
   * *Hivatkozás* -attribútum egy másik tábla a célalkalmazásban tárolt érték hivatkozó-Azonosítót tartalmaz.
   * *Karakterlánc* -attribútumot tartalmaz egy szöveges karakterlánc. 
* **Elsődleges kulcsát?** -Az attribútum e a célobjektum sémában elsődleges kulcsmezőként van definiálva.
* **Kötelező megadni?** -E a attribútum esetén a célalkalmazás vagy a rendszer feltöltését.
* **Többértékű?** -Az attribútum e több értéket támogatja.
* **Kis-és nagybetűket?** -Az attribútumok értékek e a kis-és nagybetűket módon kell kiértékelni.
* **API-kifejezés** – ne használja, kivéve, ha kifejezetten kéri a dokumentáció (például a Workday) egy adott üzembe helyezési összekötő.
* **A hivatkozott objektum attribútum** – Ha ez egy hivatkozási attribútum, akkor ebben a menüben válassza ki a tábla és egy attribútum, amely tartalmazza a rendelt érték, amely az attribútum a célalkalmazásban teszi. Például ha amelynek tárolt érték egy külön táblázatban "Részleg" objektumra hivatkozik, az "részleg" nevű attribútumot, kiválasztott "Departments.Name". Vegye figyelembe, hogy a referencia-táblák és a támogatott egy adott alkalmazás elsődleges azonosítója mezők vannak előre konfigurálva van és jelenleg nem szerkeszthető, az Azure portál használatával, de használatával szerkeszthetők a [Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Új attribútum hozzáadásához, támogatott attribútumok listájának végére, fent a megadott bemeneti adatok mezőinek feltöltéséhez, görgetve válassza ki **attribútum hozzáadása**. Válassza ki **mentése** attribútumok hozzáadása befejezésekor. Majd szüksége lesz a töltse be újra a **kiépítési** lap az új attribútumok az attribútum-hozzárendelési szerkesztőt az elérhető legyen.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Az alapértelmezett attribútumokat és attribútum-hozzárendelések visszaállítása

Meg kell keresztül kezdő és a meglévő leképezéseket biztonsági másolatot az alapértelmezett állapotba alaphelyzetbe állítása, kiválaszthatja a **visszaállítása az alapértelmezett hozzárendelések** jelölje be a jelölőnégyzetet, és a konfiguráció mentéséhez. Ez beállítja összes leképezés, ha az alkalmazás csak hozzáadott az Azure AD-bérlő számára az alkalmazás gyűjteményből. 

Ezzel a beállítással hatékonyan kényszerítheti a felhasználók újra be kell szinkronizálást, a létesítési szolgáltatás futása közben. 

>[!IMPORTANT]
>Erősen ajánlott, amelyek **előkészítési állapotát** állítható **ki** Ez a beállítás meghívása előtt.


## <a name="what-you-should-know"></a>Tudnivalók

* Microsoft Azure AD egy hatékony megvalósítása a szinkronizálási folyamat nyújt. Inicializált környezetben csak a frissítést igénylő objektumok feldolgozása során szinkronizálási ciklust. 

* Attribútum-leképezések frissítése szinkronizálási ciklust teljesítményére hatással van. Az attribútum-hozzárendelési konfigurációja frissítése reevaluated kell az összes kezelt objektum szükséges. 

* Javasoljuk, hogy a minimális attribútum-leképezései egymást követő módosítások számát is.


## <a name="next-steps"></a>További lépések

* [Felhasználói létesítési vagy megszüntetési SaaS-alkalmazásokhoz való automatizálásához](active-directory-saas-app-provisioning.md)
* [Attribútum-hozzárendelések kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](manage-apps/use-scim-to-provision-users-and-groups.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](saas-apps/tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

