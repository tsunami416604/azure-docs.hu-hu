---
title: 'Oktatóanyag: Azure Active Directory-integráció Adobe bejelentkezési |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Adobe bejelentkezési között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d91a64762181fd4c261f1ba964d366253912f009
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926012"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Azure Active Directory-integráció Adobe bejelentkezési

Ebben az oktatóanyagban elsajátíthatja Adobe bejelentkezési integrálása az Azure Active Directory (Azure AD).

Az Adobe bejelentkezési integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az Adobe bejelentkezési szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Adobe bejelentkezéshez (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további tudnivalókért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs Adobe bejelentkezési konfigurálásához lesz szüksége:

- Az Azure AD szolgáltatásra
- Az Adobe bejelentkezési egyszeri bejelentkezés engedélyezve van az előfizetés

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Az Adobe bejelentkezési hozzáadása a gyűjteményből.
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés.

## <a name="add-adobe-sign-from-the-gallery"></a>Az Adobe hozzáadása a gyűjteményből
Az Azure AD integrálása a Adobe bejelentkezési konfigurálásához kell hozzáadnia az Adobe bejelentkezési a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory ikonra képernyőképe][1]

2. Keresse meg a **vállalati alkalmazások** > **összes alkalmazás**.

    ![Képernyőfelvétel az Azure Active Directory menük, a vállalati alkalmazások és az összes kiemelt alkalmazások][2]
    
3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Képernyőfelvétel az új alkalmazás lehetőséget a párbeszédpanel tetején][3]

4. Írja be a keresőmezőbe, **Adobe bejelentkezési**.

    ![Képernyőkép a keresési mezőbe](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Az eredmények panelen válassza ki a **Adobe bejelentkezési**, majd válassza ki **Hozzáadás**.

    ![Eredmények panel képernyőképe](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés Adobe bejelentkezési "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell ismeri fel a csatolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó a Adobe bejelentkezési között.

A csatolt kapcsolatot hozhat létre az Adobe bejelentkezési a, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév**.

Az Azure AD az egyszeri bejelentkezés Adobe bejelentkezési tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#creating-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy Adobe bejelentkezési tesztfelhasználó](#creating-an-adobe-sign-test-user) való egy megfelelője a Britta Simon Adobe bejelentkezési kapcsolódó felhasználói az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assigning-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#testing-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD az egyszeri bejelentkezés az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Adobe bejelentkezési alkalmazásban.

1. Az Azure portálon a a **Adobe bejelentkezési** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Képernyőkép az Adobe bejelentkezési alkalmazás integráció lapján, az egyszeri bejelentkezés kiemelve][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen a **mód**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Képernyőkép az egyszeri bejelentkezés párbeszédpanel, mód mezőt a kijelölt](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Az a **Adobe bejelentkezési tartomány és az URL-címek** területen tegye a következőket:

    ![Képernyőkép az Adobe bejelentkezési tartomány és az URL-címek szakasz](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ: `https://<companyname>.echosign.com/`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![Képernyőkép az SAML-aláíró tanúsítványa szakasz](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Képernyőfelvétel a Mentés gombra](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. Az a **Adobe bejelentkezési konfigurációs** szakaszban jelölje be **konfigurálása Adobe bejelentkezési** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló** szakasz.

    ![Képernyőkép az Adobe bejelentkezési konfigurációs szakasz konfigurálása Adobe bejelentkezési kiemelve](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Előtt, lépjen kapcsolatba a [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) számára engedélyezett az Adobe bejelentkezési a tartományt. Ez a tartomány hozzáadása:

    a. A [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) küld Önnek egy véletlenszerűen generált jogkivonat. A tartományba, a jogkivonat a következő lesz: **adobe-bejelentkezés-ellenőrző = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Az érvényesítési jogkivonat közzététele a DNS-szöveg bejegyzést, és értesíti a [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Ez eltarthat néhány, nap, és már. Vegye figyelembe, hogy a DNS terjesztési késedelmeket, az azt jelenti, hogy egy értéket a DNS-közzététele nem lesz látható a egy óráig vagy tovább. A rendszergazdának kell lennie a token közzététele a DNS-rekord szöveg ismerő.
    
    c. Amikor értesíti a [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) támogatási jegy keresztül a token közzététele után azokat a tartomány ellenőrzése, és adja hozzá a fiókját.
    
    d. Általában ez közzététele a DNS-rekord a jogkivonatot:

    * Jelentkezzen be a tartományi fiók
    * A lap keresése a DNS-bejegyzésének frissítése. Ez a lap neve lehet DNS-kezelési, neve kiszolgáló kezelése vagy speciális beállítások.
    * A TXT-rekord található a tartományhoz.
    * Adja hozzá a TXT-rekord Adobe által biztosított teljes token értékre.
    * Mentse a módosításokat.

8. Egy másik webes böngészőablakban jelentkezzen be a Adobe bejelentkezési vállalati webhely rendszergazdaként.

9. A SAML menüben válasszon ki **Fiókbeállítások** > **SAML beállítások**.
   
    ![Az Adobe bejelentkezési SAML beállítások képernyőkép lapon](./media/adobe-echosign-tutorial/ic789520.png "fiók")

10. Az a **SAML beállítások** területen tegye a következőket:
  
    ![SAML-beállításaihoz](./media/adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
    a. A **SAML mód**, jelölje be **SAML kötelező**.
   
    b. Válassza ki **Echosign Fiókrendszergazdák engedélyezése a Echosign hitelesítő adataival bejelentkezni**.
   
    c. A **felhasználó létrehozása**, jelölje be **keresztül SAML hitelesített felhasználók automatikus hozzáadása**.

    d. Beillesztés **SAML Entitásazonosító**, amely az Azure-portálról másolta a **entitás azonosítója/kiállító URL-cím** szövegmezőben.
    
    e. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolta a **bejelentkezési URL-cím/egyszeri bejelentkezési végpont** szövegmezőben.
   
    f. Beillesztés **Sign-Out URL-cím**, amely az Azure-portálról másolta a **kijelentkezési URL-cím/SLO végpont** szövegmezőben.

    g. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömb. A tartalmának másolása a vágólapra és illessze be azt a **IdP tanúsítvány** szövegmezőben.

    h. Válassza ki **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó, az Azure portálon Britta Simon nevű létrehozása.

![Képernyőkép a tesztfelhasználói nevét az Azure-portálon][100]

1. Az a **Azure-portálon**, a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Képernyőfelvétel az Azure AD ikon](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és válassza ki **minden felhasználó**.
    
    ![Képernyőfelvétel az Azure AD menük, felhasználók, csoportok és a kijelölt minden felhasználó](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![Képernyőkép a minden felhasználó párbeszédpanel tetején, Hozzáadás lehetőséggel kiemelve](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![Képernyőfelvétel a felhasználó párbeszédpanel](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőben **BrittaSimon**.

    b. Az a **felhasználónév** szöveg mezőbe írja be a BrittaSimon e-mail címét.

    c. Válassza ki **megjelenítése jelszó**, és jegyezze fel az értéket a **jelszó**.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-adobe-sign-test-user"></a>Az Adobe bejelentkezési tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Adobe bejelentkezési bejelentkezni, akkor ki kell építenie az Adobe jelentkezzen be. Ez egy manuális feladat.

>[!NOTE]
>Adobe bejelentkezési felhasználói fiók létrehozása eszközök vagy Adobe bejelentkezési által nyújtott API-k segítségével kiépíteni az Azure AD felhasználói fiókokat. 

1. Jelentkezzen be a **Adobe bejelentkezési** vállalati hely rendszergazdaként.

2. A felső menüben válasszon ki **fiók**. Ezt követően a bal oldali panelen válassza **felhasználók és csoportok** > **hozzon létre egy új felhasználót**.
   
    ![Képernyőkép az Adobe bejelentkezési vállalati hely, a fiók, felhasználók és csoportok, és hozzon létre egy új felhasználót a kijelölt](./media/adobe-echosign-tutorial/ic789524.png "fiók")
   
3. Az a **új felhasználó létrehozása** területen tegye a következőket:
   
    ![Képernyőfelvétel az új felhasználó létrehozása szakasz](./media/adobe-echosign-tutorial/ic789525.png "felhasználó létrehozása")
   
    a. Típus a **E-mail cím**, **Utónév**, és **Vezetéknév** egy érvényes Azure AD-fiókot szeretné a kapcsolódó szövegmezőkbe kiépítéséhez.
   
    b. Válassza ki **létrehozza a felhasználó**.

>[!NOTE]
>Az Azure Active Directory fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Adobe bejelentkezési Azure egyszeri bejelentkezést, használatára.

![Képernyőfelvétel az Azure portál egyszeri bejelentkezést.][200] 

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Majd keresse meg a könyvtár nézet, írja be a **vállalati alkalmazások**, és válassza ki **összes alkalmazás**.

    ![Képernyőfelvétel az Azure portál alkalmazások a vállalati alkalmazások és a kijelölt összes alkalmazás megtekintése][201] 

2. Az alkalmazások listában válassza ki a **Adobe bejelentkezési**.

    ![A kijelölt Adobe bejelentkezési alkalmazások listáját, képernyőképe](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![Képernyőkép a menüt, felhasználók és csoportok kiemelve][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezt követően a a **hozzáadása hozzárendelés** szakaszban jelölje be **felhasználók és csoportok**.

    ![Képernyőfelvétel a felhasználók és csoportok lapon, és adja hozzá a hozzárendelés szakasz][203]

5. A **felhasználók és csoportok** párbeszédpanelen, a felhasználók listában, jelölje be a **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon**.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Az Adobe bejelentkezési csempe a hozzáférési panelen válassza ki, amikor meg kell beolvasása automatikusan bejelentkezve az Adobe bejelentkezési alkalmazáshoz. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

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
