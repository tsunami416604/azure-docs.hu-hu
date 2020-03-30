---
title: 'Oktatóanyag: Felhasználói kiépítés a Pingboardhoz – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok pingboardba való automatikus kiépítésére és kiépítésének kimerülésével.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061275"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Oktatóanyag: A Pingboard konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a felhasználói fiókok automatikus kiépítése és de-kiépítés engedélyezéséhez az Azure Active Directory (Azure AD) a Pingboard, a következő lépéseket kell végrehajtania, hogy engedélyezze az automatikus kiépítése és a felhasználói fiókok kiépítése az Azure Active Directoryból (Azure AD) a Pingboard.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure AD-bérlő
* [Pingboard-bérlőpro-fiók](https://pingboard.com/pricing)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Pingboardban

> [!NOTE]
> Az Azure AD-kiépítési integráció a [Pingboard API-t,](https://pingboard.docs.apiary.io/#)amely elérhető a fiókjában.

## <a name="assign-users-to-pingboard"></a>Felhasználók hozzárendelése a Pingboardhoz

Az Azure AD a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók szinkronizálódnak. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak kell hozzáférnie a Pingboard alkalmazáshoz. Ezután hozzárendelheti ezeket a felhasználókat a Pingboard alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Fontos tippek a felhasználók Pingboardhoz való hozzárendeléséhez

Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó pingboard a létesítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

## <a name="configure-user-provisioning-to-pingboard"></a>Felhasználói kiépítés konfigurálása a Pingboardba 

Ez a szakasz végigvezeti az Azure AD-nek a Pingboard felhasználói fiók létesítési API-hoz való csatlakoztatásával. A létesítési szolgáltatást úgy is konfigurálhatja, hogy hozzon létre, frissítsen és tiltson le a hozzárendelt felhasználói fiókokat a Pingboardban, amelyek az Azure AD-ben a felhasználói hozzárendeléseken alapulnak.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezés pingboard engedélyezéséhez kövesse az [Azure Portalon](https://portal.azure.com)található utasításokat. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Automatikus felhasználói fiók-kiépítés konfigurálása a Pingboardba az Azure AD-ben

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** szakaszban.

1. Ha már beállította a Pingboardot egyszeri bejelentkezéshez, keresse meg a Pingboard példányát a keresőmező használatával. Ellenkező esetben válassza a **Pingboard** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza a **Pingboard** elemet a keresési eredmények között, és vegye fel az alkalmazások listájára.

1. Válassza ki a Pingboard példányát, majd válassza a **Kiépítés** lapot.

1. Állítsa **a létesítési módot** **automatikusra.**

    ![Pingboard kiépítés](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** kövesse az alábbi lépéseket:

    a. A **Bérlő URL-címét**írja be, `https://your_domain.pingboard.com/scim/v2`és cserélje le a "your_domain" kifejezést a valódi tartományra.

    b. Jelentkezzen be a [Pingboardba](https://pingboard.com/) a rendszergazdai fiókjával.

    c. Válassza **a Bővítmények integrációk** > **Integrations** > **Azure Active Directory**lehetőséget.

    d. Nyissa meg a **Konfigurálás** lapot, és válassza **a Felhasználói kiépítés engedélyezése az Azure-ból**lehetőséget.

    e. Másolja a tokent az **OAuth bearer tokenbe,** és írja be **titkos jogkivonatban.**

1. Az Azure Portalon válassza a **Tesztkapcsolat teszteléséhez** az Azure AD csatlakozhat a Pingboard alkalmazáshoz. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Pingboard-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra a **Kapcsolat tesztelése** lépéssel.

1. Adja meg annak a személynek vagy csoportnak az e-mail címét, akivagy amelyet kiépítési hibaértesítéseket szeretne kapni az **Értesítési e-mail ben.** Jelölje be az alatta lévő jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.

1. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Pingboardba**lehetőséget.

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről a Pingboardra szinkronizálandó felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Pingboard felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot. További információt a [Felhasználói kiépítési attribútum-hozzárendelések testreszabása című témakörben talál.](../app-provisioning/customize-application-attributes.md)

1. Az Azure AD-kiépítési szolgáltatás pingboard engedélyezéséhez a **Beállítások** szakaszban módosítsa a **Kiépítési állapot** beállítást **Be**állásra.

1. A **Mentés gombra** a Pingboardhoz rendelt felhasználók kezdeti szinkronizálásának elindításához válassza a Mentés lehetőséget.

A kezdeti szinkronizálás futtatása hosszabb időt vesz igénybe, mint a következő szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A **Szinkronizálás részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat. A naplók ismertetik a kiépítési szolgáltatás által a Pingboard alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Jelentés az automatikus felhasználói fiók kiépítésről című témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](pingboard-tutorial.md)
