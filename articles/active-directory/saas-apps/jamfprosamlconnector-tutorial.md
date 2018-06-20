---
title: 'Oktatóanyag: Azure Active Directoryval integrált Jamf Pro |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Jamf Pro között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: a95b3d1d35642043728831068f0e01cd16f7b999
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212947"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Oktatóanyag: Azure Active Directoryval integrált Jamf Pro

Ebben az oktatóanyagban elsajátíthatja Jamf Pro integrálása az Azure Active Directory (Azure AD).

Jamf Pro integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Jamf Pro szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Jamf Pro (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Jamf Pro, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Jamf Pro egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Jamf Pro hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-jamf-pro-from-the-gallery"></a>A gyűjteményből Jamf Pro hozzáadása
Az Azure AD integrálása a Jamf Pro konfigurálásához kell hozzáadnia Jamf Pro a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Jamf Pro hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Jamf Pro**, jelölje be **Jamf Pro** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálhatja, és az Azure AD az egyszeri bejelentkezés Jamf Pro-teszthez "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Jamf Pro a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Jamf Pro közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Jamf Pro tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Jamf Pro tesztfelhasználó létrehozása](#create-a-jamf-pro-test-user)**  - való egy megfelelője a Britta Simon Jamf Pro, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Jamf Pro alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása Jamf Pro, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Jamf Pro** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Az a **Jamf Pro tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Jamf Pro tartomány és az URL-címek](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Jamf Pro tartomány és az URL-címek](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. A tényleges azonosító értéket kap **egyszeri bejelentkezés** Jamf Pro portál, az oktatóanyag későbbi részében ismertetett szakaszában. A tényleges kibonthatja **altartomány** értéket az azonosító értéket, és azt használja **altartomány** bejelentkezés és válasz URL-címe az adatokat.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozását](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban jelentkezzen be a Jamf Pro vállalati webhely rendszergazdaként.

8. Kattintson a **beállítások ikonra** az oldal jobb felső sarkában a.

    ![Jamf Pro konfiguráció](./media/jamfprosamlconnector-tutorial/configure1.png)

9. Kattintson a **egyszeri bejelentkezés**.

    ![Jamf Pro konfiguráció](./media/jamfprosamlconnector-tutorial/configure2.png)

10. Görgessen lefelé legfeljebb **IDENTITÁSSZOLGÁLTATÓ** alatt a **egyszeri bejelentkezés** szakaszt, és hajtsa végre a következő lépéseket:

    ![Jamf Pro konfiguráció](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Válassza ki **más** a beállításként az **IDENTITÁSSZOLGÁLTATÓ** legördülő menüből.

    b. Az a **egyéb SZOLGÁLTATÓ** szövegmezőhöz meg **az Azure AD**.

    c. Válassza ki **metaadatainak URL-CÍMÉT** beállításként való megadásával a a **IDENTITY PROVIDER METAADATOK forrás** legördülő menüből, majd illessze be a következő szövegmezőjének a **alkalmazás összevonási metaadatainak URL-címe** érték, amely Azure-portálról másolta.

    d. Másolás a **Entitásazonosító** vlaue és illessze be azt a **azonosítója (entitás azonosítója)** textbox **Jamf Pro tartomány és az URL-címek** Azure-portál szakaszban.

    >[!NOTE]
    > Itt `aadsso` a altartomány szerepel (Ez a hivatkozás célra). Használja ezt az értéket a bejelentkezés és válasz URL-címe a befejezéséhez a **Jamf Pro tartomány és az URL-címek** Azure-portál szakaszban.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Jamf Pro bejelentkezni, akkor ki kell építenie Jamf Pro be. Jamf Pro, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Jamf Pro vállalati webhely.

2. Kattintson a **beállítások ikonra** az oldal jobb felső sarkában a.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kattintson a **Jamf Pro felhasználói fiókok és csoportok**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kattintson az **Új** lehetőségre.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user2.png)

5. Válassza ki **Standard-fiók létrehozása**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user3.png)

6. Az a **új fiók** dailog, hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Az a **felhasználónév** szövegmezőhöz BrittaSimon teljes neve.

    b. Válassza ki a megfelelő beállítások szerint a szervezete **hozzáférési szint**, **jogosultság beállítása**, és a **hozzáférési állapota**.
    
    c. Az a **teljes nevét** szövegmezőhöz Britta Simon teljes neve.

    d. Az a **E-mail cím** szövegmezőhöz Britta Simon fiók e-mail címét.

    e. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    f. Az a **jelszó ellenőrzése** szövegmező, írja be a felhasználó jelszavát.

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Jamf Pro Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Jamf Pro, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Jamf Pro**.

    ![Az alkalmazások listáját a Jamf Pro hivatkozás](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Jamf Pro csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Jamf Pro alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

