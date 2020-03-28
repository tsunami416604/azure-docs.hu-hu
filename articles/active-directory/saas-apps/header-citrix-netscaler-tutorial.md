---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési integrációja a Citrix NetScaler-rel (fejlécalapú hitelesítés) | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Citrix NetScaler között fejlécalapú hitelesítés sel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f1c884edf6841b65495cd31ed4c7d6e63aedd9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977935"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési integrációja a Citrix NetScalerrel (fejlécalapú hitelesítés)

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Citrix NetScaler-t az Azure Active Directoryval (Azure AD). Ha integrálja a Citrix NetScaler-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Citrix NetScaler.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek a Citrix NetScaler-be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Citrix NetScaler egyszeri bejelentkezés (SSO) engedélyezve van az előfizetéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Az oktatóanyag a következő forgatókönyveket tartalmazza:

* **SP által kezdeményezett** SSO a Citrix NetScaler számára

* **Éppen időben** felhasználói kiépítés a Citrix NetScaler számára

* [Fejlécalapú hitelesítés a Citrix NetScaler számára](#publish-the-web-server)

* [Kerberos-alapú hitelesítés a Citrix NetScaler-hez](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler hozzáadása a galériából

A Citrix NetScaler és az Azure AD integrálásához először adja hozzá a Citrix NetScaler-t a felügyelt SaaS-alkalmazások listájához a galériából:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.

1. A bal oldali menüben válassza az **Azure Active Directory**lehetőséget.

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.

1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Citrix NetScaler** kifejezést a keresőmezőbe.

1. Az eredmények között válassza a **Citrix NetScaler**lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Citrix NetScaler számára

Konfigurálja és tesztelje az Azure AD SSO-t a Citrix NetScaler segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a citrix NetScaler kapcsolódó felhasználója között.

Az Azure AD SSO citrix NetScaler-rel való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. [Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso) – lehetővé teszi a felhasználók számára a funkció használatát.

    1. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) – az Azure AD egyszeri szolgáltatás b.Simon teszteléséhez.

    1. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) – lehetővé teszi b.Simon azure AD egyszeri szolgáltatás használatát.

1. [Konfigurálja a Citrix NetScaler Egyszeri biztonsági sávot](#configure-citrix-netscaler-sso) - az egyszeri sso-beállítások konfigurálásához az alkalmazás oldalon.

    * [Hozzon létre egy Citrix NetScaler teszt felhasználó](#create-a-citrix-netscaler-test-user) - egy megfelelője B.Simon a Citrix NetScaler, amely kapcsolódik a felhasználó Azure AD-ábrázolása.

1. [SSO tesztelése](#test-sso) - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez az Azure Portal használatával hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Citrix NetScaler** alkalmazásintegrációs ablaktáblán válassza a **Kezelés**csoport **egyszeri bejelentkezés**lehetőséget.

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza az **SAML**lehetőséget.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán a beállítások szerkesztéséhez jelölje ki az **egyszerű SAML-konfiguráció** **tollszerkesztésikonját.**

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban az alkalmazás **konfigurálása IDP által kezdeményezett** módban:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet, amely a következő mintát tartalmazza:`https://<Your FQDN>`

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő mintát követi:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Az alkalmazás SP által kezdeményezett módban **történő** konfigurálásához válassza **a További URL-ek beállítása** lehetőséget, és hajtsa végre a következő lépést:

    * A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő mintát követi:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Az ebben a szakaszban használt URL-címek nem valós értékek. Frissítse ezeket az értékeket az azonosító, a válasz URL-címének és a bejelentkezési URL tényleges értékeivel. Lépjen kapcsolatba a [Citrix NetScaler ügyféltámogatási csapatával,](https://www.citrix.com/contact/technical-support.html) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.
    > * Az SSO beállításához az URL-címeknek nyilvános webhelyekről kell elérhetőnek lenniük. Engedélyeznie kell a tűzfal vagy más biztonsági beállításokat a Citrix NetScaler oldalon, hogy az Azure AD-t a jogkivonat a beállított URL-címen tegye közzé.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány** szakaszban az **App Federation metaadat-url-címéhez**másolja az URL-címet, és mentse a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Citrix NetScaler alkalmazás elvárja, hogy az SAML-állítások adott formátumban legyenek, ezért egyéni attribútumleképezéseket kell hozzáadnia az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Jelölje ki a **Szerkesztés** ikont, és módosítsa az attribútum-hozzárendeléseket.

    ![Saml attribútumleképezés szerkesztése](common/edit-attribute.png)

1. A Citrix NetScaler alkalmazás azt is elvárja, hogy még néhány attribútumot visszakell adni az SAML válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek**csoportban hajtsa végre az alábbi lépéseket az SAML token attribútumok hozzáadásához a táblázatban látható módon:

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. A **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához válassza az Új **jogcím hozzáadása** lehetőséget.

    1. A **Név** mezőbe írja be az adott sorhoz megjelenített attribútumnevet.

    1. Hagyja üresen a **névteret.**

    1. Az **Attribútum csoportban**válassza a **Forrás**lehetőséget.

    1. A **Forrás attribútumlistában** adja meg az adott sorhoz megjelenített attribútumértéket.

    1. Válassza **az OK gombot.**

    1. Kattintson a **Mentés** gombra.

1. A **Citrix NetScaler beállítása** szakaszban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali menüjében válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

1. Válassza az **Új felhasználó** lehetőséget az ablaktábla tetején.

1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:

   1. A **Név** `B.Simon`mezőbe írja be a mezőbe.  

   1. A **Felhasználónév** _username@companydomain.extension_mezőbe írja be a mezőbe a. Például: `B.Simon@contoso.com`.

   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le vagy másolja le a **Jelszó**mezőben megjelenő értéket.

   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi, hogy a b.Simon felhasználó azure-sso-t használjon a Citrix NetScaler felhasználói hozzáférésének biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.

1. Az alkalmazások listájában válassza a **Citrix NetScaler**lehetőséget.

1. Az alkalmazás áttekintése csoport **Kezelés**csoportjában válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** párbeszédpanelen válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a **Felhasználók** listából. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd válassza a **Kijelölés lehetőséget.**

1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-citrix-netscaler-sso"></a>A Citrix NetScaler SSO konfigurálása

Válassza ki a konfigurálni kívánt hitelesítési rendszer hez szükséges lépésekhivatkozását:

- [A Citrix NetScaler Egyszeri szolgáltató konfigurálása fejlécalapú hitelesítéshez](#publish-the-web-server)

- [A Citrix NetScaler egyszeri szolgáltató konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>A webkiszolgáló közzététele 

Virtuális kiszolgáló létrehozása:

1. Válassza a **Forgalomkezelés** > **terheléselosztási** > **szolgáltatások lehetőséget.**
    
1. Válassza a **Hozzáadás** lehetőséget.

    ![Citrix NetScaler konfiguráció – Szolgáltatások ablaktábla](./media/header-citrix-netscaler-tutorial/web01.png)

1. Állítsa be az alkalmazásokat futtató webkiszolgáló következő értékeit:

   * **Szolgáltatásnév**
   * **Kiszolgáló IP/meglévő kiszolgálója**
   * **Protocol (Protokoll)**
   * **Port**

     ![Citrix NetScaler konfigurációs ablaktábla](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>A terheléselosztó konfigurálása

A terheléselosztó konfigurálása:

1. Nyissa meg a **Forgalomkezelés** > **terheléselosztás** > **virtuális kiszolgálóit.**

1. Válassza a **Hozzáadás** lehetőséget.

1. Állítsa be a következő értékeket az alábbi képernyőképen leírtak szerint:

    * **Név**
    * **Protocol (Protokoll)**
    * **IP-cím**
    * **Port**

1. Válassza **az OK gombot.**

    ![Citrix NetScaler konfiguráció – Alapbeállítások ablaktábla](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>A virtuális kiszolgáló kötése

A terheléselosztó és a virtuális kiszolgáló kötése:

1. A **Szolgáltatások és szolgáltatáscsoportok** ablaktáblán válassza a **No Load Balancing Virtual Server Service Binding (Nincs terheléselosztásvirtuális kiszolgálószolgáltatás-kötés**) lehetőséget.

   ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló szolgáltatáskötési ablaktáblája](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd válassza a **Bezárás**gombot.

   ![Citrix NetScaler konfiguráció – A virtuális kiszolgálószolgáltatások kötésének ellenőrzése](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>A tanúsítvány kötése

Ha a szolgáltatást SSL-ként szeretné közzétenni, kösse össze a kiszolgálótanúsítványt, majd tesztelje az alkalmazást:

1. A **Tanúsítvány csoportban**válassza a **Nincs kiszolgálói tanúsítvány lehetőséget.**

   ![Citrix NetScaler konfiguráció – Kiszolgálói tanúsítvány ablaktábla](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd válassza a **Bezárás**gombot.

   ![Citrix NetScaler konfiguráció - A tanúsítvány ellenőrzése](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML profil

A Citrix ADC SAML profil konfigurálásához hajtsa végre a következő szakaszokat:

### <a name="create-an-authentication-policy"></a>Hitelesítési házirend létrehozása

Hitelesítési házirend létrehozása:

1. Nyissa meg a **Biztonsági** > **AAA – Alkalmazásforgalom-házirendek** > **Policies** > **hitelesítési** > **hitelesítési házirendjeit.**

1. Válassza a **Hozzáadás** lehetőséget.

1. A **Hitelesítési házirend létrehozása** ablaktáblán adja meg vagy jelölje ki a következő értékeket:

    * **Név**: Adja meg a hitelesítési házirend nevét.
    * **Művelet**: Írja be **az SAML**értéket, majd válassza **a Hozzáadás**lehetőséget.
    * **Kifejezés**: Írja be az **igaz értéket.**     
    
    ![Citrix NetScaler konfiguráció – Hitelesítési házirend ablaktábla létrehozása](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Kattintson a **Létrehozás** gombra.

### <a name="create-an-authentication-saml-server"></a>Hitelesítési SAML-kiszolgáló létrehozása

Hitelesítési SAML-kiszolgáló létrehozásához lépjen a **Hitelesítési SAML-kiszolgáló létrehozása** ablaktáblára, és hajtsa végre az alábbi lépéseket:

1. A **Név mezőbe**írja be a hitelesítési SAML-kiszolgáló nevét.

1. Az **SAML-metaadatok exportálása csoportban:**

   1. Jelölje be a **Metaadatok importálása** jelölőnégyzetet.

   1. Adja meg az összevonási metaadat-URL-címet az Azure SAML felhasználói felületről, amelyet korábban másolt.
    
1. A **Kiállító neve mezőbe**írja be a megfelelő URL-címet.

1. Kattintson a **Létrehozás** gombra.

![Citrix NetScaler konfiguráció – Hitelesítési SAML-kiszolgáló létrehozása ablaktábla](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Virtuális hitelesítési kiszolgáló létrehozása

Virtuális hitelesítési kiszolgáló létrehozása:

1.  Nyissa meg a **Security** > AAA – Application Traffic Policies Authentication Authentication Virtual Servers **(Alkalmazásforgalom-házirendek** > **Policies** > **hitelesítése** > **– virtuális kiszolgálók ) lapot.**

1.  Válassza **a Hozzáadás**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **Név**mezőbe írja be a virtuális hitelesítési kiszolgáló nevét.

    1. Jelölje be a **Nem címezhető jelölőnégyzetet.**

    1. A **Protokoll esetében**válassza az **SSL**lehetőséget.

    1. Válassza **az OK gombot.**

    ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló hitelesítése](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>A virtuális hitelesítési kiszolgáló konfigurálása az Azure AD használatára

A virtuális hitelesítési kiszolgáló két szakaszának módosítása:

1.  A **Speciális hitelesítési házirendek** ablaktáblán válassza a **Nincs hitelesítési házirend lehetőséget.**

    ![Citrix NetScaler konfiguráció – Speciális hitelesítési házirendek ablaktábla](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. A **Házirendkötés** ablaktáblán jelölje ki a hitelesítési házirendet, majd kattintson a **Kötés gombra.**

    ![Citrix NetScaler konfiguráció – Házirendkötés ablaktábla](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Az **Űrlapalapú virtuális kiszolgálók** ablaktáblán válassza a **Nincs virtuális terheléselosztási virtuális kiszolgáló lehetőséget.**

    ![Citrix NetScaler konfiguráció – Űrlapalapú virtuális kiszolgálók ablaktábla](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. A **hitelesítésteljes tartománynévhez**adjon meg egy teljesen minősített tartománynevet (fqdn) (kötelező).

1. Válassza ki a terheléselosztás virtuális kiszolgáló, amely védeni szeretné az Azure AD-hitelesítéssel.

1. Válassza **a Kötés**lehetőséget.

    ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló terheléselosztási kötési ablaktáblája](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Győződjön meg róla, hogy a Virtuális hitelesítés kiszolgáló konfigurációja ablaktáblán a **Kész** lehetőséget **választja.**

1. A módosítások ellenőrzéséhez a böngészőben nyissa meg az alkalmazás URL-címét. A bérlői bejelentkezési lapnak kell látnia a korábban látott nem hitelesített hozzáférés helyett.

    ![Citrix NetScaler konfiguráció – Bejelentkezési lap webböngészőben](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>A Citrix NetScaler Egyszeri szolgáltató konfigurálása fejlécalapú hitelesítéshez

### <a name="configure-citrix-adc"></a>Citrix ADC konfigurálása

A Citrix ADC fejlécalapú hitelesítésre való konfigurálásához hajtsa végre a következő szakaszokat.

#### <a name="create-a-rewrite-action"></a>Újraírási művelet létrehozása

1. Nyissa meg az **AppExpert** > **átírási** > **átírási műveleteket.**
 
    ![Citrix NetScaler konfiguráció – Műveletek újraírása ablaktábla](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Válassza **a Hozzáadás**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **Név mezőbe**írja be az újraírási művelet nevét.

    1. A **Típus mezőbe**írja be **INSERT_HTTP_HEADER.**

    1. A **Fejlécneve**mezőbe írja be a fejléc nevét (ebben a példában _a Titkos azonosítóazonosítót használjuk)._

    1. A **Kifejezés mezőbe**írja be az **aaa értéket. Felhasználó. ATTRIBUTE("mySecretID")**, ahol **a mySecretID** az Azure AD SAML jogcím, amely et a Citrix ADC küldött.

    1. Kattintson a **Létrehozás** gombra.

    ![Citrix NetScaler konfiguráció – Újraírási művelet ablaktábla létrehozása](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Újraírási házirend létrehozása

1.  Nyissa meg az **AppExpert** > **átírási** > **újraírási irányelveit.**
 
    ![Citrix NetScaler konfiguráció – Házirendek újraírása ablaktábla](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Válassza **a Hozzáadás**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **Név mezőbe**írja be az újraírási házirend nevét.

    1. A **Művelet területen**jelölje ki az előző szakaszban létrehozott újraírási műveletet.

    1. A **Kifejezés mezőbe**írja be az **igaz**értéket.

    1. Kattintson a **Létrehozás** gombra.

    ![Citrix NetScaler konfiguráció – Újraírási szabályzat ablaktábla létrehozása](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Újraírási házirend kötése virtuális kiszolgálóhoz

Újraírási házirend virtuális kiszolgálóhoz kötése a grafikus felhasználói felület használatával:

1. Nyissa meg a **Forgalomkezelés** > **terheléselosztás** > **virtuális kiszolgálóit.**

1. A virtuális kiszolgálók listájában jelölje ki azt a virtuális kiszolgálót, amelyhez az újraírási házirendet kötni szeretné, majd válassza a **Megnyitás**lehetőséget.

1. A **Virtuális kiszolgáló terheléselosztása** ablaktáblán a **Speciális beállítások**csoportban válassza a **Házirendek**lehetőséget. A NetScaler-példányhoz konfigurált összes házirend megjelenik a listában.
 
    ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló terheléselosztási ablaktáblája](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló terheléselosztási ablaktáblája](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Jelölje be a virtuális kiszolgálóhoz kötni kívánt házirend neve melletti jelölőnégyzetet.
 
    ![Citrix NetScaler konfiguráció – Virtuális kiszolgáló terheléselosztási virtuális kiszolgálói forgalomházirend-kötési ablaktáblája](./media/header-citrix-netscaler-tutorial/header08.png)

1. A **Szöveg kiválasztása** párbeszédpanelen:

    1. A **Házirend kiválasztása csoportban**válassza a **Forgalom**lehetőséget.

    1. A **Típus kiválasztása területen**válassza a **Kérés**lehetőséget.

    ![Citrix NetScaler konfiguráció – Házirendek párbeszédpanel](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Válassza **az OK gombot.** Az állapotsorban lévő üzenet azt jelzi, hogy a házirend sikeresen konfigurálva lett.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Az SAML-kiszolgáló módosítása a jogcím attribútumainak kinyeréséhez

1.  Nyissa meg a **Security** > **AAA – Application Traffic** > **Policies** > **Authentication** > Advanced**Policies** > **Actions** > **kiszolgálókat.**

1.  Válassza ki az alkalmazáshoz megfelelő hitelesítési SAML-kiszolgálót.
 
    ![Citrix NetScaler konfiguráció – Hitelesítési SAML-kiszolgáló ablaktábla konfigurálása](./media/header-citrix-netscaler-tutorial/header09.png)

1. A **Jellemzők** fájdalom, adja meg a SAML attribútumok, hogy ki szeretne bontani, vesszővel elválasztva. A példánkban beírjuk `mySecretID`az attribútumot.
 
    ![Citrix NetScaler konfiguráció – Attribútumok ablaktábla](./media/header-citrix-netscaler-tutorial/header10.png)

1. A hozzáférés ellenőrzéséhez a böngésző URL-címén keresse meg az SAML attribútumot a **Fejlécgyűjtemény csoportban.**

    ![Citrix NetScaler konfiguráció - Fejlécgyűjtemény az URL-címen](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Citrix NetScaler tesztfelhasználó létrehozása

In this section, a user called B.Simon is created in Citrix NetScaler. A Citrix NetScaler támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs teendő. Ha a felhasználó még nem létezik a Citrix NetScaler, egy új jön létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Citrix NetScaler ügyféltámogatási csapatához.](https://www.citrix.com/contact/technical-support.html)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD SSO-konfiguráció a hozzáférési panel használatával tesztelheti.

Amikor a Citrix NetScaler csempét választja a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Citrix NetScaler-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Citrix NetScaler-t az Azure AD-vel](https://aad.portal.azure.com/)

- [A Citrix NetScaler egyszeri bejelentkezésének konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md)
