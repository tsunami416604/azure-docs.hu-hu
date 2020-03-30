---
title: 'Oktatóanyag: A követ konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat az Envoy számára.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058025"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Oktatóanyag: A követ konfigurálása az automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az Envoy és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és deknaktssel tegye ki a felhasználókat és/vagy csoportokat az Envoy számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Egy követ bérlője](https://envoy.com/pricing/)
* Felügyeleti engedélyekkel rendelkező felhasználói fiók az Envoy-ban.

## <a name="add-envoy-from-the-gallery"></a>Követ hozzáadása a galériából

Mielőtt konfigurálása envoy automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia az Azure AD-alkalmazáskatalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Azure AD alkalmazásgyűjteményből szeretne követet hozzáadni, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Követ**, válassza **a Követ** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Az eredményjegyzékben szereplő követ](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Felhasználók hozzárendelése a követhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az Envoy-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a követhez az alábbi utasítások szerint:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Fontos tippek a felhasználók követhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve az envoy az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a megbízotthoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Automatikus felhasználói kiépítés konfigurálása az Envoy számára 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Követben az Azure AD-ben lévő felhasználói és/vagy csoport-hozzárendelések alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a követszámára, a [követ egyszeri bejelentkezési oktatóanyagában](envoy-tutorial.md)megadott utasításokat követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Az automatikus felhasználói kiépítés konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Követ lehetőséget.**

    ![A Követ hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://app.envoy.com/scim/v2` csoportban adja meg a **bérlői URL-címet.** A követfiókjának **titkos tokenjének** lekéréséhez kövesse a 6.

6. Jelentkezzen be a [megbízott ikiszolgálói konzoljára.](https://dashboard.envoy.com/login) Kattintson az **Integrációk gombra.**

    ![Követ integrációk](media/envoy-provisioning-tutorial/envoy01.png)

    Kattintson a **Telepítés gombra** a **Microsoft Azure SCIM-integrációhoz.**

    ![Követ telepítése](media/envoy-provisioning-tutorial/envoy02.png)

    Kattintson a **Mentés** **szinkronizálásra az összes felhasználó .** 

    ![Követ mentése](media/envoy-provisioning-tutorial/envoy03.png)

    A titkos token feltöltése.
    
    ![OAUTH követ](media/envoy-provisioning-tutorial/envoy04.png)

7. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a megbízottfiókja rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása követhez**lehetőséget.
    
    ![Követ felhasználói attribútumai](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Tekintse át az Azure AD-ről az **Attribútumleképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok az Envoy felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Követ felhasználói attribútumai](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása a követnek**lehetőséget.

    ![Követ felhasználói leképezései](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Tekintse át az Azure AD-ről az **Attribútumleképezés** szakaszban szinkronizált csoportattribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a csoportokkal egyeztethetők meg a Követben a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Követ felhasználói leképezései](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

15. Az Azure AD-létesítési szolgáltatás engedélyezése a megbízott számára, módosítsa a **kiépítési állapot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

16. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a követnek a **beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

17. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a követen végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

