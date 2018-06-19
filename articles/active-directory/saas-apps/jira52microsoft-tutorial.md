---
title: 'Oktatóanyag: Azure Active Directory-integráció JIRA SAML-alapú egyszeri által Microsoft (V5.2) |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és JIRA SAML SSO által Microsoft (V5.2) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 150831708c970bc198fb584a48a5410aaf02209c
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35937060"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Oktatóanyag: Azure Active Directory-integráció JIRA SAML-alapú egyszeri által Microsoft (V5.2)

Ebben az oktatóanyagban elsajátíthatja JIRA SAML SSO által Microsoft (V5.2) integrálása az Azure Active Directory (Azure AD).

JIRA SAML SSO által Microsoft (V5.2) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér JIRA SAML SSO által Microsoft (V5.2) Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a JIRA SAML SSO által Microsoft (V5.2) (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Leírás

A Microsoft Azure Active Directory-fiókot használ Atlassian JIRA server egyszeri bejelentkezés engedélyezése. Ezzel a módszerrel a szervezeti felhasználók bejelentkezési JIRA alkalmazás használható az Azure AD hitelesítő adatait. A beépülő modul SAML 2.0 használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása JIRA SAML-alapú egyszeri által Microsoft (V5.2), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- JIRA Core és a szoftver 5.2 kell telepítve és konfigurálva a Windows 64 bites verziója
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
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

**Támogatott verziók:**

*   JIRA Core és a szoftver: 5.2.
*   JIRA a 6.0-s és 7,8 is támogatja. További információért kattintson [JIRA SAML SSO Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.
Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből JIRA SAML SSO által Microsoft (V5.2) hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>A gyűjteményből JIRA SAML SSO által Microsoft (V5.2) hozzáadása
Az Azure AD integrálása a JIRA SAML SSO által Microsoft (V5.2) konfigurálásához kell hozzáadnia JIRA SAML SSO által Microsoft (V5.2) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből JIRA SAML SSO által Microsoft (V5.2) hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **JIRA SAML SSO által Microsoft (V5.2)**, jelölje be **JIRA SAML SSO által Microsoft (V5.2)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![JIRA SAML SSO által Microsoft (V5.2) az eredménylistában](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri által Microsoft (V5.2) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó JIRA SAML SSO által Microsoft (V5.2) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a JIRA SAML SSO által Microsoft (V5.2) közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri által Microsoft (V5.2) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy JIRA SAML SSO Microsoft (V5.2) teszt felhasználó](#create-a-jira-saml-sso-by-microsoft-v52-test-user)**  - való egy megfelelője a Britta Simon JIRA SAML SSO által Microsoft (V5.2), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az egyszeri bejelentkezés konfigurálása a JIRA SAML SSO a Microsoft (V5.2) alkalmazás.

**Konfigurálja az Azure AD az egyszeri bejelentkezés JIRA SAML-alapú egyszeri által Microsoft (V5.2), hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **JIRA SAML SSO által Microsoft (V5.2)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. Az a **JIRA SAML SSO Microsoft Domain és URL-címek** területen tegye a következőket:

    ![JIRA SAML SSO Microsoft Domain és URL-címek egyetlen bejelentkezési adatai szerint](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Port megadása nem kötelező, abban az esetben, ha egy elnevezett URL-címet. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a JIRA példányát rendszergazdaként.

7. Vigye a mutatót a ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon1.png)

8. A bővítmények lap szakaszban kattintson **bővítmények kezelése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon7.png)

9. Töltse le a beépülő modul a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56521). A beépülő modul használatával a Microsoft által biztosított manuális feltöltéséhez **töltse fel a bővítmény** menü. A beépülő modul letöltésének hatálya alá tartozó [Microsoft szolgáltatási szerződését](https://www.microsoft.com/en-us/servicesagreement/).

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon12.png)

10. A beépülő modul telepítése után megjelenik **felhasználó telepített** bővítmények szakasz. Kattintson a **konfigurálása** konfigurálása az új beépülő modul.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon13.png)

11. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy van-e rendelve az alkalmazásnak, hogy nincs hiba elhárításához a metaadatok csak egy tanúsítványra. Ha több tanúsítvány, a metaadatok feloldása után a rendszergazda hibaüzenetet kap.

    a. A **metaadatainak URL-CÍMÉT** szövegmező, Beillesztés **alkalmazás összevonási metaadatainak URL-címét** érték, amely a Azure-portálon, majd kattintson a másolt a **megoldásához** gomb. Beolvassa a kiállító terjesztési hely metaadatainak URL-CÍMÉT, és feltölti a mezők adatai.

    b. Másolás a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** értéket, majd illessze be őket a **azonosító, a válasz URL-CÍMEN és a bejelentkezési URL-címen** szövegmezőből, illetve a **JIRA SAML SSO Microsoft (V5.2) tartományhoz és URL-címek**  Azure-portál szakaszban.

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
    > Telepítés és a hibaelhárítási kapcsolatban további információkért látogasson el a [MS JIRA SSO összekötő felügyeleti útmutató](../ms-confluence-jira-plugin-adminguide.md) és is [gyakran ismételt kérdések](../ms-confluence-jira-plugin-faq.md) a segítségért

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Hozzon létre egy JIRA SAML SSO Microsoft (V5.2) teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a helyi kiszolgáló JIRA, akkor ki kell építenie az JIRA a helyi kiszolgálóra.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyi kiszolgálóra.

2. Vigye a mutatót a ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user1.png)

3. Ekkor megnyílik a rendszergazdai hozzáférés lapon megadja a **jelszó** kattintson **megerősítése** gombra.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user2.png)

4. A **felhasználókezelés** szakasz lapra, majd **létrehozza a felhasználó**.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user3.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user4.png)

    a. Az a **E-mail cím** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    b. Az a **teljes nevét** szövegmező, a felhasználó például Britta Simon típus teljes nevét.

    c. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    e. Kattintson a **a felhasználó létrehozása**.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés JIRA SAML SSO által Microsoft (V5.2) használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése JIRA SAML SSO által Microsoft (V5.2), hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **JIRA SAML SSO által Microsoft (V5.2)**.

    ![A JIRA SAML SSO az alkalmazások listáját a Microsoft (V5.2) kapcsolat](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Gombra a JIRA SAML SSO által Microsoft (V5.2) csempe a hozzáférési panelen meg kell beolvasása automatikusan bejelentkezett a JIRA SAML SSO a Microsoft (V5.2) alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira5.2-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira5.2-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira5.2-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira5.2-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira5.2-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira5.2-tutorial/tutorial_general_200.png
[201]: ./media/msaadssojira5.2-tutorial/tutorial_general_201.png
[202]: ./media/msaadssojira5.2-tutorial/tutorial_general_202.png
[203]: ./media/msaadssojira5.2-tutorial/tutorial_general_203.png