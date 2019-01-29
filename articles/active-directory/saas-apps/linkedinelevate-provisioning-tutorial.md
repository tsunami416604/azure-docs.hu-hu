---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a LinkedIn szintjének emelése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének LinkedIn megszereznie a felhasználói fiókok.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2018
ms.author: asmalser-msft
ms.openlocfilehash: bff7b819bfe0d0a2bd62b4b6452fe64b6c12beee
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182625"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a LinkedIn jogosultságszint-emelés beállítása


Ez az oktatóanyag célja, a lépéseket kell elvégeznie a LinkedIn jogosultságszint-emelés és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből való jogosultságszint-emelés LinkedIn mutatni. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő
*   A LinkedIn jogosultságszint-emelés bérlőt 
*   Egy rendszergazdai fiók, a LinkedIn-szintjének emelése a LinkedIn-fiókkezelési központ a hozzáférést

> [!NOTE]
> Az Azure Active Directory integrálható a LinkedIn jogosultságszint-emelés használata a [SCIM](http://www.simplecloud.info/) protokollt.

## <a name="assigning-users-to-linkedin-elevate"></a>Felhasználók hozzárendelése LinkedIn szintjének emelése

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt kell dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben a felhasználók, akik hozzáférhetnek a LinkedIn jogosultságszint-emelés jelölik. Ha úgy döntött, hozzárendelheti ezeket a felhasználókat LinkedIn jogosultságszint-emelés utasításokat követve:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Felhasználók hozzárendelése LinkedIn jogosultságszint-emelés fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót rendelni LinkedIn jogosultságszint-emelés az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése LinkedIn jogosultságszint-emelés, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.


## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>LinkedIn jogosultságszint-emelés történő felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD-csatlakozás LinkedIn jogosultságszintjének SCIM felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, frissítése és letiltását konfigurálása hozzárendelt felhasználói fiókok a LinkedIn jogosultságszint-emelés felhasználó és csoport-hozzárendelés alapján az Azure ad-ben.

**Tipp:** Választhatja azt is, LinkedIn jogosultságszint-emelés SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható az Automatikus kiépítés függetlenül ellenére, hogy ezen két funkció kiegészíti egymást.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése LinkedIn megszereznie az Azure ad-ben:


Az első lépés, hogy a LinkedIn-hozzáférési token lekérése. Ha egy vállalati rendszergazda engedélyezte, önálló telepíthet egy hozzáférési jogkivonatot. Lépjen az account Center webhelyen **beállítások &gt; globális beállítások** , és nyissa meg a **SCIM telepítő** panel.

> [!NOTE]
> Ha közvetlenül helyett kapcsolaton keresztül érik el az account Center webhelyen, elérhetővé válik az alábbi lépéseket követve.

1)  Jelentkezzen be az Account Center.

2)  Válassza ki **rendszergazdai &gt; adminisztrátori beállítások** .

3)  Kattintson a **speciális Integrációk** a bal oldali oldalsáv. Átirányítjuk az account Center webhelyen.

4)  Kattintson a **+ Hozzáadás új SCIM konfigurációs** , és hajtsa végre az eljárást minden mező kitöltésével.

> Autoassign licencek nincs engedélyezve, ha az azt jelenti, hogy csak a felhasználói adatok szinkronizálva van-e.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

> Ha autolicense hozzárendelés engedélyezve van, vegye figyelembe az alkalmazás példányát és a licenc típusa kell. Vannak licencek rendelve az első származnak, először szolgálja ki alapján, amíg megnyílik a licenceket.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5)  Kattintson a **jogkivonat létrehozása**. A hozzáférési jogkivonat megjelenítési alatt kell megjelennie a **hozzáférési jogkivonat** mező.

6)  A hozzáférési jogkivonatának mentéset a vágólapra vagy a számítógépre mielőtt elhagyja a lapot.

7) Ezután jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg a a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

8) Ha már konfigurált LinkedIn jogosultságszint-emelés az egyszeri bejelentkezés, keresse meg a LinkedIn jogosultságszint-emelés a keresőmező használatával példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **LinkedIn jogosultságszint-emelés** az alkalmazás-katalógusában. Válassza ki a LinkedIn-szintjének emelése a keresési eredmények között, és adja hozzá az alkalmazások listáját.

9)  Válassza ki a LinkedIn jogosultságszint-emelés példányát, majd válassza ki a **kiépítési** fülre.

10) Állítsa be a **Kiépítési mód** való **automatikus**.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11)  Töltse ki a következő mezőket alatt **rendszergazdai hitelesítő adataival** :

* Az a **bérlői URL-cím** írja be a következőt https://api.linkedin.com.

* Az a **titkos jogkivonat** mezőben adja meg az 1. lépésben létrehozott jogkivonat, és kattintson a **kapcsolat tesztelése** .

* A portál eddig: upperright oldalán kell megjelennie a sikeres címtármódosítást jelző értesítés.

12) Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

13) Kattintson a **Save** (Mentés) gombra. 

14) Az a **attribútumleképezések** területen tekintse át a LinkedIn jogosultságszint-emelés Azure AD-ből szinkronizált felhasználói és csoportattribútum. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok használandó felel meg a felhasználói fiókokat és csoportokat a LinkedIn jogosultságszint-emelés a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15) Az Azure AD létesítési szolgáltatás a LinkedIn jogosultságszint-emelés engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

16) Kattintson a **Save** (Mentés) gombra. 

Ekkor elindul a kezdeti szinkronizálás, a felhasználók és/vagy LinkedIn megszereznie a felhasználók és csoportok szakaszban hozzárendelt csoportok. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hajtsa végre. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a LinkedIn jogosultságszint-emelés alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
