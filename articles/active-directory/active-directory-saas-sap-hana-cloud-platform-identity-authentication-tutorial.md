---
title: "Oktatóanyag: Azure Active Directory-integráció SAP felhő Platform identitás hitelesítéssel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP felhőalapú Platform identitás hitelesítés között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Azure Active Directory-integráció SAP felhő Platform identitás hitelesítéssel

Ebben az oktatóanyagban elsajátíthatja SAP felhőalapú Platform identitás hitelesítés integrálása az Azure Active Directory (Azure AD). SAP felhő Platform identitás hitelesítés hozzáférési SAP alkalmazásokhoz az Azure AD használatával a fő IdP IdP proxy-ként használatos.

SAP felhőalapú Platform identitás hitelesítés integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáférhet SAP alkalmazás szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az alkalmazások egyszeri bejelentkezés (SSO) SAP) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SAP felhő Platform identitás hitelesítéssel, a következő elemek szükségesek:

- Az Azure AD szolgáltatásra
- Egy SAP felhőalapú Platform identitás hitelesítés egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP felhőalapú Platform identitás hitelesítés hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

Technikai részleteket ról, mielőtt elengedhetetlen megérteni a fogalmakat, nézze meg fog. Az SAP felhőalapú Platform identitás hitelesítés és az Azure Active Directory összevonási lehetővé teszi több alkalmazáshoz vagy szolgáltatáshoz (mint egy IdP) AAD védi az SAP-alkalmazásokkal és szolgáltatásokkal SAP felhő Platform identitás által védett egyszeri bejelentkezés megvalósítása Hitelesítés.

Jelenleg SAP felhőalapú Platform identitás hitelesítés úgy működik, mint a Proxy identitásszolgáltató SAP-alkalmazások. Az Azure Active Directory pedig úgy működik, mint a kezdő identitásszolgáltató az ehhez a telepítéshez. 

A következő ábra szemlélteti ezt:

![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Ez a beállítás a megbízható alkalmazások az Azure Active Directoryban az SAP felhőalapú Platform identitás hitelesítés bérlő lesz beállítva. 

Az SAP felhőalapú Platform identitás hitelesítés felügyeleti konzolon SAP alkalmazások és szolgáltatások, így a védeni kívánt ezt követően konfigurálni!

Ez azt jelenti, hogy SAP alkalmazásokhoz és szolgáltatásokhoz való hozzáférés engedélyezési kell kerül sor az SAP felhőalapú Platform identitás hitelesítés (figyelésekor engedélyezési konfigurálása az Azure Active Directoryban) telepítéshez.

Az Azure Active Directory piactéren keresztül alkalmazásként SAP felhőalapú Platform identitás hitelesítés konfigurálásával irányuló beállítása szükséges az egyes jogcímek nem kell egy érvényes előállításához szükséges SAML helyességi feltételek és átalakítások / SAP-alkalmazásokból hitelesítésére szolgáló jogkivonat.

>[!NOTE] 
>Webes egyszeri bejelentkezés jelenleg csak a két fél által tesztelték. Alkalmazás-API vagy API-API kommunikációhoz szükséges adatfolyamok kell működnie, de nem lettek tesztelve, még. Azok a soron következő tevékenységek részeként kell vizsgálni.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP felhőalapú Platform identitás hitelesítés hozzáadása a gyűjteményből
Az Azure AD integrálása a SAP felhőalapú Platform identitás hitelesítés konfigurálásához kell hozzáadnia SAP felhőalapú Platform identitás hitelesítés a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**SAP felhő Platform identitás hitelesítés hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP felhőalapú Platform identitás hitelesítés**, jelölje be **SAP felhőalapú Platform identitás hitelesítés** eredmény panelen kattintson a **Hozzáadás** gombra kattint, hogy Vegye fel az alkalmazást.

    ![SAP felhőalapú Platform identitás hitelesítés az eredménylistában](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés SAP felhőalapú Platform identitás hitelesítés "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SAP felhőalapú Platform identitás hitelesítés a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAP felhőalapú Platform identitás hitelesítés közötti kapcsolat kapcsolatot kell létrehozni.

Az SAP felhőalapú Platform identitás hitelesítés, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés SAP felhő Platform identitás hitelesítés tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy SAP felhőalapú Platform identitás hitelesítés tesztfelhasználó](#create-an-sap-cloud-platform-identity-authentication-test-user)**  - való egy megfelelője a Britta Simon SAP felhőalapú Platform identitás hitelesítés, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az SAP felhőalapú Platform identitás hitelesítés alkalmazásban egyszeri bejelentkezés konfigurálása.

**SAP felhő Platform identitás hitelesítéssel konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SAP felhőalapú Platform identitás hitelesítés** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Az a **SAP felhő Platform identitás hitelesítési tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk SAP felhő Platform identitás hitelesítési tartomány és az URL-címek](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges azonosítója. Ügyfél [SAP felhő Platform identitás hitelesítési ügyfél-támogatási csoport](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) lekérni ezt az értéket. Ha ez az érték nem tudja, kövesse az SAP felhő Platform identitás Authentication – dokumentáció [bérlői SAML 2.0 konfigurációs](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk SAP felhő Platform identitás hitelesítési tartomány és az URL-címek](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    Az a **URL-cím bejelentkezési** szövegmező, adja meg a következő minta használatával URL-címe:`https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [SAP felhő Platform identitás hitelesítési ügyfél-támogatási csoport](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) lekérni ezt az értéket.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP felhő Platform identitás hitelesítési kérelem vár a SAML helyességi feltételek egy meghatározott formátumban. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanel, ha az SAP-alkalmazást például a "Keresztnév" attribútumot vár. A SAML-jogkivonat attribútumok párbeszédpanelen adja hozzá a "Keresztnév" attribútumot.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az attribútum neve "Keresztnév".
    
    c. Az a **érték** listára, válassza ki a "user.givenname" attribútum értéke.
    
    d. Kattintson az **OK** gombra.

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. A a **SAP hitelesítési felhő Platform Identitáskonfigurációs** kattintson **SAP felhő Platform identitás hitelesítés beállítása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![SAP felhő Platform identitás hitelesítési konfigurációja](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Az alkalmazáshoz konfigurált SSO, keresse fel SAP felhő Platform identitás hitelesítési felügyeleti konzolon. Az URL-címnek a következő mintát: `https://<tenant-id>.accounts.ondemand.com/admin`. Majd kövesse a dokumentáció SAP felhő Platform identitás hitelesítés [konfigurálása a Microsoft Azure AD vállalati identitás-szolgáltatóként SAP felhő Platform identitás hitelesítési](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Az Azure portálon kattintson **mentése** gombra.

12. Csak akkor, ha azt szeretné, adja hozzá, és engedélyezze az egyszeri Bejelentkezést egy másik SAP-alkalmazással, továbbra is az alábbi lépéseket. Ismételje meg a "Hozzáadás SAP felhő Platform identitás-hitelesítés a gyűjteményből" szakaszban hozzáadása az SAP felhőalapú Platform identitás hitelesítés egy másik példánya.

13. Az Azure portálon a a **SAP felhőalapú Platform identitás hitelesítés** alkalmazás integráció lapján, kattintson a **társított bejelentkezés**.

    ![Csatolt bejelentkezés konfigurálása](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. A konfiguráció mentéséhez.

>[!NOTE] 
>Az új alkalmazás előző SAP alkalmazást SSO konfigurációját fogja használni. Ellenőrizze, hogy az azonos vállalati identitás-szolgáltatóktól használhatja az SAP felhő Platform identitás hitelesítési felügyeleti konzolon.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Egy SAP felhőalapú Platform identitás hitelesítés tesztfelhasználó létrehozása

Egy felhasználó létrehozása SAP felhőalapú Platform identitás hitelesítés nincs szükségünk. Az Azure AD felhasználókhoz tartozó tárolóban lévő felhasználók számára az egyszeri bejelentkezési funkciók használhatja.

SAP felhőalapú Platform identitás hitelesítés az identitás-összevonási beállítás támogatja. Ez a beállítás lehetővé teszi, hogy az alkalmazás Ha a vállalati identitásszolgáltató által hitelesített felhasználók létezik-e a tároló az SAP felhő Platform identitás felhasználóhitelesítés. 

Az alapértelmezett beállítás, az identitás-összevonási beállítás le van tiltva. Identitás-összevonási engedélyezve van, ha csak a felhasználók az SAP felhőalapú Platform identitás hitelesítés importált képesek hozzáférni az alkalmazáshoz. 

Engedélyezheti vagy tilthatja le az identitás-összevonási SAP felhő Platform identitás hitelesítéssel kapcsolatos további információkért tekintse meg az identitás-összevonási engedélyezése SAP felhő Platform identitás hitelesítéssel a [identitás-összevonás konfigurálása az a Felhasználói tároló a SAP felhőalapú Platform identitás hitelesítés](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés SAP felhőalapú Platform identitás hitelesítés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP felhőalapú Platform identitás hitelesítés Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP felhőalapú Platform identitás hitelesítés**.

    ![Az alkalmazások listáját a SAP felhőalapú Platform identitás hitelesítés hivatkozás](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel SAP felhőalapú Platform identitás hitelesítés mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett az SAP felhőalapú Platform identitás hitelesítés alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

