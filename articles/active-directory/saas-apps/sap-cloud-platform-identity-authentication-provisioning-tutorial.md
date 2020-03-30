---
title: 'Oktatóanyag: Az SAP felhőplatform-identitáshitelesítés konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az SAP Cloud Platform-identitáshitelesítéshez.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060424"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Oktatóanyag: Az SAP felhőplatform-identitáshitelesítés konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az SAP Cloud Platform identity authentication és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat az SAP felhőplatform-identitás-hitelesítéshez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Az SAP felhőplatform-identitás-hitelesítési bérlője](https://cloudplatform.sap.com/pricing.html)
* Az SAP felhőplatform-identitáshitelesítés rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Felhasználók hozzárendelése az SAP felhőplatform-identitáshitelesítéshez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az SAP Cloud Platform-identitáshitelesítéshez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az SAP Cloud Platform identity authentication-hoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Fontos tippek a felhasználók SAP cloud platformidentitás-hitelesítéshez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az SAP Cloud Platform identity authentication az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel az SAP felhőplatform-identitás-hitelesítéshez, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>SAP felhőplatform-identitás-hitelesítés beállítása a kiépítéshez

1. Jelentkezzen be az [SAP Cloud Platform identitáshitelesítési felügyeleti konzoljára.](https://sapmsftintegration.accounts.ondemand.com/admin) Keresse meg a **Felhasználók & az engedélyezés> a rendszergazdák lehetőséget.**

    ![SAP felhőalapú platformidentitás-hitelesítés felügyeleti konzolja](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Hozzon létre egy rendszergazdai felhasználót, és jelölje ki a felhasználót.  

3.  Az Engedélyezések konfigurálása csoportban kapcsolja be a váltógombot a **Felhasználók kezelése** és a Csoportok **kezelése gombbal.**

    ![SAP felhőplatform-identitás-hitelesítés hozzáadása SCIM hozzáadása](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. E-mailben aktiválhatja fiókját, és jelszót állíthat be az **SAP Cloud Platform Identity Authentication Service szolgáltatáshoz.**

4.  Másolja a **felhasználói azonosítót** és **a jelszót**. Ezeket az értékeket a rendszergazdai felhasználónév és a rendszergazdai jelszó mezőkben, illetve az SAP Cloud Platform identity authentication alkalmazás kiépítése lapján adja meg az Azure Portalon.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP-felhőalapú platformidentitás-hitelesítés hozzáadása a gyűjteményből

Az SAP Cloud Platform Identity Authentication konfigurálása az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia az SAP Cloud Platform Identity Authentication-t az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az SAP felhőplatform-identitáshitelesítést az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP cloud platform identitáshitelesítése**, válassza **az SAP Cloud Platform identitás-hitelesítés** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SAP felhőalapú platformidentitás-hitelesítés az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Automatikus felhasználói kiépítés konfigurálása az SAP felhőplatform-identitáshitelesítéshez 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az SAP Felhőplatform-identitáshitelesítésben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az SAP Cloud Platform identity authentication számára, az [SAP Cloud Platform Identity Authentication single sign-on oktatóanyagutasításait követve.](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial) Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Az SAP Felhőplatform-identitáshitelesítés automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **SAP cloud platform identitáshitelesítése**lehetőséget.

    ![Az SAP felhőplatform-identitás-hitelesítéshivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://<tenantID>.accounts.ondemand.com/service/scim ` csoportban adja meg a **bérlői URL-címet.** Adja meg a **rendszergazdai felhasználónév** és a **rendszergazdai jelszó** korábbi verziójában beolvasott **felhasználói azonosító** és **jelszó** értékeket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az SAP felhőplatform-identitáshitelesítéshez. Ha a kapcsolat sikertelen, győződjön meg arról, hogy az SAP Cloud Platform identitás-hitelesítési fiókja rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory-felhasználók szinkronizálása az SAP felhőplatform-identitáshitelesítéssel lehetőséget.**

    ![SAP felhőalapú platformidentitás-hitelesítés felhasználói leképezései](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Tekintse át az Azure AD-ből az SAP-alapú felhőplatform-identitáshitelesítéssel szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az SAP felhőplatform-identitáshitelesítés felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![SAP felhőplatform-identitáshitelesítés felhasználói felhasználói attribútumai](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez az SAP felhőplatform-identitás-hitelesítéshez módosítsa a **kiépítési állapotot** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az SAP Cloud Platform identitás-hitelesítéshez a **beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az SAP cloud platform identitás-hitelesítésén végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Az SAP Cloud Platform Identity Authentication SCIM-végpontja bizonyos attribútumok adott formátumú. Többet tudhat ezekről az attribútumokról és azok egyedi formátumáról [itt.](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

