---
title: 'Oktatóanyag: Azure Active Directory-integráció SAP felhő Platform identitás hitelesítéssel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP felhőalapú Platform identitás hitelesítés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 388107e262f4780c6df307de51475b6bca3f5c2f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923374"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Azure Active Directory-integráció SAP felhő Platform identitás hitelesítéssel

Ebben az oktatóanyagban elsajátíthatja SAP felhőalapú Platform identitás hitelesítés integrálása az Azure Active Directory (Azure AD). SAP felhőalapú Platform identitás hitelesítés az Azure AD használata a fő IdP SAP-alkalmazásokból eléréséhez proxy IdP-ként használatos.

Integrálásakor SAP felhőalapú Platform identitás hitelesítés az Azure ad-vel, töltse le a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáfér az SAP-alkalmazásokból.
- Engedélyezheti a felhasználók automatikusan bejelentkezhet az alkalmazásoknak az Azure AD-fiókok SAP.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért tekintse meg a cikket [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SAP felhő Platform identitás hitelesítéssel, a következő elemek szükségesek:

- Az Azure AD-előfizetés.
- Egyszeri bejelentkezési a alkalmas előfizetést SAP felhőalapú Platform identitás hitelesítés.

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. SAP felhőalapú Platform identitás hitelesítés hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

A technikai részleteket alaposabban, mielőtt elengedhetetlen megérteni a fogalmakat, nézze meg fog. A SAP felhőalapú Platform identitás hitelesítés és az Active Directory összevonási szolgáltatások lehetővé teszik a SSO megvalósításához alkalmazások vagy szolgáltatások SAP alkalmazással (mint egy IdP) az Azure AD által védett és SAP felhő által védett szolgáltatásokhoz Platform identitás hitelesítés.

Jelenleg SAP felhőalapú Platform identitás hitelesítés úgy működik, mint a Proxy identitásszolgáltató SAP-alkalmazásokból. Az Azure Active Directory pedig úgy működik, mint a kezdő identitásszolgáltató az ehhez a telepítéshez. 

A következő ábra szemlélteti ezt a kapcsolatot:

![Az Azure AD tesztfelhasználó létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ez a beállítás az SAP felhőalapú Platform identitás hitelesítés bérlő az Azure Active Directory megbízható alkalmazás van konfigurálva. 

Az SAP felhőalapú Platform identitás hitelesítés felügyeleti konzolon SAP-alkalmazások és szolgáltatások, így védeni kívánt ezt követően konfigurálni.

Ezért a SAP alkalmazásokhoz és szolgáltatásokhoz való hozzáférés engedélyezése kerüljön sor SAP felhőalapú Platform identitás hitelesítés (szemben Azure Active Directory) szükséges.

Úgy konfigurálja az SAP felhőalapú Platform identitás hitelesítés az Azure Active Directory piactéren keresztül alkalmazásként, nem kell egyedi jogcímeket vagy SAML helyességi feltételek konfigurálása.

>[!NOTE] 
>Jelenleg csak a webes egyszeri bejelentkezés felek tesztelték. A szükséges alkalmazás-API vagy API-API kommunikációs adatfolyamok kell működnie, de nem tesztelte. További tevékenységek során vizsgálnak.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP felhő Platform identitás hitelesítés hozzáadása a gyűjteményből
Az SAP felhő Platform identitás hitelesítési-integráció konfigurálása az Azure ad-ben, szüksége SAP felhőalapú Platform identitás hitelesítés hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**SAP felhőalapú Platform identitás hitelesítés hozzáadása a gyűjteményből, tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs panelen, jelölje ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Hozzáadhat új az új alkalmazást, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP felhőalapú Platform identitás hitelesítés**. 

5. Válassza ki **SAP felhőalapú Platform identitás hitelesítés** az eredmények panelen, és válassza ki azt a **Hozzáadás** gombra.

    ![SAP felhőalapú Platform identitás hitelesítés az eredménylistában](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés SAP felhő Platform identitás hitelesítéssel. Konfigurálja és tesztelte "Britta Simon." nevű tesztfelhasználó

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a SAP felhőalapú Platform identitás hitelesítés megfelelőjére felhasználó, aki. Más szóval kell az SAP felhőalapú Platform identitás hitelesítés az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

SAP felhő Platform identitás hitelesítést, adjon a érték **felhasználónév** azonos érték, mint a **felhasználónév** az Azure ad-ben. Most hozott létre a hivatkozást a két felhasználó között.

Az Azure AD az egyszeri bejelentkezés SAP felhő Platform identitás hitelesítés tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy SAP felhőalapú Platform identitás hitelesítés tesztfelhasználó](#create-an-sap-cloud-platform-identity-authentication-test-user) való egy megfelelője a Britta Simon SAP felhőalapú Platform identitás hitelesítés, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az SAP felhőalapú Platform identitás hitelesítés alkalmazásban egyszeri bejelentkezés konfigurálása.

**SAP felhő Platform identitás hitelesítéssel konfigurálása az Azure AD egyszeri bejelentkezést, tegye a következőket:**

1. Az Azure portálon a a **SAP felhőalapú Platform identitás hitelesítés** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanel **SAML-alapú bejelentkezés**, jelölje be **mód** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

3. Ha az alkalmazás a konfigurálni kívánt **IDP** indított módban, a **SAP felhő Platform identitás hitelesítési tartomány és az URL-címek** területen tegye a következőket:  

    ![Az egyszeri bejelentkezés információk SAP felhő Platform identitás hitelesítési tartomány és az URL-címek](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. Az a **azonosító** mezőbe írja be a következő mintát olyan URL-címe: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Az a **válasz URL-CÍMEN** mezőbe írja be a következő mintát olyan URL-címe: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Lépjen kapcsolatba a [SAP felhő Platform identitás hitelesítési ügyfél-támogatási csoport](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) beolvasni ezeket az értékeket. Ha azonosító értéke nem világos, olvassa el a SAP felhő Platform identitás Authentication – dokumentáció [bérlői SAML 2.0 konfigurációs](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módot, válassza **megjelenítése speciális URL-beállításainak**.

    ![Az egyszeri bejelentkezés információk SAP felhő Platform identitás hitelesítési tartomány és az URL-címek](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    Az a **URL-cím bejelentkezési** mezőbe írja be olyan URL-címe a következő mintát: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Az adott üzleti alkalmazás bejelentkezési URL-CÍMÉT használja. Lépjen kapcsolatba a [SAP felhő Platform identitás hitelesítési ügyfél-támogatási csoport](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Ha kétségei vannak.

5. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

6. SAP felhő Platform identitás hitelesítési kérelem vár a SAML helyességi feltételek egy meghatározott formátumban. Ezek az attribútumok értékének kezelése a **felhasználói attribútumok** szakaszban az alkalmazás-integráció lapján. Az alábbi képernyőfelvételen látható egy példa a formátuma. 

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

7. Az SAP-alkalmazás vár egy attribútum, mint ha **Keresztnév**, vegye fel a **Keresztnév** attribútumnak a **felhasználói attribútumok** szakasz. Ez a beállítás érhető el a **egyszeri bejelentkezés** párbeszédpanel a **SAML-jogkivonat attribútumok** párbeszédpanel megnyitásához...

    a. Lehetőségre a **attribútum hozzáadása** párbeszédpanelen jelölje ki **Hozzáadás attribútum**. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be az attribútumnév **Keresztnév**.
    
    c. Az a **érték** listára, válassza ki az attribútumérték **user.givenname**.
    
    d. Válassza ki **Ok**.

8. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés mentési gomb konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

9. Az a **SAP hitelesítési felhő Platform Identitáskonfigurációs** szakaszban jelölje be **SAP felhő Platform identitás hitelesítés beállítása** megnyitásához a **bejelentkezéskonfigurálása**ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![SAP felhő Platform identitás hitelesítési konfigurációja](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

10. Az alkalmazáshoz konfigurált SSO, keresse fel a SAP felhőalapú Platform identitás hitelesítés felügyeleti konzolon. Az URL-címnek a következő mintát: `https://<tenant-id>.accounts.ondemand.com/admin`. Olvassa el a dokumentációjában az SAP felhő Platform identitás hitelességet [integráció a Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Az Azure portálon, válassza ki a **mentése** gombra.

12. Folytassa a következő csak akkor, ha azt szeretné, adja hozzá, és engedélyezze az egyszeri Bejelentkezést egy másik SAP-alkalmazással. Ismételje meg a szakaszban **hozzáadása SAP felhőalapú Platform identitás hitelesítés a gyűjteményből**.

13. Az Azure portálon a a **SAP felhőalapú Platform identitás hitelesítés** alkalmazás integrációs lapon jelölje be **társított bejelentkezés**.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

14. A konfiguráció mentéséhez.

>[!NOTE] 
>Az új alkalmazás kihasználja a egyszeri bejelentkezés az előző SAP alkalmazás konfigurációja. Ellenőrizze, hogy az azonos vállalati identitás-szolgáltatóktól használhatja az SAP felhőalapú Platform identitás hitelesítés felügyeleti konzolon.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazás!  Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez a dokumentáció a **konfigurációs** alsó szakasz. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Egy SAP felhőalapú Platform identitás hitelesítés tesztfelhasználó létrehozása

Hozzon létre egy felhasználót az SAP felhőalapú Platform identitás hitelesítés nincs szükségünk. Az Azure AD felhasználókhoz tartozó tárolóban lévő felhasználók számára az egyszeri bejelentkezési funkciók használhatja.

SAP felhőalapú Platform identitás hitelesítés az identitás-összevonási beállítás támogatja. Ez a beállítás lehetővé teszi, hogy az alkalmazás, ellenőrizze, hogy a vállalati identitásszolgáltató által hitelesített felhasználók létezik-e a tároló az SAP felhő Platform identitás felhasználóhitelesítés. 

Az identitás-összevonási beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonási engedélyezve van, csak az SAP felhőalapú Platform identitás hitelesítés az importált felhasználók érhetik el az alkalmazást. 

Engedélyezheti vagy tilthatja le az identitás-összevonási SAP felhő Platform identitás hitelesítéssel kapcsolatos további információkért lásd a "Identitás összevonási az SAP felhő Platform identitás hitelesítés engedélyezése" [identitás-összevonás konfigurálása az a Felhasználói tároló a SAP felhőalapú Platform identitás hitelesítés](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés SAP felhőalapú Platform identitás hitelesítés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SAP felhőalapú Platform identitás hitelesítés, a következő lépéseket:**

1. Az Azure portálon az alkalmazások nézet megnyitásához, és keresse meg a könyvtár nézet. A következő Ugrás **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP felhőalapú Platform identitás hitelesítés**.

    ![Az alkalmazások listáját a SAP felhőalapú Platform identitás hitelesítés hivatkozás](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra a **felhasználók és csoportok** párbeszédpanel megnyitásához.

7. Válassza ki a **hozzárendelése** gombra a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Ha bejelöli az SAP felhőalapú Platform identitás hitelesítés csempe a hozzáférési panelen beolvasása automatikusan megtörténik az SAP felhőalapú Platform identitás hitelesítés alkalmazásba.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

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
