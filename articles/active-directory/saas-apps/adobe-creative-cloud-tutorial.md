---
title: 'Oktatóanyag: Azure Active Directory-integráció az Adobe Creative Cloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Adobe Creative Cloud és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2018
ms.author: jeedes
ms.openlocfilehash: e1788de7c2372797b2034eb1753ab435c1299889
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548277"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Azure Active Directory-integráció az Adobe Creative Cloud szolgáltatásban

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az Adobe Creative Cloud szolgáltatásban az Azure Active Directoryval (Azure AD).

Az Adobe Creative Cloud szolgáltatásban integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáféréssel rendelkezik az Adobe Creative cloud szolgáltatásban az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Adobe Creative cloud szolgáltatásban (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Creative Cloud, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az Adobe Creative Cloud egyszeri bejelentkezés engedélyezve van az előfizetés
- Egy szükséges az Adobe Creative Cloud vállalati verzió

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Adobe Creative Cloud szolgáltatásban hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Az Adobe Creative Cloud szolgáltatásban hozzáadása a katalógusból

Az Adobe Creative Cloud szolgáltatásban integrálása az Azure AD beállítása, hozzá kell az Adobe Creative Cloud szolgáltatásban a galériából a felügyelt SaaS-alkalmazások listájára.

**Az Adobe Creative Cloud szolgáltatásban hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Adobe Creative Cloud**, jelölje be **Adobe Creative Cloud** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Adobe Creative Cloud szolgáltatásban a találatok listájában](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud szolgáltatásban a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó az Adobe Creative Cloud mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és az Adobe Creative Cloud szolgáltatásban a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Adobe Creative Cloud tesztfelhasználót](#create-an-adobe-creative-cloud-test-user)**  - a-megfelelője a Britta Simon szerepel az Adobe Creative Cloud szolgáltatásban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Adobe Creative Cloud alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Adobe Creative Cloud** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Az a **az Adobe Creative Cloud tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![Az Adobe Creative Cloud tartomány és URL-címek egyszeri bejelentkezési adatait](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<token>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [az Adobe Creative Cloud vállalati](https://www.adobe.com/au/creativecloud/business/teams/plans.html) beolvasni ezeket az értékeket.

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az Adobe Creative Cloud tartomány és URL-címek egyszeri bejelentkezési adatait](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket, mint: `https://adobe.com`

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Az Adobe Creative Cloudba feltöltött alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútum** az alkalmazás lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    | ---------------| ----------------|
    | FirstName |User.givenName |
    | LastName |User.surname |
    | E-mail |user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Kattintson az **OK** gombra.

    > [!NOTE]
    > Felhasználók kell licenccel kell rendelkeznie egy érvényes Office 365 ExO e-mailek jogcím értéke a SAML-válasz kell feltöltenie.

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. Az a **az Adobe Creative Cloud-konfiguráció** területén kattintson **konfigurálása az Adobe Creative Cloud szolgáltatásban** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban**.

    ![Adobe Creative Cloud Configuration](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. Egy másik böngészőablakban, jelentkezzen be [Adobe felügyeleti konzol](https://adminconsole.adobe.com) rendszergazdaként.

11. Lépjen a **beállítások** a felső navigációs sávot, és válassza a **identitás**. Megnyílik a tartományok listáját. Kattintson a **konfigurálása** hivatkozásra a tartomány alapján. Hajtsa végre az alábbi lépéseket a **egyszeri bejelentkezést a konfiguráció szükséges** szakaszban. További információkért lásd: [tartomány beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Beállítások](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "beállításai")

    a. Kattintson a **Tallózás** feltöltése az Azure AD-ből a letöltött tanúsítvány **Identitásszolgáltató tanúsítvány**.

    b. Az a **Identitásszolgáltató kibocsátója** szövegmezőbe put értékét **SAML entitásazonosító** a másolt **bejelentkezés konfigurálása** szakaszban az Azure Portalon.

    c. Az a **Identitásszolgáltató bejelentkezési URL-cím** szövegmezőbe put értékét **SAML egyszeri bejelentkezési szolgáltatás URL-címe** a másolt **bejelentkezés konfigurálása** szakaszban az Azure Portalon.

    d. Válassza ki **HTTP - átirányítási** , **Identitásszolgáltató kötés**.

    e. Válassza ki **E-mail-cím** , **felhasználói bejelentkezési beállítás**.

    f. Kattintson a **mentése** gombra.

12. Az irányítópulton, most már megjelenik az XML-fájl **"Metaadatok letöltése"** fájlt. Adobe EntityDescriptor és AssertionConsumerService URL-címe tartalmazza. Nyissa meg a fájlt, és konfigurálja őket az Azure AD-alkalmazás.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja az Adobe a megadott EntityDescriptor értéket **azonosító** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

    b. Használja az Adobe a megadott AssertionConsumerService értéket **válasz URL-cím** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Az Adobe Creative Cloud tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be Adobe Creative Cloud az Azure AD-felhasználók, akkor ki kell építeni az Adobe Creative Cloud szolgáltatásban. Az Adobe Creative Cloud, esetén kézi tevékenység kiépítése.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a [Adobe felügyeleti konzol](https://adminconsole.adobe.com) hely rendszergazdaként.

2. Összevont ID Adobe konzolon belül a felhasználó hozzáadása, és hozzárendelheti őket egy termék profilt. Felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [-felhasználók hozzáadása az Adobe felügyeleti konzol](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Ezen a ponton az Adobe bejelentkezési űrlapot, nyomja le a tab, írjon be az e-mail cím egyszerű és kell összevonva az Azure AD vissza:
    * A webes elérés: www.adobe.com > jelentkezzen be
    * Az asztali alkalmazás segédprogram belül > jelentkezzen be
    * Az alkalmazáson belül > Súgó > jelentkezzen be

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával az Adobe Creative cloud szolgáltatásban.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel az Adobe Creative Cloud, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Adobe Creative Cloud**.

    ![Az alkalmazások listáját az Adobe Creative Cloud hivatkozásra](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Adobe Creative Cloud csempére kattint, meg kell lekérése automatikusan bejelentkezett az Adobe Creative Cloud-alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Tartomány (Adobe.com webhelyre) beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Az Adobe egyszeri bejelentkezés (Adobe.com webhelyre) való használatra Azure konfigurálása](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/adobe-creative-cloud-tutorial/tutorial_general_203.png
