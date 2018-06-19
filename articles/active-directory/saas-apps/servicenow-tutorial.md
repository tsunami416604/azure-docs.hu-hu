---
title: 'Oktatóanyag: Azure Active Directoryval integrált ServiceNow |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a ServiceNow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8ed3342654b5229d0431edce1080c7771ca3778d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927674"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Oktatóanyag: Azure Active Directoryval integrált ServiceNow

Ebben az oktatóanyagban elsajátíthatja a ServiceNow integrálása az Azure Active Directory (Azure AD).

A ServiceNow integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a ServiceNow szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a ServiceNow (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a ServiceNow, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A ServiceNow, egy példány vagy bérlő ServiceNow, Calgary verzió vagy újabb
- A ServiceNow expressz, ServiceNow kifejezett, Helsinki verzió példányának vagy újabb
- A ServiceNow bérlő kell rendelkeznie a [több szolgáltató egyszeri bejelentkezést a beépülő modul](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) engedélyezve van. Ezt úgy teheti [szolgáltatási kérelem elküldése](https://hi.service-now.com).
- Automatikus konfigurálásához a szolgáltató több beépülő modul engedélyezése a ServiceNow.

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A ServiceNow hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-servicenow-from-the-gallery"></a>A ServiceNow hozzáadása a gyűjteményből
Az Azure AD integrálása a ServiceNow konfigurálásához kell hozzáadnia a ServiceNow a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A ServiceNow hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **ServiceNow**, jelölje be **ServiceNow** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A ServiceNow az eredménylistában](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a ServiceNow "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a ServiceNow tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a ServiceNow közötti kapcsolat kapcsolatot kell létrehozni.

A ServiceNow, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a ServiceNow tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása a ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD az egyszeri bejelentkezés konfigurálása ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[A ServiceNow tesztfelhasználó létrehozása](#create-a-servicenow-test-user)**  - való Britta Simon egy megfelelője a ServiceNow, amely csatolva van a felhasználó az Azure AD-ábrázolását.
5. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>A ServiceNow az Azure AD-egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a ServiceNow alkalmazásban egyszeri bejelentkezés konfigurálása.

**A ServiceNow konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ServiceNow** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Az a **ServiceNow tartomány és az URL-címek** területen tegye a következőket:

    ![A ServiceNow tartomány és az URL-címek az egyszeri bejelentkezés információk](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és az oktatóanyag későbbi részében ismertetett azonosítója lesz szüksége.

4. Az a **SAML-aláíró tanúsítványa** területen tegye a következőket: 

    ![A tanúsítvány letöltési hivatkozását](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe, mint az alkalmazás összevonási metaadatainak URL-cím lesz az oktatóanyag későbbi részében.

    b. Kattintson a **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/servicenow-tutorial/tutorial_general_400.png)

6. Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

7. Aktiválja a **integrációs - több szolgáltató egyszeri bejelentkezés telepítő** beépülő modul a következő lépéseket követve:

    a. A bal oldali navigációs ablakában keresse **rendszer Definition** szakasz a keresősávban, és kattintson a **beépülő modulok**.

    ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_03.png "beépülő modul aktiválása")

     b. Keresse meg **integrációs - több szolgáltató egyszeri bejelentkezés telepítő**.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_04.png "beépülő modul aktiválása")

    c. Válassza ki a beépülő modul. Kattintson a jobb gombbal, és válassza ki **aktiválás/frissítése**.

    d. Kattintson a **aktiválás** gombra.

8. Kétféleképpen, amelyben **ServiceNow** konfigurált automatikus és manuális lehet.

9. Konfigurálásához **ServiceNow** automatikusan kövesse a következő lépések

    a. Lépjen vissza a **ServiceNow** Signle-bejelentkezési oldal az Azure portálon.

    b. Egy kattintással konfigurálhatja az service is biztosít a ServiceNow Ez azt jelenti, hogy az Azure AD automatikusan konfigurálja ServiceNow SAML-alapú hitelesítés. Ahhoz, hogy ez a szolgáltatás Ugrás **ServiceNow konfigurációs** területén kattintson **konfigurálása ServiceNow** konfigurálása bejelentkezés ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Adja meg a ServiceNow példány nevét, a rendszergazda felhasználónevét és a rendszergazdai jelszó a **bejelentkezés konfigurálása** kialakításához, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a rendszergazdai felhasználónevet kell rendelkeznie a **security_admin** működéséhez a ServiceNow hozzárendelve szerepkör. Ellenkező esetben a SAML-Identitásszolgáltatóként az Azure AD használandó ServiceNow kézzel konfigurálásához kattintson **manuális konfigurálása egyszeri bejelentkezéshez** , és másolja a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** rövid összefoglaló szakaszából.

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

    d. Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

    e. Az automatikus konfiguráció összes a szükséges beállításokat a **ServiceNow** oldalon, de a **X.509 tanúsítvány** alapértelmezés szerint nincs engedélyezve. Manuálisan képezze le az identitásszolgáltató a ServiceNow kell. Kövesse a következő lépések ugyanazon:
    
    * A bal oldali navigációs ablaktábláján kattintson **identitás-szolgáltatóktól** alatt **több szolgáltató SSO**.

      ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a az automatikusan létrehozott identitásszolgáltató

      ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_08.png "egyszeri bejelentkezés konfigurálása")

    * Görgessen le a **X.509 tanúsítvány** szakasz. Válassza a **Szerkesztés** elemet.

      ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_09.png "egyszeri bejelentkezés konfigurálása")
    
    * A tanúsítvány kiválasztásához, majd kattintson a jobbra mutató nyíl ikonra a tanúsítvány hozzáadása

      ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_11.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a **Save** (Mentés) gombra.

    * Kattintson a **aktiválás** , az oldal jobb felső sarkában.

10. Konfigurálásához **ServiceNow** manuálisan kövesse a következő lépések

11. Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

12. A bal oldali navigációs ablakában keresse **több szolgáltató SSO** szakasz a keresősávban, és kattintson a **tulajdonságok**.

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_06.png "alkalmazás URL-CÍMEK konfigurálása")

13. Az a **több SSO tulajdonságokat** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/ic7694981.png "alkalmazás URL-CÍMEK konfigurálása")

    a. Mint **több szolgáltató SSO engedélyezése**, jelölje be **Igen**.

    b. Mint **automatikus importálása engedélyezése a felhasználók a felhasználói táblázatba identitás-szolgáltatóktól származó**, jelölje be **Igen**.

    c. Mint **engedélyezése hibakeresési naplózás több szolgáltatójának SSO integrációs**, jelölje be **Igen**.

    d. A **a mezőt, a felhasználó táblázat...**  szövegmezőhöz típus **felhasználónév**.

    e. Kattintson a **Save** (Mentés) gombra.

14. A bal oldali navigációs ablakában keresse **több szolgáltató SSO** szakasz a keresősávban, és kattintson a **x509 tanúsítványok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_05.png "egyszeri bejelentkezés konfigurálása")

15. Az a **X.509-tanúsítványokat** párbeszédpanel, kattintson a **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694974.png "egyszeri bejelentkezés konfigurálása")

16. Az a **X.509-tanúsítványokat** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694975.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza ki **aktív**.

    c. Mint **formátum**, jelölje be **PEM**.

    d. Mint **típus**, jelölje be **megbízható tároló Cert**.

    e. Nyissa meg a Jegyzettömbben az Azure-ból letöltött Base64 kódolású tanúsítvány, a tartalmának másolása a vágólapra és illessze be azt a **PEM tanúsítvány** szövegmező.

     f. Kattintson a **nyújt**.

17. A bal oldali navigációs ablaktábláján kattintson **identitás-szolgáltatóktól**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

18. Az a **identitás-szolgáltatóktól** párbeszédpanel, kattintson a **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694977.png "egyszeri bejelentkezés konfigurálása")

19. Az a **identitás-szolgáltatóktól** párbeszédpanel, kattintson a **egy SAML2 1. frissítés?**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694978.png "egyszeri bejelentkezés konfigurálása")

20. Egy SAML2 1. frissítés tulajdonságai párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/idp.png "egyszeri bejelentkezés konfigurálása")

    a. Válassza ki **URL-cím** beállítást **Identity Provider metaadatok importálása** párbeszédpanelt.

    b. Adja meg a **alkalmazás összevonási metaadatainak URL-címe** ami Azure-portálon másolta.

    c. Kattintson az **Importálás** gombra.

21. Beolvassa a kiállító terjesztési hely metaadatainak URL-CÍMÉT, és feltölti a mezők adatai.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (például **SAML 2.0**).
    
    b. Másolás **ServiceNow kezdőlap** értékét, illessze be a **bejelentkezési URL-cím** textbox **ServiceNow tartomány és az URL-címek** Azure-portál szakaszban.

    > [!NOTE]
    > A ServiceNow példány kezdőlapon összefűzése a **ServieNow bérlői URL-cím** és **/navpage.do** (például:`https://fabrikam.service-now.com/navpage.do`).

    c. Másolás **Entitásazonosító / kibocsátó** értékét, illessze be **azonosító** textbox **ServiceNow tartomány és az URL-címek** Azure-portál szakaszban.

    d. Kattintson a **speciális**. Az a **felhasználói mező** szövegmezőhöz típus **e-mail** vagy **felhasználónév**, attól függően, melyik mezőt a ServiceNow környezetben a felhasználók egyedi azonosítására szolgál.

    > [!NOTE]
    > Konfigurálhatja az Azure AD vagy az Azure AD felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail cím a SAML-jogkivonat egyedi azonosítóként kibocsátásához címen a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakasza az Azure-portálon a kívánt mező a leképezési és a **nameidentifier** attribútum. A kijelölt attribútum (például az egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

    e. A **x509 tanúsítvány**, a tanúsítványt az előző lépésben létrehozott sorolja fel.

    > [!NOTE]
    > A ServiceNow nem teszi lehetővé a kiállító terjesztési hely aktiválásának nélkül kapcsolat tesztelése gombra kattint, bírálja felül, azonos, hajtsa végre az alábbi lépéseket.

22. Kattintson a menü ikonra a konfigurálás részeként létrehozott új Identitásszolgáltatóként az és a listában válassza ki a **sys_id másolása**

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694992.png "egyszeri bejelentkezés konfigurálása")

23. A bal felső keresési párbeszédpanelen keresse meg **sys_properties.list** , és nyomja le az enter.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694993.png "egyszeri bejelentkezés konfigurálása")

24. Kattintson az **Új** lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694994.png "egyszeri bejelentkezés konfigurálása")

25. Az a **rendszer tulajdonság** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694995.png "egyszeri bejelentkezés konfigurálása")

    a. Adja meg `glide.authenticate.sso.redirect.idp` értéket a szövegmezőben.

    b. Az a **érték** szövegmező, illessze be a másolási sys_id érték, amely az előző lépésben másolt.

    c. Válassza ki **titkos**.

    d. Kattintson a **nyújt**.

26. Kattintson az **Új** lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694994.png "egyszeri bejelentkezés konfigurálása")

27. Az a **rendszer tulajdonság** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694996.png "egyszeri bejelentkezés konfigurálása")

    a. Adja meg `glide.authenticate.multisso.test.connection.mandatory` értéket a szövegmezőben.

    b. Az a **érték** szövegmezőhöz meg **hamis**.

    c. Kattintson a **nyújt**.

28. Ezzel a lépéssel fent, után most lesz az új identitásszolgáltató aktiválásához, és az SSO kell működnie

> [!NOTE]
> Továbbá ne feledje, hogy az új Idp konfigurációjának tesztelése incognito új ablakban

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>A ServiceNow Express az Azure AD-egyszeri bejelentkezés konfigurálása

1. Az Azure portálon a a **ServiceNow** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. Az a **ServiceNow tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [ServiceNow ügyfél-támogatási csoport](https://www.servicenow.com/support/contact-support.html) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_general_400.png)

6. Egy kattintással konfigurálhatja az service is biztosít a ServiceNow Ez azt jelenti, hogy az Azure AD automatikusan konfigurálja ServiceNow SAML-alapú hitelesítés. Ahhoz, hogy ez a szolgáltatás Ugrás **ServiceNow konfigurációs** területén kattintson **konfigurálása ServiceNow** konfigurálása bejelentkezés ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Adja meg a ServiceNow példány nevét, a rendszergazda felhasználónevét és a rendszergazdai jelszó a **bejelentkezés konfigurálása** kialakításához, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a rendszergazdai felhasználónevet kell rendelkeznie a **security_admin** működéséhez a ServiceNow hozzárendelve szerepkör. Ellenkező esetben a SAML-Identitásszolgáltatóként az Azure AD használandó ServiceNow kézzel konfigurálásához kattintson **manuális konfigurálása egyszeri bejelentkezéshez** , és másolja a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** rövid összefoglaló szakaszából.

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

8. Jelentkezzen be rendszergazdaként a ServiceNow Express-alkalmazás.

9. A bal oldali navigációs ablaktábláján kattintson **egyszeri bejelentkezés**.

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/ic7694980ex.png "alkalmazás URL-CÍMEK konfigurálása")

10. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a jobb felső konfigurációs ikonjára, és állítsa be a következő tulajdonságokat:

    ![Alkalmazás URL-CÍMEK konfigurálása](./media/servicenow-tutorial/ic7694981ex.png "alkalmazás URL-CÍMEK konfigurálása")

    a. Váltás **több szolgáltató SSO engedélyezése** jobbra.
    
    b. Váltás **engedélyezése hibakeresési naplózás több szolgáltatójának SSO integrációs** jobbra.
    
    c. A **a mezőt, a felhasználó táblázat...**  szövegmezőhöz típus **felhasználónév**.

11. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a **új tanúsítvány hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694973ex.png "egyszeri bejelentkezés konfigurálása")

12. Az a **X.509-tanúsítványokat** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694975.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza ki **aktív**.

    c. Mint **formátum**, jelölje be **PEM**.

    d. Mint **típus**, jelölje be **megbízható tároló Cert**.

    e. Nyissa meg a Jegyzettömbben az Azure portálról letöltött Base64 kódolású tanúsítvány, a tartalmának másolása a vágólapra és illessze be azt a **PEM tanúsítvány** szövegmező.

    f. Kattintson a **frissítés**

13. Az a **egyszeri bejelentkezés** párbeszédpanel, kattintson a **hozzáadása új IdP**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694976ex.png "egyszeri bejelentkezés konfigurálása")

14. Az a **hozzáadása új identitásszolgáltató** párbeszédpanelen, a **identitásszolgáltató konfigurálása**, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (például: **SAML 2.0**).

    b. Az a **identitási szolgáltató URL-cím** mezőbe illessze be az értékét **identitás Szolgáltatóazonosító**, amely Azure-portálon másolta.
    
    c. Az a **identitásszolgáltató AuthnRequest** mezőbe illessze be az értékét **hitelesítési kérelem URL-cím**, amely az Azure-portálon másolta.

    d. Az a **identitásszolgáltató SingleLogoutRequest** mezőbe illessze be az értékét **egyetlen Sign-Out URL-címe**, amely az Azure-portálon másolta

    e. Mint **szolgáltató Identitástanúsítvány**, válassza ki az előző lépésben létrehozott tanúsítványt.

15. Kattintson a **speciális beállítások**, majd a **további identitás szolgáltató tulajdonságai**, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694983ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **protokoll kötése az IDP SingleLogoutRequest** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:bindings:HTTP-átirányítási**.

    b. Az a **NameID házirend** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: nem meghatározott**.

    c. Az a **AuthnContextClassRef metódus**, típus `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Kapcsolja ki **hozzon létre egy AuthnContextClass**.

16. A **további szolgáltató tulajdonságai**, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694984ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **ServiceNow kezdőlap** szövegmezőhöz a ServiceNow példány kezdőlap URL-címét.

    > [!NOTE]
    > A ServiceNow példány kezdőlapon összefűzése a **ServieNow bérlői URL-cím** és **/navpage.do** (például: `https://fabrikam.service-now.com/navpage.do`).

    b. Az a **Entitásazonosító / kibocsátó** szövegmezőhöz a ServiceNow bérlői URL-címét.

    c. Az a **célközönség URI** szövegmezőhöz a ServiceNow bérlői URL-címét.

    d. A **óra döntés** szövegmezőhöz típus **60**.

    e. Az a **felhasználói mező** szövegmezőhöz típus **e-mail** vagy **felhasználónév**, attól függően, melyik mezőt a ServiceNow környezetben a felhasználók egyedi azonosítására szolgál.

    > [!NOTE]
    > Konfigurálhatja az Azure AD vagy az Azure AD felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail cím a SAML-jogkivonat egyedi azonosítóként kibocsátásához címen a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakasza az Azure-portálon a kívánt mező a leképezési és a **nameidentifier** attribútum. A kijelölt attribútum (például az egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/servicenow-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/servicenow-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/servicenow-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/servicenow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-servicenow-test-user"></a>A ServiceNow tesztfelhasználó létrehozása

Ez a szakasz célja a ServiceNow Britta Simon nevű felhasználó létrehozásához. A ServiceNow támogatja automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](servicenow-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

> [!NOTE]
> Hozza létre a felhasználó manuálisan kell, ha kapcsolatba kell [ServiceNow ügyfél-támogatási csoport](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít a ServiceNow által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**A ServiceNow Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ServiceNow**.

    ![Az alkalmazások listáját a ServiceNow hivatkozás](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a ServiceNow csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a ServiceNow alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](servicenow-provisioning-tutorial.md)


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
