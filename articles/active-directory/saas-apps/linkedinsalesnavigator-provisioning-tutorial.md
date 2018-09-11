---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a LinkedIn Sales Navigator |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének LinkedIn Sales Navigator felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 1c5b8f2f8f8ea43e37bc65eb8f6ad03c3f198878
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347443"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása LinkedIn Sales Navigator konfigurálása


Ez az oktatóanyag célja, a lépéseket kell elvégeznie a LinkedIn Sales Navigator és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a LinkedIn Sales Navigator mutatni. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő
*   A LinkedIn Sales Navigator-bérlő 
*   Egy rendszergazdai fiók, hozzáféréssel a LinkedIn-fiókkezelési központ a LinkedIn Sales Navigator

> [!NOTE]
> Az Azure Active Directory integrálható a LinkedIn Sales Navigator használatával a [SCIM](http://www.simplecloud.info/) protokollt.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Felhasználók hozzárendelése a LinkedIn Sales Navigator

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt kell dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben a felhasználók, akik hozzáférhetnek a LinkedIn Sales Navigator jelölik. Ha úgy döntött, utasításokat követve hozzárendelheti ezeket a felhasználókat LinkedIn Sales Navigator:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Felhasználók hozzárendelése a LinkedIn Sales Navigator fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót az üzembe helyezési konfiguráció teszteléséhez a LinkedIn Sales Navigator rendelni. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése a LinkedIn Sales Navigator, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.


## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>LinkedIn Sales Navigator felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti a LinkedIn Sales Navigator SCIM felhasználói fiók üzembe helyezési API az Azure AD-csatlakozás, valamint konfigurálása az eszközkiépítési szolgáltatás létrehozása, frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a felhasználó alapján a LinkedIn Sales Navigator és csoport-hozzárendelést az Azure ad-ben.

> [!TIP]
> Választhatja azt is, a LinkedIn Sales Navigator SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható az Automatikus kiépítés függetlenül ellenére, hogy ezen két funkció kiegészíti egymást.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus fióklétesítés LinkedIn Sales Navigator az Azure ad-ben:


Az első lépés, hogy a LinkedIn-hozzáférési token lekérése. Ha egy vállalati rendszergazda engedélyezte, önálló telepíthet egy hozzáférési jogkivonatot. Lépjen az account Center webhelyen **beállítások &gt; globális beállítások** , és nyissa meg a **SCIM telepítő** panel.

> [!NOTE]
> Ha közvetlenül helyett kapcsolaton keresztül érik el az account Center webhelyen, elérhetővé válik az alábbi lépéseket követve.

1)  Jelentkezzen be az Account Center.

2)  Válassza ki **rendszergazdai &gt; adminisztrátori beállítások** .

3)  Kattintson a **speciális Integrációk** a bal oldali oldalsáv. Átirányítjuk az account Center webhelyen.

4)  Kattintson a **+ Hozzáadás új SCIM konfigurációs** , és hajtsa végre az eljárást minden mező kitöltésével.

> Autoassign licencek nincs engedélyezve, ha az azt jelenti, hogy csak a felhasználói adatok szinkronizálva van-e.

![LinkedIn Sales Navigator-kiépítés](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

> Ha autolicense hozzárendelés engedélyezve van, vegye figyelembe az alkalmazás példányát és a licenc típusa kell. Vannak licencek rendelve az első származnak, először szolgálja ki alapján, amíg megnyílik a licenceket.

![LinkedIn Sales Navigator-kiépítés](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5)  Kattintson a **jogkivonat létrehozása**. A hozzáférési jogkivonat megjelenítési alatt kell megjelennie a **hozzáférési jogkivonat** mező.

6)  A hozzáférési jogkivonatának mentéset a vágólapra vagy a számítógépre mielőtt elhagyja a lapot.

7) Ezután jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg a a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

8) Ha a LinkedIn Sales Navigator már konfigurált az egyszeri bejelentkezés, keresse meg a keresési mező használatával a LinkedIn Sales Navigator-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **LinkedIn Sales Navigator** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a LinkedIn Sales Navigator, és adja hozzá az alkalmazások listáját.

9)  Válassza ki a LinkedIn Sales Navigator-példányát, majd válassza ki a **kiépítési** fülre.

10) Állítsa be a **Kiépítési mód** való **automatikus**.

![LinkedIn Sales Navigator-kiépítés](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11)  Töltse ki a következő mezőket alatt **rendszergazdai hitelesítő adataival** :

* Az a **bérlői URL-cím** írja be a következőt https://api.linkedin.com.

* Az a **titkos jogkivonat** mezőben adja meg az 1. lépésben létrehozott jogkivonat, és kattintson a **kapcsolat tesztelése** .

* A portál eddig: upperright oldalán kell megjelennie a sikeres címtármódosítást jelző értesítés.

12) Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

13) Kattintson a **Save** (Mentés) gombra. 

14) Az a **attribútumleképezések** területen tekintse át a felhasználói és csoportattribútum, amely az Azure ad-ből LinkedIn Sales Navigator szinkronizálódnak. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok használandó felel meg a felhasználói fiókokat és csoportokat a LinkedIn Sales Navigator a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

![LinkedIn Sales Navigator-kiépítés](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15) Az Azure AD létesítési szolgáltatás a LinkedIn Sales Navigator engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

16) Kattintson a **Save** (Mentés) gombra. 

Ekkor elindul a kezdeti szinkronizálás, a felhasználók és/vagy LinkedIn Sales Navigator a felhasználók és csoportok szakaszban hozzárendelt csoportok. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hajtsa végre. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a LinkedIn Sales Navigator-alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
