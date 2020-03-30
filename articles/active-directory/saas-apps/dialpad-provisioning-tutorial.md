---
title: 'Oktatóanyag: A Tárcsázó konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Dialpadszolgáltatásba.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058346"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Oktatóanyag: A Tárcsázó konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Dialpad és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok tárcsázási rendszerbe történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Dialpad bérlő](https://www.dialpad.com/pricing/).
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Tárcsázóban.

## <a name="assign-users-to-dialpad"></a>Felhasználók hozzárendelése a Tárcsázóhoz
Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Dialpadhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Dialpadhoz az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Fontos tippek a felhasználók Dialpadhoz való hozzárendeléséhez

 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve dialpad az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Tárcsázóhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-dialpad-for-provisioning"></a>Beállítási tárcsázási panel létesítéshez

A Dialpad konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, be kell olvasnia néhány létesítési információt a Tárcsázóból.

1. Jelentkezzen be a [Tárcsázó felügyeleti konzoljára,](https://dialpadbeta.com/login) és válassza a **Felügyeleti beállítások lehetőséget**. Győződjön meg arról, hogy a **Saját vállalat** ki van jelölve a legördülő menüből. Nyissa meg a **hitelesítési > API-kulcsokat.**

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Hozzon létre egy új kulcsot a **Kulcs hozzáadása** gombra kattintva, és konfigurálja a titkos jogkivonat tulajdonságait.

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kattintson a Kattintás gombra a legutóbb létrehozott API-kulcs értékgombjának **megjelenítéséhez,** és másolja a megjelenített értéket. Ez az érték a **Titkos jogkivonat** mezőben lesz megadva a Dialpad alkalmazás Kiépítés lapján az Azure Portalon. 

    ![Tárcsázó token létrehozása](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dialpad hozzáadása a gyűjteményből

A Dialpad konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a Dialpadot az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Tárcsázót az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Tárcsázó**, válassza a **Tárcsázó** elemet az eredménypanelen.
    ![Dialpad az eredménylistában](common/search-new-app.png)

5. Keresse meg az alább kiemelt **URL-t** egy külön böngészőben. 

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. A jobb felső sarokban válassza a **Bejelentkezés > Dialpad használata online**lehetőséget.

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Mivel a Dialpad egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezzen be a Dialpad ba.

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Tárcsázó fiókjába.

    ![Tárcsázó hozzáadása SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Automatikus felhasználói kiépítés konfigurálása a Tárcsázó szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Dialpadban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Az Azure AD-ben a Dialpad automatikus felhasználói kiépítésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Tárcsázó lehetőséget.**

    ![A Tárcsázó hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://dialpad.com/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg a Dialpad programból korábban beolvasott és mentett értéket **a Titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Tárcsázóhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Tárcsázó fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory felhasználóinak szinkronizálása tárcsázóba**lehetőséget.

    ![Tárcsázó felhasználói hozzárendelései](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Tekintse át az Azure AD-ről a Dialpadra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Tárcsázó rendszer felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Tárcsázó felhasználói attribútumai](media/dialpad-provisioning-tutorial/dialpad07.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás tárcsázási engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Tárcsázóba, ha a **Beállítások** szakasz **Hatókör** területén kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Dialpadon végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.
##  <a name="connector-limitations"></a>Összekötő korlátai
* A Dialpad ma nem támogatja a csoportátnevezést. Ez azt jelenti, hogy az Azure AD-ben egy csoport **displayName-jének** módosításai nem frissülnek, és nem jelennek meg a Tárcsázóban.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
