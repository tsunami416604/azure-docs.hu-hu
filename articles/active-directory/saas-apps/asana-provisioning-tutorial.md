---
title: 'Oktatóanyag: Felhasználói kiépítés az Asana - Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és az Asana-ra való kiépítésének kiépítésével.
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
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058907"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Oktatóanyag: Az Asana konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa az Asana és az Azure Active Directory (Azure AD) által az Azure AD-től az Asana-hoz való automatikus kiépítéséhez és a felhasználói fiókok kiépítésének ki- és kiépítésének kiteljesítéséhez szükséges lépéseket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure AD-bérlő
* Asana-bérlő [vállalati](https://www.asana.com/pricing) csomaggal vagy jobban engedélyezve
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók Asanában

> [!NOTE]
> Az Azure AD-kiépítési integráció az [Asana API-t,](https://asana.com/developers/api-reference/users)amely elérhető az Asana.

## <a name="assign-users-to-asana"></a>Felhasználók hozzárendelése az Asanához

Az Azure AD egy *feladatkiosztásnevű* koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók szinkronizálódnak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak kell hozzáférnie az Asana-alkalmazáshoz. Ezután hozzárendelheti ezeket a felhasználókat az Asana alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Fontos tippek a felhasználók Asana-hoz való hozzárendeléséhez

Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó a Sana a létesítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

## <a name="configure-user-provisioning-to-asana"></a>Felhasználói kiépítés konfigurálása az Asana rendszerbe

Ez a szakasz végigvezeti az Azure AD-nek az Asana felhasználói fiók létesítési API-hoz való csatlakoztatásával. A kiépítési szolgáltatást úgy is konfigurálhatja, hogy az Azure AD-ben megadott felhasználói fiókokat hozzon létre, frissítsen és tiltson le az Asana-ban.

> [!TIP]
> Az Asana SAML-alapú egyszeri bejelentkezésének engedélyezéséhez kövesse az [Azure Portalon](https://portal.azure.com)található utasításokat. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatikus felhasználói fiók-kiépítés konfigurálása az Asana szolgáltatásba az Azure AD-ben

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** szakaszban.

1. Ha már konfigurálta az Asana-t egyszeri bejelentkezéshez, keresse meg az Asana példányát a keresőmező használatával. Ellenkező esetben válassza az **Asana** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza az **Asana** elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

1. Válassza ki az Asana példányát, majd válassza a **Kiépítés** lapot.

1. Állítsa **a létesítési módot** **automatikusra.**

    ![Asana kiépítés](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. A **Rendszergazdai hitelesítő adatok** szakaszban kövesse az alábbi utasításokat a jogkivonat létrehozásához és **titkos jogkivonatban való beviteléhez:**

    a. Jelentkezzen be az [Asana-ba](https://app.asana.com) a rendszergazdai fiókjával.

    b. A felső sávon jelölje ki a profilképet, és adja meg az aktuális szervezeti névbeállításokat.

    c. Nyissa meg a **Szolgáltatásfiókok** lapot.

    d. Válassza **a Szolgáltatásfiók hozzáadása lehetőséget.**

    e. Szükség szerint frissítse **a Név** és a **Beet** képet, valamint a profilfotót. Másolja a tokenre a **Token**ben , és jelölje ki a **Módosítások mentése című**részben.

1. Az Azure Portalon válassza a **Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD képes legyen csatlakozni az Asana alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az Asana-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra a **Kapcsolat tesztelése** lépéssel.

1. Adja meg annak a személynek vagy csoportnak az e-mail címét, akivagy amelyet kiépítési hibaértesítéseket szeretne kapni az **Értesítési e-mail ben.** Jelölje be az alatta lévő jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.

1. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása az Asana szolgáltatással**lehetőséget.

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és az Asana között szinkronizálandó felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok az Asana felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot. További információt a [Felhasználói kiépítési attribútum-hozzárendelések testreszabása című témakörben talál.](../app-provisioning/customize-application-attributes.md)

1. Az Asana Azure AD-létesítési szolgáltatásának engedélyezéséhez a **Beállítások** szakaszban módosítsa a **Kiépítés állapotát** Be **állapotúra.**

1. Kattintson a **Mentés** gombra.

Most a kezdeti szinkronizálás elindul minden felhasználó hozzárendelt Asana a **felhasználók** szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A **Szinkronizálás részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat. A naplózási naplók ismertetik az Asana-alkalmazás létesítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Jelentés az automatikus felhasználói fiók kiépítésről című témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése a vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](asana-tutorial.md)
