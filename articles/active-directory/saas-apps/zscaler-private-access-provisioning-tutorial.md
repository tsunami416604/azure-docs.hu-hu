---
title: 'Oktatóanyag: A Zscaler Private Access (ZPA) konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt a Zscaler Private Access (ZPA) felhasználói fiókok automatikus kiépítésére és kiépítésének kioldására.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064155"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler Private Access (ZPA) konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Zscaler Private Access (ZPA) és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan üzembe helyezze a felhasználókat és/vagy csoportokat a Zscaler Private Access (ZPA) számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Zscaler Private Access (ZPA) bérlő](https://www.zscaler.com/pricing-and-plans#contact-us)
* A Zscaler Private Access (ZPA) rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Felhasználók hozzárendelése a Zscaler Private Access (ZPA) rendszerhez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Zscaler Private Access (ZPA) szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler Private Access (ZPA) rendszerhez az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Fontos tippek a felhasználók Zscaler Private Access (ZPA) hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve zscaler private access (ZPA) az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Zscaler Private Access (ZPA) szolgáltatáshoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Zscaler Private Access (ZPA) beállítása a kiépítéshez

1. Jelentkezzen be a [Zscaler Private Access (ZPA) felügyeleti konzoljára.](https://admin.private.zscaler.com/) Nyissa meg **a Felügyeleti > IdP-konfigurációt.**

    ![Zscaler privát hozzáférés (ZPA) felügyeleti konzol](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Ellenőrizze, hogy az **egyszeri bejelentkezés idp-jének** konfigurálása van-e. Ha nincs IdP beállítás, akkor adjon hozzá egyet a képernyő jobb felső sarkában lévő plusz ikonra kattintva.

    ![Zscaler private access (ZPA) SCIM hozzáadása](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Az **IdP-konfiguráció hozzáadása** varázslóban adjon hozzá egy IdP-t. Hagyja az **Egyszeri bejelentkezés** mezőt A **Felhasználó beállításra**állítva. Adjon **meg egy nevet,** és válassza ki a **Tartományok elemet** a legördülő listából. Kattintson a **Tovább** gombra a következő ablakhoz való navigáláshoz.

    ![Zscaler privát hozzáférés (ZPA) IdP hozzáadása](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Töltse le a **szolgáltatói tanúsítványt**. Kattintson a **Tovább** gombra a következő ablakhoz való navigáláshoz.

    ![Zscaler Private Access (ZPA) SP tanúsítvány](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. A következő ablakban töltse fel a korábban letöltött **szolgáltatói tanúsítványt.**

    ![Zscaler Private Access (ZPA) feltöltési tanúsítvány](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Görgessen le az **egyszeri bejelentkezési URL-cím** és az **IdP-entitásazonosító megadásához.**

    ![Zscaler privát hozzáférés (ZPA) azonosító](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Görgessen le az **SCIM-szinkronizálás engedélyezése mezőben.** Kattintson **az Új token létrehozása** gombra. Másolja a **tulajdonosi token**. Ezt az értéket a Zscaler Private Access (ZPA) alkalmazás Kiépítés lapján a Titkos jogkivonat mezőben adja meg az Azure Portalon.

    ![Zscaler privát hozzáférés (ZPA) Token létrehozása](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  A **bérlői URL megkereséséhez** keresse meg a **Felügyeleti > IdP-konfigurációt.** Kattintson az oldalon felsorolt újonnan hozzáadott IdP-konfiguráció nevére.

    ![Zscaler privát hozzáférés (ZPA) Idp neve](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Görgessen le az **SCIM szolgáltató végpontjának** megtekintéséhez a lap végén. Másolja az **SCIM-szolgáltató végpontját**. Ez az érték a Bérlő URL-cím mezőjében lesz megadva a Zscaler Private Access (ZPA) alkalmazás kiépítés lapján az Azure Portalon.

    ![Zscaler private access (ZPA) SCIM URL-címe](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) hozzáadása a galériából

A Zscaler Private Access (ZPA) konfigurálása előtt az Azure AD automatikus felhasználói kiépítés, hozzá kell adnia zscaler private access (ZPA) az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Zscaler Private Access (ZPA) az Azure AD alkalmazásgyűjtemény, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Private Access (ZPA) kifejezést,** válassza a **Zscaler Private Access (ZPA)** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler Private Access (ZPA) az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Automatikus felhasználói kiépítés konfigurálása a Zscaler Private Access (ZPA) szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Zscaler Private Access (ZPA) rendszerében az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> A Zscaler Private Access (ZPA) használatával engedélyezheti az SAML-alapú egyszeri bejelentkezést a Zscaler Private Access (ZPA) [egyszeri bejelentkezési oktatóanyagában.](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a Zscaler Private Access SCIM-végpontjáról, olvassa el [ezt](https://www.zscaler.com/partners/microsoft)a .

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>A Zscaler Private Access (ZPA) automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler Private Access (ZPA) lehetőséget.**

    ![A Zscaler Private Access (ZPA) hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg az **SCIM-szolgáltató végpontjának** korábbi beolvasási értékét a **bérlői URL-címben.** Adja meg a titkos **jogkivonat**korábbi beolvasása után beolvasott **tulajdonosi token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Zscaler Private Access (ZPA) szolgáltatáshoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Zscaler Private Access (ZPA) fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Zscaler Private Access (ZPA) (ZPA) lehetőséget.**

    ![Zscaler private access (ZPA) felhasználói leképezések](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és a Zscaler Private Access (ZPA) között az **Attribútumleképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler Private Access (ZPA) felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Zscaler Private Access (ZPA) felhasználói attribútumai](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Zscaler Private Access (ZPA) lehetőséget.**

    ![Zscaler private access (ZPA) csoportleképezések](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és zscaler private access (ZPA) közötti csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler Private Access (ZPA) csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Zscaler Private Access (ZPA) csoport attribútumai](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás zscaler private access (ZPA) engedélyezéséhez módosítsa a **kiépítési állapot** **ot be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Zscaler Private Access (ZPA) számára a **Beállítások** szakasz **Hatókör** csoportjában a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Zscaler Private Access (ZPA) végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

