---
title: 'Oktatóanyag: Az összevont könyvtár konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat az összevont címtárba.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057919"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Oktatóanyag: Az összevont könyvtár konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az összevont címtárban és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket, hogy az Azure AD automatikusan üzembe helyezze és dekonizálja a felhasználókat és/vagy csoportokat az összevont címtárba.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Federated Directory](https://www.federated.directory/pricing).
* Az Összevont címtárban rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="assign-users-to-federated-directory"></a>Felhasználók hozzárendelése összevont címtárhoz
Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az összevont címtárhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Federated Directoryhoz az alábbi utasításokat követve:

 * [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Fontos tippek a felhasználók összevont címtárhoz való hozzárendeléséhez
 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az összevont címtárhoz az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az Összevont címtárhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Összevont címtár beállítása kiépítéshez

Mielőtt konfigurálná az Összevont címtár automatikus felhasználói kiépítés az Azure AD,engedélyeznie kell a SCIM kiépítése az összevont címtárban.

1. Bejelentkezés az [összevont címtárfelügyeleti konzolra](https://federated.directory/of)

    ![Összevont könyvtár oktatóanyaga](media/federated-directory-provisioning-tutorial/companyname.png)

2. Keresse meg **a könyvtárakat > felhasználói könyvtárakat,** és válassza ki a bérlőt. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Állandó tulajdonosi jogkivonat létrehozásához keresse meg a **címtárkulcsokat > Új kulcs létrehozása elemre.** 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated01.png)

4. Hozzon létre egy címtárkulcsot. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Másolja a **hozzáférési jogkivonat** értékét. Ez az érték az Összevont címtár-alkalmazás kiépítés lapján az Azure Portalon a **Titkos jogkivonat** mezőben lesz megadva. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Fededelkönyvtár hozzáadása a galériából

Az Összevont címtár konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia az Összevont címtár az Azure AD alkalmazáskatalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Összevont címtár hozzáadása az Azure AD alkalmazáskatalógusból, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Összevont könyvtár**at , és válassza az **Összevont könyvtár** lehetőséget az eredménypanelen.

    ![Összevont könyvtár az eredménylistában](common/search-new-app.png)

5. Keresse meg az alább kiemelt **URL-t** egy külön böngészőben. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Kattintson **a BEJELENTKEZÉS gombra.**

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Mivel a Federated Directory egy OpenIDConnect alkalmazás, válassza ki, hogy microsoftos munkahelyi fiókjával jelentkezik be az Összevont címtárba.
    
    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Federated Directory-fiókjába.

    ![összevont könyvtár Hozzáadása SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Automatikus felhasználói kiépítés konfigurálása az összevont címtárba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az Összevont címtárban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Az összevont címtár automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Összevont címtár**lehetőséget.

    ![Az Összevont címtár hivatkozás a Kérelmek listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://api.federated.directory/v2/` szakaszban adja meg a bérlői URL-címet. Adja meg azt az értéket, amelyet korábban titkos **jogkivonatban**kért be és mentett a Federated Directoryból. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen az összevont címtárhoz csatlakozni. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az összevont címtárfiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása az összevont címtárba**lehetőséget.

    ![Összevont könyvtár oktatóanyaga](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Tekintse át az Azure AD-ről az Összevont könyvtárra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok az összevont címtárban lévő felhasználói fiókok egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Összevont könyvtár oktatóanyaga](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Összevont címtár Azure AD-létesítési szolgáltatásának engedélyezéséhez módosítsa a **Kiépítés állapotát** **be beállításra** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az összevont címtárba, ha kiválasztja a kívánt értékeket a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD létesítési szolgáltatás által az összevont címtárban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.
## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
