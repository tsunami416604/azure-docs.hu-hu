---
title: 'Oktatóanyag: A Cisco Webex konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Cisco Webex szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058497"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Oktatóanyag: A Cisco Webex konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Cisco Webex és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és de-kiépítse a felhasználókat a Cisco Webex számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Cisco Webex bérlője](https://www.webex.com/pricing/index.html).
* A Cisco Webex rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a galériából

Mielőtt konfigurálna a Cisco Webexet az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia a Cisco Webex-et az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Cisco Webex-et az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cisco Webex**kifejezést , válassza a **Cisco Webex** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Cisco Webex az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Felhasználók hozzárendelése a Cisco Webex-hez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak a felhasználók és/vagy csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak kell hozzáférnie a Cisco Webexhez. Miután eldöntötte, ezeket a felhasználókat a Cisco Webex-hez rendelheti az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Fontos tippek a felhasználók Cisco Webex hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Cisco Webex az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

* Amikor egy felhasználót a Cisco Webexhez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Automatikus felhasználói kiépítés konfigurálása a Cisco Webex rendszerbe

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói feladatok alapján hozzanak létre, frissítsenek és tiltsanak le felhasználókat a Cisco Webexben.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>A Cisco Webex automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure portalra,](https://portal.azure.com) és válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Cisco Webex**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Cisco Webex**lehetőséget.

    ![A Cisco Webex hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Cisco Webex kiépítés](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Cisco Webex kiépítés](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet**és a Cisco Webex-fiók **titkos tokenjét.**

    ![Cisco Webex kiépítés](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  A **Bérlő URL-címe** mezőben adjon `https://api.ciscoweb.com/v1/scim/[OrgId]`meg egy értéket a formájában. A `[OrgId]`beszerzéshez jelentkezzen be [a Cisco Webex Control Hub-ba.](https://admin.webex.com/login) Kattintson a szervezet nevére a bal alsó sarokban, és másolja az értéket a **szervezeti azonosítóból.** 

    * A **titkos jogkivonat**értékének beszerzéséhez keresse meg ezt az [URL-címet.](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose) A megjelenő webex bejelentkezési lapon jelentkezzen be a teljes Cisco Webex rendszergazdai fiókkal a szervezetéhez. Megjelenik egy hibaoldal, amely szerint a webhely nem érhető el, de ez normális.

        ![Cisco Webex kiépítés](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Másolja a létrehozott tulajdonosi token értékét az URL-címből az alábbi módon. Ez a jogkivonat 365 napig érvényes.
        
        ![Cisco Webex kiépítés](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Az 5. **Test Connection** Ha a kapcsolat megszakad, győződjön meg arról, hogy a Cisco Webex-fiókja rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)
   
8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása a Cisco Webex szolgáltatással**lehetőséget.

    ![Cisco Webex kiépítés](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Tekintse át az Azure AD és a Cisco Webex között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Cisco Webex felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Cisco Webex kiépítés](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Cisco Webex, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. A Beállítások **szakasz** **hatókörében** a kívánt értékek kiválasztásával határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Cisco Webex-nek.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Cisco Webex-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Cisco Webex jelenleg a Cisco korai field testing (EFT) fázisban. További információért forduljon [a Cisco támogatási csapatához.](https://www.webex.co.in/support/support-overview.html) 
* A Cisco Webex konfigurációjáról további információt a Cisco [dokumentációjában talál.](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)