---
title: 'Oktatóanyag: Azure Active Directoryval integrált hűség NetBenefits |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az adatok megbízhatóságának NetBenefits között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: c3959e6e33ef603edb51d8f0b29ee140d6bd01c7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223059"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Oktatóanyag: Azure Active Directoryval integrált hűség NetBenefits

Ebben az oktatóanyagban elsajátíthatja hűség NetBenefits integrálása az Azure Active Directory (Azure AD).

Rögzített NetBenefits integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér hűség NetBenefits szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az adatok megbízhatóságának NetBenefits (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Rögzített NetBenefits konfigurálása az Azure AD-integrációs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A rögzített NetBenefits egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Rögzített NetBenefits hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Rögzített NetBenefits hozzáadása a gyűjteményből
Az Azure AD integrálása a rögzített NetBenefits konfigurálásához kell hozzáadnia hűség NetBenefits a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel a rögzített NetBenefits a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **hűség NetBenefits**, jelölje be **hűség NetBenefits** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában rögzített NetBenefits](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapuló rögzített NetBenefits.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a partner felhasználói hűség NetBenefits a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a rögzített NetBenefits közötti kapcsolat kapcsolatot kell létrehozni.

Az adatok megbízhatóságának NetBenefits **felhasználói** leképezési el kell végezni a **Azure AD-felhasználó** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az adatok megbízhatóságának NetBenefits tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Rögzített NetBenefits tesztfelhasználó létrehozása](#create-a-fidelity-netbenefits-test-user)**  - való egy megfelelője a Britta Simon hűség NetBenefits, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az adatok megbízhatóságának NetBenefits alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az adatok megbízhatóságának NetBenefits konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **hűség NetBenefits** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. Az a **hűség NetBenefits tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk hűség NetBenefits tartomány és az URL-címek](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. Az a **azonosító** szövegmezőhöz URL-címet írja be:

    Tesztelési környezetben:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Éles környezetben:  `urn:sp:fidelity:geninbndnbparts20`

    b. Az a **válasz URL-CÍMEN** szövegmezőhöz URL-címet írja be:

    Tesztelési környezetben:  `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    Éles környezetben:  `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. Rögzített NetBenefits alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Hozzárendelt igazolnia a **felhasználói azonosító** rendelkező a **user.userprincipalname**. Leképezheti a **employeeid** vagy más jogcímet, amely a szervezetben alkalmazandó **felhasználói azonosító**. Az alábbi képernyőfelvételen a csak egy példa látható.

    ![Rögzített NetBenefits attribútum](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Rögzített NetBenefits statikus és dinamikus összevonási támogatja. A statikus azt jelenti, nem fogja használni a SAML-alapú csak idő szereplő felhasználó üzembe helyezési és dinamikus csak az idő a felhasználók átadása támogatja. Az igény szerinti alapú létesítési felhasználóknak kell néhány további jogcímeket adhatnak hozzá az Azure AD-például a felhasználó születési dátumot stb. Ezek az adatok által biztosított a [hűség NetBenefits támogatási csoport](mailto:SSOMaintenance@fmr.com) és ahhoz, hogy ez a példány dinamikus összevonási rendelkeznek.
    
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. Az a **hűség NetBenefits konfigurációs** területen kattintson **konfigurálása hűség NetBenefits** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Rögzített NetBenefits konfiguráció](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **hűség NetBenefits** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja**, **SAML-alapú egyszeri bejelentkezési URL-címe** és  **SAML Entitásazonosító** való [hűség NetBenefits támogatási csoport](mailto:SSOMaintenance@fmr.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Rögzített NetBenefits tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói hűség NetBenefits Britta Simon meghívta hoz létre. Statikus összevonási létrehozásakor adja együttműködve [hűség NetBenefits támogatási csoport](mailto:SSOMaintenance@fmr.com) hűség NetBenefits platform felhasználók létrehozásához. Ezek a felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

Dinamikus összevonási felhasználók létrehozásának időpontja csak a felhasználók átadása. Az igény szerinti alapú létesítési felhasználóknak kell néhány további jogcímeket adhatnak hozzá az Azure AD-például a felhasználó születési dátumot stb. Ezek az adatok által biztosított a [hűség NetBenefits támogatási csoport](mailto:SSOMaintenance@fmr.com) és ahhoz, hogy ez a példány dinamikus összevonási rendelkeznek.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés hűség NetBenefits Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az adatok megbízhatóságának NetBenefits Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **hűség NetBenefits**.

    ![Az alkalmazások listáját a rögzített NetBenefits hivatkozás](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel hűség NetBenefits mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett az adatok megbízhatóságának NetBenefits alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/fidelitynetbenefits-tutorial/tutorial_general_203.png

