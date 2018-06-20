---
title: 'Oktatóanyag: Azure Active Directoryval integrált súgó Scout |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Súgó Scout között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: bf3afa56aeb51d3bcdaa84c1c2d75c86ee764f7b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216242"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Oktatóanyag: Azure Active Directoryval integrált Scout Súgó

Ebben az oktatóanyagban elsajátíthatja súgó Scout integrálása az Azure Active Directory (Azure AD).

Súgó Scout integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáférhet súgó Scout szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Súgó Scout (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs súgó Scout konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- A Súgó Scout egyszeri bejelentkezés engedélyezve van az előfizetésben

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Súgó Scout hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-help-scout-from-the-gallery"></a>Súgó Scout hozzáadása a gyűjteményből
Az Azure AD integrálása a Súgó Scout konfigurálásához kell hozzáadnia Scout súgó a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből súgó Scout hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **súgó Scout**, jelölje be **súgó Scout** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Scout Súgó](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Súgó Scout "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Súgó Scout a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Súgó Scout közötti kapcsolat kapcsolatot kell létrehozni.

Súgó Scout bejelentkezések e-mail címet használja, ezért a hivatkozást kapcsolat létrehozására, azonos **e-mail cím** , **felhasználónév** az Azure ad-ben.

Az Azure AD egyszeri bejelentkezést a Súgó Scout tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Súgó Scout tesztfelhasználó létrehozása](#create-a-help-scout-test-user)**  - való egy megfelelője a Britta Simon súgó Scout, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Súgó Scout alkalmazásban egyszeri bejelentkezés konfigurálása.

**Súgó Scout konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **súgó Scout** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Az a **Scout tartományban Help és URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Súgóadatok Scout tartomány és az URL-címek egyszeri bejelentkezés](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Azonosító** van a **"Célközönség URI (Service Provider entitás azonosítója)"** a Súgó Scout kezdődik `urn:`

    b. **Válasz URL-cím** van a **"Utáni hátsó URL-címe (helyességi feltétel ügyfél szolgáltatás URL-címe)"** a Súgó Scout kezdődik `https://` 

    > [!NOTE] 
    > Az URL-címek értékei csak bemutatásához. Frissítenie kell ezeket az értékeket a tényleges válasz URL-CÍMEN és azonosítója. Ezeket az értékeket a kap a **egyszeri bejelentkezés** lapon a hitelesítési szakasz, amelynek az ismertetése, az oktatóanyag későbbi részében.

4. Ha konfigurálja az alkalmazást a **SP** kezdeményezett módban, a jelölőnégyzet **megjelenítése speciális URL-beállításainak** és hajtsa végre a következő lépést:

    ![Súgóadatok Scout tartomány és az URL-címek egyszeri bejelentkezés](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://secure.helpscout.net/members/login/`
     
5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/helpscout-tutorial/tutorial_general_400.png)


7. A a **súgó Scout konfigurációs** kattintson **súgó Scout konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz**.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/config.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a Súgó Scout vállalati webhely rendszergazdaként.

9. Ha van bejelentkezve a kattintson a **"Kezelése"** a felső menüben, és válassza ki a **"Vállalati"** a legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings1.png) 
 
10. Válassza ki **"Hitelesítés"** a bal oldali menüből. 

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings2.png) 

11. Ezzel megnyitná a SAML-beállítások szakaszban, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings3.png) 
 
    a. Másolás a **visszaküldés URL-címe (helyességi feltétel ügyfél szolgáltatás URL-címe)** értékét, és illessze be az értéket a **válasz URL-CÍMEN** mezőben az Azure portál, Súgó Scout **tartomány és az URL-címek** szakasz.
    
    b. Másolás a **célközönség URI-azonosítója (Service Provider entitás azonosítója)** értékét, és illessze be az értéket a **azonosító** mezőben az Azure portál, Súgó Scout **tartomány és az URL-címek** szakasz.

12. Váltás **SAML engedélyezése** a és a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings4.png) 
 
    a. A **egyszeri bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
    
    b. Kattintson a **tanúsítvány feltöltése** feltölteni a **Certificate(Base64)** Azure portálról letöltve.

    c. Adja meg a szervezet e-mail tartomány(ok) e.x. - `contoso.com` a a **E-mail-tartomány** szövegmező. Elkülönítheti a több tartomány vesszővel válassza el. Bármikor a Súgó Scout felhasználó vagy rendszergazda, aki jeleníti meg, hogy adott tartományban a [súgó Scout napló oldal](https://secure.helpscout.net/members/login/) továbbítja a identitásszolgáltató hitelesítő adataikkal hitelesítést.

    d. Végül visszaváltható **kényszerített SAML bejelentkezés** Ha azt szeretné, hogy a felhasználók csak jelentkezzenek be súgó Scout keresztül ezzel a módszerrel. Ha továbbra is szeretné, hogy a Súgó Scout hitelesítő adatokkal jelentkezhetnek be, hogy a beállítást, akkor is hagyhatja váltható ki. Akkor is, ha engedélyezve van, egy fiók tulajdonosának mindig tudnak bejelentkezni súgó Scout az a fiók jelszavát.

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/helpscout-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/helpscout-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/helpscout-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/helpscout-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-help-scout-test-user"></a>Súgó Scout tesztfelhasználó létrehozása

Ez a szakasz célja súgó Scout Britta Simon nevű felhasználót létrehozni. Súgó Scout támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Súgó Scout, egy új súgó Scout elérésére tett kísérlet során jön létre.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés érdekében Scout Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Súgó Scout Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **súgó Scout**.

    ![Az alkalmazások listáját a Scout Súgó hivatkozásra](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel segítségével Scout mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett az súgó Scout alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png

