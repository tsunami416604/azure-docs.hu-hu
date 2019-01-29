---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP Felhőplatform Identitáshitelesítésével között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: ea8b6506857a17a2095bfdee4041fd62b7a4b232
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187742"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Felhőplatform Identitáshitelesítésével integrálása az Azure Active Directory (Azure AD). SAP Felhőplatform Identitáshitelesítésével IdP-proxyként szolgál, amelyek használják az Azure ad-ben a fő Identitásszolgáltatóként SAP-alkalmazások eléréséhez.

SAP Felhőplatform Identitáshitelesítésével integrálása az Azure ad-ben, kérje le a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az SAP-alkalmazások Azure AD-ben.
- Engedélyezheti a felhasználók automatikusan bejelentkezhetnek az SAP-alkalmazásait az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért tekintse meg a cikket [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az SAP Felhőplatform Identitáshitelesítésével konfigurálásához lesz szüksége a következő elemek:

- Az Azure AD-előfizetés.
- Egyszeri bejelentkezés-a-kompatibilis előfizetést SAP Felhőplatform Identitáshitelesítésével.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure AD-próba környezetet [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. SAP Felhőplatform Identitáshitelesítésével hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Mielőtt alaposabban elmerülne a technikai részleteket, létfontosságú a szereplőknek tekintse meg fog a fogalmak megértéséhez. Az SAP Felhőplatform Identitáshitelesítésével és az Active Directory összevonási szolgáltatások lehetővé teszi az egyszeri bejelentkezés megvalósítása a különböző alkalmazások vagy szolgáltatások, az SAP-alkalmazások (mint az IdP) az Azure AD által védett és védett SAP Cloud services Felhőplatform Identitáshitelesítésével.

SAP Felhőplatform Identitáshitelesítésével jelenleg egy SAP-alkalmazások Proxy identitásszolgáltató funkcionál. Az Azure Active Directory ezután ezt a beállítást a vezető identitásszolgáltató funkcionál. 

A következő ábra szemlélteti ezt a kapcsolatot:

![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ezzel a beállítással az SAP Felhőplatform Identitáshitelesítésével bérlő az Azure Active Directory megbízható alkalmazás van konfigurálva. 

Minden SAP-alkalmazások és szolgáltatások, amelyek így védeni kívánt ezt követően konfigurálni a SAP Felhőplatform Identitáshitelesítésével felügyeleti konzolon.

Ezért az engedélyezési el az SAP-alkalmazások és szolgáltatások kerüljön sor az SAP Felhőplatform Identitáshitelesítésével (szemben az Azure Active Directory) szükséges.

Konfigurálja az SAP Felhőplatform Identitáshitelesítésével alkalmazást az Azure Active Directory Marketplace-en keresztül, nem kell egyéni igények vagy SAML helyességi feltételek konfigurálása.

>[!NOTE] 
>Mindkét fél jelenleg csak a webes egyszeri bejelentkezés tesztelve lett. A flow alkalmazás API- vagy API-API-kommunikáció szükséges működik, de nem tesztelte. Egymást követő tevékenységek során teszthellyel.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Felhőplatform Identitáshitelesítésével hozzáadása a katalógusból
Az integráció az SAP Felhőplatform Identitáshitelesítésével konfigurálása az Azure ad-ben, hozzá kell SAP Felhőplatform Identitáshitelesítésével a galériából a felügyelt SaaS-alkalmazások listájára.

**SAP Felhőplatform Identitáshitelesítésével hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új az új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SAP Felhőplatform Identitáshitelesítésével**. 

1. Válassza ki **SAP Felhőplatform Identitáshitelesítésével** az eredmények panelen, és válassza ki a **Hozzáadás** gombra.

    ![SAP Felhőplatform Identitáshitelesítésével a találatok listájában](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Felhőplatform Identitáshitelesítésével. Konfigurálja, és tesztelje azt az úgynevezett "Britta Simon." tesztfelhasználó

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a SAP Felhőplatform Identitáshitelesítésével tartozó felhasználó, akik. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat a Felhőplatform Identitáshitelesítésével SAP.

SAP Felhőplatform Identitáshitelesítésével, adjon az érték **felhasználónév** azonos értéket **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között.

Az Azure AD egyszeri bejelentkezés az SAP Felhőplatform Identitáshitelesítésével tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. [Hozzon létre egy SAP Felhőplatform Identitáshitelesítésével tesztfelhasználót](#create-an-sap-cloud-platform-identity-authentication-test-user) van egy megfelelője a Britta Simon SAP Felhőplatform Identitáshitelesítésével, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezést az SAP Felhőplatform Identitáshitelesítésével alkalmazásban konfigurálja.

**Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Felhőplatform Identitáshitelesítésével, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SAP Felhőplatform Identitáshitelesítésével** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanel **SAML-alapú bejelentkezés**, jelölje be **mód** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

1. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, a **SAP Cloud platformon identitás hitelesítési tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:  

    ![Az SAP Cloud platformon identitás hitelesítési tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. Az a **azonosító** mezőbe írja be egy URL-CÍMÉT a következő mintának: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Forduljon a [SAP Cloud platformon identitás hitelesítési ügyfél-támogatási csapatának](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) beolvasni ezeket az értékeket. Ha nem ismeri a azonosító értékét, olvassa el az SAP Felhőplatform Identitáshitelesítésével dokumentáció [bérlői SAML 2.0 konfigurációs](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

1. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett üzemmódnál válassza **speciális URL-beállítások megjelenítése**.

    ![Az SAP Cloud platformon identitás hitelesítési tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    Az a **bejelentkezési URL-** mezőbe írja be egy URL-címet a következő mintával: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Az adott üzleti alkalmazás bejelentkezési URL-CÍMÉT használja. Forduljon a [SAP Cloud platformon identitás hitelesítési ügyfél-támogatási csapatának](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Ha kétségei vannak.

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **metaadatainak XML**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

1. SAP-Felhőplatform Identitáshitelesítésével alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Ezek az attribútumok értékeinek kezelése a **felhasználói attribútumok** szakaszban az alkalmazás integrációs oldalán található. A következő képernyőképen látható egy példa a formátumra. 

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

1. Ha az SAP-alkalmazás vár egy attribútum, például **firstName**, adja hozzá a **firstName** az attribútum a **felhasználói attribútumok** szakaszban. Ez a beállítás érhető el a **egyszeri bejelentkezési** párbeszédpanelén a **SAML-jogkivonat attribútumai** párbeszédpanel megnyitásához...

    a. Megnyitásához a **attribútum hozzáadása** párbeszédpanelen jelölje ki **attribútum hozzáadása**. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be az attribútumnév **firstName**.
    
    c. Az a **érték** listájához, válassza ki az attribútum értéke **user.givenname**.
    
    d. Kattintson az **OK** gombra.

1. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés konfigurálása Mentés gombra](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

1. Az a **SAP Cloud Platform identitás hitelesítési konfiguráció** szakaszban jelölje be **konfigurálja az SAP Felhőplatform Identitáshitelesítésével** megnyitásához a **bejelentkezéskonfigurálása**ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Az SAP Cloud Platform identitás hitelesítési konfiguráció](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált keresse fel az SAP Felhőplatform Identitáshitelesítésével felügyeleti konzoljára. Az URL-címnek a következő mintának: `https://<tenant-id>.accounts.ondemand.com/admin`. Ezután olvassa el a dokumentációjában az SAP Felhőplatform Identitáshitelesítésével, [a Microsoft Azure AD-integráció](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

1. Az Azure Portalon válassza ki a **mentése** gombra.

1. Folytassa a következő csak akkor, ha azt szeretné, adja hozzá, és engedélyezze az egyszeri Bejelentkezést az SAP egy másik alkalmazás. Ismételje meg a szakasz alatt **hozzáadása SAP Felhőplatform Identitáshitelesítésével a katalógusból**.

1. Az Azure Portalon az a **SAP Felhőplatform Identitáshitelesítésével** alkalmazás integráció lapon jelölje be **csatolt bejelentkezés**.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

1. Mentse a konfigurációt.

>[!NOTE] 
>Az új alkalmazás használja az előző SAP alkalmazás egyszeri bejelentkezési konfiguráció. Ellenőrizze, hogy használja az ugyanazon vállalati identitás-szolgáltatóktól az SAP Felhőplatform Identitáshitelesítésével felügyeleti konzolon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez dokumentáció a **konfigurációs** alul található szakaszában. További tudnivalók a beágyazott dokumentáció funkció [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Egy SAP Felhőplatform Identitáshitelesítésével tesztfelhasználó létrehozása

Hozzon létre egy felhasználót az SAP Felhőplatform Identitáshitelesítésével nincs szükségünk. Az Azure ad-ben felhasználókhoz tartozó tárolóban lévő felhasználók számára az egyszeri bejelentkezési funkciókat használhat.

SAP Felhőplatform Identitáshitelesítésével támogatja az identitás-összevonási lehetőséget. Ez a beállítás lehetővé teszi, hogy az alkalmazást, ellenőrizze, hogy a vállalati identitás szolgáltató által hitelesített felhasználók létezik-e az a felhasználó store, az SAP Felhőplatform Identitáshitelesítésével. 

Az identitás-összevonási beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonási engedélyezve van, csak az SAP Felhőplatform Identitáshitelesítésével az importált felhasználók is elérhetik az alkalmazást. 

Engedélyezheti vagy tilthatja le az identitás-összevonás az SAP Felhőplatform Identitáshitelesítésével kapcsolatos további információkért lásd a "Engedélyezése identitás összevonási az SAP Felhőplatform Identitáshitelesítésével" [identitás-összevonás konfigurálása az a Felhasználói Store az SAP-Felhőplatform Identitáshitelesítésével](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP Felhőplatform Identitáshitelesítésével Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SAP Felhőplatform Identitáshitelesítésével, tegye a következőket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és keresse meg a könyvtár nézetben. Folytassa **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAP Felhőplatform Identitáshitelesítésével**.

    ![Az alkalmazások listáját az SAP Felhőplatform Identitáshitelesítésével hivatkozásra](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombra a **felhasználók és csoportok** párbeszédpanel bezárásához.

1. Válassza ki a **hozzárendelése** gombra a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az SAP Felhőplatform Identitáshitelesítésével csempe kiválasztásakor a hozzáférési panelen, első automatikusan megtörténik az SAP Felhőplatform Identitáshitelesítésével alkalmazásba.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
