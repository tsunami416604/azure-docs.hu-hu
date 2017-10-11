---
title: "Az Azure AD-attribútum-leképezésekhez testreszabása |} Microsoft Docs"
description: "Ismerje meg, milyen attribútum-leképezésekhez SaaS-alkalmazásokhoz az Azure Active Directoryban, hogyan módosíthatja azokat az üzleti igények kielégítéséhez."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Attribútum-leképezésekhez kiépítés az SaaS-alkalmazásokhoz az Azure Active Directory felhasználói testreszabása
Microsoft Azure AD támogatást nyújt a felhasználók átadása, például a Salesforce, a Google Apps és a többi külső SaaS-alkalmazásokban. Ha egy külső SaaS-alkalmazáshoz engedélyezett kiépítés felhasználó, az Azure felügyeleti portálon szabályozza az űrlap konfiguráció "címtárattribútum-leképezésben." nevű attribútum értékei

Nincs olyan előre konfigurált attribútum-leképezésekhez az Azure AD felhasználói és minden SaaS-alkalmazás felhasználói objektumok között. Egyes alkalmazások más típusú objektumok, például a csoportok vagy névjegyek kezelése. <br> 
 Az alapértelmezett attribútum-leképezésekhez igényeinek megfelelően testre. Ez azt jelenti, módosítsa vagy törölje a meglévő attribútum-leképezésekhez vagy hozzon létre új attribútum-leképezésekhez.

Az Azure AD portálon érhető el ez a szolgáltatás kattint egy **hozzárendelések** konfigurációja **kiépítési** a a **kezelése** szakasza egy  **A vállalati alkalmazás**.


![Salesforce][5] 

Kattintson egy **hozzárendelések** konfigurációs, megnyílik a kapcsolódó **attribútum leképezési** panelen.  
Nincsenek attribútum-leképezésekhez egy SaaS-alkalmazáshoz megfelelő működéséhez szükséges. A szükséges attribútumokat a **törlése** szolgáltatás nem érhető el.


![Salesforce][6]  

A fenti példában láthatja, hogy a **felhasználónév** attribútum egy felügyelt objektum a Salesforce-ban a telepítéskor a **userPrincipalName** érték a csatolt Azure Active Directory objektum.

Testre szabhat meglévő **attribútum-leképezésekhez** leképezéseket kattintva. Ekkor megnyílik a **attribútum szerkesztése** panelen.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Attribútum-leképezés típusainak ismertetése
Az attribútum-leképezésekhez meghatározhatja, hogyan attribútumok megjelenik a külső SaaS-alkalmazáshoz. Számos négy különböző hozzárendelési típusokhoz támogatott.

* **Közvetlen** – a target attribútummal fel van töltve a csatolt objektum attribútum értékét az Azure ad-ben.
* **Állandó** – a target attribútum a telepítéskor megadott egy adott karakterláncot.
* **Kifejezés** -a target attribútum egy parancsfájl-szerű kifejezés eredménye alapján van feltöltve. 
  További információkért lásd: [írás kifejezések az Azure Active Directoryban attribútum-leképezésekhez](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nincs** -marad a cél attribútumának változatlan. Azonban a célattribútum valaha is üres, ha a telepítéskor, amely az alapértelmezett érték.

Ezek négy alapvető attribútum hozzárendelési típusokhoz mellett egyéni attribútum-leképezésekhez támogatja egy nem kötelező fogalma **alapértelmezett** érték-hozzárendelés. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a target attribútummal megadni értéket, ha nincs sem értéket, és a célobjektum, nem az Azure ad-ben. A leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.


## <a name="understanding-attribute-mapping-properties"></a>Leképezési attribútumtulajdonságok ismertetése

Az előző szakaszban akkor már vezettek attribútum leképezés típusa tulajdonság.
Mellett ez a tulajdonság a attribútum-leképezésekhez is támogatja a következő attribútumokat:

- **Adatforrás-attribútum** -a felhasználói attribútum a forrásrendszerből (pl.: Azure Active Directory).
- **Cél attribútumának** – a felhasználói attribútum a célrendszeren (pl.: a ServiceNow).
- **Ezzel az attribútummal objektumok megfelelő** – függetlenül attól, ez a leképezés kell használni a forrás és cél közötti felhasználók egyedi azonosítására. Ez általában be van állítva a userPrincipalName vagy mail attribútum az Azure AD, amely általában egy felhasználónév mező a célalkalmazásban van hozzárendelve.
- **Megfelelő sorrend** – több egyező attribútumok állítható be. Ha több, kiértékelésük, ez a mező által megadott sorrendben. Amint a program egyezést talál, további megfelelő attribútumok kiértékelése.
- **Ez a leképezés alkalmazása**
    - **Mindig** – Ez a leképezés alkalmazni mind a felhasználó létrehozása és a műveletek frissítése
    - **Csak létrehozásakor** – Ez a leképezés csak a felhasználó létrehozási műveletek alkalmazása


## <a name="what-you-should-know"></a>Tudnivalók

Microsoft Azure AD egy hatékony megvalósítása a szinkronizálási folyamat nyújt. Inicializált környezetben csak a frissítést igénylő objektumok feldolgozása során szinkronizálási ciklust. Attribútum-leképezésekhez frissítése szinkronizálási ciklust teljesítményére hatással van. Az leképezési konfigurációjának frissítése valamennyi felügyelt objektum reevaluated kell igényel. Javasoljuk, hogy az attribútum-leképezésekhez minimális egymást követő módosítások számát is.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Felhasználói létesítési vagy megszüntetési SaaS-alkalmazásokhoz való automatizálásához](active-directory-saas-app-provisioning.md)
* [Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](active-directory-scim-provisioning.md)
* [Alkalmazás-kiépítési értesítések](active-directory-saas-account-provisioning-notifications.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

