---
title: 'Oktatóanyag: A Zscaler konfigurálása az automatikus felhasználó-kiépítés Azure Active Directorykal | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy a három Zscaler automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 43ae028e57578634c34c69357a264fdb180b8a1f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515362"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler három beállítása a felhasználók automatikus kiépítési felállításához

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) a felhasználók és/vagy csoportok automatikus kiépítésének és megszüntetésének Zscaler háromra.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működéséről és működéséről, valamint a gyakori kérdésekre adott válaszokról a következő témakörben talál további információt: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett lépések végrehajtásához a következőkre lesz szüksége:

* Egy Azure AD-bérlő.
* Egy Zscaler három bérlő.
* Egy felhasználói fiók a Zscaler-ben három rendszergazdai engedélyekkel.

> [!NOTE]
> Az Azure AD kiépítési integrációja a Zscaler ZSCloud SCIM API-ra támaszkodik, amely vállalati fiókok számára érhető el.

## <a name="adding-zscaler-three-from-the-gallery"></a>Három Zscaler hozzáadása a katalógusból

Mielőtt a Zscaler-t az Azure AD-vel való automatikus felhasználók számára konfigurálja, az Azure AD-Zscaler hármat hozzá kell adnia a felügyelt SaaS-alkalmazások listájához.

A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**elemet:

![Vállalati alkalmazások](common/enterprise-applications.png)

Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

![Új alkalmazás kiválasztása](common/add-new-app.png)

A keresőmezőbe írja be a **Zscaler Three**kifejezést. Válassza ki a **Zscaler három** elemet az eredmények között, majd válassza a **Hozzáadás**lehetőséget.

![Találatok listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Felhasználók kiosztása három Zscaler

Az Azure AD-felhasználóknak hozzá kell rendelniük a hozzáférést a kiválasztott alkalmazásokhoz, mielőtt azok használni tudnák őket. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a három Zscaler való hozzáféréshez. Miután eldöntötte, hogy ezeket a felhasználókat és csoportokat hozzá tudja rendelni a Zscaler-hoz, a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)című témakör utasításait követve.

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Fontos Tippek a felhasználók három Zscaler való hozzárendeléséhez

* Javasoljuk, hogy először egyetlen Azure AD-felhasználót rendeljen hozzá három Zscaler, hogy tesztelje az automatikus felhasználó-kiépítési konfigurációt. Később további felhasználókat és csoportokat is hozzárendelhet.

* Ha a felhasználó három Zscaler rendel hozzá, akkor a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-automatic-user-provisioning"></a>Automatikus felhasználó-kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben a felhasználók és csoportok Zscaler való létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Előfordulhat, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezni szeretné a Zscaler számára. Ha ezt teszi, kövesse a [Zscaler három egyszeri bejelentkezési oktatóanyagának](zscaler-three-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, de a két szolgáltatás kiegészíti egymást.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások** > **minden alkalmazás** > **Zscaler három**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler három**lehetőséget:

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza ki  a kiépítés fület:

    ![Zscaler három kiépítés](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. A **kiépítési mód** beállítása automatikusra :

    ![A kiépítési mód beállítása](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler három fiókjának **bérlői URL-címét** és titkos jogkivonatát a következő lépésben leírtak szerint.

6. A bérlői **URL-cím** és a **titkos jogkivonat**beszerzéséhez nyissa meg a **felügyeleti** > **hitelesítési beállításokat** a Zscaler három portálon, és válassza az **SAML** lehetőséget a **Hitelesítés típusa**területen:

    ![Zscaler három hitelesítési beállítás](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Válassza az **SAML konfigurálása** lehetőséget az **SAML konfigurálása** ablak megnyitásához:

    ![SAML-ablak konfigurálása](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Jelölje be az **scim létesítésének engedélyezése** jelölőnégyzetet, és másolja ki az **alap URL-címet** és a **tulajdonosi**jogkivonatot, majd mentse a beállításokat. A Azure Portal illessze be az **alap URL-címet** a **bérlői URL-cím** mezőbe és a **tulajdonos** tokenjét a **titkos jogkivonat** mezőbe.

7. A **bérlői URL-cím** és a **titkos jogkivonat** mezőiben szereplő értékek megadása után válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad csatlakozhasson a Zscaler. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler három fiók rendelkezik rendszergazdai engedélyekkel, és próbálkozzon újra.

    ![Kapcsolat tesztelése](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. **Ha hiba történik, válassza az e-mail-értesítés küldése**lehetőséget:

    ![Értesítő e-mail beállítása](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók ZscalerThree**:

    ![Azure AD-felhasználók szinkronizálása](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakasz három Zscaler. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a három Zscaler felhasználói fiókjainak a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Attribútumleképezések](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása ZscalerThree**lehetőséget:

    ![Azure AD-csoportok szinkronizálása](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ből szinkronizált Zscaler az **attribútum** -hozzárendelések szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler három csoportjának a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Attribútumleképezések](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](./../active-directory-saas-scoping-filters.md)utasításait.

15. Ahhoz, hogy az Azure AD-kiépítési szolgáltatást a Zscaler három számára engedélyezze, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban:

    ![Kiépítési állapot](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Adja meg a Zscaler kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Beállítások** szakaszban a **hatókör** területen a kívánt értékeket választja:

    ![Hatókör értékei](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget:

    ![Mentés kiválasztása](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a **Beállítások** szakaszban a **hatókör** szakaszban meghatározott összes felhasználó és csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, feltéve, hogy az Azure AD kiépítési szolgáltatás fut. Nyomon követheti a folyamat előrehaladását a **szinkronizálás részletei** szakaszban. A kiépítési tevékenységre vonatkozó jelentésre mutató hivatkozásokat is követheti, amelyek az Azure AD-kiépítési szolgáltatás által a három Zscaler végzett összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../active-directory-saas-provisioning-reporting.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
