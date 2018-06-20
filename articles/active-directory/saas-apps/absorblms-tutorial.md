---
title: 'Oktatóanyag: Azure Active Directoryval integrált felvegye LMS |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és felvegye LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 4cf4e0ffc339bb90f013315a53025c83fcaad21f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217571"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Azure Active Directoryval integrált felvegye LMS

Ebben az oktatóanyagban elsajátíthatja felvegye LMS integrálása az Azure Active Directory (Azure AD).

Felvegye LMS integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, hogy felvegye LMS hozzáféréssel rendelkező Azure AD-ben.
- Engedélyezheti a felhasználók automatikusan jelentkezzen be felvegye LMS (keresztül egyszeri bejelentkezés) az Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Ha azt szeretné, az Azure AD egy szolgáltatott szoftverként (SaaS) alkalmazás integrációt, tudnia további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs felvegye LMS konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy felvegye LMS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Azt javasoljuk, nem éles környezetben ehhez az oktatóanyaghoz.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

* A gyűjteményből felvegye LMS hozzáadása
* És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-absorb-lms-from-the-gallery"></a>Adja hozzá a LMS felvegye a gyűjteményből
Konfigurálása az Azure AD integrálása a LMS felvegye, vegye fel LMS felvegye a gyűjteményből a felügyelt SaaS-alkalmazásokhoz.

A gyűjteményből felvegye LMS hozzáadásához tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások ablaktábla][2]
    
3. Hozzáadhat egy alkalmazást, válassza ki a **új alkalmazás** gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **felvegye LMS**, jelölje be **felvegye LMS** a panel vezethet, és válassza ki a **Hozzáadás** gombra.

    ![Az eredménylistában LMS felvegye](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés felvegye LMS Britta Simon nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a felvegye LMS megfelelőjére felhasználó Újdonságok az Azure ad-ben. Más szóval a felhasználó Azure AD-ben és a megfelelő felhasználó között LMS felvegye a hivatkozás kapcsolatot kell létesítenie.

Kapcsolatot hoz létre a kapcsolat kapcsolat hozzárendelésével a *felhasználónév* érték az Azure AD szolgáltatásba a *felhasználónév* felvegye LMS értéket.

Az Azure AD egyszeri bejelentkezést a felvegye LMS tesztelése és konfigurálása, végezze el a következő öt szakaszokban építőelemeit.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az felvegye LMS alkalmazásban.

Konfigurálása az Azure AD az egyszeri bejelentkezés LMS felvegye, tegye a következőket:

1. Az Azure portálon a a **felvegye LMS** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanel a **mód** mezőben válassza **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Az a **felvegye LMS tartomány és az URL-címek** területen tegye a következőket:

    ![Felvegye LMS tartomány és az URL-címek egyetlen bejelentkezés információk](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. Az a **azonosító** mezőbe írjon be egy URL-címet, a következő szintaxist: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. Az a **válasz URL-CÍMEN** mezőbe írjon be egy URL-címet, a következő szintaxist: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Ezen URL-címei nem a tényleges értékek. Frissítse azokat a tényleges azonosítója és a válasz URL-címek. Ezek az értékek beszerzéséhez forduljon a [felvegye LMS ügyfél-támogatási csoport](https://www.absorblms.com/support). 

4. A a **SAML-aláíró tanúsítványa** részben, a a **letöltése** oszlopból válassza ki **metaadatainak XML-kódja**, majd mentse a metaadat-fájlt a számítógépre.

    ![Az aláíró tanúsítvány letöltési hivatkozását](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. Az a **felvegye LMS konfigurációs** szakaszban jelölje be **felvegye LMS konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablakot, majd másolja a **Sign-Out URL-cím** a a **rövid összefoglaló szakasz.**

    ![A felvegye LMS konfigurációs ablaktábla](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. Az egy új böngészőablakban nyílik jelentkezzen be a felvegye LMS vállalati webhely rendszergazdaként.

8. Válassza ki a **fiók** gomb jobb felső sarokban. 

    ![A fiók gomb](./media/absorblms-tutorial/1.png)

9. A fiók ablaktábla, válassza a **portál beállításait**.

    ![A portál beállításait hivatkozás](./media/absorblms-tutorial/2.png)
    
10. Válassza a **Felhasználók** lapot.

    ![A felhasználók lap](./media/absorblms-tutorial/3.png)

11. Egyszeri bejelentkezés konfigurációs lapján tegye a következőket:

    ![Az egyszeri bejelentkezés konfigurálása lapon](./media/absorblms-tutorial/4.png)

    a. Az a **mód** mezőben válassza **identitás szolgáltató által kezdeményezett**.

    b. A Jegyzettömbben nyissa meg az Azure-portálról letöltött tanúsítvány. Távolítsa el a **---BEGIN CERTIFICATE---** és **---vége tanúsítvány---** címkék. Ezt követően a a **kulcs** mezőbe illessze be a maradék tartalmat.
    
    c. Az a **azonosítóját megadó tulajdonságot** mezőben adja meg, amely az Azure ad-ben a felhasználói azonosító értéke. Például ha *userPrincipalName* az Azure ad-ben, válassza ki van jelölve **felhasználónév**.

    d. Az a **bejelentkezési URL-cím** mezőbe illessze be a **felhasználói URL-CÍMEN** az alkalmazás **tulajdonságok** az Azure-portálon oldalán.

    e. Az a **kijelentkezési URL-cím**, illessze be a **Sign-Out URL-cím** fájlból másolt érték a **bejelentkezés konfigurálása** ablakot, az Azure-portálon.

12. Váltás **csak engedélyezése az Egyszeri bejelentkezés** való **a**.

    ![Az Egyszeri bejelentkezés csak engedélyezése váltása](./media/absorblms-tutorial/5.png)

13. Válassza ki **mentéséhez.**

> [!TIP]
> Ezeket az utasításokat a tömör verzióját el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Miután hozzáadta az alkalmazásból a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott hozzáférési a dokumentáció a **konfigurációs** alsó szakasz. További információkért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ebben a szakaszban az Azure portálon Britta Simon tesztfelhasználó hoz létre.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

Tesztfelhasználó létrehozása az Azure ad-ben, tegye a következőket:

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/absorblms-tutorial/create_aaduser_01.png) 

2. Válassza ki azon felhasználók listájának megjelenítéséhez **felhasználók és csoportok** > **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/absorblms-tutorial/create_aaduser_02.png) 

3. A párbeszédpanel tetején válassza **Hozzáadás**.
 
    ![A Hozzáadás gombra.](./media/absorblms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A felhasználó párbeszédpanel](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz Britta Simon e-mail címét.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értéket a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-an-absorb-lms-test-user"></a>Hozzon létre egy felvegye LMS tesztfelhasználó számára

Az Azure Active Directory-felhasználók felvegye LMS bejelentkezni hogy meg kell LMS felvegye a.  

A telepítő felvegye LMS kézi tevékenység esetén.

Beállíthat egy felhasználói fiókot, tegye a következőket:

1. Jelentkezzen be rendszergazdaként a felvegye LMS vállalati webhelyre.

2. A bal oldali panelen válassza ki a **felhasználók**.

    ![A felvegye LMS felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_users.png)

3. Az a **felhasználók** ablaktáblán válassza előbb **felhasználók**.

    ![A felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_userssub.png)

4. Az a **új hozzáadása** legördülő listában válassza **felhasználói**.

    ![Az új hozzáadása legördülő lista](./media/absorblms-tutorial/absorblms_createuser.png)

5. Az a **felhasználó hozzáadása** lapján tegye a következőket:

    ![A felhasználó hozzáadása oldalon](./media/absorblms-tutorial/user.png)

    a. Az a **Utónév** mezőbe írja be például a Keresztnév **Britta**.

    b. Az a **Vezetéknév** mezőbe írja be például a Vezetéknév **Simon**.
    
    c. Az a **felhasználónév** mezőbe írja be a teljes nevet, például a **Britta Simon**.

    d. Az a **jelszó** Britta Simon jelszót írja be.

    e. Az a **jelszó megerősítése** mezőbe írja be ismét a jelszót.
    
    f. Állítsa be a **aktív** kapcsolót **aktív**.  

6. Válassza ki **mentéséhez.**
 
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban a felhasználó által biztosított hozzáférés felvegye LMS Azure egyszeri bejelentkezéshez használandó Britta Simon engedélyezi.

![A felhasználói szerepkör hozzárendelése][200]

Felhasználó Britta Simon hozzárendelése LMS felvegye, tegye a következőket:

1. Az Azure portálon, az alkalmazások nézet megnyitásához, nyissa meg a könyvtár nézetet, és válassza **vállalati alkalmazások** > **összes alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][201] 

2. Az a **alkalmazások** listáról válassza ki **felvegye LMS**.

    ![Az alkalmazások listáját a felvegye LMS hivatkozás](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A hozzáférési panelen válassza a **felvegye LMS** csempe automatikusan bejelentkezik, az felvegye LMS alkalmazás. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png

