---
title: 'Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - szükséglet portál |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Palo Alto hálózatok - szükséglet Portal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aaa2adc30d1d798312b50370d4b635d0d8a123e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347414"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - szükséglet portál

Ebben az oktatóanyagban elsajátíthatja Palo Alto hálózatok - szükséglet portálon az Azure Active Directoryval (Azure AD) integrálása.

Palo Alto hálózatok - szükséglet portálon az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáférhet Palo Alto hálózatok - szükséglet portál Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Palo Alto hálózatokhoz - szükséglet portál (egyszeri bejelentkezés) az Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Palo Alto hálózatokkal - szükséglet portál, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Palo Alto hálózatok - függő Portal egyszeri bejelentkezés engedélyezve az előfizetéshez

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Palo Alto hálózatok - szükséglet portál a gyűjteményből hozzáadását
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Palo Alto hálózatok - szükséglet portál a gyűjteményből hozzáadását
Palo Alto hálózatok - függő portálon az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto hálózatok – a felügyelt SaaS-alkalmazások listájának függő portált a gyűjteményből.

**Adja hozzá a Palo Alto hálózatok - függő Portal a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Palo Alto hálózatok - szükséglet Portal**, jelölje be **Palo Alto hálózatok - szükséglet Portal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást .

    ![Palo Alto hálózatok - az eredménylistában függő portál](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal - szükséglet Portal "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Palo Alto hálózatokban - szükséglet Portal a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Palo Alto hálózatokban - szükséglet portál közötti kapcsolat kapcsolatot kell létrehozni.

Palo Alto hálózatokban - szükséglet portál, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal - tesztelése és konfigurálása függő portálon kell a következő építőelemeket befejezéséhez:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Palo Alto hálózatok - szükséglet Portal tesztfelhasználó létrehozása](#create-a-palo-alto-networks---captive-portal-test-user)**  - rendelkezik egy megfelelője a Britta Simon Palo Alto hálózatokból - szükséglet portál, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Palo Alto hálózatokon - szükséglet Portal-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD konfigurálása egyszeri bejelentkezéshez Palo Alto hálózatokkal - szükséglet portál, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Palo Alto hálózatok - szükséglet Portal** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

3. Az a **Palo Alto hálózatok - függő Portal tartomány és az URL-címek** területen tegye a következőket:

    ![Palo Alto hálózatok - függő Portal tartomány- és URL-címek egyszeri bejelentkezés információk](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Palo Alto hálózatok - szükséglet Portal támogatási csoport](https://support.paloaltonetworks.com/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

6. Nyissa meg a Palo Alto egy másik böngészőablakban rendszergazdaként.

7. Kattintson a **eszköz**.

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Válassza ki **SAML-Identitásszolgáltatóként** a bal oldali navigációs sáv megnyitásához, majd kattintson a "Importálása" a metaadatait tartalmazó fájl importálása.

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Hajtsa végre a következő importálási ablakban műveletek

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Az a **profilnév** szövegmező, adjon meg egy nevet például Azure AD felügyeleti felhasználói felület.
    
    b. A **Identity Provider metaadatok**, kattintson a **Tallózás** jelölje ki a metadata.xml fájlt, amely az Azure-portálról letöltött
    
    c. Kattintson az **OK** gombra

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Palo Alto hálózatok - szükséglet Portal tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon meghívta Palo Alto hálózatok - szükséglet Portal felhasználó létrehozásához. Palo Alto hálózatok - függő portál már támogatja az just-in-time átadása, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Palo Alto hálózatok - szükséglet Portal, ha még nem létezik elérésére tett kísérlet során. 

> [!NOTE]
> Hozza létre a felhasználó manuálisan kell, ha szeretné-e lépjen kapcsolatba a [Palo Alto hálózatok - szükséglet Portal támogatási csoport](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto hálózatok - szükséglet portál Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Palo Alto hálózatok - szükséglet Portal Britta Simon hozzárendelése a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Palo Alto hálózatok - szükséglet Portal**.

    ![Palo Alto hálózatok - szükséglet Portal hivatkozásra az alkalmazások listáját](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Függő portál Windows virtuális gépre a tűzfal mögött van konfigurálva.  Egyszeri bejelentkezés bejelentkezés a Windows virtuális gép RDP-szükséglet portál teszteléséhez. Az RDP-munkameneten belül nyissa meg egy böngészőt, és a webhely, az automatikusan megnyílik az egyszeri bejelentkezési URL-cím és a hitelesítés kérése. Hitelesítés végrehajtása után meg kell tudni navgiate webhelyekre. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

