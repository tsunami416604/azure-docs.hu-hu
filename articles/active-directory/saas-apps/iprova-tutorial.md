---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iProva |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iProva között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c65f8e023e087c3ca49e0d53f5760fbcd9a5f4d9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869207"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iProva

Ebben az oktatóanyagban elsajátíthatja, hogyan iProva integrálása az Azure Active Directory (Azure AD).
IProva integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá iProva Azure AD-ben.
* A felhasználók számára, hogy automatikusan jelentkezzenek be az Azure AD-fiókjukkal (egyszeri bejelentkezés) iProva engedélyezheti.
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

IProva az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, megtekintheti egy hónapos próbaverzió a [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/) webhelyén.
* Egy iProva előfizetés SSO engedélyezése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés tesztelési környezetben:

* iProva SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-iprova-from-the-gallery"></a>IProva hozzáadása a katalógusból

IProva integrálása az Azure AD beállításához adja hozzá a katalógus iProva a felügyelt SaaS-alkalmazások listájában.

A katalógusból iProva hozzáadásához kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **iProva**. Válassza ki **iProva** az eredmény panelen, és válassza ki a **Hozzáadás** , vegye fel az alkalmazást.

     ![az eredmények listájában iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés iProva Britta Simon nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés működjön, meg kell hivatkozás közötti kapcsolat létrehozásához egy Azure AD-felhasználót és a kapcsolódó felhasználó iProva a.

Az Azure AD egyszeri bejelentkezés az iProva tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

- [Konfigurációs adatok lekérését iProva](#retrieve-configuration-information-from-iprova) egy előkészítő lépések szerint.
- [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
- [IProva egyszeri bejelentkezés konfigurálása](#configure-iprova-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
- [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
- [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
- [Hozzon létre egy iProva tesztfelhasználót](#create-an-iprova-test-user) Britta Simon egy megfelelője a iProva, amely kapcsolódik a felhasználó Azure ad-ben reprezentációja az rendelkeznie.
- [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="retrieve-configuration-information-from-iprova"></a>Konfigurációs adatok lekérését iProva

Ebben a szakaszban az adatok lekérését az Azure AD egyszeri bejelentkezés konfigurálása iProva.

1. Nyisson meg egy webböngészőt, és nyissa meg a **egy SAML2 info** a következő URL-minta használatával iProva lapján:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Megtekintheti a iProva egy SAML2 információs oldalán](media/iprova-tutorial/iprova-saml2-info.png)

2. Hagyja nyitva a böngészőlapot, miközben, folytassa a következő lépéseket egy új böngészőlapon.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iProva, kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com/), az a **iProva** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza ki a **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Az alapszintű SAML-konfigurációja ikonjának szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket.

    a. Töltse ki a **azonosító** mögött a felirat jelenik meg érték a rendszerrel együtt **EntityID** a a **iProva egy SAML2 info** lapot. Ez a lap megnyitva, a másik böngészőlapon található.

    b. Töltse ki a **válasz-URL-cím** mögött a felirat jelenik meg érték a rendszerrel együtt **válasz URL-cím** a a **iProva egy SAML2 info** lapot. Ez a lap megnyitva, a másik böngészőlapon található.

    c. Töltse ki a **bejelentkezési URL-** mögött a felirat jelenik meg érték a rendszerrel együtt **bejelentkezési URL-** a a **iProva egy SAML2 info** lapot. Ez a lap megnyitva, a másik böngészőlapon található.

    ![iProva tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

5. A iProva alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás integrációs oldalán található. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel bezárásához.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakasz a **felhasználói attribútumok** párbeszédpanelen adja meg az SAML-jogkivonat attribútum az előző képen látható módon. Kövesse az alábbi lépéseket.

    | Name (Név) | Forrásattribútum| Névtér |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Válassza ki **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel bezárásához.

    ![Felhasználói jogcímek](common/new-save-attribute.png)

    ![Felhasználói jogcímek párbeszédpanel kezelése](common/new-attribute-details.png)

    b. Az a **neve** mezőben megadhatja azon attribútum nevét, a sorhoz látható.

    c. Az a **Namespace** listában, adja meg a névtér értéke a sorhoz látható.

    d. Válassza ki a **forrás** lehetőséget igény **attribútum**.

    e. Az a **forrásattribútum** listában, adja meg az adott sorhoz feltüntetett attribútum értéke.

    f. Kattintson az **OK** gombra.

    g. Kattintson a **Mentés** gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **másolási** ikonra kattintva másolja a **alkalmazás összevonási metaadatainak URL-címe**  , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>IProva egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be iProva használatával a **rendszergazda** fiókot.

2. Nyissa meg a **Ugrás** menü.

3. Válassza ki **Alkalmazáskezelés**.

4. Válassza ki **általános** a a **rendszerbeállítások** panel.

5. Válassza a **Szerkesztés** elemet.

6. Görgessen le a **hozzáférés-vezérlés**.

    ![Hozzáférés-vezérlési beállításokkal iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Keresse meg a beállítás **automatikusan bejelentkezett felhasználó hálózati fiókjukkal**, és módosítsa a következőre **Igen, az SAML-n keresztül hitelesítést**. Most további lehetőségek jelennek meg.

8. Válassza ki **beállítása**.

9. Kattintson a **Tovább** gombra.

10. iProva megkérdezi összevonási adatokat letölteni egy URL-címet, vagy töltse fel egy fájlból. Válassza ki a **URL-CÍMRŐL** lehetőséget.

    ![Az Azure AD-metaadatok letöltése](media/iprova-tutorial/iprova-download-metadata.png)

11. Illessze be az "Azure AD konfigurálása egyszeri bejelentkezéshez" szakasz az előző lépésben mentett metaadatok URL-címe.

12. Válassza ki a alakú nyíl gombra kattintva töltse le a metaadatokat az Azure ad-ből.

13. Ha a letöltés befejeződött, a megerősítést kérő üzenet **letöltött fájl érvényes összevonási adatok** jelenik meg.

14. Kattintson a **Tovább** gombra.

15. Hagyja ki a **teszt bejelentkezési** most lehetőséget, majd válassza ki **tovább**.

16. Az a **használandó Jogcímszabály** legördülő mezőben válassza ki **windowsaccountname**.

17. Válassza a **Finish** (Befejezés) elemet.

18. Ekkor visszatér a **általános beállításainak szerkesztése** képernyő. Görgessen le a lap alján, és válassza ki **OK** kattintva mentse a konfigurációt.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és csoportok, valamint minden felhasználói kapcsolat](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** párbeszédpanelen kövesse az alábbi lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőben adjon meg egy nevet, például **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be *yourname@yourcompanydomain.extension*. 
    Például: BrittaSimon@contoso.com.

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iProva való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **iProva**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **iProva**.

    ![Az alkalmazások listáját a iProva hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A felhasználók és csoportok hivatkozás](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása párbeszédpanel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki **Britta Simon** a a **felhasználók** listában, és válassza a **válassza** a képernyő alján.

6. Ha a várt bármely szerepkör értékét az SAML-előfeltétel a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Válasszon **kiválasztása** a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-an-iprova-test-user"></a>Hozzon létre egy iProva tesztfelhasználó számára

1. Jelentkezzen be iProva használatával a **rendszergazda** fiókot.

2. Nyissa meg a **Ugrás** menü.

3. Válassza ki **Alkalmazáskezelés**.

4. Válassza ki **felhasználók** a a **felhasználók és csoportok** panel.

5. Válassza a **Hozzáadás** lehetőséget.

6. Az a **felhasználónév** mezőbe írja be *brittasimon@yourcompanydomain.extension*. 
    Például: BrittaSimon@contoso.com.

7. Az a **teljes fájlvisszaállítási név** mezőbe írja be például a teljes nevet **BrittaSimon**.

8. Válassza ki a **(használata egyszeri bejelentkezéshez) jelszavát nem** lehetőséget.

9. Az a **E-mail-cím** mezőbe írja be *yourname@yourcompanydomain.extension*. 
   Például: BrittaSimon@contoso.com.

10. Görgessen a lap aljára, és válassza ki **Befejezés**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A iProva csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a iProva, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva – egy SAML2 egyszeri bejelentkezés konfigurálása](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
