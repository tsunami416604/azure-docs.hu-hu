---
title: 'Oktatóanyag: Azure Active Directoryval integrált JIRA SAML SSO Microsoft |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Microsoft által JIRA SAML SSO között.
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
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 99d50e67ea7194444a4cbff1e48d370ab7f4f370
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698696"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directoryval integrált JIRA SAML SSO Microsoft

Ebben az oktatóanyagban elsajátíthatja JIRA SAML SSO Microsoft integrálása az Azure Active Directory (Azure AD).

Microsoft JIRA SAML SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér JIRA SAML SSO Microsoft Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a JIRA SAML SSO (egyszeri bejelentkezés) a Microsoft által a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Leírás

A Microsoft Azure Active Directory-fiókot használ Atlassian JIRA server egyszeri bejelentkezés engedélyezése. Ezzel a módszerrel a szervezeti felhasználók bejelentkezési JIRA alkalmazás használható az Azure AD hitelesítő adatait. A beépülő modul SAML 2.0 használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

JIRA SAML-alapú egyszeri Microsoft Azure AD-integrációs konfigurálásához a következőkre van szükség:

- Az Azure AD szolgáltatásra
- JIRA Core és a 7,8 szoftver 6.0-s vagy a JIRA szolgáltatás ügyfélszolgálati 3.0 3.2 telepítve kell, és konfigurálva a Windows 64 bites verziója
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

*   JIRA Core és a szoftver: 6.0 való 7,8
*   JIRA ügyfélszolgálatához 3.0 a 3.2.
*   JIRA 5.2 is támogatja. További információért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA 5.2](./active-directory-saas-jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.
Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Microsoft által JIRA SAML SSO hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>A Microsoft által JIRA SAML SSO hozzáadása a gyűjteményből
Az Azure AD integrálása a Microsoft által JIRA SAML SSO konfigurálásához kell hozzáadnia JIRA SAML SSO Microsoft által a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a SAML SSO JIRA a Microsoft által a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **JIRA SAML SSO Microsoft**, jelölje be **JIRA SAML SSO Microsoft** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![JIRA SAML SSO Microsoft által a eredményeinek listája](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri "Britta Simon" nevű tesztfelhasználó alapján a Microsoft által.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó JIRA SAML SSO Microsoft által a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Microsoft által JIRA SAML SSO közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri Microsoft tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy JIRA SAML SSO Microsoft teszt felhasználó](#create-a-jira-saml-sso-by-microsoft-test-user)**  - való egy megfelelője a Britta Simon JIRA SAML SSO, amely a felhasználó az Azure AD-ábrázolását csatolva van a Microsoft által.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a JIRA SAML SSO konfigurálása egyszeri bejelentkezéshez Microsoft-alkalmazás.

**Az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri Microsoft megadásához a következő lépésekkel:**

1. Az Azure portálon a a **JIRA SAML SSO Microsoft** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_samlbase.png)

3. Az a **JIRA SAML SSO Microsoft Domain és URL-címek** területen tegye a következőket:

    ![JIRA SAML SSO Microsoft Domain és URL-címek egyetlen bejelentkezési adatai szerint](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Port megadása nem kötelező, abban az esetben, ha egy elnevezett URL-címet. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-msaadssojira-tutorial/tutorial_metadataurl.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a JIRA példányát rendszergazdaként.

7. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon1.png)

8. Töltse le a beépülő modul a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56506). A beépülő modul használatával a Microsoft által biztosított manuális feltöltéséhez **töltse fel a bővítmény** menü. A beépülő modul letöltésének hatálya alá tartozó [Microsoft szolgáltatási szerződését](https://www.microsoft.com/en-us/servicesagreement/).

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon12.png)

9. A beépülő modul telepítése után megjelenik **felhasználó telepített** bővítmények szakasza **kezelése bővítmény** szakasz. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon13.png)

10. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Egyszeri bejelentkezés konfigurálása](.\media\active-directory-saas-msaadssojira-tutorial\addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy van-e rendelve az alkalmazásnak, hogy nincs hiba elhárításához a metaadatok csak egy tanúsítványra. Ha több tanúsítvány, a metaadatok feloldása után a rendszergazda hibaüzenetet kap.

    a. A a **metaadatainak URL-CÍMÉT** szövegmező, Beillesztés **alkalmazás összevonási metaadatainak URL-címét** érték, amely a Azure-portálon, majd kattintson a másolt a **megoldásához** gomb. Beolvassa a kiállító terjesztési hely metaadatainak URL-CÍMÉT, és feltölti a mezők adatai.

    b. Másolás a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** értéket, majd illessze be őket a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** szövegmezőből, illetve a **JIRA SAML SSO Microsoft Domain és URL-címek** Azure-portál szakaszban.

    c. A **bejelentkezési gomb neve** írja be a szervezet által a felhasználók a bejelentkezési képernyőn látható gombra.

    d. A **SAML felhasználói azonosító helyek** válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító attribútum elem van**.  Ez az azonosító nem lehet a JIRA felhasználói azonosítóját. Ha a felhasználói azonosító nem egyezik, majd rendszer nem segítségével a felhasználók a bejelentkezéshez.

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye azonosítója. Állítson be egy attribútum lehetőséget, és adja meg a megfelelő attribútum nevét.

    e. Ha **felhasználói azonosító attribútum elem van** beállítás, ezt a **attribútum neve** szövegmezőbe írja be a felhasználói azonosítót várt attribútum neve.

    f. Ha az Azure ad-val (például az AD FS stb.) az összevont tartományt használ, majd kattintson a a **engedélyezése Hitelesítőtartományának feltárási** lehetőséget, majd konfigurálja a **tartománynév**.

    g. A **tartománynév** írja be a tartomány nevét az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **engedélyezése egyszeri bejelentkezéshez kimenő** kívánja-e az Azure AD, ha a JIRA kijelentkezik a felhasználói jelentkezzen ki.

    i. Kattintson a **mentése** gombra a beállítások mentéséhez.

    > [!NOTE]
    > Telepítés és a hibaelhárítási kapcsolatban további információkért látogasson el a [MS JIRA SSO összekötő felügyeleti útmutató](ms-confluence-jira-plugin-adminguide.md) és is [gyakran ismételt kérdések](ms-confluence-jira-plugin-faq.md) a segítségért

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

### <a name="create-a-jira-saml-sso-by-microsoft-test-user"></a>Hozzon létre egy JIRA SAML SSO Microsoft teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a helyi kiszolgáló JIRA, azok ki kell építenie JIRA SAML SSO a Microsoft által. A Microsoft által JIRA a SAML SSO egy kézi tevékenység.

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

Ebben a szakaszban Britta Simon JIRA SAML SSO Microsoft által biztosított hozzáférés szerint használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Microsoft JIRA SAML SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **JIRA SAML SSO Microsoft**.

    ![A JIRA SAML SSO által az alkalmazások listáját a Microsoft-hivatkozás](.\media\active-directory-saas-msaadssojira-tutorial\tutorial_singlesign-onforjira_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Gombra a JIRA SAML SSO által Microsoft csempe a hozzáférési panelen meg kell beolvasása automatikusan bejelentkezett a JIRA SAML SSO a Microsoft alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

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