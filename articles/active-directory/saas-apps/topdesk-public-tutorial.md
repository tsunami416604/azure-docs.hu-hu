---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TOPdesk – nyilvános |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés TOPdesk – nyilvános és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: f3190cb7f837464dd211acb127c4ab6f86e7a9f4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153300"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TOPdesk – nyilvános

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja TOPdesk - nyilvános, az Azure Active Directoryval (Azure AD).

TOPdesk – nyilvános és az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá TOPdesk – nyilvános Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a TOPdesk – nyilvános (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az TOPdesk - nyilvános, a következőkre van szüksége:

- Azure AD-előfizetés
- Egy TOPdesk – nyilvános egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás TOPdesk – nyilvános a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-topdesk---public-from-the-gallery"></a>Hozzáadás TOPdesk – nyilvános a katalógusból
TOPdesk – az Azure AD-be nyilvános integráció konfigurálásához hozzá kell TOPdesk – nyilvános a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a TOPdesk – nyilvános katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **TOPdesk – nyilvános**, jelölje be **TOPdesk – nyilvános** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![TOPdesk – nyilvános a találatok listájában](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az TOPdesk tesztelése és konfigurálása,-nyilvános "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés használatát Azure ad-ben tudnia kell, milyen a partner felhasználó TOPdesk – a nyilvános, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TOPdesk - hivatkozás kapcsolata nyilvános kell létrehozni.

TOPdesk – a nyilvános, az értéket a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

A konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TOPdesk – a nyilvános, az alábbi építőelemeket végrehajtásához szükséges:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy TOPdesk – nyilvános tesztfelhasználó](#create-a-topdesk---public-test-user)**  – egy megfelelője a Britta Simon TOPdesk – a felhasználó Azure ad-ben ábrázolása csatolt nyilvános van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a TOPdesk – nyilvános alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása TOPdesk - nyilvános, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TOPdesk – nyilvános** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. Az a **TOPdesk – a nyilvános tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![TOPdesk – a nyilvános tartomány és URL-címek egyszeri bejelentkezési adatait](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net`
    
    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Válasz URL-cím explaned az oktatóanyag későbbi részében. Kapcsolattartó [TOPdesk – nyilvános ügyfél-támogatási csapatának](https://help.topdesk.com/saas/enterprise/user/) beolvasni ezeket az értékeket.  

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. Az a **TOPdesk – nyilvános Bővítménykonfiguráció** területén kattintson **TOPdesk konfigurálása – nyilvános** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![TOPdesk – nyilvános konfiguráció](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Jelentkezzen be a **TOPdesk – nyilvános** rendszergazdaként a vállalati webhely.

1. Az a **TOPdesk** menüben kattintson a **beállítások**.
   
    ![Beállítások](./media/topdesk-public-tutorial/ic790598.png "beállításai")

1. Kattintson a **bejelentkezés beállításai**.
   
    ![Bejelentkezési beállítások](./media/topdesk-public-tutorial/ic790599.png "bejelentkezési beállítások")

1. Bontsa ki a **bejelentkezési beállítások** menüre, majd **általános**.
   
    ![Általános](./media/topdesk-public-tutorial/ic790600.png "általános")

1. Az a **nyilvános** szakaszában a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Műszaki beállítások](./media/topdesk-public-tutorial/ic790601.png "műszaki beállítások")
   
    a. Kattintson a **letöltése** töltse le a nyilvános metaadatait tartalmazó fájl, és mentse helyileg a számítógépen.
   
    b. Nyissa meg a letöltött metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Másolás a **AssertionConsumerService** értékét, illessze be ezt az értéket a **válasz URL-cím** szövegmezőjébe **TOPdesk – a nyilvános tartomány és URL-címek** szakaszban.      
   
1. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:
    
    ![Tanúsítvány](./media/topdesk-public-tutorial/ic790606.png "tanúsítvány")
    
    a. Az Azure Portalról nyissa meg a letöltött metaadatait tartalmazó fájl.
    
    b. Bontsa ki a **található Securitytokenservicetype** csomópont, amely rendelkezik egy **xsi: type** , **megváltoztat: ApplicationServiceType**.
    
    c. Másolja az értéket, a **X509Certificate** csomópont.
    
    d. Mentse a másolt **X509Certificate** érték helyileg a számítógépen egy fájlban.

1. Az a **nyilvános** területén kattintson **Hozzáadás**.
    
    ![SAML-bejelentkezési](./media/topdesk-public-tutorial/ic790625.png "SAML-bejelentkezés")

1. Az a **SAML-konfigurációja Segéd** párbeszédpanel lapon, a következő lépésekkel:
    
    ![SAML-konfigurációja Segéd](./media/topdesk-public-tutorial/ic790608.png "SAML-konfigurációja Segéd")
    
    a. Az Azure Portalról letöltött metaadatfájl feltöltése a **összevonási metaadatok**, kattintson a **Tallózás**.

    b. A feltölteni a tanúsítványfájlt, **tanúsítvány (RSA)**, kattintson a **Tallózás**.

    c. A kapott az TOPdesk ügyfélszolgálathoz alatt embléma fájlt feltölteni **embléma ikon**, kattintson a **Tallózás**.

    d. Az a **felhasználói név attribútum** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Az a **megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-topdesk---public-test-user"></a>Hozzon létre egy TOPdesk – nyilvános tesztfelhasználó számára

Ahhoz, hogy Azure AD-felhasználók TOPdesk - szolgáltatásba való bejelentkezéshez, ki kell építeni TOPdesk - nyilvános, nyilvános.  
TOPdesk - esetén nyilvános kiépítése a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **TOPdesk – nyilvános** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **TOPdesk \> új \> támogatófájljait \> személy**.
   
    ![Személy](./media/topdesk-public-tutorial/ic790628.png "személy")

1. Új személy párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Új személy](./media/topdesk-public-tutorial/ic790629.png "új személy")
   
    a. Kattintson az Általános lapon.

    b. Az a **Vezetéknév** szövegmezőbe írja be például a Simon a felhasználó vezetékneve
 
    c. Válassza ki a **hely** a fiókhoz.
 
    d. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más TOPdesk – nyilvános felhasználói fiók létrehozása eszközöket használhatja, vagy TOPdesk – az Azure AD-felhasználói fiókok kiépítése nyilvános által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított TOPdesk - nyilvános hozzáférés Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése TOPdesk – nyilvános, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **TOPdesk – nyilvános**.

    ![A TOPdesk – nyilvános hivatkozást alkalmazásainak listájában](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattint a TOPdesk – nyilvános csempére a hozzáférési panelen, amikor meg kell lekérése automatikusan bejelentkezett, a TOPdesk – nyilvános alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

