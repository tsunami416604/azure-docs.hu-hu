---
title: 'Oktatóanyag: a Zscaler privát hozzáférésének (ZPA) konfigurálása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a privát hozzáférés Zscaler (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: c5274f3c38782394b01f8f29c56823753783307c
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287722"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Oktatóanyag: a Zscaler privát hozzáférésének (ZPA) konfigurálása a felhasználók automatikus kiépítési felállításához

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Zscaler (ZPA) és Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a privát Zscaler (ZPA).

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Zscaler privát hozzáférés (ZPA) bérlő](https://www.zscaler.com/pricing-and-plans#contact-us)
* Egy felhasználói fiók a Zscaler Private Accessben (ZPA) rendszergazdai engedélyekkel.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Felhasználók kiosztása Zscaler privát hozzáféréshez (ZPA)

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá a Zscaler privát eléréséhez (ZPA). A döntés megkezdése után ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler (ZPA) a következő utasítások követésével:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Fontos Tippek a felhasználók privát Zscaler való hozzárendeléséhez (ZPA)

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Zscaler privát eléréséhez (ZPA) az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználót a Zscaler (ZPA) rendeli hozzá, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>A Zscaler privát hozzáférésének (ZPA) beállítása a kiépítés számára

1. Jelentkezzen be a [Zscaler Private Access (ZPA) felügyeleti konzolra](https://admin.private.zscaler.com/). Navigáljon az **adminisztráció > identitásszolgáltató-konfiguráció**elemre.

    ![Zscaler magánhálózati hozzáférés (ZPA) felügyeleti konzol](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Győződjön meg arról, hogy az **egyszeri bejelentkezéshez** identitásszolgáltató van konfigurálva. Ha nincs beállítva identitásszolgáltató, a képernyő jobb felső sarkában található plusz ikonra kattintva vegyen fel egyet.

    ![Zscaler privát hozzáférés (ZPA) SCIM hozzáadása](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. A identitásszolgáltató hozzáadásához kövesse a **identitásszolgáltató-konfiguráció hozzáadása** varázslót. Hagyja meg az **egyszeri bejelentkezés** mezőt a **felhasználó**értékre. Adjon meg egy **nevet** , és válassza ki a **tartományokat** a legördülő listából. A tovább **gombra** kattintva navigáljon a következő ablakra.

    ![Zscaler privát hozzáférés (ZPA) identitásszolgáltató hozzáadása](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Töltse le a **szolgáltatói tanúsítványt**. A tovább **gombra** kattintva navigáljon a következő ablakra.

    ![Zscaler Private Access (ZPA) SP-tanúsítvány](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. A következő ablakban töltse fel a korábban letöltött **Service Provider-tanúsítványt** .

    ![Zscaler privát hozzáférés (ZPA) – tanúsítvány feltöltése](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Görgessen le az **egyszeri bejelentkezési URL-cím** és a **IDENTITÁSSZOLGÁLTATÓ-entitás azonosítójának**megadásához.

    ![Zscaler privát hozzáférés (ZPA) identitásszolgáltató azonosítója](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Görgessen le a **scim szinkronizálásának engedélyezéséhez**. Kattintson az **új jogkivonat előállítása** gombra. Másolja a **tulajdonosi jogkivonatot**. Ez az érték a Zscaler Private Access (ZPA) alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében lesz megadva a Azure Portal.

    ![Zscaler privát hozzáférés (ZPA) jogkivonat létrehozása](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  A **bérlői URL-cím** megkereséséhez navigáljon az **Adminisztráció > identitásszolgáltató-konfiguráció**elemre. Kattintson a lapon látható, újonnan hozzáadott identitásszolgáltató-konfiguráció nevére.

    ![Zscaler magánhálózati hozzáférés (ZPA) identitásszolgáltató neve](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Görgessen le a **scim szolgáltatói végpont** megjelenítéséhez a lap végén. Másolja a **scim-szolgáltató végpontját**. Ezt az értéket a Zscaler Private Access (ZPA) alkalmazás létesítés lapjának bérlői URL-címe mezőjében kell megadni a Azure Portal.

    ![Zscaler privát hozzáférés (ZPA) SCIM URL-címe](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler privát hozzáférésének (ZPA) hozzáadása a gyűjteményből

Mielőtt konfigurálja a Zscaler privát hozzáférését (ZPA) az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Zscaler privát hozzáférését (ZPA) az Azure AD Application Galleryből a felügyelt SaaS-alkalmazások listájához.

**A következő lépésekkel adhatja hozzá a Zscaler privát hozzáférését (ZPA) az Azure AD Application Galleryből:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler privát elérését (ZPA)** , válassza az **Zscaler privát elérését (ZPA)** az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler privát hozzáférése (ZPA) az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Automatikus felhasználó-kiépítés konfigurálása a Zscaler privát eléréséhez (ZPA) 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Zscaler alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Zscaler privát eléréséhez (ZPA) a [Zscaler Private Access (ZPA) egyszeri bejelentkezési oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a Zscaler SCIM-végpontról, olvassa el [ezt a témakört](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a Zscaler privát eléréséhez (ZPA) az Azure AD-ben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler privát hozzáférés (ZPA)** lehetőséget.

    ![Az Zscaler privát hozzáférés (ZPA) hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címben**korábban lekért **scim-szolgáltatói végpont** értékét. Adja meg a **titkos jogkivonatban**korábban lekért **tulajdonosi jogkivonat** értékét. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Zscaler privát eléréséhez (ZPA). Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler magánhálózati hozzáférési (ZPA) fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Zscaler privát hozzáférés (ZPA)** lehetőséget.

    ![Zscaler privát hozzáférés (ZPA) felhasználói leképezései](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az Zscaler privát hozzáférésének (ZPA) az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler privát hozzáférés (ZPA) felhasználói fiókjainak a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler privát hozzáférés (ZPA) felhasználói attribútumai](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget a Zscaler (ZPA) lehetőségre**.

    ![Zscaler privát hozzáférés (ZPA) csoport leképezései](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált Zscaler (ZPA) az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler privát hozzáférés (ZPA) csoportjának a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler Private Access (ZPA) csoport attribútumai](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Zscaler privát eléréséhez (ZPA), módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Zscaler (ZPA) szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a Zscaler Private Accessben (ZPA).

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

