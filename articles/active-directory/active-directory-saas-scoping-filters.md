---
title: "Alkalmazások helyezése hatókörszűrőkkel kiépítése |} Microsoft Docs"
description: "Megtudhatja, hogyan objektumok alkalmazások automatizált felhasználókiépítése létre, ha az objektum nem elégíti ki az üzleti igényeknek a támogató hatókörének meghatározásához szűrők használata."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7a2322239945a529a544054c2273e37a3d65abf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Alkalmazások Attribútumalapú üzembe helyezése hatókörszűrőkkel
Ez a cikk célja tartalmazó szűrők használatát, amelyek meghatározzák, hogy mely felhasználók egy alkalmazás kiépített Attribútumalapú szabályok meghatározásához.

## <a name="scoping-filter-use-cases"></a>Hatókörére szűrő használati esetek

A hatókört szűrő segítségével az Azure Active Directory (Azure AD) szolgáltatás kiépítését vagy kizárja a bármely olyan attribútum, amely megfelel a megadott értékkel rendelkező felhasználók. Például amikor egy értékesítési csoport által használt SaaS-alkalmazás az Azure AD felhasználók átadása, megadhatja, hogy csak a felhasználók az "Értékesítési" a "Részleg" attribútummal rendelkező kialakítási hatókörében kell lennie.

Helyezése hatókörszűrőkkel használható másképp létesítési összekötő típusától függően:

* **Kimenő kiépítése az Azure AD SaaS-alkalmazásokhoz való**. Ha az Azure AD a forrásrendszerben [felhasználó és csoport-hozzárendelések](active-directory-coreapps-assign-user-azure-portal.md) a leggyakrabban használt módszer meghatározásához, hogy mely felhasználók vannak üzembe helyezéséhez hatókörében van. A hozzárendelések is engedélyezése egyszeri bejelentkezéshez használt, és kezelheti a hozzáférést és üzembe helyezését egy módszert biztosít. Helyezése hatókörszűrőkkel segítségével szükség esetén hozzárendelések mellett vagy helyett, szűrhetők a felhasználók attribútumértékei alapján.

    >[!TIP]
    > Kiépítés beállításait egy vállalati alkalmazás-hozzárendelései alapján bármikor letilthatja a [hatókör](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) a telepítési beállítások menüt **minden felhasználó és csoport szinkronizálása**. Ez a beállítást és tartalmazó szűrők Attribútumalapú biztonságicsoport-alapú hozzárendelések használnak, mint gyorsabb teljesítményt nyújt.  

* **Bejövő történő átadása HCM alkalmazások az Azure AD és az Active Directory**. Ha egy [HCM alkalmazásadatokat, például a Workday](active-directory-saas-workday-tutorial.md) forrás rendszert tartalmazó szűrők a következők az elsődleges módszer meghatározásához, hogy mely felhasználók kell építhető ki az Active Directory vagy az Azure AD HCM alkalmazásból.

Alapértelmezés szerint az Azure AD létesítési összekötők nincs konfigurálva tartalmazó Attribútumalapú szűrők. 

## <a name="scoping-filter-construction"></a>Konstrukció. szűrő hatókörének meghatározásához.

Hatókörként szűrőt tartalmaz egy vagy több *záradékok*. Záradékok határozza meg, hogy mely felhasználók számára engedélyezett az áthaladás, minden felhasználói attribútumok kiértékelésével tartalmazó szűrőt. Például lehetséges, hogy egy feltételt, amelyhez az, hogy egy felhasználó "Állapot" attribútum értéke "New York", így csak a győri felhasználók törlődnek az alkalmazásba. 

Egyetlen záradék egy egyetlen attribútum értéke egyetlen feltételt határoz meg. Több záradékot tartalmazó szűrőben jönnek létre, ha azok még kiértékelése együtt pedig "AND" logikát. Ez azt jelenti, hogy minden záradékok ki kell értékelnie minden "true"értékre a rendelés egy felhasználó úgy kell létrehozni.

Végezetül több tartalmazó szűrő egyetlen alkalmazás hozható létre. Több tartalmazó szűrők jelen, ha azok még kiértékelése együtt pedig "Vagy" logikát. Ez azt jelenti, hogy a konfigurált hatókörének meghatározásához szűrők valamelyikében a záradékok kiértékelni a "true"értékre, ha a felhasználó ki van építve.

Minden felhasználó vagy csoport az Azure AD-létesítési szolgáltatás által feldolgozott mindig képest értékeli ki egyenként mindegyik tartalmazó szűrő.

Tegyük fel vegye figyelembe a következő tartalmazó szűrő:

![Hatókör-beállítási szűrője](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

A hatókört szűrő megfelelően felhasználók úgy kell létrehozni a következő feltételeknek kell megfelelnie:

* Győri kell lennie.
* A mérnöki csapathoz részleg kell működnek.
* A vállalat alkalmazott azonosítója 1 000 000 és 2,000,000 között kell lennie.
* A feladat-cím nem lehet null vagy üres.

## <a name="create-scoping-filters"></a>Hatókörként szűrők létrehozása
Hatókörként szűrőit, minden Azure AD-felhasználó összekötő kiépítés attribútum leképezéseit részeként. A következő eljárás feltételezi, hogy korábban már beállított automatikus kiépítés [a támogatott alkalmazások közül](active-directory-saas-tutorial-list.md) és az azt tartalmazó szűrőt ad hozzá.

### <a name="create-a-scoping-filter"></a>Hatókörként szűrő létrehozása
1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** szakasz.

2. Válassza ki az alkalmazást, amelynek konfigurált automatikus kiépítés: például: "ServiceNow".

3. Válassza ki a **kiépítési** fülre.

4. Az a **hozzárendelések** területen válassza ki a leképezést, amely tartalmazó szűrőt beállítani kívánt: például "szinkronizálása Azure Active Directory felhasználók számára a ServiceNow".

5. Válassza ki a **objektum hatóköre forrás** menü.

6. Válassza ki **tartalmazó szűrő hozzáadásához**.

7. Állítsa a záradékot a megfelelő forrás **attribútumnév**, egy **operátor**, és egy **attribútumérték** az egyeztetéshez. Az alábbi műveleteket támogatja:

   a. **EGYENLŐ**. Záradék visszaadja az "true", ha a kiértékelt attribútum megegyezik-e a bemeneti karakterlánc pontosan (kis-és nagybetűket).

   b. **NEM EGYENLŐ**. Záradék "true", ha a kiértékelt attribútum nem egyezik meg a bemeneti karakterlánc (kis-és nagybetűket) értéket ad vissza.

   c. **IGAZ**. Záradék "true", ha a kiértékelt attribútum IGAZ logikai értéket adja vissza.

   d. **HAMIS**. Záradék "true", ha a kiértékelt attribútum tartalmazza a HAMIS logikai értéket adja vissza.

   e. **NULL ÉRTÉKŰ**. Záradék "true", ha a kiértékelt attribútum üres adja vissza.

   f. **ÉRTÉKE NEM NULL**. Záradék "true", ha a kiértékelt attribútum nem üres adja vissza.

   g. **REGULÁRIS KIFEJEZÉSSEL EGYEZŐ**. Záradék "true", ha a kiértékelt attribútum egyezik egy reguláris kifejezési minta adja vissza. Példa: ([1-9][0-9]) megfelel minden szám 10-es és 99 között.

   h. **NEM REGULÁRIS KIFEJEZÉSSEL EGYEZŐ**. Záradék "true", ha a kiértékelt attribútum nem egyezik meg a reguláris kifejezési minta adja vissza.

8. Válassza ki **hozzáadása új hatókört záradék**.

9. Szükség esetén ismételje meg a lépéseket 7-8 hozzáadása további tartalmazó záradékot.

10. A **hatókörére szűrő cím**, vegye fel a tartalmazó szűrő nevét.

11. Kattintson az **OK** gombra.

12. Válassza ki **OK** meg újra a **hatókörének szűrők** képernyő. Ha szükséges ismételje meg a 6-11 egy másik hatókört szűrő hozzáadásához.

13. Válassza ki **mentése** a a **attribútum leképezési** képernyő. 

>[!IMPORTANT] 
> Egy új hatókört szűrő eseményindítók mentése az alkalmazás, ahol minden felhasználó a forrásrendszerben értékeli ki a rendszer újra az új hatókört szűrő új teljes szinkronizálást. Ha a felhasználó az alkalmazás korábban hatókörébe kiépítés, de a hatókörén kívül esik, a fiók le van tiltva vagy az alkalmazás platformelőfizetés.


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Alkalmazások kezelése az Azure Active Directoryban cikk indexe](active-directory-apps-index.md)
* [Kiépítés és megszüntetés SaaS-alkalmazásokhoz való felhasználói automatizálásához](active-directory-saas-app-provisioning.md)
* [Testre szabhatja a felhasználók átadása attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md)
* [Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Alkalmazás-kiépítési értesítések](active-directory-saas-account-provisioning-notifications.md)
* [SCIM használata a felhasználók és csoportok az Azure Active Directory alkalmazások automatikus kiépítés engedélyezése](active-directory-scim-provisioning.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)

