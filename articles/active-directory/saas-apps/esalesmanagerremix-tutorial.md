---
title: 'Oktatóanyag: Azure Active Directoryval integrált E értékesítés Manager Remix |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az E értékesítés Manager Remix között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 5592a0f6c465d4057ad5e3ee217d7a93f75ef88f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926046"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Az Azure Active Directory integrálása E értékesítési vezető Remix

Ebben az oktatóanyagban elsajátíthatja Azure Active Directory (Azure AD) integrálása E értékesítés Manager Remix.

Által az Azure AD integrálása E értékesítés Manager Remix, töltse le a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér E értékesítés Manager Remix szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy az első bejelentkezett automatikusan E értékesítés Manager Remix (egyszeri bejelentkezés, vagy az SSO) és az Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

E értékesítés Manager Remix konfigurálása az Azure AD-integrációs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az E értékesítés Manager Remix SSO-kompatibilis előfizetéssel

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, azt javasoljuk, hogy végezzen *nem* használja az éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

* E értékesítés Manager Remix hozzáadása a gyűjteményből
* És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Adja hozzá az E értékesítés Manager Remix a gyűjteményből
A rendszerrel történő integráció konfigurálása az Azure AD E értékesítés Manager Remix, vegye fel E értékesítés Manager Remix a gyűjteményből a felügyelt SaaS-alkalmazásokhoz a következő módon:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A "Vállalati alkalmazások" ablak][2]
    
3. Új alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **E értékesítés Manager Remix**, jelölje be **E értékesítés Manager Remix** az eredmények listájában, és válassza a **Hozzáadás**.

    ![E értékesítés Manager Remix az eredménylistában](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az E értékesítés Manager Remix, "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure AD-E értékesítés Manager Remix felhasználó és a megfelelő azonosítania kell. Más szóval E értékesítés Manager Remix azonos felhasználói, valamint az Azure AD-felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést az E értékesítés Manager Remix tesztelése és konfigurálása, végezze el az építőelemeket, a következő öt szakaszokban:

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az egyszeri bejelentkezés konfigurálása az E értékesítés Manager Remix alkalmazásban a következő módon:

1. Az Azure portálon a a **E értékesítés Manager Remix** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

2. Az a **egyszeri bejelentkezés** ablakban, a a **egyszeri bejelentkezés mód** mezőben válassza **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. A **E értékesítés Manager Remix tartomány és az URL-címek**, tegye a következőket:

    ![Az egyszeri bejelentkezés információkat E értékesítés Manager Remix tartomány és az URL-címek](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe, írja be egy URL-címet a következő formátumban: *https://\<Server-alapú-URL-cím > /\<altartomány > / esales-pc*.

    b. Az a **azonosító** mezőbe, írja be egy URL-címet a következő formátumban: *https://\<Server-alapú-URL-cím > /\<altartomány > /*.

    c. Megjegyzés: a **azonosító** értéket az oktatóanyag későbbi használatra.
    
    > [!NOTE] 
    > Az előző értékei nem valódi. Frissítse azokat a tényleges bejelentkezési URL-cím és azonosítója. Az értékek beszerzéséhez forduljon [E értékesítés Manager Remix ügyfél-támogatási csoport](mailto:esupport@softbrain.co.jp).

4. A **SAML-aláíró tanúsítványa**, jelölje be **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány (Base64) letöltési hivatkozását](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Válassza ki a **nézet és egyéb felhasználói attribútumok szerkesztése** jelölje be a jelölőnégyzetet, majd válassza ki a **emailaddress** attribútum.
    
    ![A felhasználói attribútumok ablak](./media/esalesmanagerremix-tutorial/configure1.png)

    A **attribútum szerkesztése** ablak nyílik meg.

6. Másolás a **Namespace** és **neve** értékeket. Az érték létrehozása a mintában  *\<Namespace > /\<neve >*, és mentse az oktatóanyag későbbi használatra.

    ![Az attribútum szerkesztése ablak](./media/esalesmanagerremix-tutorial/configure2.png)

7. A **E értékesítés Manager Remix konfigurációs**, jelölje be **konfigurálása E értékesítés Manager Remix**.

    ![E értékesítési vezető Remix konfiguráció](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    A **bejelentkezés konfigurálása** ablak nyílik meg.

8. Az a **rövid összefoglaló** szakaszban, másolja a kijelentkezési URL-cím és az SAML-alapú egyszeri bejelentkezési szolgáltatás URL-címet.

9. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

10. Jelentkezzen be rendszergazdaként az E értékesítés Manager Remix alkalmazás.

11. Jobb felső sarokban, válassza ki a **Rendszergazda menü**.

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

12. A bal oldali panelen válassza ki a **rendszerbeállítások** > **külső rendszer együttműködve**.

    ![A "Rendszer beállítások" és "Külső rendszer együttműködve" hivatkozások](./media/esalesmanagerremix-tutorial/configure5.png)
    
13. Az a **külső rendszer együttműködve** ablakban válassza ki **SAML**.

    ![A "Külső rendszer együttműködve" ablak](./media/esalesmanagerremix-tutorial/configure6.png)

14. A **SAML-hitelesítési beállítás**, tegye a következőket:

    ![A "SAML-alapú hitelesítés" szakasz](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Válassza ki a **PC verzió** jelölőnégyzetet.
    
    b. Az a **együttműködés elem** csoportban, a legördülő listában jelölje be **e-mail**.

    c. Az a **együttműködés elem** mezőbe illessze be a korábban kimásolt Azure-portálról jogcím értékét (Ez azt jelenti, hogy **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. Az a **kibocsátó (entitás azonosítója)** mezőbe illessze be a korábban kimásolt az azonosító értéket a **E értékesítés Manager Remix tartomány és az URL-címek** szakasza az Azure-portálon.

    e. Azure-portálról letöltött tanúsítvány feltöltésének, válassza ki a **kijelölés fájl**.

    f. Az a **azonosító szolgáltató bejelentkezési URL-cím** mezőbe illessze be a SAML-alapú egyszeri bejelentkezési szolgáltatás URL-címet, amelyet korábban az Azure-portálon vágólapra másolt.

    g. A **Identity Provider kijelentkezési URL-cím** mezőbe illessze be a kijelentkezési URL-címével, amely az Azure-portálon korábban kimásolt.

    h. Válassza ki **beállítás teljes**.

> [!TIP]
> Hoz létre az alkalmazást, mert egy előző utasításait tömör verziója elolvashatja a [Azure-portálon](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a a beágyazott dokumentációjában találhatók a **konfigurációs** szakasz alján. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

![Hozzon létre egy Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Az aktuális felhasználó listáját jeleníti meg, jelölje be **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. Felső részén a **minden felhasználó** ablakban válassza ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A **felhasználói** ablak nyílik meg.

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Az E értékesítés Manager Remix tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként az E értékesítés Manager Remix alkalmazás.

2. Válassza ki **Rendszergazda menü** jobb felső sarokban a menüből.

    ![E értékesítési vezető Remix konfiguráció](./media/esalesmanagerremix-tutorial/configure4.png)

3. Válassza ki **a vállalati beállítások** > **karbantartás a részleg és az alkalmazottak**, majd válassza ki **regisztrált alkalmazottak**.

    ![A "Alkalmazottak regisztrált" lap](./media/esalesmanagerremix-tutorial/user1.png)

4. Az a **új alkalmazott regisztrációs** területen tegye a következőket:
    
    ![Az "új alkalmazott regisztrációs" szakasz](./media/esalesmanagerremix-tutorial/user2.png)

    a. Az a **alkalmazott neve** mezőbe írja be annak a felhasználónak a nevét (például **Britta**).

    b. Töltse ki a többi kötelező mezőket.
    
    c. SAML engedélyezése, ha a rendszergazda nem tud bejelentkezni, a bejelentkezési oldalról. Támogatás rendszergazdai bejelentkezési jogosultságokat a felhasználó kiválasztásával a **rendszergazdai bejelentkezés** jelölőnégyzetet.

    d. Válassza ki **regisztrációs**.

5. A jövőben, jelentkezzen be rendszergazdaként, hogy jelentkezzen be a rendszergazdai engedélyekkel rendelkezik, és ezután jobb felső sarokban, válassza ki a felhasználó **Rendszergazda menü**.

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban a felhasználó által biztosított hozzáférés E értékesítés Manager Remix Azure egyszeri bejelentkezéshez használandó Britta Simon engedélyezi. Ehhez tegye a következőket: 

![A felhasználói szerepkör hozzárendelése][200] 

1. Az Azure portálon, nyissa meg a **alkalmazások** nézet, keresse fel a **Directory** megtekintéséhez, majd válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Vállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

2. Az a **alkalmazások** listáról válassza ki **E értékesítés Manager Remix**.

    ![Az E értékesítés Manager Remix hivatkozás](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Válassza ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** ablakban válassza ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Az E értékesítés Manager Remix csempe a hozzáférési panelen válassza ki, ha be kell jelentkeznie automatikusan az E értékesítés Manager Remix alkalmazására.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

