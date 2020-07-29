---
title: 'Oktatóanyag: a Velpic konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064121"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Velpic konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a Velpic és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Velpic.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Velpic-bérlő a [nagyvállalati csomaggal](https://www.velpic.com/pricing.html) vagy jobb engedélyezéssel
* Rendszergazdai jogosultságokkal rendelkező Velpic felhasználói fiók

## <a name="assigning-users-to-velpic"></a>Felhasználók kiosztása a Velpic

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a Velpic alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Velpic-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Fontos Tippek a felhasználók Velpic való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Velpic a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Velpic rendel hozzá egy felhasználóhoz, ki kell választania a **felhasználói** szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Vegye figyelembe, hogy az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítés során, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-velpic"></a>A felhasználók üzembe helyezésének beállítása a Velpic

Ez a szakasz végigvezeti az Azure AD-nek a Velpic felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és Velpic alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Dönthet úgy is, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a Velpic, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-beli Velpic:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a Velpic az egyszeri bejelentkezéshez, keresse meg a Velpic-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Velpic** az alkalmazás-gyűjteményben. Válassza a Velpic lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Velpic példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Velpic kiépítés](./media/velpic-provisioning-tutorial/Velpic1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet&Velpic titkos jogkivonatát** . (Ezek az értékek a Velpic-fiókban találhatók: **kezelés**  >  **Integráció**  >  **Beépülő modul**  >  **Scim**)

    ![Engedélyezési értékek](./media/velpic-provisioning-tutorial/Velpic2.png)

6. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Velpic-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Velpic-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra az 5. lépéssel.

7. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

8. Kattintson a **Save** (Mentés) gombra.

9. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Velpic**lehetőséget.

10. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az Velpic-be szinkronizálandó felhasználói attribútumokat. Vegye figyelembe, hogy a **megfelelő** tulajdonságokként kiválasztott attribútumok a Velpic felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a Save (Mentés) gombra.

11. Az Azure AD-kiépítési szolgáltatás Velpic való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

12. Kattintson a **Save** (Mentés) gombra.

Ezzel elindítja a felhasználók és csoportok szakaszban Velpic rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb ideig tart, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységek jelentéseire mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)