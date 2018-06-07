---
title: 'Oktatóanyag: Azure Active Directory-integráció a Adobe kreatív felhőalapú |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Adobe kreatív felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeedes
ms.openlocfilehash: 53513b2d07f75e917845ad307a2833a5149ab677
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590011"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Azure Active Directoryval integrált Adobe kreatív felhő

Ebben az oktatóanyagban elsajátíthatja Adobe kreatív felhő integrálása az Azure Active Directory (Azure AD).

Az Adobe kreatív felhő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik az Adobe kreatív felhőbe szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Adobe kreatív felhőbe (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Adobe kreatív felhőalapú, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Adobe kreatív felhő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Az Adobe kreatív felhő hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Az Adobe kreatív felhő hozzáadása a gyűjteményből
Az Azure AD integrálása a Adobe kreatív felhő konfigurálásához kell hozzáadnia Adobe kreatív felhőalapú a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Adobe kreatív felhő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Adobe kreatív felhő**, jelölje be **Adobe kreatív felhő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Adobe kreatív felhő](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Adobe kreatív felhő.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Adobe kreatív felhőben Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Adobe kreatív felhőben közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Adobe kreatív felhőalapú tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Adobe kreatív felhő tesztfelhasználó](#create-an-adobe-creative-cloud-test-user)**  - rendelkezik egy megfelelője a Britta Simon Adobe kreatív felhőben, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Adobe kreatív felhőalapú alkalmazásokhoz.

**Adobe kreatív felhő konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre a következő lépéseket:**

1. Az Azure portálon a a **Adobe kreatív felhő** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Az a **Adobe kreatív felhőalapú tartományt és URL-címek** szakaszban, ha szeretne beállítani az alkalmazás által kezdeményezett IDP módban, hajtsa végre az alábbi lépéseket:

    ![Az egyszeri bejelentkezés információk Adobe kreatív felhőalapú tartományt és URL-címek](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<token>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Adobe kreatív felhőalapú ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) beolvasni ezeket az értékeket.

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Adobe kreatív felhőalapú tartományt és URL-címek](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket, mint: `https://adobe.com`

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe kreatív felhőalapú alkalmazásnál a SAML helyességi feltételek vár egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútum** az alkalmazás lapon. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:

    | Attribútum neve | Attribútum értéke |
    | ---------------| ----------------|
    | Utónév |User.givenName |
    | Vezetéknév |User.surname |
    | E-mail |user.mail |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Kattintson az **OK** gombra.

    > [!NOTE]
    > Felhasználók szükséges érvényes Office 365 ExO licenc az e-mailek jogcím értéke a SAML-válasz feltöltését.

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. A a **Adobe egy Felhőkonfigurációk kreatív** kattintson **Adobe kreatív felhő konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz**.

    ![Adobe Creative Cloud Configuration](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. Egy másik webes böngészőablakban bejelentkezés az [Adobe felügyeleti konzol](https://adminconsole.adobe.com) rendszergazdaként.

11. Lépjen **beállítások** a felső navigációs sáv megnyitásához, majd válassza a **identitás**. A tartományok listáját nyitja meg. Kattintson a **konfigurálása** hivatkozás a tartomány alapján. Végezze el az alábbi lépéseket a **egyszeri bejelentkezési a konfiguráció szükséges** szakasz. További információkért lásd: [tartomány beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Beállítások](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "beállítások")

    a. Kattintson a **Tallózás** az Azure AD-be a letöltött tanúsítvány feltöltése **IDP tanúsítvány**.

    b. Az a **IDP kibocsátó** szövegmező, írja be az értéket a **SAML entitásazonosító** , amely a másolt **bejelentkezés konfigurálása** szakaszban az Azure portálon.

    c. Az a **IDP bejelentkezési URL-cím** szövegmező, írja be az értéket a **SAML SSO URL-címe** , amely a másolt **bejelentkezés konfigurálása** szakaszban az Azure portálon.

    d. Válassza ki **HTTP - átirányítási** , **IDP kötés**.

    e. Válassza ki **E-mail cím** , **felhasználói bejelentkezési beállítás**.

    f. Kattintson a **mentése** gombra.

12. Az irányítópult mostantól megjelennek az XML-fájl **"Metaadatok letöltése"** fájlt. Adobe EntityDescriptor és URL-címe AssertionConsumerService tartalmaz. Nyissa meg a fájlt, és konfigurálja őket az Azure AD-alkalmazás.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja az Adobe meg a megadott EntityDescriptor értéket **azonosító** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

    b. Használja az Adobe meg a megadott AssertionConsumerService értéket **válasz URL-CÍMEN** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Az Adobe kreatív felhő tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Adobe kreatív felhő bejelentkezni, akkor ki kell építenie Adobe kreatív felhőbe. Adobe kreatív felhő, ha egy kézi tevékenység.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be [Adobe felügyeleti konzol](https://adminconsole.adobe.com) hely rendszergazdaként.

2. Összevont ID Adobe konzolon belül a felhasználó hozzáadása, majd rendelje hozzá őket egy termék profilt. Felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [Adobe felügyeleti konzol több felhasználó hozzáadása](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Ezen a ponton, írja be az e-mail cím/egyszerű felhasználónév az Adobe signin űrlapra, a tab billentyű megnyomásával és kell összevont vissza az Azure AD:
    * Webalkalmazás-hozzáférés: www.adobe.com > bejelentkezési
    * Az asztali alkalmazás segédprogram belül > bejelentkezési
    * Az alkalmazáson belül > Súgó > bejelentkezési

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít az Adobe kreatív felhő által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Az Adobe kreatív felhő Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Adobe kreatív felhő**.

    ![Az alkalmazások listáját az Adobe kreatív felhő hivatkozásra](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel Adobe kreatív felhő mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett az Adobe kreatív felhő alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [(Adobe.com webhelyre) tartomány beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Azure konfigurálása Adobe egyszeri Bejelentkezést (Adobe.com webhelyre) való használatra](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
