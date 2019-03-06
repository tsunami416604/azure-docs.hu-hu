---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Pega rendszerek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa3a3dbda3f184b0c2541dc9df6c39afa86458e4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437632"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel

Ebben az oktatóanyagban megismerheti, hogyan Pega rendszerek integrálását az Azure Active Directory (Azure AD).

Pega rendszerek integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Pega rendszerek Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Pega rendszerekhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Pega rendszerekkel, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Pega rendszerek egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Pega rendszerek hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-pega-systems-from-the-gallery"></a>Pega rendszerek hozzáadása a katalógusból
Pega rendszerek integrálását az Azure AD beállítása, hozzá kell Pega rendszerek a galériából a felügyelt SaaS-alkalmazások listájára.

**Pega rendszerek hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Pega rendszerek**válassza **Pega rendszerek** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Pega rendszerek](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Pega rendszerek, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Pega rendszerekben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Pega rendszerekben hivatkozás kapcsolata kell létrehozni.

Pega rendszerekben, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés Pega rendszerekkel való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Pega rendszerek tesztfelhasználót](#create-a-pega-systems-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Pega rendszerekben.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Pega Systems alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Pega rendszerekkel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Pega rendszerek** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

1. Az a **Pega rendszerek tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Pega rendszerek tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Pega rendszerek tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját, válasz URL-cím és továbbító állapot URL-címe. Az oktatóanyag későbbi részében ismertetett Pega alkalmazásból azonosítója és a válasz URL-cím értékeit is megtalálhatja. Továbbítási állapot, lépjen kapcsolatba [Pega rendszerek ügyfél-támogatási csapatának](https://www.pega.com/contact-us) a gépkulcsengedélyek értékének. 

1. A Pega rendszerek alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Ezeket a jogcímeket adott ügyfél és az igényektől függ. Következő opcionális jogcímek olyan például amelyek csak az alkalmazás konfigurálható. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/pegasystems-tutorial/tutorial_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | egyedi azonosítója | *********** |
    | CN  | *********** |
    | levelezés | *********** |
    | accessgroup | *********** |
    | szervezet | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Munkacsoport | *********** |
    | Telefonszám | *********** |

    > [!NOTE]
    > Ezek a vásárlói adott értékekre. Adja meg a megfelelő értékekre.

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_general_400.png)
    
1. Az egyszeri bejelentkezés konfigurálása **Pega rendszerek** oldalán, nyissa meg a **Pega portál** rendszergazdai fiókkal egy másik böngészőablakban.

1. Válassza ki **létrehozása** -> **SysAdmin (rendszergazda)** -> **hitelesítési szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
1. A következő műveleteket végrehajtására **hitelesítési szolgáltatás-létrehozás** képernyőjén:

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Válassza ki **SAML 2.0-s** típusból

    b. Az a **neve** szövegmezőbe, adja meg a bármely neve például: Azure AD egyszeri bejelentkezés

    c. Az a **rövid leírása** szövegmezőben adjon meg minden olyan leírást  

    d. Kattintson a **létrehozása, és nyissa meg** 
    
1. A **identitásszolgáltató (IdP) információt** területén kattintson a **importálás IdP-metaadatok** , és keresse meg a metaadat-fájlt, amely már letöltötte az Azure Portalról. Kattintson a **küldés** a metaadatok betöltése.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
1. Az IdP-adat Ez kitölti a lent látható módon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
1. A következő műveleteket végrehajtására **Service Provider (SP) beállítások** szakaszban:

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Másolás a **entitás azonosító** értékét, és illessze be az Azure portálon **azonosító** szövegmezőbe.

    b.  Másolás a **helyességi feltétel fogyasztói Service (ACS) helyen** értékét, és illessze be az Azure portálon **válasz URL-cím** szövegmezőbe.

    c. Válassza ki **tiltsa le a kérelem-aláírás**.

1. Kattintson a **Mentés** gombra.
    
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/pegasystems-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/pegasystems-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/pegasystems-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-pega-systems-test-user"></a>Pega rendszerek tesztfelhasználó létrehozása

Ez a szakasz célja egy Pega rendszerekben Britta Simon nevű felhasználó létrehozásához. Együttműködve [Pega rendszerek ügyfél-támogatási csapatának](https://www.pega.com/contact-us) Pega Sysyems a felhasználók létrehozásához.


### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pega rendszerek Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Pega rendszerek, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Pega rendszerek**.

    ![Az alkalmazások listáját a Pega rendszerek hivatkozás](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Pega rendszerek csempére kattint, meg kell lekérése automatikusan bejelentkezett az Pega rendszerek alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

