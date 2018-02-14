---
title: "Oktatóanyag: Azure Active Directoryval integrált munkahelyi által Facebook-on |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 4f34f6509d762cba6aba98a6eba010fd94656e67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Oktatóanyag: Azure Active Directoryval integrált munkahelyi által Facebook-on

Ebben az oktatóanyagban elsajátíthatja által Facebook munkahelyi integrálása az Azure Active Directory (Azure AD).

Munkahelyi által Facebook integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik a munkahelyi Facebook által szabályozható.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni munkahelyi által aláírt Facebook (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen kezelheti: az Azure-portálon.

Szoftver egy szolgáltatott szoftverként (SaaS) alkalmazás integráció az Azure ad-vel kapcsolatos további tudnivalókért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs munkahelyi által Facebook, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A munkahelyi Facebook egyszeri bejelentkezés (SSO) által engedélyezett előfizetés

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban a Azure AD SSO tesztkörnyezetben tesztelni. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adja hozzá a munkahelyi Facebook által a gyűjteményből.
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Adja hozzá a munkahelyi Facebook által a gyűjteményből
Konfigurálja az Azure AD integrálása a munkahely által Facebook, vegye fel munkahelyi Facebook által a gyűjteményből a kezelt SaaS-alkalmazások listáját.

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Keresse meg a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Az új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **által Facebook munkahelyi**, és válassza ki **által Facebook munkahelyi** eredménybe. Válassza ki **Hozzáadás**, hogy vegye fel az alkalmazást.

    ![Munkahelyi Facebook által az eredménylistában](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban konfigurálása és tesztelése a Facebook, a "Britta Simon." nevű tesztfelhasználó alapján által munkahelyi Azure AD egyszeri bejelentkezés

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, a partner felhasználó által Facebook munkahelyi Újdonságok egy felhasználó számára az Azure ad-ben. Más szóval közötti egy Azure AD-felhasználó és a kapcsolódó felhasználó a munkahelyi Facebook által hivatkozott kapcsolatot kell létrehozni.

Ez kapcsolatot létesíteni a értékének hozzárendelésével a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** munkahelyi Facebook által.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és egyszeri bejelentkezés konfigurálása Facebook-alkalmazás által a munkahelyen.

1. Az Azure portálon a a **által Facebook munkahelyi** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen jelölje ki **mód** , **SAML-alapú bejelentkezés** SSO engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Az a **Facebook-tartomány és az URL-címek munkahelyi** területen tegye a következőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ:`https://<company subdomain>.facebook.com`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ:`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Ezek az értékek csak egy példa. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Lépjen kapcsolatba a [Facebook ügyfél-támogatási csoport által munkahelyi](https://workplace.fb.com/faq/) beolvasni ezeket az értékeket. 

4. A a **SAML-aláíró tanúsítványa** szakaszban jelölje be **tanúsítvány (Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. Az a **Facebook konfigurációja munkahelyi** szakaszban jelölje be **munkahelyi konfigurálása által a Facebook** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló** szakasz.

    ![Munkahelyi Facebook-konfigurációja](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a munkahelyi Facebook vállalati hely rendszergazdaként.
  
   > [!NOTE] 
   > A SAML-hitelesítési folyamat részeként munkahelyi használhatja lekérdezési karakterláncok legfeljebb 2,5 kilobájt méretű ahhoz, hogy az Azure AD-paraméterekkel.

8. Az a **vállalati irányítópult**, navigáljon a **hitelesítési** fülre.

9. A **SAML-alapú hitelesítés**, jelölje be **SSO csak** a legördülő listából.

10. Adja meg az értékeket, átmásolva a **Facebook konfigurációja munkahelyi** szakasza a megfelelő mezőkbe az Azure-portálon:

    *   Az a **SAML-alapú URL-cím** szöveg mezőbe illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolta.
    *   Az a **SAML kiállítójának URL-címe** szöveg mezőbe illessze be az értékét **SAML Entitásazonosító**, amely az Azure portálról másolta.
    *   A **SAML kijelentkezési átirányítási (nem kötelező)**, illessze be az értékét **Sign-Out URL-cím**, amely az Azure portálról másolta.
    *   Nyissa meg a **base-64 kódolású tanúsítvány** Azure-portálról letöltött a Jegyzettömbben. A tartalmának másolása a vágólapra és illessze be azt a **SAML tanúsítvány** szövegmezőben.

11. Szükség lehet a célközönség URL-címet, címzett alatt felsorolva, és ACS (helyességi feltétel fogyasztói szolgáltatás) URL-címe, a **SAML-alapú konfigurációs** szakasz.

12. A szakasz alján görgessen, és válassza ki **teszt SSO**. Egy előugró ablak jelenik meg, az az Azure AD bejelentkezési oldal. A hitelesítéshez adja meg a hitelesítő adatok szokásos módon. Győződjön meg arról a címre küldi vissza az Azure AD vissza a ugyanaz a munkahelyi fiókkal jelentkezett be.

13. Ha a vizsgálat sikeresen befejeződött, görgessen a lap, és válasszon alján **mentése**.

14. Munkahelyi felhasználója számára most jelenik meg az Azure AD bejelentkezési oldalára hitelesítéshez.

Kiválaszthatja, konfigurálhatja az egy URL-cím, az Azure AD kijelentkezési oldalon ponthoz használható SAML kijelentkezett. Ha ezt a beállítást engedélyezve és konfigurálva van, a felhasználó már nem irányítja a rendszer a munkahelyi kijelentkezési lapra. Ehelyett a felhasználó átirányítási URL-címet, amely az SAML kijelentkezési átirányítási beállítás lett hozzáadva.


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg a állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban egyszerűen jelölje be a **egyszeri bejelentkezés** lapra, és elérheti a beágyazott keresztül dokumentáció a **konfigurációs** szakasz alján. További embedded dokumentációjából szolgáltatásáról a [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Újrahitelesítés gyakoriságának konfigurálása

Beállíthatja a munkahelyi bekéri a egy SAML-ellenőrzést minden nap, három nap, egy hét, a két hét, egy hónap, vagy egyáltalán nem.

> [!NOTE] 
>A mobilalkalmazás az SAML-ellenőrzését minimális értéke egy hét.

A SAML alaphelyzetbe állítja az összes felhasználó számára is kényszeríthető. Ehhez használja a **szükséges SAML-alapú hitelesítés az összes felhasználó most** gombra.


### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

1. Az a **Azure-portálon**, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és válassza ki **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőben **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe, írja be a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó**, és írja le.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Hozzon létre egy munkahelyi Facebook teszt felhasználó

Ebben a szakaszban Britta Simon nevű felhasználó létrehozta a munkaterületen Facebook-on. Munkahelyi Facebook által támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.

Nincs olyan művelet, ebben a szakaszban. Ha a felhasználó nem létezik a munkahely által Facebook, egy új hozta létre munkahelyi elérésére tett kísérlet során Facebook-on.

>[!Note]
>Ha a felhasználó manuális létrehozása, forduljon a [Facebook ügyfél-támogatási csoport által munkahelyi](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Azure SSO nyújtó munkahelyi Facebook által használandó Britta Simon engedélyezi.

![Felhasználó hozzárendelése][200] 

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. A könyvtár nézet megnyitása, írja be a **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **által Facebook munkahelyi**.

    ![A munkahely által az alkalmazások listáját a Facebook-hivatkozás](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezt követően a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **válasszon**.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Az SSO-beállítások tesztelésére, nyissa meg a hozzáférési Panel.
További információkért lásd: [Bevezetés a Hozzáférési panel használatába](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [SaaS-alkalmazásokhoz az Azure Active Directoryval integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md).
* Olvasási [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).
* További tudnivalók a [konfigurálja, a felhasználók átadása](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

