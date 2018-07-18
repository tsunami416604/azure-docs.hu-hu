---
title: 'Oktatóanyag: Azure Active Directory-integráció az Atlassian Cloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Atlassian felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: jeedes
ms.openlocfilehash: 68613b8613a2e5a9139b83eb23e66884659efc47
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114934"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Oktatóanyag: Azure Active Directory-integráció az Atlassian felhő

Ebben az oktatóanyagban elsajátíthatja, hogyan Atlassian Felhőbeli integrálása az Azure Active Directory (Azure AD).

Atlassian Felhőbeli integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Az Azure ad-ben Atlassian felhőbe való hozzáféréssel rendelkező szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan bejelentkeznie (egyszeri bejelentkezés) Atlassian felhőbe, az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Atlassian felhőalapú Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetés.
- Engedélyezi a Security Assertion Markup Language (SAML) egyszeri bejelentkezést az Atlassian felhőalapú termékek, szüksége Atlassian hozzáférés beállítása. Tudjon meg többet [Atlassian hozzáférés]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy nem használja éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az oktatóanyagban ismertetett forgatókönyv két fő építőelemeket áll:

* Atlassian felhő hozzáadása a katalógusból
* Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-atlassian-cloud-from-the-gallery"></a>Atlassian felhő hozzáadása a katalógusból
A rendszerrel történő integráció konfigurálása a Atlassian felhőalapú Azure ad-ben, hozzá Atlassian felhőalapú a katalógusból a kezelt SaaS-alkalmazások listáját az alábbiak szerint:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az "új alkalmazás" gombra][3]

4. A Keresés mezőbe írja be a **Atlassian felhőalapú**, az eredmények listájában válassza **Atlassian felhőalapú**, majd válassza ki **Hozzáadás**.

    ![Az eredmények listájában Atlassian felhő](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Atlassian felhővel *Britta Simon*.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell állapítania az Atlassian Felhőbeli felhasználó- és a megfelelő Azure AD-ben. Más szóval, kapcsolatot kell létesítenie egy hivatkozás egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti Atlassian felhőben.

A hivatkozás kapcsolat létrehozására, rendelje hozzá a Atlassian felhőként *felhasználónév* ugyanazt az értéket az Azure ad hozzárendelt *felhasználónév*.

Konfigurálása és az Azure AD egyszeri bejelentkezés Atlassian felhővel való teszteléséhez szüksége hajtsa végre az alábbi szakaszokban található építőelemeket.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Atlassian felhőalapú alkalmazás egyszeri bejelentkezés konfigurálása.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Atlassian felhő, tegye a következőket:

1. Az Azure Portalon az a **Atlassian felhőalapú** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** ablakban, a a **egyszeri bejelentkezési mód** jelölje ki **SAML-alapú bejelentkezés**.

    ![Egyszeri bejelentkezési ablak](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Az alkalmazás konfigurálása **Identitásszolgáltató által kezdeményezett** mód alatt **Atlassian felhőalapú tartomány és URL-címek**, tegye a következőket:

    ![Atlassian felhőalapú tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Az a **azonosító** mezőbe írja be egy URL-címet a következő mintával: `https://auth.atlassian.com/saml/<unique ID>`.
    
    b. Az a **válasz URL-cím** mezőbe írja be egy URL-címet a következő mintával: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`.

    c. Ellenőrizze **speciális URL-beállítások megjelenítése**.

    d. Az a **továbbítási állapot** mezőbe írja be egy URL-címet a következő mintával: `https://<instancename>.atlassian.net`.

    > [!NOTE]
    > Az előző értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Valódi ezeket az értékeket az oktatóanyag későbbi részében ismertetett Atlassian felhőalapú SAML-konfigurációja képernyőről fog kapni.

4. Az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban, jelölje be a **speciális URL-beállítások megjelenítése** , majd a **bejelentkezési URL-cím** mezőbe írja be egy URL-címet a következő mintával: `https://<instancename>.atlassian.net`.

    ![Atlassian felhőalapú tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > A fenti bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Atlassian felhőalapú ügyfél-támogatási csapatának](https://support.atlassian.com/) lekérni ezt az értéket.

5. Alatt **SAML-aláíró tanúsítvány**válassza **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Atlassian felhőalapú alkalmazás vár egy megadott formátumban, amelyhez egyéni attribútum-leképezéshez hozzá a SAML-jogkivonat attribútumai a SAML helyességi feltételek kereséséhez. 

    Alapértelmezés szerint a **felhasználóazonosító** érték user.userprincipalname van leképezve. Ez az érték user.mail leképezése módosítható. Azt is beállíthatja bármely más megfelelő érték a szervezet telepítő megfelelően, de a legtöbb esetben működnie kell az e-mailt.

    ![A tanúsítvány letöltési hivatkozás](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Kattintson a **Mentés** gombra.

    ![A konfigurálás egyszeri Mentés gomb](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Megnyitásához a **bejelentkezés konfigurálása** ablakban, a a **Atlassian Felhőkonfiguráció** szakaszban jelölje be **Atlassian felhő konfigurálása**.

9. Az a **rövid összefoglaló** területén másolja a **SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím**.

    ![Atlassian felhőkonfiguráció](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Egyszeri bejelentkezés az alkalmazáshoz konfigurált kapni, jelentkezzen be rendszergazdai hitelesítő adatait az Atlassian-portálra.

11. Ellenőrizze a tartományt egyszeri bejelentkezés konfigurálása előtt kell. További információkért lásd: [Atlassian tartomány-ellenőrzés](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentumot.

12. A bal oldali panelen válassza ki a **SAML egyszeri bejelentkezés**. Ha ezt még nem tette meg, az előfizetés Atlassian Identity Manager.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. Az a **hozzáadása SAML-konfigurációja** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Az a **identitásszolgáltató Entitásazonosító** mezőbe illessze be az SAML-entitás azonosítója az Azure Portalról másolt.

    b. Az a **identitásszolgáltató egyszeri bejelentkezési URL-cím** mezőbe illessze be a SAML egyszeri bejelentkezési szolgáltatás URL-cím, az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítvány egy .txt fájlban, az Azure portálról másolja az értéket (nélkül a *megkezdéséhez tanúsítvány* és *End Certificate* sorok), majd illessze be a a **nyilvános X509 tanúsítvány** mezőbe.
    
    d. Válassza ki **konfiguráció mentése**.
     
14. Győződjön meg arról, hogy meg van adva a megfelelő URL-címeket, frissítse az Azure AD-beállításokat az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. A SAML ablakban másolja a **SP Szolgáltatásidentitás azonosítója** , majd az Azure portal alatt Atlassian felhőalapú **tartomány és URL-címek**, illessze be a **azonosító** mezőbe.
    
    b. Az SAML ablakban másolja a **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** , majd az Azure portal alatt Atlassian felhőalapú **tartomány és URL-címek**, illessze be a **válasz URL-cím** mezőbe. A bejelentkezési URL-bérlői URL-címét az Atlassian Felhőbeli.

    > [!NOTE]
    > Hogy egy meglévő ügyfél frissítése után a **SP Szolgáltatásidentitás azonosítója** és **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** értékeket az Azure Portalon válassza ki **Igen, frissítse a konfigurációt**. Ha Ön új ügyfél, kihagyhatja ezt a lépést.
    
15. Az Azure Portalon válassza ki a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez válassza **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. Az a **minden felhasználó** ablakban válassza **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-an-atlassian-cloud-test-user"></a>Hozzon létre egy Atlassian felhőalapú tesztfelhasználót

Ahhoz, hogy jelentkezzen be a Atlassian felhőalapú Azure AD-felhasználók, a felhasználói fiókok manuális Atlassian felhőben kiosztani az alábbiak szerint:

1. Az a **felügyeleti** ablaktáblán válassza előbb **felhasználók**.

    ![A Felhőbeli felhasználók Atlassian-hivatkozás](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Egy felhasználó Atlassian felhőben létrehozásához válassza **meghívó felhasználói**.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Az a **E-mail-cím** mezőbe írja be a felhasználó e-mail címét, és rendelje hozzá az alkalmazás-hozzáférést.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Szeretne küldeni a felhasználónak e-mailes meghívót, válassza ki a **felhasználók meghívása**. E-mailt küld a felhasználónak, és elfogadja a meghívást, miután a felhasználó nem aktív, a rendszer.

>[!NOTE]
>Tömegesen is-felhasználók létrehozása kiválasztásával a **tömeges létrehozásához** gombra a **felhasználók** szakaszban.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon a hozzáférés biztosításával Atlassian felhőbe Azure egyszeri bejelentkezés használatára. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200]

1. Az Azure Portalon nyissa meg a **alkalmazások** megtekintése, nyissa meg a könyvtár nézet, és válassza **vállalati alkalmazások** > **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az a **alkalmazások** listáról válassza ki **Atlassian felhőalapú**.

    ![Az alkalmazások listáját az Atlassian felhőalapú hivatkozásra](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** ablakban válassza **kiválasztása**.

7. Az a **hozzárendelés hozzáadása** ablakban válassza **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Amikor kiválasztja a **Atlassian felhőalapú** csempéje a hozzáférési panelen, be kell jelentkeznie automatikusan az Atlassian felhőalapú alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
