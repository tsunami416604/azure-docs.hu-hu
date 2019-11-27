---
title: 'Oktatóanyag: felhasználói kiépítés a ThousandEyes – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a ThousandEyes.
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
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaf019303c311519c4b7d483d8f9193f432b8385
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278831"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés ThousandEyes konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a ThousandEyes és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a ThousandEyes. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Azure Active Directory-bérlő
* Egy ThousandEyes-bérlő a [standard csomaggal](https://www.thousandeyes.com/pricing) vagy jobb engedélyezéssel 
* Rendszergazdai jogosultságokkal rendelkező ThousandEyes felhasználói fiók 

> [!NOTE]
> Az Azure AD-kiépítés integrációja a [THOUSANDEYES scim API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)-ra támaszkodik, amely ThousandEyes-csapatok számára érhető el a standard csomag vagy jobb.

## <a name="assigning-users-to-thousandeyes"></a>Felhasználók kiosztása a ThousandEyes

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a ThousandEyes alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a ThousandEyes-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Fontos Tippek a felhasználók ThousandEyes való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a ThousandEyes a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor ThousandEyes rendel hozzá egy felhasználóhoz, ki kell választania a **felhasználói** szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítés során, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>A felhasználók üzembe helyezésének beállítása a ThousandEyes 

Ez a szakasz végigvezeti az Azure AD-nek a ThousandEyes felhasználói fiók létesítési API-hoz való csatlakoztatásának és a kiépítési szolgáltatás konfigurálásának az Azure AD-beli felhasználó-és ThousandEyes alapján történő létrehozásához, frissítéséhez és letiltásához. .

> [!TIP]
> Dönthet úgy is, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a ThousandEyes, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-beli ThousandEyes

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a ThousandEyes az egyszeri bejelentkezéshez, keresse meg a ThousandEyes-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **ThousandEyes** az alkalmazás-gyűjteményben. Válassza a ThousandEyes lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a ThousandEyes példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![ThousandEyes kiépítés](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a ThousandEyes fiókja által generált **OAuth tulajdonosi jogkivonatot** (a ThousandEyes fiók **profilja** szakaszban találhatja meg és generálhatja a jogkivonatot).

    ![ThousandEyes kiépítés](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a ThousandEyes-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a ThousandEyes-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra az 5. lépéssel.

7. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az értesítő e-mail-értesítéseket az **értesítési e-mail** mezőben, és jelölje be az "e-mail-értesítés küldése hiba esetén" jelölőnégyzetet.

8. Kattintson a **Save** (Mentés) gombra.

9. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a ThousandEyes**lehetőséget.

10. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az ThousandEyes-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ThousandEyes felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD-kiépítési szolgáltatás ThousandEyes való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

12. Kattintson a **Save** (Mentés) gombra.

Ez a művelet elindítja a felhasználók és csoportok szakaszban ThousandEyes rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. A **szinkronizálás részletei** szakasz használatával figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet leírják.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
