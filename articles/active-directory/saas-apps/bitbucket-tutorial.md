---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAML SSO a Bitbucket felbontása GmbH |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Bitbucket SAML SSO közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: c91f62aa2f47cfab7de22def631a7149ab37ba46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434975"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory-integráció az SAML SSO a Bitbucket GmbH felbontása

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja a Bitbucket SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.

Feloldási GmbH az Azure AD által a Bitbucket SAML SSO integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SAML SSO a bitbucket-alapú megoldás GmbH által az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, a Bitbucket SAML SSO felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integráció a Bitbucket SAML SSO-val feloldási GmbH, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A SAML SSO a bitbucket-alapú megoldás GmbH egyszeri bejelentkezés engedélyezve előfizetés szerint

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Feloldási GmbH SAML SSO a Bitbucket hozzáadását a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH SAML SSO a Bitbucket hozzáadását a katalógusból
Integráció konfigurálásához a Bitbucket SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell SAML SSO a Bitbucket felbontása GmbH a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Bitbucket SAML SSO felbontása GmbH a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SAML SSO a Bitbucket felbontása GmbH**, jelölje be **bitbucket-alapú megoldás GmbH által a SAML SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![SAML egyszeri bejelentkezés, a Bitbucket felbontása GmbH, a találatok listájában](./media/bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Bitbucket SAML SSO-val egy "Britta Simon" nevű tesztelési felhasználó alapján GmbH felbontása.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó a SAML SSO a Bitbucket felbontása GmbH, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO a bitbucket-alapú megoldás által hivatkozás kapcsolatának GmbH kell létrehozni.

SAML egyszeri bejelentkezés az Bitbucket felbontása GmbH, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Bitbucket SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy bitbucket-alapú megoldás GmbH teszt felhasználó által a SAML SSO](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  - megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH által van egy megfelelője a Britta Simon SAML SSO a Bitbucket.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd állítsa egyszeri bejelentkezés a SAML SSO a Bitbucket GmbH kérelem felbontása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a Bitbucket SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **SAML SSO a Bitbucket felbontása GmbH** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

1. Az a **SAML SSO Bitbucket felbontása GmbH tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![SAML SSO a Bitbucket felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](./media/bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![SAML SSO a Bitbucket felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](./media/bitbucket-tutorial/tutorial_bitbucket_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [bitbucket-alapú megoldás GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/bitbucket-tutorial/tutorial_general_400.png)
    
1. Bejelentkezés a SAML SSO, a bitbucket-alapú megoldás GmbH vállalati hely rendszergazdaként.

1. A fő eszköztár jobb oldalán kattintson a **beállítások**.

1. Ugrás a fiókok területen, kattintson a **SAML egyszeri bejelentkezés** a menüsávon a.

    ![A Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

1. Az a **SAML egyszeri bejelentkezés beépülő modul konfigurációs lapon**, kattintson a **identitásszolgáltató hozzáadása**. 

    ![Az identitásszolgáltató hozzáadása](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

1. Az a **válassza ki a SAML-identitásszolgáltató** lapon tegye a következőket:

    ![Az identitásszolgáltató](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Válassza ki **identitásszolgáltató típusa** , **AZURE ad-ben**.

    b. Az a **neve** szövegmezőbe írja be a nevét.

    c. Az a **leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

1. Az a **Identity provider konfigurációs lapon**, kattintson a **tovább**.

    ![Az identitás-config](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

1.  A a **SAML-identitásszolgáltató metaadatok importálása** kattintson **fájl betöltése** feltölteni a **METAADATAINAK XML** fájlt, amely az Azure Portalról letöltött.

    ![A idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
1. Kattintson a **Tovább** gombra.

1. Kattintson a **beállítások mentése**.

    ![A Mentés](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/bitbucket-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bitbucket-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/bitbucket-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/bitbucket-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Hozzon létre egy bitbucket-alapú megoldás GmbH teszt felhasználó által a SAML SSO

Ez a szakasz célja egy nevű felhasználó létrehozásához Britta Simon a SAML SSO a Bitbucket GmbH felbontása. SAML SSO felbontása GmbH Bitbucket támogatja a létesítést igény és a felhasználók is manuálisan is létrehozhatók, lépjen kapcsolatba [bitbucket-alapú megoldás GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) a követelmény alapján.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon SAML SSO a Bitbucket, a hozzáférés biztosításával felbontása GmbH Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a Bitbucket SAML SSO felbontása GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAML SSO a Bitbucket felbontása GmbH**.

    ![Az SAML SSO a bitbucket-alapú megoldás GmbH csatlakozásonkénti alkalmazásainak listájában](./media/bitbucket-tutorial/tutorial_bitbucket_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A SAML SSO a Bitbucket kattintva által feloldási GmbH csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett a SAML SSO a Bitbucket, felbontása GmbH alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitbucket-tutorial/tutorial_general_01.png
[2]: ./media/bitbucket-tutorial/tutorial_general_02.png
[3]: ./media/bitbucket-tutorial/tutorial_general_03.png
[4]: ./media/bitbucket-tutorial/tutorial_general_04.png

[100]: ./media/bitbucket-tutorial/tutorial_general_100.png

[200]: ./media/bitbucket-tutorial/tutorial_general_200.png
[201]: ./media/bitbucket-tutorial/tutorial_general_201.png
[202]: ./media/bitbucket-tutorial/tutorial_general_202.png
[203]: ./media/bitbucket-tutorial/tutorial_general_203.png

