---
title: 'Oktatóanyag: Azure Active Directory-integráció G Suite |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a G Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: aaa42283f44bc090d54db85ff5062edffa1b4b02
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Oktatóanyag: Azure Active Directory-integráció G csomaggal

Ebben az oktatóanyagban elsajátíthatja G Suite integrálása az Azure Active Directory (Azure AD).

G Suite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér G Suite szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a G Suite (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása G csomaggal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A G Suite egyszeri bejelentkezés engedélyezve van az előfizetés
- A Google Apps-előfizetés vagy a Google Cloud Platform előfizetés.

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések
1.  **K: az integráció támogatásától Google Cloud Platform SSO integráció az Azure ad-val?**
    
    V: Igen. Google Cloud Platform és a Google Apps ugyanazt a hitelesítési azonos platformot. Ezért a GCP integrációs ehhez szüksége lesz az egyszeri bejelentkezés beállítása a Google Apps.


2. **K: Chromebooks és egyéb Chrome eszközök kompatibilisek legyenek az Azure AD az egyszeri bejelentkezés?**
   
    V: Igen, a felhasználók jelentkezhetnek be a Azure AD hitelesítő adataik használatával Chromebook eszközeiket. Ez [G Suite támogatási cikk](https://support.google.com/chrome/a/answer/6060880) információt arról, hogy miért felhasználók lehet kérni a hitelesítő adatok kétszer.

3. **Kérdés: Ha az egyszeri bejelentkezés engedélyezése felhasználók fogja tudni az Azure AD hitelesítő adatai segítségével jelentkezzen be Google bármely termék, például a Google osztályteremben, GMail, Google meghajtó, YouTube stb?**
   
    V: Igen, attól függően [mely G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) úgy dönt, hogy engedélyezi vagy letiltja a szervezet számára.

4. **K: engedélyezhető az egyszeri bejelentkezés saját G Suite felhasználók csak egy része számára?**
   
    A: nem bekapcsolásával egyszeri bejelentkezés azonnal minden a G Suite felhasználótól megköveteli a hitelesítést az Azure AD hitelesítő adatait. Mivel G csomag nem támogatja több identitás-szolgáltatóktól rendelkező, a G Suite környezetnek az identitásszolgáltató lehet az Azure AD vagy Google –, de nem mindkettőt egyszerre.

5. **K:, ha van bejelentkezett felhasználó Windows keresztül, azok automatikusan hitelesíteniük kell G Suite bekéri jelszó nélkül?**
   
    A: Ez a forgatókönyv engedélyezésének két lehetőség áll rendelkezésre. Először sikerült jelentkeznek be Windows 10-eszközöket az [Azure Active Directory csatlakozási](active-directory-azureadjoin-overview.md). Azt is megteheti, sikerült jelentkeznek be Windows-eszközök, amelyek a tartományhoz, amely az egyszeri bejelentkezés az Azure AD-keresztül engedélyezve van a helyszíni Active Directory egy [Active Directory összevonási szolgáltatások (AD FS)](active-directory-aadconnect-user-signin.md) központi telepítés. Mindkét lehetőség kell ahhoz, hogy az egyszeri bejelentkezés az Azure AD között az alábbi oktatóanyag hajtsa végre a lépéseket és G csomag.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből G csomag hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-g-suite-from-the-gallery"></a>A gyűjteményből G csomag hozzáadása
Az Azure AD integrálása a G Suite konfigurálásához kell hozzáadnia G Suite a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből G Suite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **G Suite**, jelölje be **G Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában G csomag](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés G Suite "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó G csomagban található a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a G Suite közötti kapcsolat kapcsolatot kell létrehozni.

G Suite, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés G Suite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[G Suite tesztfelhasználó létrehozása](#create-a-g-suite-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely csatolva van a felhasználó az Azure AD-ábrázolását G csomagban található.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az G Suite alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés G csomaggal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **G Suite** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. Az a **G Suite tartomány és az URL-címek** szakaszban, ha a konfigurálni kívánt a **Gmail** hajtsa végre a következő lépéseket:

    ![Az egyszeri bejelentkezés információk G Suite tartomány és az URL-címek](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_urlgmail.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-cím URL-címe: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [G Suite ügyfél-támogatási csoport](https://www.google.com/contact/) beolvasni ezeket az értékeket.

4. Az a **G Suite tartomány és az URL-címek** szakaszban, ha a konfigurálni kívánt a **Google Cloud Platform** hajtsa végre a következő lépéseket:

    ![Az egyszeri bejelentkezés információk G Suite tartomány és az URL-címek](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url1.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-cím URL-címe: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [G Suite ügyfél-támogatási csoport](https://www.google.com/contact/) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

7. A a **G Suite konfigurációs** kattintson **G-csomag konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML-alapú egyszeri bejelentkezési URL-címe és a módosítás jelszó URL-cím** a a **rövid összefoglaló szakasz.**

    ![G teljes telepítés konfigurálása](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

8. Új lap megnyitása a böngészőben, és jelentkezzen be a [G Suite felügyeleti konzol](http://admin.google.com/) a rendszergazdai fiók használatával.

9. Kattintson a **biztonsági**. Ha nem látja a hivatkozásra, akkor előfordulhat, hogy rejtve alatt a **további vezérlők** menü a képernyő alján.
   
    ![Kattintson a Security (Biztonság) elemre.][10]

10. Az a **biztonsági** kattintson **beállítása az egyszeri bejelentkezés (SSO).**
   
    ![Kattintson az egyszeri Bejelentkezést.][11]

11. Hajtsa végre a következő konfigurációs módosításokat:
   
    ![Egyszeri bejelentkezés konfigurálása][12]
   
    a. Válassza ki **külső identitásszolgáltatótól telepítő SSO**.

    b. Az a **bejelentkezési URL-címe** G Suite mezőbe illessze be az értékét **egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. Az a **kijelentkezési URL-címe** G Suite mezőbe illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta. 

    d. Az a **Módosítsa jelszavát URL-címet** G Suite mezőbe illessze be az értékét **Módosítsa jelszavát URL-címet** ami Azure-portálon másolta. 

    e. A G Suite az a **ellenőrző tanúsítvány**, az Azure-portálról letöltött tanúsítvány feltöltése.

    f. Válassza ki **tartomány adott kiállító**.

    g. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-g-suite-test-user"></a>G Suite tesztfelhasználó létrehozása

Ez a szakasz célja G Suite szoftver Britta Simon nevű felhasználót létrehozni. G Suite támogatja automatikus kiépítés, amely alapértelmezés szerint van engedélyezve. Nincs olyan művelet, ebben a szakaszban. Ha a felhasználó nem létezik a G Suite szoftver, egy új G Suite szoftver elérésére tett kísérlet során jön létre.

>[!NOTE] 
>Ha a felhasználó manuális létrehozása, forduljon a [Google támogatási csoport](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés G Suite Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése G Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **G Suite**.

    ![Az alkalmazások listáját a G Suite hivatkozás](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen G Suite csempére kattint, meg kell beolvasása automatikusan bejelentkezett a G Suite alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

