---
title: 'Oktatóanyag: Azure Active Directoryval integrált való összefolyás felett SAML SSO Microsoft |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Microsoft által való összefolyás felett SAML SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: c89be4917bb266bcd0244f2409bfa703dd3e523c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directoryval integrált való összefolyás felett SAML SSO Microsoft

Ebben az oktatóanyagban elsajátíthatja való összefolyás felett SAML SSO Microsoft integrálása az Azure Active Directory (Azure AD).

Microsoft való összefolyás felett SAML SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér való összefolyás felett SAML SSO Microsoft Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a SAML SSO való összefolyás felett (egyszeri bejelentkezés) a Microsoft által a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Leírás:

A Microsoft Azure Active Directory-fiókot használ Atlassian való összefolyás felett server egyszeri bejelentkezés engedélyezése. Ezzel a módszerrel a szervezeti felhasználók bejelentkezési való összefolyás felett alkalmazás használható az Azure AD hitelesítő adatait. A beépülő modul SAML 2.0 használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Való összefolyás felett SAML-alapú egyszeri Microsoft Azure AD-integrációs konfigurálásához a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Való összefolyás felett kiszolgáló alkalmazás telepítve van egy 64 bites Windows server (a helyszínen vagy a felhő infrastruktúra-szolgáltatási infrastruktúra)
- A kiszolgálóhoz való összefolyás felett HTTPS-kompatibilis
- Megjegyzés: a támogatott verziók való összefolyás felett beépülő modul szakasz alatt szerepel.
- Való összefolyás felett kiszolgáló elérhető-e internetes különösen az Azure AD bejelentkezési lapot a hitelesítéshez, és képes a jogkivonat fogadására Azure ad-érdemes
- Rendszergazdai hitelesítő adataival való összefolyás felett beállítása
- WebSudo le van tiltva, az való összefolyás felett
- A való összefolyás felett kiszolgálói alkalmazásban létrehozott tesztfelhasználó

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem javasoljuk való összefolyás felett az éles környezetben. Az integráció először tesztelje fejlesztési vagy tesztelési környezetben az alkalmazás, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Való összefolyás felett támogatott verziói 

Mostantól való összefolyás felett következő verziói támogatottak:

- Való összefolyás felett: 5.0 5.10

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A katalógusból való összefolyás felett SAML SSO Microsoft hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>A katalógusból való összefolyás felett SAML SSO Microsoft hozzáadása
Az Azure AD-be való összefolyás felett SAML SSO Microsoft integrálása konfigurálásához kell hozzáadnia való összefolyás felett SAML SSO Microsoft által a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A katalógusból való összefolyás felett SAML SSO Microsoft hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **való összefolyás felett SAML SSO Microsoft**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Az eredmények panelen válassza ki a **való összefolyás felett SAML SSO Microsoft**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez való összefolyás felett SAML-alapú egyszeri "Britta Simon" nevű tesztfelhasználó alapján a Microsoft által.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználóhoz való összefolyás felett SAML SSO Microsoft által a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Microsoft által való összefolyás felett SAML SSO közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezéshez való összefolyás felett SAML-alapú egyszeri Microsoft tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy való összefolyás felett SAML SSO Microsoft teszt felhasználó létrehozása](#creating-a-confluence-saml-sso-by-microsoft-test-user)**  - való egy megfelelője a Britta Simon való összefolyás felett SAML SSO, amely a felhasználó az Azure AD-ábrázolását csatolva van a Microsoft által.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a való összefolyás felett SAML SSO konfigurálása egyszeri bejelentkezéshez Microsoft-alkalmazás.

**Az Azure AD egyszeri bejelentkezéshez való összefolyás felett SAML-alapú egyszeri Microsoft megadásához a következő lépésekkel:**

1. Az Azure portálon a a **való összefolyás felett SAML SSO Microsoft** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. Az a **való összefolyás felett SAML SSO Microsoft Domain és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Port megadása nem kötelező, abban az esetben, ha egy elnevezett URL-címet. Ezek az értékek fogadásának való összefolyás felett beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a való összefolyás felett példányát rendszergazdaként.

7. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. Töltse le a beépülő modul a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56503). A beépülő modul használatával a Microsoft által biztosított manuális feltöltéséhez **töltse fel a bővítmény** menü. A beépülő modul letöltésének hatálya alá tartozó [Microsoft szolgáltatási szerződését](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. A beépülő modul telepítése után megjelenik **felhasználó telepített** bővítmények szakasza **kezelése bővítmény** szakasz. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy van-e rendelve az alkalmazásnak, hogy nincs hiba elhárításához a metaadatok csak egy tanúsítványra. Ha több tanúsítvány, a rendszergazda lekérdezi a metaadatok feloldása során hiba történt.

    a. A a **metaadatainak URL-CÍMÉT** szövegmező, Beillesztés **alkalmazás összevonási metaadatainak URL-címét** érték, amely a Azure-portálon, majd kattintson a másolt a **megoldásához** gomb. Beolvassa a kiállító terjesztési hely metaadatainak URL-CÍMÉT, és feltölti a mezők adatai.

    b. Másolás a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** értéket, majd illessze be őket a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** szövegmezőből, illetve a **való összefolyás felett SAML SSO Microsoft Domain és URL-címek**  Azure-portál szakaszban.

    c. A **bejelentkezési gomb neve** írja be a szervezet által a felhasználók a bejelentkezési képernyőn látható gombra.

    d. A **SAML felhasználói azonosító helyek**, válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító attribútum elem van**.  Ez az azonosító nem lehet a való összefolyás felett felhasználói azonosítóját. Ha a felhasználói azonosító nem egyezik, majd rendszer nem segítségével a felhasználók a bejelentkezéshez. 

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye azonosítója. Állítson be egy attribútum lehetőséget, és adja meg a megfelelő attribútum nevét.
    
    e. Ha **felhasználói azonosító attribútum elem van** beállítás, ezt a **attribútum neve** szövegmezőbe írja be a felhasználói azonosítót várt attribútum neve. 

    f. Ha az Azure ad-val (például az AD FS stb.) az összevont tartományt használ, majd kattintson a a **engedélyezése Hitelesítőtartományának feltárási** lehetőséget, majd konfigurálja a **tartománynév**.
    
    g. A **tartománynév** írja be a tartomány nevét az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **engedélyezése egyszeri bejelentkezéshez kimenő** kívánja-e az Azure AD, ha a való összefolyás felett kijelentkezik a felhasználói jelentkezzen ki. 

    i. Kattintson a **mentése** gombra a beállítások mentéséhez.

    > [!NOTE]
    > Telepítés és a hibaelhárítási kapcsolatban további információkért látogasson el a [MS való összefolyás felett SSO összekötő felügyeleti útmutató](ms-confluence-jira-plugin-adminguide.md) és is [gyakran ismételt kérdések](ms-confluence-jira-plugin-faq.md) a segítségért

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Egy való összefolyás felett SAML SSO által Microsoft tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók való összefolyás felett jelentkezzen be a helyi kiszolgálón, akkor ki kell építenie való összefolyás felett SAML SSO be Microsoft által. A Microsoft által való összefolyás felett a SAML SSO egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a való összefolyás felett a kiszolgálón helyi rendszergazdaként.

2. Vigye a mutatót a ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. A felhasználók szakaszban kattintson **felhasználók hozzáadása az** fülre. Az a **hozzáadni egy felhasználót** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. Az a **felhasználónév** szövegmező, írja be a felhasználó Britta Simon például az e-mailt.

    b. Az a **teljes nevét** szövegmező, írja be például a Britta Simon felhasználói teljes nevét.

    c. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőhöz Britta Simon írja be a jelszót.

    e. Kattintson a **jelszó megerősítése** írja be újból a jelszót.
    
    f. Kattintson a **Hozzáadás** gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon való összefolyás felett SAML SSO Microsoft által biztosított hozzáférés szerint használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Microsoft való összefolyás felett SAML SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **való összefolyás felett SAML SSO Microsoft**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Gombra a való összefolyás felett SAML SSO által Microsoft csempe a hozzáférési panelen meg kell beolvasása automatikusan bejelentkezett a való összefolyás felett SAML SSO a Microsoft alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png
