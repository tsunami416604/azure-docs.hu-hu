---
title: 'Oktatóanyag: Az iPass SmartConnect konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az iPass SmartConnect szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057525"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Bemutató: Az iPass SmartConnect konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az iPass SmartConnect és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonkóba helyezze a felhasználókat és/vagy csoportokat az iPass SmartConnect szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy iPass SmartConnect bérlő.](https://www.ipass.com/buy-ipass/)
* Az iPass SmartConnect felhasználói fiókja rendszergazdai engedélyekkel.

## <a name="assigning-users-to-ipass-smartconnect"></a>Felhasználók hozzárendelése az iPass SmartConnect-hez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az iPass SmartConnect-hez. Miután eldöntötte, ezeket a felhasználókat és /vagy csoportokat hozzárendelheti az iPass SmartConnect-hez az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Fontos tippek a felhasználók iPass SmartConnect hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve az iPass SmartConnect az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel az iPass SmartConnect-hez, ki kell választania minden érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Az iPass SmartConnect beállítása a kiépítéshez

Mielőtt konfigurálna az iPass SmartConnect-et az Azure AD-vel való automatikus felhasználói kiépítéshez, be kell olvasnia a konfigurációs adatokat az iPass SmartConnect felügyeleti konzolról:

1. Az iPass SmartConnect SCIM végpontja elleni hitelesítéshez szükséges tulajdonosi token lekéréséhez olvassa el az iPass SmartConnect beállításának első alkalommal történő beállítását, mivel ez az érték csak akkor érhető el. 
2. Ha nem rendelkezik a bemutatóra szóló token, lépjen kapcsolatba [az iPass SmartConnect ügyfélszolgálatával,](mailto:help@ipass.com) hogy újat szerezzen be.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Az iPass SmartConnect hozzáadása a galériából

Az iPass SmartConnect konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia az iPass SmartConnect-et az Azure AD alkalmazásgalériából a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az iPass SmartConnect-et az Azure AD alkalmazásgalériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **iPass SmartConnect**kifejezést , válassza az **iPass SmartConnect** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![iPass SmartConnect az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Automatikus felhasználói kiépítés konfigurálása az iPass SmartConnect rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az iPass SmartConnect ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
>  Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a BitaBIZ számára, az [iPass SmartConnect Single sign-on tutorial](ipasssmartconnect-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Az iPass SmartConnect automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **iPass SmartConnect**lehetőséget.

    ![Az iPass SmartConnect hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://openmobile.ipass.com/moservices/scim/v1` csoportban adja meg a **bérlői URL-címet.** Adja meg a titkos jogkivonat korábbi, korábbi beolvasása után kért **tokenjét.** Kattintson **a Kapcsolat tesztelése** gombra, ha biztosítani szeretné, hogy az Azure AD csatlakozni tud az iPass SmartConnect hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az iPass SmartConnect fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza az **Azure Active Directory-felhasználók szinkronizálása lehetőséget az iPass SmartConnect szolgáltatáshoz.**

    ![iPass SmartConnect felhasználói leképezések](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből az iPass SmartConnect szolgáltatással szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az iPass SmartConnect felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![iPass SmartConnect felhasználói leképezések](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-létesítési szolgáltatás engedélyezéséhez az iPass SmartConnect, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az iPass SmartConnect-be a **Beállítások** szakasz **Hatókör** területén található kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az iPass SmartConnect-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Az iPass SmartConnect csak olyan felhasználóneveket fogad el, amelyek domainje regisztrálva van az iPass SmartConnect admin konzolon.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
