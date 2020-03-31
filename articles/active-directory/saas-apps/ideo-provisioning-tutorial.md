---
title: 'Oktatóanyag: Az IDEO konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat az IDEO-ba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057566"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Oktatóanyag: Az IDEO konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az IDEO és az Azure Active Directory (Azure AD) végrehajtásához szükséges lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok IDEO-ba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [IDEO-bérlő](https://www.shape.space/product/pricing)
* Felhasználói fiók az IDEO-n | Rendszergazdai engedélyekkel rendelkező alakzat.

## <a name="assign-users-to-ideo"></a>Felhasználók hozzárendelése az IDEO-hoz

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az IDEO-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az IDEO-hoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Fontos tippek a felhasználók IDEO-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve IDEO az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az IDEO-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-ideo-for-provisioning"></a>Az IDEO beállítása a kiépítéshez

Az IDEO konfigurálása az Azure AD automatikus felhasználói kiépítéshez, le kell kérnie néhány kiépítési információt az IDEO-ból.

1. Titkos **jogkivonat** esetén lépjen productsupport@ideo.comkapcsolatba az IDEO támogatási csapatával a(z) oldalon. Ez az érték a **Titkos jogkivonat** mezőben lesz megadva az IDEO-alkalmazás kiépítés lapján az Azure Portalon. 

## <a name="add-ideo-from-the-gallery"></a>IDEO hozzáadása a gyűjteményből

Az IDEO konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia az IDEO-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **IDEO**parancsot, és válassza az **IDEO** elemet az eredménypanelen. 

    ![IDEO az eredménylistában](common/search-new-app.png)

5. Válassza ki az **IDEO-regisztráció** gombot, amely átirányítja az IDEO bejelentkezési oldalára. 

    ![IDEO OIDC hozzáadás](media/ideo-provisioning-tutorial/signup.png)

6. Mivel az IDEO egy OpenIDConnect alkalmazás, jelentkezzen be az IDEO-ba a Microsoft munkahelyi fiókjával.

    ![IDEO OIDC bejelentkezés](media/ideo-provisioning-tutorial/login.png)

7. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja az IDEO-fiókjába.

    ![IDEO OIDC hozzájárulása](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Automatikus felhasználói kiépítés konfigurálása ideo-ba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az IDEO-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Az IDEO automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **IDEO**lehetőséget.

    ![Az IDEO hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://profile.ideo.com/api/scim/v2` csoportban adja meg a **bérlői URL-címet.** Adja meg az IDEO támogatási csapattól a **Titkos jogkivonatban**beolvasott értéket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes-e csatlakozni az IDEO-hoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy az IDEO-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása IDEO-val**lehetőséget.

    ![IDEO felhasználói leképezések](media/ideo-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ről IDEO-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok az IDEO felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![IDEO felhasználói attribútumok](media/ideo-provisioning-tutorial/userattributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezése ideo, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](media/ideo-provisioning-tutorial/groupmappings.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az IDEO-ba, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](media/ideo-provisioning-tutorial/groupattributes.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az IDEO-n végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)


