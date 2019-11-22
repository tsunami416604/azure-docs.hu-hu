---
title: 'Oktatóanyag: felhasználói kiépítés a LucidChart – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a LucidChart.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3791992586edbdc5188c3078b1f1bb108ce580d7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276852"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés LucidChart konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a LucidChart és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a LucidChart. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Azure Active Directory-bérlő
* LucidChart-bérlő a [nagyvállalati csomaggal](https://www.lucidchart.com/user/117598685#/subscriptionLevel) vagy jobb engedélyezéssel
* Rendszergazdai jogosultságokkal rendelkező LucidChart felhasználói fiók

## <a name="assigning-users-to-lucidchart"></a>Felhasználók kiosztása a LucidChart

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a LucidChart alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a LucidChart-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Fontos Tippek a felhasználók LucidChart való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a LucidChart a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor LucidChart rendel hozzá egy felhasználóhoz, ki kell választania a **felhasználói** szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítés során, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-lucidchart"></a>A felhasználók üzembe helyezésének beállítása a LucidChart

Ez a szakasz végigvezeti az Azure AD-nek a LucidChart felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és LucidChart alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Dönthet úgy is, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a LucidChart, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-beli LucidChart

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a LucidChart az egyszeri bejelentkezéshez, keresse meg a LucidChart-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **LucidChart** az alkalmazás-gyűjteményben. Válassza a LucidChart lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a LucidChart példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![LucidChart kiépítés](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a LucidChart fiókja által generált **titkos jogkivonatot** (az Ön fiókja alatt találja a tokent: **Team** > **app Integration** > **scim**).

    ![LucidChart kiépítés](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a LucidChart-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a LucidChart-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra az 5. lépéssel.

7. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az értesítő e-mail-értesítéseket az **értesítési e-mail** mezőben, és jelölje be az "e-mail-értesítés küldése hiba esetén" jelölőnégyzetet.

8. Kattintson a **Save** (Mentés) gombra.

9. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a LucidChart**lehetőséget.

10. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az LucidChart-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a LucidChart felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD-kiépítési szolgáltatás LucidChart való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

12. Kattintson a **Save** (Mentés) gombra.

Ez a művelet elindítja a felhasználók és csoportok szakaszban LucidChart rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. A **szinkronizálás részletei** szakasz használatával figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet leírják.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
