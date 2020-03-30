---
title: 'Oktatóanyag: Jutalomátjáró konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat a Reward Gateway szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060995"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Oktatóanyag: Jutalomátjáró konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Jutalom átjáró és az Azure Active Directory (Azure AD) végrehajtásához szükséges lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok automatikus kiépítéséhez és de-provision-hoz a Reward Gateway.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* A [Jutalom átjáró bérlője.](https://www.rewardgateway.com/)
* A Jutalomátjáró rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-reward-gateway"></a>Felhasználók hozzárendelése a Jutalom átjáróhoz 

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Jutalomátjáróhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Jutalomátjáróhoz, ha követi a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című útmutató utasításait.


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Fontos tippek a felhasználók jutalomátjáróhoz való hozzárendeléséhez 

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Jutalom átjáró az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Jutalomátjáróhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-reward-gateway--for-provisioning"></a>Telepítési jutalomátjáró a kiépítéshez
A Jutalomátjáró konfigurálása az Azure AD automatikus felhasználói kiépítéséhez engedélyeznie kell az SCIM-kiépítést a Jutalomátjárón.

1. Jelentkezzen be a [Reward Gateway Admin Console -ba.](https://rewardgateway.photoshelter.com/login/) Kattintson **az Integrációk gombra.**

    ![Jutalomátjáró felügyeleti konzolja](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Válassza **a Saját integráció lehetőséget.**

    ![Jutalomátjáró felügyeleti konzolja](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Másolja az **SCIM URL-cím (v2)** és az **OAuth bearer token értékeit.** Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőjében lesznek megadva a Jutalomátjáró-alkalmazás Kiépítés lapján az Azure Portalon.

    ![Jutalomátjáró felügyeleti konzolja](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Jutalomátjáró hozzáadása a galériából

A Jutalom átjáró konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Jutalom átjárót az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Jutalomátjáró hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Jutalomátjáró**kifejezést, válassza a **Jutalomátjáró** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Jutalom átjáró az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Automatikus felhasználói kiépítés konfigurálása a Jutalom átjáróhoz  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Jutalomátjáróban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Reward Gateway-hez, a [Reward Gateway Egyszeri bejelentkezési oktatóanyag](reward-gateway-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>A Jutalomátjáró automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Jutalomátjáró**lehetőséget.

    ![A Jutalom átjáró hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg az **SCIM URL-címét (v2)** és **az OAuth bearer token** értékeket a **bérlői URL-címés** a **titkos jogkivonat** korábban. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozhasson az átjáró jutalmazásához. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a jutalomátjáró-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása az átjáró jutalmazására**lehetőséget.

    ![Jutalomátjáró felügyeleti konzolja](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Tekintse át az Azure AD-ből az **Attribútum-leképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a Jutalomátjáró felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Jutalomátjáró felügyeleti konzolja](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás a Jutalom átjáró engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Jutalomátjáróra, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Jutalom átjárón végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

A Jutalom átjáró jelenleg nem támogatja a csoportkiépítést.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

[További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
