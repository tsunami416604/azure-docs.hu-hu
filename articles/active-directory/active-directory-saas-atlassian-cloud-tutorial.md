---
title: 'Oktatóanyag: Azure Active Directory-integráció a Atlassian felhőalapú |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Atlassian felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 261baed7b8e1e5480396c06a9f92ecfb48a8b7ac
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338856"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Oktatóanyag: Azure Active Directoryval integrált Atlassian felhő

Ebben az oktatóanyagban elsajátíthatja Atlassian felhő integrálása az Azure Active Directory (Azure AD).

Atlassian felhő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik Atlassian felhőbe szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan bejelentkezve (egyszeri bejelentkezés) Atlassian felhőhöz, az Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Az Azure AD egy szolgáltatott szoftverként (SaaS) alkalmazásintegráció, szoftverrel kapcsolatos további információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Atlassian felhőalapú, a következőkre van szükség:

- Az Azure AD-előfizetés.
- A Security Assertion Markup Language (SAML) egyszeri bejelentkezés engedélyezése Atlassian felhő termékek, be kell Identity Manager fel. További információ [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, ajánlott, hogy nem éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.
Az oktatóanyag ismertetett forgatókönyvben két fő építőelemeket áll:

* A gyűjteményből Atlassian felhő hozzáadása
* És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-atlassian-cloud-from-the-gallery"></a>A gyűjteményből Atlassian felhő hozzáadása
A rendszerrel történő integráció konfigurálása a Atlassian felhőalapú Azure AD, hozzáadása Atlassian felhőalapú a gyűjteményből a kezelt SaaS-alkalmazások listáját a következő módon:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások ablaktábla][2]
    
3. Hozzáadhat egy alkalmazást, válassza ki a **új alkalmazás**.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **Atlassian felhő**, az eredmények listájában válassza **Atlassian felhő**, majd válassza ki **Hozzáadás**.

    ![Az eredménylistában Atlassian felhő](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Atlassian felhőalapú nevű tesztfelhasználó alapján *Britta Simon*.

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure ad-ben a Atlassian felhő felhasználó és a megfelelő azonosítania kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létesítenie Atlassian felhőben.

A hivatkozás kapcsolat létrehozására, rendelje hozzá a Atlassian felhők *felhasználónév* ugyanazt az értéket, amely hozzá van rendelve az Azure AD *felhasználónév*.

Az Azure AD egyszeri bejelentkezést a Atlassian felhőalapú tesztelése és konfigurálása, hogy végrehajtásához szükséges az építőelemeket, az alábbi szakaszokban található.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Atlassian felhőalapú alkalmazásokhoz.

Konfigurálja az Azure AD egyszeri bejelentkezést a Atlassian felhőalapú, tegye a következőket:

1. Az Azure portálon a a **Atlassian felhő** alkalmazás integrációs ablaktáblán válassza előbb **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** ablakban, a a **egyszeri bejelentkezés mód** mezőben válassza **SAML-alapú bejelentkezés**.

    ![Egyszeri bejelentkezés ablak](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Az alkalmazás a kiállító terjesztési hely által kezdeményezett módban konfigurálásához **Atlassian felhőalapú tartományt és URL-címek**, tegye a következőket:

    ![Az egyszeri bejelentkezés információk Atlassian felhőalapú tartományt és URL-címek](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Az a **azonosító** mezőbe írja be **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. Az a **válasz URL-CÍMEN** mezőbe írja be **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. Az a **továbbítási állapotot** mezőbe írja be egy URL-címet a következő szintaxissal: **`https://<instancename>.atlassian.net`**.

4. A Szolgáltató által kezdeményezett módban az alkalmazás konfigurálásához jelölje ki a **megjelenítése speciális URL-beállításainak** , majd a a **bejelentkezési URL-cím** mezőbe írja be egy URL-címet a következő szintaxissal: **`https://<instancename>.atlassian.net`** .

    ![Az egyszeri bejelentkezés információk Atlassian felhőalapú tartományt és URL-címek](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Az előző értékei nem valódi. Frissítse azokat a tényleges azonosítóját, a válasz URL-CÍMEN és a bejelentkezési URL-értékek. A valódi értékek lekérheti a Atlassian felhő SAML konfigurálására szolgáló képernyőn. Az oktatóanyag későbbi részében értékek azt ismertetik.

5. A **SAML-aláíró tanúsítványa**, jelölje be **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. A Atlassian felhőalapú alkalmazásnál a SAML helyességi feltételek található egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. 

    Alapértelmezés szerint a **felhasználói azonosító** érték user.userprincipalname van leképezve. Módosítsa ezt az értéket user.mail van leképezve. Másik lehetőségként a szervezet beállítását szerint más megfelelő érték, de az esetek többségében, e-mailt kell működnie.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Kattintson a **Mentés** gombra.

    ![A konfigurálása egyszeri bejelentkezéshez mentési gomb](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Lehetőségre a **bejelentkezés konfigurálása** ablakban, a a **Atlassian Felhőkonfiguráció** szakaszban jelölje be **Atlassian felhő konfigurálása**.

9. Az a **rövid összefoglaló** szakaszban, másolja a **SAML Entitásazonosító** és **SAML-alapú egyszeri bejelentkezési URL-címe**.

    ![Atlassian felhőkonfiguráció](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, jelentkezzen be rendszergazdai hitelesítő adatokkal a Atlassian portálra.

11. Vissza kell igazolnia a tartományban az egyszeri bejelentkezés konfigurálása előtt. További információkért lásd: [Atlassian tartományok ellenőrzésének](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentum.

12. A bal oldali panelen válassza ki a **SAML-alapú egyszeri bejelentkezést**. Ha még nem tette meg, az előfizetés Atlassian Identity Manager.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. Az a **hozzáadása SAML-alapú konfigurációs** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Az a **identitásszolgáltató Entitásazonosító** mezőbe illessze be az Azure-portálról másolt SAML entitás azonosítója.

    b. Az a **identitásszolgáltató egyszeri bejelentkezési URL-cím** mezőbe illessze be a SAML-alapú egyszeri bejelentkezési szolgáltatás URL-cím, az Azure portálról másolt.

    c. Nyissa meg a letöltött tanúsítvány egy .txt fájlban, az Azure portálról kimásolhatja az értéket (nélkül a *megkezdéséhez tanúsítvány* és *End Certificate* sorok), majd illessze be azt a **nyilvános X509 tanúsítvány** mezőbe.
    
    d. Válassza ki **konfigurációjának mentéséhez**.
     
14. Győződjön meg arról, hogy meg van adva a megfelelő URL-címeket, frissítse az Azure AD-beállításokat a következő módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. A SAML ablakban másolja a **SP identitás azonosító** , majd a az Azure portál, Atlassian felhő **tartomány és az URL-címek**, illessze be a **azonosítója** mezőbe.
    
    b. A SAML ablakban másolja a **SP helyességi feltétel ügyfél szolgáltatás URL-címe** , majd a az Azure portál, Atlassian felhő **tartomány és az URL-címek**, illessze be a **válasz URL-CÍMEN** mezőbe. A bejelentkezési URL-je a Atlassian felhő bérlői URL-CÍMÉT.

    > [!NOTE]
    > Ha Ön egy meglévő ügyfél frissítése után a **SP identitás azonosító** és **SP helyességi feltétel ügyfél szolgáltatás URL-címe** értékek az Azure portálon válassza **Igen, a konfigurációfrissítése**. Ha egy új ügyfél, kihagyhatja ezt a lépést.
    
15. Válassza ki az Azure-portálon **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Válassza ki azon felhasználók listájának megjelenítéséhez **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Az a **minden felhasználó** ablakban válassza ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-an-atlassian-cloud-test-user"></a>Hozzon létre egy Atlassian felhő tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók jelentkezhetnek be Atlassian felhőbe, a felhasználói fiókok manuális Atlassian felhőben kiépítése a következő módon:

1. Az a **felügyeleti** ablaktáblán válassza előbb **felhasználók**.

    ![A felhő felhasználóinak Atlassian hivatkozás](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. A felhasználó Atlassian felhőben létrehozásához válassza **a meghívás felhasználói**.

    ![Hozzon létre Atlassian felhő felhasználót](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Az a **E-mail cím** mezőbe, majd rendelje hozzá az alkalmazás-hozzáférés és a felhasználó e-mail címét adja meg.

    ![Hozzon létre Atlassian felhő felhasználót](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. A felhasználó felkérést e-mailt küldeni, válassza ki a **meghívott felhasználóknak**. E-mailek meghívót küld a felhasználónak, és elfogadja a meghívót, miután a felhasználó nem aktív, a rendszer.

>[!NOTE]
>Tömegesen is-felhasználók létrehozása kiválasztásával a **tömeges létrehozása** gombra a **felhasználók** szakasz.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban a felhasználó által biztosított hozzáférés Atlassian felhőalapú Azure egyszeri bejelentkezéshez használandó Britta Simon engedélyezi. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200]

1. Az Azure portálon, nyissa meg a **alkalmazások** nézetben nyissa meg a könyvtár nézetet, és válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az a **alkalmazások** listáról válassza ki **Atlassian felhő**.

    ![Az alkalmazások listáját a Atlassian felhő hivatkozás](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** ablakban válassza ki **válasszon**.

7. Az a **hozzáadása hozzárendelés** ablakban válassza ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha bejelöli a **Atlassian felhő** csempére a hozzáférési panelen, be kell jelentkeznie automatikusan Atlassian felhő Alkalmazásmódosítások.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
