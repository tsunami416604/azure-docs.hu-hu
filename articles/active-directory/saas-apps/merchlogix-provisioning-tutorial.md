---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása MerchLogix |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének MerchLogix felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263596"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása MerchLogix konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a MerchLogix és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a MerchLogix bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* Egy MerchLogix bérlő
* A technikai kapcsolattartó MerchLogix, amely biztosít az SCIM-végpont URL-címe és a felhasználók átadásának szükséges titkos jogkivonat

## <a name="adding-merchlogix-from-the-gallery"></a>MerchLogix hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus MerchLogix konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában MerchLogix hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában MerchLogix hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]

3. MerchLogix hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **MerchLogix**.

5. Az eredmények panelen válassza ki a **MerchLogix**, majd kattintson a **hozzáadása** gombra kattintva adhat hozzá MerchLogix a SaaS-alkalmazások listájára.

    ![MerchLogix kiépítése][4]

## <a name="assigning-users-to-merchlogix"></a>Felhasználók hozzárendelése MerchLogix

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak. 

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben MerchLogix hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok MerchLogix utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Felhasználók hozzárendelése MerchLogix fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve MerchLogix a kezdeti felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok rendelhetők később követően a tesztek sikere esetén.

* Amikor egy felhasználó hozzárendelése MerchLogix, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>MerchLogix történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a MerchLogix.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az MerchLogix engedélyezése, a biztonsági utasítások megadott a [MerchLogix egyszeri bejelentkezéses oktatóanyag](merchlogix-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása MerchLogix az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Válassza ki a MerchLogix SaaS-alkalmazások listájából.

3. Válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![MerchLogix kiépítése](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban:

    * Az a **bérlői URL-cím** mezőben adja meg az SCIM végponti URL-cím MerchLogix műszaki partnere által biztosított.

    * Az a **titkos jogkivonat** mezőben adja meg titkos jogkivonat MerchLogix műszaki partnere által biztosított.

6. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD MerchLogix csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, MerchLogix fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

8. Kattintson a **Save** (Mentés) gombra.

9. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a MerchLogix**.

10. Tekintse át a MerchLogix a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek MerchLogix levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

11. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, MerchLogix**.

12. Tekintse át a csoportattribútumok MerchLogix a az Azure AD-ből szinkronizált a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek a MerchLogix a frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

13. Az Azure AD létesítési szolgáltatás MerchLogix engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

14. Ha készen áll rendelkezésre, kattintson a **mentése**.

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a MerchLogix által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
