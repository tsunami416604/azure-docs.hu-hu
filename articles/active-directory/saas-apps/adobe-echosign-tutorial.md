---
title: 'Oktatóanyag: Azure Active Directory-integráció az Adobe Sign |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Adobe Sign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053998"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Azure Active Directory-integráció az Adobe Sign

Ebben az oktatóanyagban elsajátíthatja, hogyan Adobe Sign integrálása az Azure Active Directory (Azure AD).

Az Adobe Sign integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az Adobe Sign Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Adobe Sign (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Sign, a következők szükségesek:

- Az Azure AD-előfizetéshez
- Az Adobe Sign egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Adobe Sign hozzáadása a katalógusból.
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés.

## <a name="add-adobe-sign-from-the-gallery"></a>Az Adobe Sign hozzáadása a katalógusból
Az Azure AD-be az Adobe Sign integráció konfigurálásához, hozzá kell Adobe Sign a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Képernyőkép az Azure Active Directory ikonra][1]

2. Keresse meg a **vállalati alkalmazások** > **minden alkalmazás**.

    ![Képernyőkép az Azure Active Directory menük, a vállalati alkalmazások és a minden kiemelésével][2]
    
3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Képernyőfelvétel az új alkalmazás lehetőséget a párbeszédpanel tetején][3]

4. A Keresés mezőbe írja be a **Adobe Sign**.

    ![Képernyőkép a keresőmezőbe](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Az eredmények panelen válassza ki a **Adobe Sign**, majd válassza ki **Hozzáadás**.

    ![Eredmények panel képernyőképe](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Adobe Sign, a teszt "Britta Simon." nevű felhasználó

Egyszeri bejelentkezés működjön, az Azure ad-ben kell ismeri fel a társított kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó az Adobe Sign között.

A társított kapcsolat létrehozásához az Adobe Sign, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév**.

Az Azure AD egyszeri bejelentkezés az Adobe Sign tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#creating-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Hozzon létre egy Adobe Sign tesztfelhasználót](#creating-an-adobe-sign-test-user) szeretné, hogy egy megfelelője a Britta Simon az Adobe Sign, akik kapcsolódik az Azure AD felhasználói ábrázolása.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assigning-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#testing-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Adobe Sign alkalmazás egyszeri bejelentkezés konfigurálása.

1. Az Azure Portalon az a **Adobe Sign** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Képernyőkép az Adobe Sign application integration oldala, amelyen egyszeri bejelentkezésre vannak kiemelve][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanelen a **mód**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Képernyőkép az egyszeri bejelentkezési párbeszédpanel, mód mező kiemelésével](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Az a **az Adobe Sign tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Képernyőkép az Adobe Sign tartomány és URL-címek szakaszt](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet, amely használja a következő mintának: `https://<companyname>.echosign.com/`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet, amely használja a következő mintának: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Kapcsolattartó [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![Képernyőfelvétel: az SAML aláírási tanúsítvány szakaszában](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Képernyőkép a Mentés gombra](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. Az a **az Adobe Sign konfigurációs** szakaszban jelölje be **konfigurálása az Adobe Sign** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** a a **rövid összefoglaló** szakaszban.

    ![Képernyőkép az Adobe Sign konfigurációs szakasz, konfigurálja az Adobe Sign kiemelésével](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Mielőtt konfigurációs, lépjen kapcsolatba a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) az engedélyezési listára saját tartományt az Adobe Sign az. Íme a tartomány hozzáadása:

    a. A [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) küld Önnek egy véletlenszerűen létrehozott tokent. A tartomány a jogkivonat az alábbihoz hasonló lesz: **adobe-bejelentkezés-ellenőrző = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Az ellenőrzési token közzététele a DNS-szöveges rekord, és értesítheti a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Ez is igénybe vehet néhány nap, vagy már. Vegye figyelembe, hogy a DNS-propagálás késések jelenti azt, hogy egy érték közzététele a DNS-ben nem lesz látható a tartozó egy vagy több óráig. A rendszergazdának kell lennie a token közzététele a DNS-szöveges rekord alapos ismerete.
    
    c. Ha értesíteni a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) keresztül egy támogatási jegyet a token közzététele után, a tartomány érvényesítése, és adja hozzá a fiókjához.
    
    d. Általában a következő közzététele a DNS-rekord a jogkivonatot:

    * Jelentkezzen be a tartományi fiók
    * A DNS-rekord frissítésére szolgáló lap található. Ez a lap neve lehet DNS-kezelést, a névkiszolgáló kezelése vagy a Speciális beállítások.
    * Keresse meg a txt típusú rekordok a tartomány.
    * Adjon hozzá egy txt típusú rekordot a teljes token Adobe által megadott értékkel.
    * Mentse a módosításokat.

8. Egy másik böngészőablakban jelentkezzen be az Adobe Sign vállalati hely rendszergazdaként.

9. A SAML menüben válassza ki a **fiókbeállításokat** > **SAML-beállítások**.
   
    ![Képernyőkép az Adobe Sign SAML-beállítások oldal](./media/adobe-echosign-tutorial/ic789520.png "fiók")

10. Az a **SAML-beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
  
    ![Képernyőkép a SAML-beállítások](./media/adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
    a. A **SAML mód**válassza **SAML kötelező**.
   
    b. Válassza ki **Echosign Fiókrendszergazdák engedélyezése Echosign hitelesítő adataival bejelentkezni**.
   
    c. A **felhasználó létrehozása**válassza **elvégezte a hitelesítést SAML felhasználóinak automatikus hozzáadása**.

    d. Beillesztés **SAML Entitásazonosító**, azokat az Azure Portalról másolt a **entitás azonosítója/kiállító URL-címe** szövegmező.
    
    e. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt a **bejelentkezési URL-cím/egyszeri bejelentkezési végpont** szövegmezőben.
   
    f. Beillesztés **kijelentkezéses URL-cím**, azokat az Azure Portalról másolt a **kijelentkezési URL-cím/SLO végpont** szövegmező.

    g. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben. A tartalmát a vágólapra másolja és illessze be azt a **identitásszolgáltató tanúsítvány** szövegmezőben.

    h. Válassza ki **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja hozzon létre egy tesztfelhasználót nevű Britta Simon, az Azure Portalon.

![Képernyőkép a tesztfelhasználói nevét az Azure Portalon][100]

1. Az a **az Azure portal**, a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Képernyőkép az Azure ad-ben ikon](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és válassza ki **minden felhasználó**.
    
    ![Képernyőkép az Azure AD a menük, a felhasználók és csoportok és minden felhasználó kiemelésével](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![Képernyőkép a minden felhasználó párbeszédpanel tetején, a Hozzáadás opció kiemelésével](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![Képernyőfelvétel a felhasználó párbeszédpanel](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőben **BrittaSimon**.

    b. Az a **felhasználónév** szöveg írja be a BrittaSimon e-mail-címét.

    c. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-adobe-sign-test-user"></a>Az Adobe Sign tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be Adobe Sign, akkor ki kell építeni az Adobe Sign. Ez a manuális feladat.

>[!NOTE]
>Az Adobe Sign felhasználói fiók létrehozása eszközöket és az Adobe Sign által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése. 

1. Jelentkezzen be a **Adobe Sign** rendszergazdaként a vállalati webhely.

2. A felső menüben válassza ki a **fiók**. Majd a bal oldali panelen válassza ki **felhasználók és csoportok** > **hozzon létre egy új felhasználót**.
   
    ![Képernyőkép az Adobe Sign hely, a fiók, felhasználók és csoportok, vállalati, és hozzon létre egy új felhasználót kiemelt](./media/adobe-echosign-tutorial/ic789524.png "fiók")
   
3. Az a **új felhasználó létrehozása** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Képernyőfelvétel az új felhasználó létrehozása szakaszban](./media/adobe-echosign-tutorial/ic789525.png "felhasználó létrehozása")
   
    a. Írja be a **E-mail cím**, **Utónév**, és **Vezetéknév** egy érvényes Azure AD-fiók létrehozásához a kapcsolódó szövegmezőket szeretne.
   
    b. Válassza ki **felhasználó létrehozása**.

>[!NOTE]
>Az Azure Active Directory fióktulajdonos kap egy e-mailt, amelyben a hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést, a hozzáférés biztosításával az Adobe Sign használatára.

![Képernyőkép az Azure portal egyszeri bejelentkezés][200] 

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Keresse meg a könyvtár nézet, ugorjon a **vállalati alkalmazások**, és válassza ki **minden alkalmazás**.

    ![Képernyőkép az Azure portál alkalmazások megtekintése, a vállalati alkalmazások és a minden kiemelésével][201] 

2. Az alkalmazások listájában jelölje ki a **Adobe Sign**.

    ![Alkalmazások listáját, az Adobe Sign kiemelésével – képernyőfelvétel](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![Képernyőkép a menü, a felhasználók és csoportok vannak kiemelve][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezt követően a a **hozzárendelés hozzáadása** szakaszban jelölje be **felhasználók és csoportok**.

    ![Képernyőfelvétel a felhasználók és csoportok lapon és a hozzárendelés szakasz hozzáadása][203]

5. A **felhasználók és csoportok** párbeszédpanelen, a felhasználók listában, válassza ki a **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása**.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Az Adobe Sign csempe kiválasztásakor a hozzáférési panelen azt kell lekérése automatikusan bejelentkezett az Adobe Sign-alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
