---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval összevont könyvtár konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Directory összevont felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840588"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Oktatóanyag: Összevont Directory konfigurálása a felhasználók automatikus átadása

Ez az oktatóanyag célja a lépések végrehajtása összevont Directory és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok Directory összevont is bemutatják.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Egy Azure AD-bérlő.
* [A Directory összevont](https://www.federated.directory/pricing).
* Egy felhasználói fiók összevont címtárban rendszergazdai jogosultságokkal rendelkezik.

## <a name="assign-users-to-federated-directory"></a>Felhasználók hozzárendelése összevont könyvtár
Az Azure Active Directory egy fogalom, más néven hozzárendeléseket használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure AD Directory összevont hozzáféréssel kell rendelkezniük. Ha úgy döntött, rendelhet a felhasználók és csoportok Directory összevont utasításokat követve:

 * [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Felhasználók hozzárendelése Directory összevont fontos tippek
 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve összevont Directory a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Összevont könyvtárba való hozzárendelésekor a felhasználó, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva, a létesítési.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Összevont Directory beállítása az üzembe helyezés

Mielőtt konfigurálná a Directory összevont felhasználók automatikus üzembe helyezés az Azure ad-vel, szüksége lesz az összevont Directory SCIM-kiépítés engedélyezése.

1. Jelentkezzen be a [összevont könyvtár-felügyeleti konzol](https://federated.directory/of)

    ![Összevont Directory oktatóanyag](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigáljon a **könyvtárak > felhasználó könyvtárak** , és válassza ki a bérlő. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Hozzon létre egy állandó tulajdonosi jogkivonattal, navigáljon a **Directory kulcsok > új kulcs létrehozása.** 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated01.png)

4. Hozzon létre egy könyvtárat kulcsot. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Másolás a **Access Token** értéket. Ezt az értéket fog megadni az **titkos jogkivonat** mezőt a kiépítés lapon az összevont Directory-alkalmazás az Azure Portalon. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Összevont könyvtár hozzáadása a katalógusból

Összevont könyvtár konfigurálása az Azure AD-felhasználók automatikus, szüksége az Azure AD alkalmazáskatalógusában összevont könyvtár hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Directory összevont hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Directory összevont**válassza **Directory összevont** az eredmények panelen.

    ![Az eredmények listájában összevont könyvtár](common/search-new-app.png)

5. Keresse meg a **URL-cím** kiemelve alább a egy külön böngészőablakot. 

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Kattintson a **bejelentkezés**.

    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Mivel Directory összevont OpenIDConnect alkalmazás, válassza a bejelentkezés az összevont Directory, a Microsoft munkahelyi fiókjával.
    
    ![összevont könyvtár](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. A sikeres hitelesítés után fogadja el a hozzájárulást kérő lap hozzájárulási kérése. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja az összevont Directory-fiókjához.

    ![összevont directory SCIM hozzáadása](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Összevont Directory történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok az Azure AD-ben a felhasználó és/vagy a csoport-hozzárendelések alapján összevont Directory konfigurálásának lépéseit.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása összevont címtár Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Directory összevont**.

    ![Az alkalmazások listáját az összevont Directory hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://api.federated.directory/v2/` a bérlői URL-cím. Az értéket, amely kéri le, és a korábban mentett Directory összevont **titkos jogkivonat**. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Directory összevont csatlakozhat. Ha a kapcsolat nem sikerül, ellenőrizze a Directory összevont fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók Directory összevont**.

    ![Összevont Directory oktatóanyag](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Tekintse át az összevont címtárhoz az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságai megfelelően a frissítési műveletek összevont címtárban levő felhasználói fiókokat használnak. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Összevont Directory oktatóanyag](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás összevont Directory engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. A felhasználók és/vagy a csoportok, adja meg kiépítése összevont könyvtárba való meghatározásához válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás összevont könyvtár által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)