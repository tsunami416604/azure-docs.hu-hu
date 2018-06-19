---
title: 'Oktatóanyag: Azure Active Directoryval integrált zene rendezése |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Settling zene között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 4c74e4b23ad753ad3e9ff7503080b7a3190bc4b5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923734"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Oktatóanyag: Azure Active Directoryval integrált zene rendezése

Ebben az oktatóanyagban elsajátíthatja Settling zene integrálása az Azure Active Directory (Azure AD).

Settling zene integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér rendezni zene szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett rendezése zene (egyszeri bejelentkezés) a saját Azure AD-fiókok számára.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs zene rendezni, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Settling zene egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből zene Settling
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-settling-music-from-the-gallery"></a>A gyűjteményből zene Settling
Az Azure AD integrálása a Settling zene konfigurálásához kell hozzáadnia Settling zene a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Settling zene hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **zene rendezése**, jelölje be **zene rendezése** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában zene rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a zene "Britta Simon" nevű tesztfelhasználó alapján rendezni.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Settling zene a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó zene rendeznie közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés Settling zene tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Settling zene tesztfelhasználó létrehozása](#create-a-settling-music-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely csatolva van a felhasználó az Azure AD-ábrázolását zene rendeznie.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Settling zene alkalmazásban.

**Zene rendezése konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **zene rendezése** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

3. Az a **zene tartomány és az URL-címek rendezése** területen tegye a következőket:

    ![Zene tartomány és az URL-címek egyetlen bejelentkezési adatokat rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [zene ügyfél-támogatási csoport rendezése](https://rakurakuseisan.jp/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/settlingmusic-tutorial/tutorial_general_400.png)

6. A a **zene konfigurációs rendezése** kattintson **konfigurálása rendezése zene** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Zene konfigurációs rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

7. Egy másik webes böngészőablakban, zene, a biztonsági rendszergazda rendezése bejelentkezni.

8. A lap tetején kattintson **felügyeleti** fülre.

    ![Zene 1. lépés rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

9. Kattintson a **rendszerbeállítás** fülre.

    ![Zene 2. lépés rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

10. Váltás **biztonsági** fülre.

    ![Zene 3. lépés rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

11. Az a **az egyszeri bejelentkezés beállítása** területen tegye a következőket:

    ![Zene step5 rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Kattintson a **engedélyezése**.

    b. Az a **bejelentkezési URL-címet a azonosító szolgáltató** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. Az a **azonosító szolgáltató kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    d. Kattintson a **Choose File** feltölteni a **tanúsítvány (Base64)** amely letöltött alkotnak Azure-portálon.

    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/settlingmusic-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/settlingmusic-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/settlingmusic-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-settling-music-test-user"></a>Settling zene tesztfelhasználó létrehozása

Ebben a szakaszban egy Settling zene Britta Simon nevű felhasználót hoz létre. Együttműködve [zene ügyfél-támogatási csoport rendezése](https://rakurakuseisan.jp/) a felhasználók hozzáadása a Settling zene platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó zene rendezése való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Settling zene, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **zene rendezése**.

    ![Az alkalmazások listáját a Settling zene hivatkozás](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Settling zene csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Settling zene alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

