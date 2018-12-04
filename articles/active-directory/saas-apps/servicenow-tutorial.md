---
title: 'Oktatóanyag: Azure Active Directory-integráció az ServiceNow |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a ServiceNow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 5b7baffea8e718810a91ea9687a007d36c806aab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850006"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Oktatóanyag: Azure Active Directory-integráció a servicenow segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan ServiceNow integrálása az Azure Active Directory (Azure AD).

A ServiceNow integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a ServiceNow Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a servicenow-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a servicenow segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- ServiceNow, egy példány vagy a bérlő ServiceNow, Calgary verzió vagy újabb
- ServiceNow expressz, egy példányát a ServiceNow kifejezett, Helsinki verzió vagy újabb
- A ServiceNow-bérlőnek kell rendelkeznie a [több szolgáltató egyszeri bejelentkezést a beépülő modul](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) engedélyezve van. Ezt teheti [szolgáltatási kérelem elküldése](https://hi.service-now.com).
- Az automatikus konfiguráláshoz a többszolgáltatós beépülő modul engedélyezése servicenow-hoz készült.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A ServiceNow hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-servicenow-from-the-gallery"></a>A ServiceNow hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a ServiceNow, hozzá kell ServiceNow a galériából a felügyelt SaaS-alkalmazások listájára.

**A ServiceNow hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **ServiceNow**válassza **ServiceNow** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![ServiceNow a találatok listájában](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ServiceNow, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a ServiceNow tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a ServiceNow hivatkozás kapcsolatának kell létrehozni.

ServiceNow, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a servicenow segítségével tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Servicenow-hoz készült Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on-for-servicenow)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure AD egyszeri bejelentkezés konfigurálása, a ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Hozzon létre egy ServiceNow tesztfelhasználót](#create-a-servicenow-test-user)**  – egy megfelelője a Britta Simon van a ServiceNow, amely kapcsolódik az Azure AD felhasználói ábrázolása.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Servicenow-hoz készült Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a ServiceNow alkalmazás egyszeri bejelentkezés konfigurálása.

**A servicenow segítségével konfigurálja az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ServiceNow** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_300.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_301.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_302.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ServiceNow-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket a tényleges bejelentkezési URL- és az oktatóanyag későbbi részében ismertetett azonosító frissíteni kell.

6. Az a **SAML-aláíró tanúsítvány** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A tanúsítvány letöltési hivatkozás](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe, mivel az alkalmazás összevonási metaadatainak URL-címet az oktatóanyag későbbi részében fogja használni.

    b. Kattintson a **letöltése** letöltéséhez **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

7. Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

8. Aktiválja a **integráció – több szolgáltató egyszeri bejelentkezéses telepítő** beépülő modul a következő lépéseket követve:

    a. A bal oldali navigációs ablaktábláján keressen **rendszer definíció** szakasz a keresősávba, és kattintson a **beépülő modulok**.

    ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_03.png "beépülő modul aktiválása")

     b. Keresse meg **integráció – több szolgáltató egyszeri bejelentkezéses telepítő**.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_04.png "beépülő modul aktiválása")

    c. Válassza ki a beépülő modult. Kattintson a jobb gombbal, és válassza ki **aktiválás/frissítés**.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate.png "beépülő modul aktiválása")

    d. Kattintson a **aktiválás** gombra.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate1.png "beépülő modul aktiválása")

9. A bal oldali navigációs ablaktábláján keressen **Többszolgáltatós SSO** szakasz a keresősávba, és kattintson a **tulajdonságok**.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_06.png "alkalmazás URL-CÍMEK konfigurálása")

10. Az a **több szolgáltató egyszeri bejelentkezési tulajdonságok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694981.png "alkalmazás URL-CÍMEK konfigurálása")

    * Mint **több szolgáltató SSO engedélyezése**válassza **Igen**.
  
    * Mint **automatikus importálás engedélyezése a felhasználók minden, a felhasználó táblába identitás-szolgáltatóktól származó**válassza **Igen**.

    * Mint **engedélyezése hibakeresési naplózás a több szolgáltató SSO integrációs**válassza **Igen**.

    * A **a mezőt a felhasználó táblával, amely...**  szövegmezőbe írja be **felhasználónév**.
  
    * Kattintson a **Save** (Mentés) gombra.

11. Kétféleképpen, amelyben **ServiceNow** konfigurálható – automatikus és kézi.

12. Konfigurálásához **ServiceNow** automatikusan, kövesse az alábbi lépéseket:

    * Lépjen vissza a **ServiceNow** egyszeri bejelentkezés az Azure Portalon az oldalon.

    * Egyetlen kattintással szolgáltatás konfigurálása kerül a servicenow-hoz készült, hogy, az Azure AD automatikus konfigurálása a ServiceNow SAML-alapú hitelesítéshez. Ahhoz, hogy ez a szolgáltatás Ugrás **ServiceNow konfigurációs** területén kattintson **konfigurálása ServiceNow** konfigurálása bejelentkezési ablak megnyitásához.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Adja meg a ServiceNow-példány neve, a rendszergazdai felhasználónév és a rendszergazdai jelszó a **bejelentkezés konfigurálása** alkotnak, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a megadott rendszergazdai felhasználónév kell rendelkeznie a **security_admin** ServiceNow ennek működéséhez a hozzárendelt szerepkörrel. Ellenkező esetben kézzel adja meg ServiceNow használata az Azure AD SAML identitás-szolgáltatóként, kattintson **egyszeri bejelentkezés manuális konfigurálása** , és másolja a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** rövid összefoglaló szakaszából.

        ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

    * Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

    * Az automatikus beállítás az összes szükséges beállításainak konfigurálása a **ServiceNow** ügyféloldali, de a **X.509-tanúsítvány** alapértelmezés szerint nincs engedélyezve. Manuálisan feleltesse meg az identitásszolgáltató a ServiceNow kell. kövesse az alábbi lépéseket ugyanazon:

    * A bal oldali navigációs ablaktábláján keressen **Többszolgáltatós SSO** szakasz a keresősávba, és kattintson a **Identitásszolgáltatók**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a az automatikusan létrehozott identitásszolgáltató

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_08.png "egyszeri bejelentkezés konfigurálása")

    *  Az a **identitásszolgáltató** szakaszban, hajtsa végre az alábbi lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/automatic_config.png "egyszeri bejelentkezés konfigurálása")

        * Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például **a Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **identitásszolgáltató SingleLogoutRequest** a szövegmező értékét.

        * Másolás **ServiceNow kezdőlap** értékét, illessze be a **bejelentkezési URL-** szövegmezőjébe **ServiceNow tartomány és URL-címek** szakaszban az Azure Portalon.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapjára összefűzésével a **ServieNow bérlői URL-cím** és **/navpage.do** (például:`https://fabrikam.service-now.com/navpage.do`).

        * Másolás **Entitásazonosító / kibocsátó** értékét, illessze be a **azonosító** szövegmezőjébe **ServiceNow tartomány és URL-címek** szakaszban az Azure Portalon.

        * Győződjön meg arról, hogy **NameID házirend** értékre van állítva `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéket. 

    * Görgessen le a **X.509-tanúsítvány** szakaszban jelölje be **szerkesztése**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_09.png "egyszeri bejelentkezés konfigurálása")

    * Válassza ki a tanúsítványban, és a jobbra mutató nyíl ikonra kattintva vegye fel a tanúsítványt

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_11.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a **Save** (Mentés) gombra.

    * Kattintson a **kapcsolat tesztelése** az oldal jobb felső sarkában található.

        ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate2.png "beépülő modul aktiválása")

    * Kattintás után a a **kapcsolat tesztelése**, kap a előugró ablak, ahol meg kell adnia hitelesítő adatait, és az alább eredményeket tartalmazó lap jelenik meg. A **Egyszeri kijelentkezési teszteredmények** várt hiba figyelmen kívül hagyhatja a hibát, és kattintson a **aktiválás** gombra.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "egyszeri bejelentkezés konfigurálása")
  
13. Konfigurálásához **ServiceNow** manuálisan, kövesse az alábbi lépéseket:

    * Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

    * A bal oldali navigációs panelén kattintson **Identitásszolgáltatók**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identitásszolgáltatók** párbeszédpanelen kattintson a **új**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694977.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identitásszolgáltatók** párbeszédpanelen kattintson a **SAML**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694978.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identity Provider metaadatok importálása** előugró ablakban hajtsa végre az alábbi lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/idp.png "egyszeri bejelentkezés konfigurálása")

        * Adja meg a **alkalmazás összevonási metaadatainak URL-címe** Azure Portalról másolt.

        * Kattintson az **Importálás** gombra.

    * Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982.png "egyszeri bejelentkezés konfigurálása")

        * Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például **a Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **identitásszolgáltató SingleLogoutRequest** a szövegmező értékét.

        * Másolás **ServiceNow kezdőlap** értékét, illessze be a **bejelentkezési URL-** szövegmezőjébe **ServiceNow tartomány és URL-címek** szakaszban az Azure Portalon.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapjára összefűzésével a **ServiceNow-bérlői URL-cím** és **/navpage.do** (például:`https://fabrikam.service-now.com/navpage.do`).

        * Másolás **Entitásazonosító / kibocsátó** értékét, illessze be a **azonosító** szövegmezőjébe **ServiceNow tartomány és URL-címek** szakaszban az Azure Portalon.

        * Győződjön meg arról, hogy **NameID házirend** értékre van állítva `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéket.

        * Kattintson a **speciális**. Az a **felhasználói mező** szövegmezőbe írja be **e-mail** vagy **felhasználónév**, attól függően, hogy melyik mezőt a ServiceNow-telepítésben a felhasználók egyedi azonosítására szolgál.

            > [!NOTE]
            > Konfigurálhatja az Azure AD vagy az Azure AD-felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail-cím egyedi azonosítóként a SAML-jogkivonat kibocsátható nyissa meg a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakaszában az Azure Portalon leképezés a kívánt mezőt, és a **nameidentifier** attribútum. A kiválasztott attribútumnak (Ha például egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

        * Kattintson a **kapcsolat tesztelése** az oldal jobb felső sarkában található.

        * Kattintás után a a **kapcsolat tesztelése**, kap a előugró ablak, ahol meg kell adnia hitelesítő adatait, és az alább eredményeket tartalmazó lap jelenik meg. A **Egyszeri kijelentkezési teszteredmények** várt hiba figyelmen kívül hagyhatja a hibát, és kattintson a **aktiválás** gombra.

          ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "egyszeri bejelentkezés konfigurálása")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>A ServiceNow expressz az Azure AD egyszeri bejelentkezés konfigurálása

1. Az Azure Portalon az a **ServiceNow** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_300.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_301.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_302.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Kapcsolattartó [ServiceNow ügyfél-támogatási csapatának](https://www.servicenow.com/support/contact-support.html) beolvasni ezeket az értékeket.

6. Az a **SAML-aláíró tanúsítvány** szakaszt, kattintson a **letöltése** letöltéséhez **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

7. Egyetlen kattintással szolgáltatás konfigurálása kerül a servicenow-hoz készült, hogy, az Azure AD automatikus konfigurálása a ServiceNow SAML-alapú hitelesítéshez. Ahhoz, hogy ez a szolgáltatás Ugrás **állítsa be a ServiceNow** területén kattintson **részletes útmutató megtekintéséhez** konfigurálása bejelentkezési ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

8. Adja meg a ServiceNow-példány neve, a rendszergazdai felhasználónév és a rendszergazdai jelszó a **bejelentkezés konfigurálása** alkotnak, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a megadott rendszergazdai felhasználónév kell rendelkeznie a **security_admin** ServiceNow ennek működéséhez a hozzárendelt szerepkörrel. Ellenkező esetben kézzel adja meg ServiceNow használata az Azure AD SAML identitás-szolgáltatóként, kattintson **egyszeri bejelentkezés manuális konfigurálása** , és másolja a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** rövid összefoglaló szakaszából.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

9. Jelentkezzen be rendszergazdaként a ServiceNow Express-alkalmazás.

10. A bal oldali navigációs panelén kattintson **egyszeri bejelentkezés**.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694980ex.png "alkalmazás URL-CÍMEK konfigurálása")

11. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a jobb felső részén található konfigurációs ikonra, és állítsa be a következő tulajdonságokat:

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694981ex.png "alkalmazás URL-CÍMEK konfigurálása")

    a. Váltógomb **több szolgáltató SSO engedélyezése** jobbra.

    b. Váltógomb **engedélyezése hibakeresési naplózás a több szolgáltató SSO integrációs** jobbra.

    c. A **a mezőt a felhasználó táblával, amely...**  szövegmezőbe írja be **felhasználónév**.

12. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a **új tanúsítvány hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694973ex.png "egyszeri bejelentkezés konfigurálása")

13. Az a **X.509 tanúsítványok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694975.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza ki **aktív**.

    c. Mint **formátum**válassza **PEM**.

    d. Mint **típusa**válassza **Store tanúsítvány megbízható**.

    e. Nyissa meg a Jegyzettömbben az Azure portálról letöltött Base64-kódolású tanúsítvány, a tartalmát a vágólapra másolja és illessze be azt a **PEM tanúsítvány** szövegmezőbe.

    f. Kattintson a **Update**

14. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a **adja hozzá az új identitásszolgáltató**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694976ex.png "egyszeri bejelentkezés konfigurálása")

15. Az a **hozzáadása új identitásszolgáltató** párbeszédpanelen, a **identitásszolgáltató konfigurálása**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: **SAML 2.0**).

    b. Az a **identitásszolgáltató szolgáltató URL-címe** mezőbe illessze be az értéket a **identitás Szolgáltatóazonosító**, az Azure Portalról másolt.

    c. Az a **identitásszolgáltató AuthnRequest** mezőbe illessze be az értéket a **hitelesítési kérelem URL-Címének**, az Azure Portalról másolt.

    d. Az a **identitásszolgáltató SingleLogoutRequest** mezőbe illessze be az értéket a **egyszeri kijelentkezéses szolgáltatás URL-cím**, az Azure Portalról másolt

    e. Mint **szolgáltató Identitástanúsítványt**, válassza ki az előző lépésben létrehozott tanúsítványt.

16. Kattintson a **speciális beállítások**, majd a **további identitás szolgáltató tulajdonságai**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694983ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **protokoll kötést az Identitásszolgáltató SingleLogoutRequest** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:2.0:bindings:HTTP-átirányítási**.

    b. Az a **NameID házirend** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: nem meghatározott**.

    c. Az a **AuthnContextClassRef metódus**, típus `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Kapcsolja ki **hozzon létre egy AuthnContextClass**.

17. A **további szolgáltató tulajdonságai**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694984ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **ServiceNow kezdőlap** szövegmezőbe írja be a ServiceNow-példány kezdőlap URL-CÍMÉT.

    > [!NOTE]
    > A ServiceNow-példány kezdőlapjára összefűzésével a **ServiceNow-bérlői URL-cím** és **/navpage.do** (például: `https://fabrikam.service-now.com/navpage.do`).

    b. Az a **Entitásazonosító / kibocsátó** szövegmezőbe írja be a ServiceNow-bérlői URL-CÍMÉT.

    c. Az a **célközönség URI** szövegmezőbe írja be a ServiceNow-bérlői URL-CÍMÉT.

    d. A **óra tevékenységdiagramon** szövegmezőbe írja be **60**.

    e. Az a **felhasználói mező** szövegmezőbe írja be **e-mail** vagy **felhasználónév**, attól függően, hogy melyik mezőt a ServiceNow-telepítésben a felhasználók egyedi azonosítására szolgál.

    > [!NOTE]
    > Konfigurálhatja az Azure AD vagy az Azure AD-felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail-cím egyedi azonosítóként a SAML-jogkivonat kibocsátható nyissa meg a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakaszában az Azure Portalon leképezés a kívánt mezőt, és a **nameidentifier** attribútum. A kiválasztott attribútumnak (Ha például egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicenow-tutorial/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicenow-tutorial/create_aaduser_02.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-a-servicenow-test-user"></a>A ServiceNow tesztfelhasználó létrehozása

Ez a szakasz célja a ServiceNow Britta Simon nevű felhasználó létrehozásához. ServiceNow támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](servicenow-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolatba kell [ServiceNow ügyfél-támogatási csoport](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ServiceNow Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **ServiceNow**.

    ![A ServiceNow-hivatkozás alkalmazásainak listájában](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ServiceNow csempére kattint, akkor kell lekérése automatikusan bejelentkezett a ServiceNow alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](servicenow-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
