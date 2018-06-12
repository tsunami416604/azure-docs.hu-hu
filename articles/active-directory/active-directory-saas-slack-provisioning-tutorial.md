---
title: 'Oktatóanyag: Slackhez konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: 'Útmutató: Azure Active Directory konfigurálása automatikusan kiépítés és deaktiválás rendelkezés felhasználói fiókokhoz slackhez.'
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
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 7037ad45680b56e26a05352416148432e053423a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292524"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Slackhez konfigurálása


Ez az oktatóanyag célja mutatjuk be, a lépéseket kell elvégeznie a Slackhez és az Azure AD automatikus kiépítés és deaktiválás rendelkezés felhasználói fiókok Azure ad-slackhez. 

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Active directory-bérlő
*   A Slackhez-bérlőben a [plusz terv](https://aadsyncfabric.slack.com/pricing) vagy jobban engedélyezve 
*   A Slackhez Team rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

Megjegyzés: Az Azure AD-integrációs kiépítés támaszkodik a [Slackhez SCIM API](https://api.slack.com/scim) Slack csoportokhoz elérhető a plusz a terv és jobb.

## <a name="assigning-users-to-slack"></a>Felhasználók hozzárendelése a Slackhez

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt kell döntenie, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az Slack alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve ezek a felhasználók rendelhet a Slack-alkalmazásba:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Felhasználók hozzárendelése a Slackhez fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzárendelni Slackhez teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése a Slackhez, ki kell választania a **felhasználói** vagy a "Csoport" szerepkör-hozzárendelés párbeszédpanelen. A "Default" szerepkör nem működik történő üzembe helyezéséhez.


## <a name="configuring-user-provisioning-to-slack"></a>A felhasználók átadása slackhez konfigurálása 

Ez a szakasz végigvezeti az Azure AD csatlakozás a Slackhez a felhasználói fiók kiépítése API, és a Slackhez, a felhasználó és csoport-hozzárendelés az Azure AD-alapú felhasználói fiókok létrehozásához, frissítéséhez és tiltsa le a létesítési szolgáltatás konfigurálása hozzárendelve.

**Tipp:** is választhatja, hogy engedélyezze a SAML-alapú egyszeri bejelentkezés a Slackhez, utasításai (az Azure portálon) [https://portal.azure.com]. Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése slackhez Azure AD-ben:


1)  Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2) Ha már konfigurált Slackhez egyszeri bejelentkezést, keresse meg a Slackhez, használja a keresőmezőt példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Slackhez** az alkalmazás katalógusában. A keresési eredmények közül válassza ki a Slackhez, és adja hozzá az alkalmazások listáját.

3)  Jelölje ki a Slackhez példányát, majd válassza ki a **kiépítési** fülre.

4)  Állítsa be a **kiépítési üzemmódját** való **automatikus**.

![Slack-kiépítés](./media/active-directory-saas-slack-provisioning-tutorial/Slack1.PNG)

5)  Az a **rendszergazdai hitelesítő adataival** kattintson **engedélyezés**. A Slack engedélyezési párbeszédpanel megnyílik egy új böngészőablakban. 

6) Az új ablakban jelentkezzen be arra a Slackhez a csapat rendszergazda fiók használatával. az eredményül kapott engedélyezési párbeszédpanelen válassza ki a Slack, amely engedélyezi a kiépítést, majd válassza ki **engedélyezés**. Ezt követően térjen vissza az Azure-portálon a létesítési konfiguráció befejezéséhez.

![Engedélyezési párbeszédpanel](./media/active-directory-saas-slack-provisioning-tutorial/Slack3.PNG)

7) Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a Slack alkalmazást. Ha nem sikerül, győződjön meg arról, a Slack fiók Team rendszergazdai jogosultságokkal rendelkezik, és ismételje meg az "Engedélyezés" lépést.

8) Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

9) Kattintson a **Save** (Mentés) gombra. 

10) A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók slackhez**.

11) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok szinkronizálva lesznek az Azure AD az Slackhez. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok felel meg a felhasználói fiókokat a Slackhez a frissítési műveletek használatával. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

12) Az Azure AD szolgáltatás a Slackhez kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

13) Kattintson a **Save** (Mentés) gombra. 

Elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban slackhez hozzárendelt csoportok. Figyelje meg, hogy a kezdeti szinkronizálás hosszabb ideig tart hajtsa végre ezt követő szinkronizálások, amely körülbelül 10 percenként történik, amíg a szolgáltatás fut-nál. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az közzététele a Slack-alkalmazás által végzett összes műveletet írják le.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Választható] Csoport objektum kiépítés slackhez konfigurálása 

Másik lehetőségként engedélyezheti a slackhez Azure ad-csoport objektumok kiépítését. Ez eltér a "felhasználói csoportok hozzárendelése", az, hogy a tényleges csoport tagjai mellett objektumot a rendszer replikálja az Azure AD Slackhez. Például ha a "Saját csoport" nevű az Azure AD-csoport, egy "Saját csoport" nevű identitical csoportot az Slackhez belül létrejön.

### <a name="to-enable-provisioning-of-group-objects"></a>Az objektumok kiépítés engedélyezése:

1) A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-csoportok slackhez**.

2) Az attribútum hozzárendelése panelen beállítása engedélyezve igen.

3) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a slackhez az Azure Active Directoryból szinkronizált csoport attribútumait. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok felel meg a frissítési műveleteket a Slackhez a csoportok használatával. 

4) Kattintson a **Save** (Mentés) gombra.

Ez bármilyen a Slackhez rendelt objektumok eredményez a **felhasználók és csoportok** Slackhez teljesen szinkronizált Azure ad-szakasz. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amelyek ismertetik a Slack app a létesítési szolgáltatás által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
