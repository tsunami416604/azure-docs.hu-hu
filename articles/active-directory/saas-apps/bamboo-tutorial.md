---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAML SSO bambusz a felbontása GmbH |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez bambusz a SAML SSO és az Azure Active Directory közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 95aada1303a807034d22689f71cea37696df4154
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432456"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAML SSO a bambusz GmbH felbontása

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja a bambusz SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.

Bambusz a SAML SSO integrálása a megoldás az Azure ad-vel GmbH nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SAML SSO a bambusz felbontása GmbH Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a SAML SSO a bambusz felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integrációs bambusz a SAML SSO-val feloldási GmbH, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A SAML egyszeri bejelentkezés az bambusz feloldási GmbH egyszeri bejelentkezés engedélyezve előfizetés szerint

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Feloldási GmbH bambusz a SAML SSO hozzáadását a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH bambusz a SAML SSO hozzáadását a katalógusból
Integráció konfigurálásához a bambusz SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell bambusz a SAML SSO GmbH felbontása a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a SAML SSO a bambusz felbontása GmbH a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **bambusz feloldási GmbH által a SAML SSO**, jelölje be **bambusz feloldási GmbH által a SAML SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![SAML SSO a bambusz felbontása GmbH, a találatok listájában](./media/bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés bambusz a SAML SSO-val egy "Britta Simon" nevű tesztelési felhasználó alapján GmbH felbontása.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó bambusz a SAML SSO a felbontása GmbH, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO bambusz a megoldás által hivatkozás kapcsolatának GmbH kell létrehozni.

SAML egyszeri bejelentkezés az bambusz felbontása GmbH, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés bambusz a SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy bambusz feloldási GmbH teszt felhasználó által a SAML SSO](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  – bambusz a SAML SSO-megfelelője a Britta Simon van megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH által.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd állítsa egyszeri bejelentkezés a SAML egyszeri bejelentkezés az bambusz felbontása GmbH alkalmazás.

**Konfigurálja az Azure AD egyszeri bejelentkezés bambusz a SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **bambusz feloldási GmbH által a SAML SSO** alkalmazás integrációs oldalán kattintson **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/bamboo-tutorial/tutorial_bamboo_samlbase.png)

1. Az a **SAML SSO bambusz felbontása GmbH tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![A felbontása GmbH tartomány bambusz SAML SSO és URL-címek egyszeri bejelentkezési adatait](./media/bamboo-tutorial/tutorial_bamboo_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![A felbontása GmbH tartomány bambusz SAML SSO és URL-címek egyszeri bejelentkezési adatait](./media/bamboo-tutorial/tutorial_bamboo_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [bambusz feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bamboo-tutorial/tutorial_bamboo_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/bamboo-tutorial/tutorial_general_400.png)

1. Bejelentkezés a SAML SSO bambusz feloldási GmbH vállalati hely által a rendszergazdaként.

1. A fő eszköztár jobb oldalán kattintson a **beállítások** > **bővítmények**.

    ![A beállítások](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. BIZTONSÁG szakaszában keresse meg, kattintson a **SAML egyszeri bejelentkezés** a menüsávon a.

    ![A Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Az a **SAML egyszeri bejelentkezés beépülő modul konfigurációs lapon**, kattintson a **identitásszolgáltató hozzáadása**. 

    ![Az identitásszolgáltató hozzáadása](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Az a **válassza ki a SAML-identitásszolgáltató** lapon tegye a következőket:

    ![Az identitásszolgáltató](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Válassza ki **identitásszolgáltató típusa** , **AZURE ad-ben**.

    b. Az a **neve** szövegmezőbe írja be a nevét.

    c. Az a **leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

1. Az a **identitás-szolgáltató konfigurációjának** lapon kattintson **tovább**.

    ![Az identitás-config](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1.  A a **SAML-identitásszolgáltató metaadatok importálása** kattintson **fájl betöltése** feltölteni a **METAADATAINAK XML** fájlt, amely az Azure Portalról letöltött.

    ![A idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **beállítások mentése**.

    ![A Mentés](./media/bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/bamboo-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bamboo-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/bamboo-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/bamboo-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Hozzon létre egy SAML SSO bambusz feloldási GmbH teszt felhasználó számára

Ez a szakasz célja egy nevű felhasználó létrehozásához Britta Simon a SAML SSO a bambusz GmbH felbontása. SAML SSO felbontása GmbH bambusz támogatja a létesítést igény és a felhasználók is manuálisan is létrehozhatók, lépjen kapcsolatba [bambusz feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) a követelmény alapján.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés bambusz a SAML SSO felbontása GmbH Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a bambusz SAML SSO felbontása GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **bambusz feloldási GmbH által a SAML SSO**.

    ![Az SAML egyszeri bejelentkezés az alkalmazásainak listájában feloldási GmbH csatlakozásonkénti bambusz](./media/bamboo-tutorial/tutorial_bamboo_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Az SAML egyszeri bejelentkezés az bambusz kattintva által feloldási GmbH csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett, a SAML egyszeri bejelentkezés az bambusz felbontása GmbH alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-tutorial/tutorial_general_203.png

