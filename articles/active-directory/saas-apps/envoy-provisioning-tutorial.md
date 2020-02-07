---
title: 'Oktatóanyag: a megbízottat konfigurálása a felhasználók automatikus üzembe helyezéséhez Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a megbízott felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058025"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Oktatóanyag: a megbízottat konfigurálása automatikus felhasználó-kiépítési folyamathoz

Ennek az oktatóanyagnak a célja, hogy bemutassa a megbízottat és Azure Active Directory (Azure AD) végrehajtandó lépéseit az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a megbízottat.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy megbízottat bérlő](https://envoy.com/pricing/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók.

## <a name="add-envoy-from-the-gallery"></a>Megbízottat hozzáadása a katalógusból

Mielőtt beállítja a megbízottat az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia egy megbízottat az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha a megbízottat az Azure AD-alkalmazás-katalógusból szeretné felvenni, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **megbízottat**, válassza a **megbízottat** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Az eredmények listájában szereplő megbízottat](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Felhasználók kiosztása a megbízottnak

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjai férhetnek hozzá a megbízotthoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a megbízotthoz:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Fontos Tippek a felhasználók a megbízottat való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a megbízottat a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót a megbízottat rendel hozzá, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>A felhasználók automatikus kiépítési megbízottjának konfigurálása 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoport-hozzárendelések alapján a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a megbízott számára, a [megbízottat egyszeri bejelentkezés oktatóanyagában](envoy-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-beli megbízottat:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **megbízottat**.

    ![A megbízottat mutató hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a `https://app.envoy.com/scim/v2` a **bérlői URL-címben**. A megbízottat tartalmazó fiók **titkos jogkivonatának** lekéréséhez kövesse a 6. lépésben leírt útmutatót.

6. Jelentkezzen be a [megbízott felügyeleti konzolra](https://dashboard.envoy.com/login). Kattintson az **integrációk**elemre.

    ![A megbízottat integrációk](media/envoy-provisioning-tutorial/envoy01.png)

    Kattintson a **telepítés** gombra a **Microsoft Azure scim-integrációhoz**.

    ![Megbízott telepítése](media/envoy-provisioning-tutorial/envoy02.png)

    Kattintson a **Mentés** gombra az **összes felhasználó szinkronizálásához**. 

    ![Megbízott mentése](media/envoy-provisioning-tutorial/envoy03.png)

    Töltse le a titkos tokent.
    
    ![Megbízott OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a megbízotthoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a megbízott fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a megbízottat**lehetőséget.
    
    ![Felhasználói attribútumok megbízottat](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a megbízott felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Felhasználói attribútumok megbízottat](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a megbízottat**lehetőséget.

    ![Felhasználó-hozzárendelések megbízottja](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a megbízott csoportoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Felhasználó-hozzárendelések megbízottja](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a megbízottat, módosítsa a **kiépítési állapotot** a **következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a megbízottat szeretné kiépíteni. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

17. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti az üzembe helyezési tevékenység jelentésre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a megbízotton végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

