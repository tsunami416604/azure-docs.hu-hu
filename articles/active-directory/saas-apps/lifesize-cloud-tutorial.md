---
title: 'Oktatóanyag: Azure Active Directory-integráció Lifesize felhővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Lifesize felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c03456dcda2b3ee44686b070cdebb5fc81c3968c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449179"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Oktatóanyag: Azure Active Directory-integráció Lifesize felhővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Lifesize Felhőbeli integrálása az Azure Active Directory (Azure AD).

Lifesize Felhőbeli integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy hozzáféréssel rendelkező Lifesize felhőalapú Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Lifesize felhőbe (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Lifesize felhőalapú Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy felhőalapú Lifesize egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Lifesize felhő hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Lifesize felhő hozzáadása a katalógusból
Lifesize Felhőbeli integrálása az Azure AD beállítása, hozzá kell Lifesize felhőalapú a galériából a felügyelt SaaS-alkalmazások listájára.

**Lifesize felhő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Lifesize felhőalapú**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

1. Az eredmények panelen válassza ki a **Lifesize felhőalapú**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és Lifesize felhővel az Azure AD egyszeri bejelentkezés tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Lifesize felhőben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Lifesize felhőben hivatkozás kapcsolata kell létrehozni.

Lifesize felhőben hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Lifesize felhővel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Teszt Lifesize Felhőbeli felhasználó létrehozása](#creating-a-lifesize-cloud-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Lifesize felhőben.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Lifesize felhőalapú alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Lifesize felhő, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Lifesize felhőalapú** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

1. Az a **Lifesize felhőalapú tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.lifesizecloud.com/ls/?acs`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.lifesizecloud.com/<companyname>`

     
1. Ellenőrizze **speciális URL-beállítások megjelenítése**, hajtsa végre a következő lépést:    
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-címe: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Frissítse a tényleges bejelentkezési URL-továbbítási állapot és azonosító ezeket az értékeket kell. Kapcsolattartó [Lifesize felhőalapú ügyfél-támogatási csapatának](https://www.lifesize.com/support) bejelentkezési URL-CÍMÉT, és azonosító értéket, és beszerezheti a továbbítási állapot értékét az oktatóanyag későbbi részében kifejtett egyszeri bejelentkezés konfigurálása.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

1. Az a **Lifesize Felhőkonfiguráció** területén kattintson **Lifesize felhő konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

1. Egyszeri bejelentkezés az alkalmazáshoz, jelentkezzen be a rendszergazdai jogosultságokat Lifesize felhőalkalmazásba konfigurált beolvasása.

1. A jobb felső sarokban kattintson a nevére, és kattintson a a **speciális beállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

1. A most kattintson a speciális beállításokban az **egyszeri bejelentkezési konfiguráció** hivatkozásra. Megnyílik a példány SSO konfigurálási lapjához.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

1. Most konfigurálja a következő értékeket a SSO-konfigurációs felhasználói felületen.    
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. A **identitásszolgáltató kibocsátója** szövegmezőjébe illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.

    b.  A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    c. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
  
    d. Adja meg az értéket az SAML attribútumleképezések Utónév szövegmező **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. Az attribútum a SAML-leképezés az a **Vezetéknév** szövegmezőben adja meg az értéket **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. Az attribútum a SAML-leképezés az a **E-mail** szövegmezőben adja meg az értéket **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

1. A kattintson a konfiguráció ellenőrzéséhez a **teszt** gombra.
   
    >[!NOTE]
    >A sikeres teszteléséhez szüksége a konfigurációs varázsló befejezéséhez az Azure ad-ben, és hozzáférést is biztosít a felhasználók vagy csoportok, akiknek a tesztet hajthat végre.

1. Az egyszeri bejelentkezés engedélyezése ehhez a a **SSO engedélyezése** gombra.

1. Ezután a **frissítés** gombot, amellyel az összes a beállítások lesznek mentve. Ezt a paramétert értéket hoz létre. Másolása paramétert értékét, ami akkor jön létre, a szövegmezőbe, illessze be a **továbbítási állapot** szövegmező alatt **Lifesize felhőalapú tartomány és URL-címek** szakaszban. 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Teszt Lifesize Felhőbeli felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Lifesize felhőbeli felhasználó hoz létre. Lifesize felhő támogatja a felhasználók automatikus átadása. Az Azure ad a sikeres hitelesítés után a felhasználó automatikusan kiépítheti az alkalmazásban. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Lifesize felhőbe Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon Lifesize felhőbe való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Lifesize felhőalapú**.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Lifesize felhőalapú csempére kattint, Lifesize felhőalkalmazás bejelentkezési lapjának szerezheti be.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png

