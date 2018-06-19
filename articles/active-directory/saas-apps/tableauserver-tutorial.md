---
title: 'Oktatóanyag: Azure Active Directory-integráció Tableau kiszolgálóval |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Tableau kiszolgáló között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: a10e0bda9728aed9aae74a85d4bb4bdb4113292a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35931456"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Oktatóanyag: Azure Active Directory-integráció Tableau kiszolgálóval

Ebben az oktatóanyagban elsajátíthatja Tableau kiszolgáló integrálása az Azure Active Directory (Azure AD).

Tableau kiszolgáló integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Tableau-kiszolgálóhoz hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Tableau kiszolgálóra (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau kiszolgálóval, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Tableau Server egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Tableau kiszolgáló hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-tableau-server-from-the-gallery"></a>Tableau kiszolgáló hozzáadása a gyűjteményből
Az Azure AD integrálása a Tableau kiszolgáló konfigurálásához szüksége Tableau kiszolgáló hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Tableau kiszolgáló hozzáadása a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Tableau Server**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Az eredmények panelen válassza ki a **Tableau Server**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Tableau kiszolgálóval

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Tableau Server Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Tableau Server közötti kapcsolat kapcsolatot kell létrehozni.

A Tableau kiszolgáló rendelje értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Tableau kiszolgálóval tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Tableau Server tesztfelhasználó létrehozása](#creating-a-tableau-server-test-user)**  - való Britta Simon egy megfelelője a Tableau kiszolgáló, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Tableau kiszolgálóalkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Tableau kiszolgálóval, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Tableau Server** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Az a **Tableau kiszolgáló tartományával és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://azure.<domain name>.link`
    
    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://azure.<domain name>.link`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > A fenti értékek nem valódi értékek. Később akkor módosítsa a tényleges URL-cím és a Tableau kiszolgáló konfigurációs lapon azonosítója. 

4. Tableau kiszolgálóalkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **"Felhasználói attribútumok"** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa az azonos.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/3.png)
    
5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | felhasználónév | *user.mailnickname* |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**


6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, meg kell bejelentkezés a Tableau Server bérlő rendszergazdaként.
   
   a. A Tableau kiszolgálókonfiguráció lapon kattintson a **SAML** fülre.
  
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. A jelölőnégyzet bejelölésével **az egyszeri bejelentkezéshez használható SAML**.
   
   c. Tableau kiszolgálói válasz URL-cím – az URL-címet, amely a Tableau Server felhasználók hozzáférhetnek, például a http://tableau_server. Használatával http://localhost nem ajánlott. Egy URL-cím használata a záró perjelet (például http://tableau_server/) nem támogatott. Másolás **Tableau kiszolgálói válasz URL-cím** és illessze be az Azure AD **URL-cím bejelentkezési** textbox **Tableau kiszolgáló tartományával és URL-címek** szakasz.
   
   d. SAML Entitásazonosító – az entitás azonosítója egyedileg azonosítja a kiállító terjesztési hely a Tableau Server telepítése. Adhatja meg a Tableau URL-címe újra ide, ha szeretné, de nem rendelkezik a Tableau kiszolgáló URL-címét. Másolás **SAML Entitásazonosító** és illessze be az Azure AD **azonosító** textbox **Tableau kiszolgáló tartományával és URL-címek** szakasz.
     
   e. Kattintson a **metaadat-fájl exportálása** , majd nyissa meg a szöveg szerkesztő alkalmazásban. Keresse meg a helyességi feltétel fogyasztói szolgáltatás URL-cím elé Http Post és Index 0, és másolja az URL-címet. Most illessze be az Azure AD **válasz URL-CÍMEN** textbox **Tableau kiszolgáló tartományával és URL-címek** szakasz.
   
   f. Keresse meg az összevonási metaadatok fájlt az Azure portálról letöltött, majd töltse fel azt a **SAML Idp metaadatfájl**.
   
   g. Kattintson a **OK** gomb a Tableau kiszolgálókonfiguráció lapon.
   
    >[!NOTE] 
    >Ügyfél töltse fel a tanúsítványt a Tableau kiszolgáló SAML SSO konfigurációban kell, és figyelmen beolvasása az SSO folyamatában.
    >Ha később kell súgó a SAML konfigurálása a Tableau kiszolgálón, majd tekintse meg a cikk [SAML konfigurálása](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-tableau-server-test-user"></a>A Tableau Server tesztfelhasználó létrehozása

Ez a szakasz célja a Tableau Server Britta Simon nevű felhasználót létrehozni. Kell kiépíteni a Tableau kiszolgáló összes felhasználóját. 

A felhasználó a felhasználónév meg kell felelnie a állított be az Azure AD egyéni attribútum értéke **felhasználónév**. A megfelelő leképezéssel az integráció kell működnie [az Azure AD konfigurálása egyszeri bejelentkezéshez](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha a rendszergazdától Tableau a szervezet szeretné.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon hozzáférést biztosít a Tableau kiszolgáló által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Tableau Server, a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Tableau Server**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel Tableau Server mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett a Tableau Server alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

