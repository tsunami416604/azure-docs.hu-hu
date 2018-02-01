---
title: "Oktatóanyag: Azure Active Directory-integráció a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: jeedes
ms.openlocfilehash: 710aa59fb3cc69cb1f5a20389eca13b1be93d223
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-azure-active-directory-integration-with-microsoft-azure-active-directory-single-sign-on-for-jira"></a>Oktatóanyag: Azure Active Directory-integráció a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA

Ebben az oktatóanyagban elsajátíthatja a Microsoft Azure Active Directory egyszeri bejelentkezés JIRA az Azure Active Directory (Azure AD) integrálása.

A Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a Microsoft Azure Active Directory egyszeri bejelentkezés JIRA az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a Microsoft Azure Active Directory egyszeri bejelentkezéshez az JIRA (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Leírás

A Microsoft Azure Active Directory-fiókot használ Atlassian JIRA server egyszeri bejelentkezés engedélyezése. Ezzel a módszerrel a szervezeti felhasználók bejelentkezési JIRA alkalmazás használható az Azure AD hitelesítő adatait. A beépülő modul SAML 2.0 használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- JIRA Core és a 7.2.0 szoftver 6.0-s vagy a JIRA szolgáltatás ügyfélszolgálati 3.0 3.2 telepítve kell, és konfigurálva a Windows 64 bites verziója
- JIRA kiszolgáló HTTPS-kompatibilis
- Megjegyzés: a támogatott verziók JIRA beépülő modul szakasz alatt szerepel.
- JIRA kiszolgáló elérhető-e internetes különösen az Azure AD bejelentkezési lapot a hitelesítéshez, és képes a jogkivonat fogadására Azure ad-érdemes
- Rendszergazdai hitelesítő adataival JIRA beállítása
- WebSudo JIRA le van tiltva
- A JIRA kiszolgálói alkalmazásban létrehozott tesztfelhasználó

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem javasoljuk a JIRA éles környezetben. Az integráció először tesztelje fejlesztési vagy tesztelési környezetben az alkalmazás, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>JIRA támogatott verziói

*   JIRA Core és a szoftver: 6.0 való 7.2.0
*   JIRA ügyfélszolgálatához 3.0 a 3.2.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA a gyűjteményből hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-microsoft-azure-active-directory-single-sign-on-for-jira-from-the-gallery"></a>A Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA a gyűjteményből hozzáadása
Az Azure AD integrálása a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA konfigurálásához kell hozzáadnia a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA**, jelölje be **Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA** eredmény panelen kattintson a **hozzáadása**  gombra kattintva vegye fel az alkalmazást.

    ![A Microsoft Azure Active Directory egyszeri bejelentkezés JIRA az eredmények listájában](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés a Microsoft Azure Active Directory egyszeri bejelentkezéshez az JIRA "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA a párjukhoz felhasználó a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Microsoft Azure Active Directory egyszeri bejelentkezés JIRA teszt felhasználó](#create-a-microsoft-azure-active-directory-single-sign-on-for-jira-test-user)**  - Britta Simon egy partner, a Microsoft Azure Active Directory egyszeri bejelentkezést a felhasználó az Azure AD ábrázolását kapcsolódó JIRA rendelkezik.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Microsoft Azure Active Directory egyszeri bejelentkezés JIRA alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**A Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_samlbase.png)

3. Az a **Microsoft Azure Active Directory egyszeri bejelentkezés JIRA tartomány és az URL-címek** területen tegye a következőket:

    ![A Microsoft Azure Active Directory egyszeri bejelentkezés JIRA tartomány- és URL-címek egyetlen bejelentkezési adatok](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<domain:port>/`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Port megadása nem kötelező, abban az esetben, ha egy elnevezett URL-címet. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.
 
4. Létrehozásához a **metaadatok** URL-címe, hajtsa végre a következő lépéseket:

    a. Kattintson a **App regisztrációk**.
    
    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\appregistrations.png)
   
    b. Kattintson a **végpontok** megnyitásához **végpontok** párbeszédpanel megnyitásához.  
    
    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\endpointicon.png)

    c. Kattintson a Másolás gombra másolása **ÖSSZEVONÁSI METAADAT-dokumentum** URL-címet, és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\endpoint.png)
     
    d. Most lépjen a tulajdonságlapján **Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA** , és másolja a **alkalmazásazonosító** használatával **másolási** gombra, majd illessze be a Jegyzettömbbe.
 
    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\appid.png)

    e. Készítése a **metaadatainak URL-CÍMÉT** használatával a következő mintát: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` , és másolja ezt az értéket a Jegyzettömbben, a rendszer később a beépülő modul konfigurációját.

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a JIRA példányát rendszergazdaként.

7. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon1.png)

8. Töltse le a beépülő modul a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56506). A beépülő modul használatával a Microsoft által biztosított manuális feltöltéséhez **töltse fel a bővítmény** menü.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon12.png)

9. A beépülő modul telepítése után megjelenik **felhasználó telepített** bővítmények szakasza **kezelése bővítmény** szakasz. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon13.png)

10. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy van-e rendelve az alkalmazásnak, hogy nincs hiba elhárításához a metaadatok csak egy tanúsítványra. Ha több tanúsítvány, a metaadatok feloldása után a rendszergazda hibaüzenetet kap.
 
    a. A **metaadatainak URL-CÍMÉT** illessze be a **metaadatainak URL-CÍMÉT** jön létre az Azure AD-ből, majd kattintson a **megoldásához** gombra. Beolvassa a kiállító terjesztési hely metaadatainak URL-CÍMÉT, és feltölti a mezők adatai.

    b. Másolás a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** értéket, majd illessze be őket a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** szövegmezőből, illetve a **Microsoft Azure Active Directoryval az egyszeri bejelentkezés JIRA tartomány és az URL-címek** Azure-portál szakaszban.

    c. A **bejelentkezési gomb neve** írja be a szervezet által a felhasználók a bejelentkezési képernyőn látható gombra.

    d. A **SAML felhasználói azonosító helyek** válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító attribútum elem van**.  Ez az azonosító nem lehet a JIRA felhasználói azonosítóját. Ha a felhasználói azonosító nem egyezik, majd rendszer nem segítségével a felhasználók a bejelentkezéshez. 

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye azonosítója. Állítson be egy attribútum lehetőséget, és adja meg a megfelelő attribútum nevét.

    e. Ha **felhasználói azonosító attribútum elem van** beállítás, ezt a **attribútum neve** szövegmezőbe írja be a felhasználói azonosítót várt attribútum neve. 

    f. Ha az Azure ad-val (például az AD FS stb.) az összevont tartományt használ, majd kattintson a a **engedélyezése Hitelesítőtartományának feltárási** lehetőséget, majd konfigurálja a **tartománynév**.
    
    g. A **tartománynév** írja be a tartomány nevét az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **engedélyezése egyszeri bejelentkezéshez kimenő** kívánja-e az Azure AD, ha a JIRA kijelentkezik a felhasználói jelentkezzen ki. 

    i. Kattintson a **mentése** gombra a beállítások mentéséhez.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](.\media\active-directory-saas-msaadssojira-tutorial\create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-microsoft-azure-active-directory-single-sign-on-for-jira-test-user"></a>Hozzon létre egy Microsoft Azure Active Directory egyszeri bejelentkezés JIRA teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a helyi kiszolgáló JIRA, akkor ki kell építenie a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA. A Microsoft Azure Active Directory egyszeri bejelentkezéshez az JIRA egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyi kiszolgálóra.

2. Vigye a mutatót a ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](.\media\active-directory-saas-msaadssojira-tutorial\user1.png) 

3. Ekkor megnyílik a rendszergazdai hozzáférés lapon megadja a **jelszó** kattintson **megerősítése** gombra.

    ![Alkalmazott hozzáadása](.\media\active-directory-saas-msaadssojira-tutorial\user2.png) 

4. A **felhasználókezelés** szakasz lapra, majd **létrehozza a felhasználó**.

    ![Alkalmazott hozzáadása](.\media\active-directory-saas-msaadssojira-tutorial\user3.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](.\media\active-directory-saas-msaadssojira-tutorial\user4.png) 

    a. Az a **E-mail cím** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    b. Az a **teljes nevét** szövegmező, a felhasználó például Britta Simon típus teljes nevét.

    c. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    e. Kattintson a **a felhasználó létrehozása**.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200] 

**A Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA**.

    ![A Microsoft Azure Active Directory egyszeri bejelentkezéshez az alkalmazások listáját JIRA hivatkozás](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Amikor a Microsoft Azure Active Directory egyszeri bejelentkezés JIRA csempe a hozzáférési panelen kattint, meg kell beolvasása automatikusan aláírt a a Microsoft Azure Active Directoryval az egyszeri bejelentkezés JIRA alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_01.png
[2]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_02.png
[3]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_03.png
[4]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_04.png

[100]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_100.png

[200]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_200.png
[201]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_201.png
[202]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_202.png
[203]: .\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_203.png
