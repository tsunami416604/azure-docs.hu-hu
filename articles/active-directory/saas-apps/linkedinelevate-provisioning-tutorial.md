---
title: 'Oktatóanyag: Konfigurálása LinkedIn jogosultságszint-emelés automatikus a felhasználók átadása az Azure Active Directoryhoz |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a felhasználói fiókok LinkedIn jogosultságszintjének emeléséhez.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2018
ms.author: asmalser-msft
ms.openlocfilehash: 65c57e514f7d3dbd69b963f7a7a881f034962d86
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927554"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálása LinkedIn jogosultságszint-emelés automatikus a felhasználók átadása


Ez az oktatóanyag célja a lépéseket kell elvégeznie a LinkedIn jogosultságszint-emelés és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure AD-ből való jogosultságszint-emelés LinkedIn mutatjuk be. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Directory-bérlő
*   A LinkedIn jogosultságszint-emelés bérlő 
*   A LinkedIn Account Center hozzáféréssel rendelkező LinkedIn jogosultságszint-emelés a rendszergazdai fiók

> [!NOTE]
> Az Azure Active Directory integrálható LinkedIn jogosultságszint-emelés használatával a [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-linkedin-elevate"></a>Felhasználók hozzárendelése LinkedIn jogosultságszint-emelés

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt kell döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik LinkedIn jogosultságszint-emelés elérésére. Ha úgy döntött, itt cikk utasításait követve ezeket a felhasználókat rendelhet LinkedIn jogosultságszintjének emeléséhez:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Felhasználók hozzárendelése LinkedIn jogosultságszint-emelés fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzárendelni LinkedIn jogosultságszint-emelés teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése LinkedIn jogosultságszint-emelés, ki kell választania a **felhasználói** szerepkör-hozzárendelés párbeszédpanelen. A "Default" szerepkör nem működik történő üzembe helyezéséhez.


## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Felhasználók átadására LinkedIn jogosultságszintjének emeléséhez

Ez a szakasz végigvezeti a csatlakozás az Azure AD LinkedIn jogosultságszintjének SCIM felhasználói fiókhoz kiépítés API, és a felhasználói fiókok a LinkedIn jogosultságszint-emelés alapján a felhasználó és csoport-hozzárendelés létrehozásához, frissítéséhez és tiltsa le a létesítési szolgáltatás konfigurálása hozzárendelve az Azure ad-ben.

**Tipp:** is választhatja, hogy engedélyezze SAML-alapú egyszeri bejelentkezést a LinkedIn jogosultságszint-emelés, a megjelenő utasításokat követve [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, abban az esetben, ha ez a két funkció egészítik ki egymást.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése LinkedIn jogosultságszintjének emeléséhez Azure AD-ben:


Az első lépés a LinkedIn-jogkivonatot lekérdezni. Ha a vállalati rendszergazdák, önálló megadhat egy hozzáférési jogkivonatot. A fiók központban navigáljon **beállítások &gt; globális beállítások** , és nyissa meg a **SCIM telepítő** panel.

> [!NOTE]
> Ha az account center érik el, hanem közvetlenül mutató hivatkozás, csatlakozni tud hozzá az alábbi lépéseket követve.

1)  Jelentkezzen be Center fiók.

2)  Válassza ki **Admin &gt; rendszergazdai beállítások** .

3)  Kattintson a **speciális integrációja** a bal oldali oldalsávon. Az account center van irányítva.

4)  Kattintson a **+ Hozzáadás új SCIM konfigurációs** és hajtsa végre az eljárást minden mező kitöltésével.

> Autoassign licencek nincs engedélyezve, az azt jelenti, hogy csak a felhasználói adatok van-e szinkronizálva.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

> Ha autolicense hozzárendelés engedélyezve van, jegyezze fel az alkalmazáspéldány és licenc típusa kell. Elsőként érkezett a hozzárendelt licenceket, először kiszolgálására alapján, addig, amíg a licencek kerül.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5)  Kattintson a **Generate token**. A hozzáférési token megjelenítési alatt kell megjelennie a **hozzáférési jogkivonat** mező.

6)  Az oldal elhagyása előtt mentse a vágólapra vagy a számítógép a hozzáférési jogkivonat.

7) Ezután jelentkezzen be a [Azure-portálon](https://portal.azure.com), és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

8) Ha már konfigurált LinkedIn jogosultságszint-emelés egyszeri bejelentkezést, keresse meg a LinkedIn jogosultságszint-emelés használja a keresőmezőt példányát. Máskülönben válassza **Hozzáadás** keresse meg a **LinkedIn jogosultságszint-emelés** az alkalmazás katalógusában. Válassza ki a LinkedIn jogosultságszint-emelés a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

9)  Jelölje ki a LinkedIn jogosultságszint-emelés példányát, majd válassza ki a **kiépítési** fülre.

10) Állítsa be a **kiépítési üzemmódját** való **automatikus**.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11)  Töltse ki a következő mezőket a **rendszergazdai hitelesítő adataival** :

* Az a **bérlői URL-cím** mezőbe írja be https://api.linkedin.com.

* Az a **titkos Token** mezőben adja meg az 1. lépésben létrehozott jogkivonat, és kattintson a **kapcsolat tesztelése** .

* A portál upperright oldalán egy sikeres következő értesítésnek kell megjelennie.

12) Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

13) Kattintson a **Save** (Mentés) gombra. 

14) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói és csoportattribútum LinkedIn jogosultságszintjének emeléséhez az Azure Active Directoryból szinkronizált. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok felel meg a felhasználói fiókok és csoportok LinkedIn jogosultságszint-emelés frissítés műveletekhez használható. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

![LinkedIn jogosultságszint-emelés kiépítése](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15) Az Azure AD szolgáltatás LinkedIn jogosultságszint-emelés kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

16) Kattintson a **Save** (Mentés) gombra. 

Elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy csoportok rendelt LinkedIn jogosultságszint-emelés a felhasználók és csoportok szakaszban. Figyelje meg, hogy a kezdeti szinkronizálás hosszabb ideig tart hajtsa végre ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek ismertetik a LinkedIn jogosultságszint-emelés app a létesítési szolgáltatás által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
