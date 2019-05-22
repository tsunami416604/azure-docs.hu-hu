---
title: 'Oktatóanyag: A MyWorkDrive Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a MyWorkDrive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 61173d21c52d061f0d02ab02eb2f1083507983c2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991680"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Oktatóanyag: MyWorkDrive integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a MyWorkDrive integrálása az Azure Active Directory (Azure AD) lesz. MyWorkDrive integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a MyWorkDrive Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a MyWorkDrive Azure AD-fiókjukkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* MyWorkDrive egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a MyWorkDrive **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-myworkdrive-from-the-gallery"></a>MyWorkDrive hozzáadása a katalógusból

Az Azure AD integrálása a MyWorkDrive konfigurálásához hozzá kell MyWorkDrive a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **MyWorkDrive** kifejezést a keresőmezőbe.
1. Válassza ki **MyWorkDrive** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata MyWorkDrive **Britta Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a MyWorkDrive szüksége.

A MyWorkDrive Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[MyWorkDrive egyszeri bejelentkezést](#configure-myworkdrive-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre MyWorkDrive tesztfelhasználót](#create-myworkdrive-test-user)**  – egy megfelelője a Britta Simon MyWorkDrive, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **MyWorkDrive** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lapon, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, adja meg az értékeket a következő mező:

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Adjon meg egy saját vállalati MyWorkDrive Server gazdagép name:e.g.
    > 
    > Válasz URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Bejelentkezési URL-címe:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kapcsolattartó [MyWorkDrive támogatási csapatának](mailto:support@myworkdrive.com) Ha nem tudja, hogyan állíthatja be a saját állomás nevét és az SSL-tanúsítványt ezeket az értékeket.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** a vágólapra.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>MyWorkDrive egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be MyWorkDrive-Security-rendszergazdaként.

2. A MyWorkDrive kiszolgálón a rendszergazda panelen, kattintson a **vállalati** , és hajtsa végre az alábbi lépéseket:

    ![A rendszergazda](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Engedélyezése **SAML/AD FS SSO**.

    b. Válassza ki **SAML - Azure ad-ben**

    c. Az a **Azure alkalmazás összevonási metaadatainak URL-címe** szövegmezőjébe illessze be az értéket, **alkalmazás összevonási metaadatainak URL-címe** az Azure Portalról másolt.

    d. Kattintson a **Save** (Mentés) gombra

    > [!NOTE]
    > További információkat tekintse át a [MyWorkDrive Azure AD-támogatási cikk](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés MyWorkDrive Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **MyWorkDrive**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű MyWorkDrive hoz létre. Együttműködve [MyWorkDrive támogatási csapatának](mailto:support@myworkdrive.com) a felhasználók hozzáadása az MyWorkDrive platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A MyWorkDrive csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett a MyWorkDrive, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)