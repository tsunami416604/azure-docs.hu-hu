---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés beállítása a Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058346"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés konfigurálása

Ennek az oktatóanyagnak a célja, hogy bemutassa az Azure AD konfigurálásához Azure Active Directory szükséges lépéseket, hogy a felhasználók és/vagy csoportok automatikus kiosztását és kiépítését (Azure AD) is be lehessen állítani.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy](https://www.dialpad.com/pricing/)meglevő bérlő.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók.

## <a name="assign-users-to-dialpad"></a>Felhasználók társításának kiosztása
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok számára milyen hozzáférésre van szükség. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat:
 
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Fontos Tippek a felhasználók kiosztásához

 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a felhasználók automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználóhoz rendeli a kiosztást, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-dialpad-for-provisioning"></a>A telepítés a kiépítés során

Az Azure AD-vel való automatikus felhasználó-kiépítés konfigurálása előtt be kell szereznie néhány kiépítési információt a sablonból.

1. Jelentkezzen be a megadható [felügyeleti konzolra](https://dialpadbeta.com/login) , és válassza a **rendszergazdai beállítások**lehetőséget. Győződjön meg arról, hogy a **vállalatom** ki van választva a legördülő listából. Navigáljon a **hitelesítés > API-kulcsok**elemre.

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Új kulcs létrehozásához kattintson **a kulcs hozzáadása** és a titkos token tulajdonságainak konfigurálása elemre.

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kattintson a **kattintson ide az érték megjelenítése** gombra a legutóbb létrehozott API-kulcshoz, és másolja a megjelenő értéket. Ez az érték a Azure Portalban a kiépítés lapjának **titkos jogkivonat** mezőjében lesz megadva. 

    ![A létrehozott jogkivonat létrehozása](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>A gyűjteményhez tartozó Hozzáadás a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés konfigurálásához az Azure AD-alkalmazás-katalógusból kell megadnia a felügyelt SaaS-alkalmazások listáját.

**Az Azure AD-alkalmazás-katalógusból az alábbi lépéseket követve adhatja hozzá a-t:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A **keresőmezőbe írja be**a megjelenő kifejezést **, válassza az eredmények panelen a** kiválasztva lehetőséget.
    ![Az eredmények listájában](common/search-new-app.png)

5. Nyissa meg az alábbi **URL-címet** egy külön böngészőben. 

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/dialpad05.png)

6. A jobb felső sarokban válassza a **bejelentkezés > az online kapcsolat használata**lehetőséget.

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Mivel a OpenIDConnect egy alkalmazás, a Microsoft munkahelyi fiókjával való bejelentkezéshez válassza a megfelelő lehetőséget.

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/loginpage.png)

8. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a felhasználói fiókhoz.

    ![SCIM hozzáadása](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Az automatikus felhasználó-kiépítés beállítása a kiosztott értékre

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a felhasználókat és/vagy csoportokat.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való használatra:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások **listában válassza a**kiválasztva lehetőséget.

    ![Az alkalmazások listájában található, az alkalmazáshoz tartozó hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://dialpad.com/scim` meg a **bérlői URL-címet**. Adja meg a beolvasott és a korábban mentett értéket a **titkos jogkivonatban**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a megadott fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók számára**lehetőséget.

    ![Felhasználói hozzárendelések](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt felhasználói fiókok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A felhasználói attribútumok](media/dialpad-provisioning-tutorial/dialpad07.png)

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a (z) számára, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni, ha a **Settings** ( **hatókör** ) területen a kívánt értékeket választja.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md) helyezésével kapcsolatban.
##  <a name="connector-limitations"></a>Összekötő korlátozásai
* A (z) rendszer nem támogatja a csoport-átnevezni még ma. Ez azt jelenti, hogy az Azure AD-beli csoportok **DisplayName** -beli módosításait nem frissíti a rendszer, és az a következő módon jelenik meg:.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
