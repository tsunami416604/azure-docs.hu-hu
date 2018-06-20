---
title: 'Oktatóanyag: Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS) |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Symantec webes biztonsági szolgáltatás (VSS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7eaf64c736201df044a2b34bcd88f2ed17047fb3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211835"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS)

Ebben az oktatóanyagban elsajátíthatja Symantec webes biztonsági szolgáltatás (VSS) fiókja integrálása az Azure Active Directory (Azure AD-) fiók, így a WSS az Azure AD SAML-alapú hitelesítést használó kiépítve a felhasználó hitelesítésére, és a felhasználó vagy csoport szintű szabályzat szabályok érvényesítése lesz.

Symantec webes biztonsági szolgáltatás (VSS) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Összes felhasználók és csoportok az Azure AD portálon WSS fiókját használják kezelése. 

- Lehetővé teszi a felhasználók hitelesítsék magukat WSS használata az Azure AD hitelesítő adatait.

- A felhasználó végrehajtásának engedélyezése, és csoportosítsa a WSS-fiókjához megadott szintű szabályzat szabályok.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Symantec webes biztonsági szolgáltatás (VSS), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Symantec webes biztonsági szolgáltatás (VSS) fiók

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem javasoljuk a WSS fiókkal, amely jelenleg használatban van az üzemi célra.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja a WSS-fiókot, amely jelenleg használja a teszteléshez üzemi célra nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban konfigurálhatja az Azure AD az egyszeri bejelentkezés az Azure AD-fiókot a megadott felhasználói hitelesítő adatok használatával WSS való engedélyezéséhez.
Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Symantec webes biztonsági szolgáltatás (VSS) alkalmazás hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Symantec webes biztonsági szolgáltatás (VSS) hozzáadása a gyűjteményből
Az Azure AD integrálása Symantec webes biztonsági szolgáltatás (VSS) konfigurálásához kell hozzáadnia Symantec webes biztonsági szolgáltatás (VSS) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Symantec webes biztonsági szolgáltatás (VSS) a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Symantec webes biztonsági szolgáltatás (VSS)**, jelölje be **Symantec webes biztonsági szolgáltatás (VSS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Symantec webes biztonsági szolgáltatás (VSS) az eredménylistában](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Symantec webes biztonsági szolgáltatás (WSS) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Symantec webes biztonsági szolgáltatás (VSS) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Symantec webes biztonsági szolgáltatás (VSS) közötti kapcsolat kapcsolatot kell létrehozni.

A Symantec webes biztonsági szolgáltatás (VSS), rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Symantec webes biztonsági szolgáltatás (VSS) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Symantec webes biztonsági szolgáltatás (VSS) tesztfelhasználó létrehozása](#create-a-symantec-web-security-service-wss-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a Symantec webes biztonsági szolgáltatás (WSS), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Symantec webes biztonsági szolgáltatás (VSS) alkalmazás egyszeri bejelentkezés konfigurálása.

**Symantec webes biztonsági szolgáltatás (VSS) konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Symantec webes biztonsági szolgáltatás (VSS)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Az a **Symantec webes biztonsági szolgáltatás (VSS) tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Symantec webes biztonsági szolgáltatás (VSS) tartományhoz és URL-címek](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Az a **válasz URL-CÍMEN** szövegmező, írja be az URL-cím: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Vegye fel a kapcsolatot a [Symantec webes biztonsági szolgáltatás (VSS) ügyfél-támogatási csoport](https://www.symantec.com/contact-us) Ha értékeit a **azonosító** és **válasz URL-CÍMEN** valamilyen okból kifolyólag nem működik.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/symantec-tutorial/tutorial_general_400.png)
    
6. Az egyszeri bejelentkezést a Symantec webes biztonsági szolgáltatás (VSS) oldalon megadásához tekintse meg a WSS online dokumentációt. A letöltött **metaadatainak XML-kódja** fájl lehet a WSS-portálon importálni kell. Lépjen kapcsolatba a [Symantec webes biztonsági szolgáltatás (VSS) támogatási csoport](https://www.symantec.com/contact-us) a konfigurációval a WSS-portál segítségért.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/symantec-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/symantec-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/symantec-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/symantec-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Symantec webes biztonsági szolgáltatás (VSS) tesztfelhasználó létrehozása

Ebben a szakaszban nevű Britta Simon Symantec webes biztonsági szolgáltatás (VSS) a felhasználó létrehozása. A megfelelő befejezési felhasználónév manuálisan is létrehozható a WSS-portálon, vagy megvárhatja a felhasználók/csoportok üzembe helyezve (~ 15 perc). néhány perc múlva a WSS-portál szinkronizálásának engedélyezése az Azure AD-ben. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. A nyilvános IP-cím használatával keresse meg a webhelyek végfelhasználó gép is ki kell építeni a Symantec webes biztonsági szolgáltatás (VSS) portálon.

> [!NOTE]
> Adjon [ide](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) beolvasni a gépek tartozó nyilvános IP-cím.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Symantec webes biztonsági szolgáltatás (VSS) nyújtó Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Symantec webes biztonsági szolgáltatás (VSS) Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Symantec webes biztonsági szolgáltatás (VSS)**.

    ![A Symantec webes biztonsági szolgáltatás (VSS) hivatkozásra az alkalmazások listáját](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz azt tesztelni fogja, az egyszeri bejelentkezés funkció most, hogy konfigurálta az Azure AD SAML-alapú hitelesítéshez használandó WSS fiókját.

Miután konfigurálta WSS, proxy-forgalom a böngészőben nyissa meg a webböngészőt, és tallózással keresse meg a hely fogja átirányítani az Azure bejelentkezési oldalra, majd próbálja meg. Adja meg az Azure ad (Ez azt jelenti, hogy BrittaSimon) hiba teszt felhasználó hitelesítő adatait, és a kapcsolódó jelszót. Hitelesítését követően lesz a kiválasztott webhelyre navigálhat. Készítsen házirendszabály a WSS oldalon BrittaSimon letiltása az adott hely számára a böngészési akkor kell megjelennie a WSS-blokk lapon keresse meg, hogy a hely BrittaSimon felhasználóként tett kísérlet során.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png

