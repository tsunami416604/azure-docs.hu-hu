---
title: 'Oktatóanyag: A ServiceNow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967169"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Oktatóanyag: A ServiceNow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directory

Ennek az oktatóanyagnak a célja, hogy megmutassa a ServiceNow és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a ServiceNow.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ServiceNow való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- ServiceNow, a ServiceNow, a Calgary vagy a magasabb verziójának példánya vagy bérlője
- A ServiceNow Express esetében a ServiceNow Express, Helsinki vagy újabb verziójának példánya

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik Azure AD-próbaverzióval, [ingyenes fiókot](https://azure.microsoft.com/free/)szerezhet be.

## <a name="assigning-users-to-servicenow"></a>Felhasználók kiosztása a ServiceNow

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a ServiceNow alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a ServiceNow-alkalmazáshoz: [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a ServiceNow a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.
>*   Ha a felhasználó ServiceNow rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.
>*   A szerepkörök Azure AD-beli létrehozásával és konfigurálásával kapcsolatos további információkért tekintse meg ezt a [hivatkozást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a ServiceNow felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és ServiceNow alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
>Dönthet úgy is, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a ServiceNow, a Azure Portalban megadott [](https://portal.azure.com)utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a ServiceNow az egyszeri bejelentkezéshez, keresse meg a ServiceNow-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **ServiceNow** az alkalmazás-gyűjteményben. Válassza a ServiceNow lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a ServiceNow példányát, majd válassza a kiépítés lapot.

1. Állítsa a **kiépítési** módot **automatikus**értékre. 

    ![kiépítés folyamatban](./media/servicenow-provisioning-tutorial/provisioning.png)

1. A rendszergazdai hitelesítő adatok szakaszban hajtsa végre a következő lépéseket:
   
    a. A **ServiceNow-példány neve** szövegmezőbe írja be a ServiceNow-példány nevét.

    b. A **ServiceNow rendszergazda felhasználóneve** szövegmezőbe írja be a rendszergazda felhasználónevét.

    c. A **ServiceNow rendszergazdai jelszó** szövegmezőben a rendszergazda jelszava.

1. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a ServiceNow-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a ServiceNow-fiókja rendelkezik rendszergazdai jogosultságokkal, és ismételje meg a **"rendszergazdai hitelesítő adatok"** lépést.

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítő e-mailek** mezőben, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a ServiceNow lehetőséget.**

1. Az **attribútum** -hozzárendelések szakaszban tekintse át az Azure ad-ből az ServiceNow-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ServiceNow felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD-kiépítési szolgáltatás ServiceNow való engedélyezéséhez módosítsa a kiépítési **állapotot** a következőre a beállítások szakaszban:

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban ServiceNow rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a ServiceNow alkalmazásban végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](servicenow-tutorial.md)


