---
title: SAML egyszeri bejelentkezés - nem galéria alkalmazások - Microsoft identity platform | Microsoft dokumentumok
description: Egyszeri bejelentkezés konfigurálása nem katalógusalkalmazásokba a Microsoft identity platformon (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244211"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>SAML-alapú egyszeri bejelentkezés konfigurálása nem galériaalkalmazásokba

Amikor [hozzáad egy galériaalkalmazást](add-gallery-app.md) vagy egy [nem galéria alapú webalkalmazást](add-non-gallery-app.md) az Azure AD Enterprise Applications alkalmazáshoz, az ön számára elérhető egyszeri bejelentkezési lehetőségek egyike az [SAML-alapú egyszeri bejelentkezés.](what-is-single-sign-on.md#saml-sso) Amikor csak lehetséges, válassza az SAML lehetőséget az saml protokollok valamelyikével hitelesítő alkalmazásokhoz. Saml egyszeri bejelentkezéssel az Azure AD hitelesíti magát az alkalmazás a felhasználó Azure AD-fiókjával. Az Azure AD egy kapcsolatprotokollon keresztül közli a bejelentkezési adatokat az alkalmazással. A felhasználókat az SAML-jogcímekben megadott szabályok alapján leképezheti adott alkalmazásszerepkörökhöz. Ez a cikk azt ismerteti, hogyan konfigurálhatja az SAML-alapú egyszeri bejelentkezést egy nem katalógusalkalmazáshoz. 

> [!NOTE]
> Katalógusalkalmazást szeretne hozzáadni? Részletes telepítési útmutatók keresése a [SaaS-alkalmazások oktatóanyagainak listájában](../saas-apps/tutorial-list.md)

Saml egyszeri bejelentkezés konfigurálásához egy nem katalógus alkalmazás kód írása nélkül, előfizetéssel kell rendelkeznie egy Azure AD Premium-licenccel együtt, és az alkalmazásnak támogatnia kell az SAML 2.0-s verziót. Az Azure AD-verziókról az [Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című webhelyen talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás még nem lett hozzáadva az Azure AD-bérlőhöz, olvassa el a Nem katalógusalkalmazás hozzáadása című [témakört.](add-non-gallery-app.md)

## <a name="step-1-edit-the-basic-saml-configuration"></a>1. lépés Az alapvető SAML-konfiguráció szerkesztése

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy felhőalapú alkalmazás-rendszergazdaként, vagy egy alkalmazás-rendszergazda az Azure AD-bérlő.

2. Keresse meg az **Azure Active Directory** > **Enterprise alkalmazásokat,** és válassza ki az alkalmazást a listából. 
   
   - Az alkalmazás kereséséhez válassza az **Alkalmazás típusa** **menüBen**a Minden alkalmazás lehetőséget, majd az **Alkalmaz parancsot.** Írja be az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

3. A **Kezelés csoportban** válassza az **Egyszeri bejelentkezés**lehetőséget. 

4. Válassza **az SAML lehetőséget.** Megjelenik **az Egyszeri bejelentkezés beállítása SAML - Előzetes verzió** lapon.

   ![1. lépés Az alapvető SAML-konfiguráció szerkesztése](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Az alapvető SAML konfigurációs beállítások szerkesztéséhez válassza a **Szerkesztés** ikont (ceruzát) az **Egyszerű SAML-konfiguráció** szakasz jobb felső sarkában.

1. Adja meg az alábbi beállításokat. Az értékeket az alkalmazás szállítójától kell beszereznie. Manuálisan is megadhatja az értékeket, vagy feltölthet egy metaadatfájlt a mezők értékének kibontásához.

    | Alapvető SAML konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
    |:--|:--|:--|:--|
    | **Azonosító (entitásazonosító)** | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedileg azonosítja az alkalmazást. Az Azure AD elküldi az azonosítót az alkalmazásnak az SAML-token Közönség paramétereként. Az alkalmazás várhatóan érvényesíti azt. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik. Adjon meg egy URL-címet,<subdomain>amely a következő mintát használja: "https:// .contoso.com" Ezt az értéket az *alkalmazás által küldött **AuthnRequest** (SAML-kérelem) **kiállítóelemeként** találja.* |
    | **Válasz URL-címe** | Kötelező | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. A további válasz URL-mezőivel több válasz URL-címet adhat meg. Előfordulhat például, hogy több altartományhoz további válaszURL-címekre van szükség. Tesztelési célokra egyszerre több válasz URL-t is megadhat (helyi állomás és nyilvános URL-ek). |
    | **Bejelentkezési URL** | Kötelező | Ne adja meg | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-címet használja az alkalmazás elindításához az Office 365-ből vagy az Azure AD access panelről. Ha üres, az Azure AD idP által kezdeményezett bejelentkezést hajt végre, amikor egy felhasználó elindítja az alkalmazást az Office 365-ből, az Azure AD access panelről vagy az Azure AD egyszeri bejelentkezés URL-címéről.|
    | **Továbbítási állapot** | Optional | Optional | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe. Egyes alkalmazások azonban eltérően használják ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.
    | **Kijelentkezés URL-címe** | Optional | Optional | Az SAML kijelentkezési válaszok visszaküldésének az alkalmazásnak való elküldésére szolgál.

További információ: [Single sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>2. lépés Felhasználói attribútumok és jogcímek konfigurálása 

Amikor egy felhasználó hitelesíti magát az alkalmazás, az Azure AD kiadja az alkalmazás egy SAML-jogkivonat ot információkat (vagy jogcímeket) a felhasználóról, aki egyedileg azonosítja őket. Alapértelmezés szerint ez az információ tartalmazza a felhasználó felhasználónevét, e-mail címét, utónevét és vezetéknevét. Előfordulhat, hogy testre kell szabnia ezeket a jogcímeket, ha például az alkalmazásnak speciális jogcímértékekre vagy a felhasználónévtől eltérő **névformátumra** van szüksége. A galériaalkalmazások követelményeit az [alkalmazásspecifikus oktatóanyagok ismertetik,](../saas-apps/tutorial-list.md)vagy megkérdezheti az alkalmazás forgalmazóját. A felhasználói attribútumok és jogcímek konfigurálásának általános lépéseit az alábbiakban ismertetjük.

1. A **Felhasználói attribútumok és jogcímek** csoportban válassza a **Szerkesztés** ikont (ceruzát) a jobb felső sarokban.

   ![2. lépés Felhasználói attribútumok és jogcímek konfigurálása](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Ellenőrizze a **névazonosító értékét**. Az alapértelmezett érték a *user.principalname*. A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

3. A **névazonosító értékének**módosításához válassza a **Névazonosító értéke** mező **Szerkesztés** ikonját (ceruzát). Szükség szerint módosítsa az azonosító formátumát és forrását. További információt a [Névazonosító szerkesztése című témakörben talál.](../develop/active-directory-saml-claims-customization.md#editing-nameid) Mentse a módosításokat, ha végzett. 
 
4. A csoportjogcímek konfigurálásához jelölje ki a **jogcímmezőben visszaadott csoportok** **Szerkesztés** ikonját. További információt a [Csoportjogcímek konfigurálása című témakörben talál.](../hybrid/how-to-connect-fed-group-claims.md)

5. Jogcím hozzáadásához válassza az **Új jogcím hozzáadása** lehetőséget a lap tetején. Írja be a **Nevet,** és válassza ki a megfelelő forrást. Ha az **Attribútumforrást választja,** ki kell választania a használni kívánt **Forrás attribútumot.** Ha a **Fordítás forrást választja,** akkor ki kell választania a használni kívánt **átalakítást** és **1-es paramétert.** További információt az [Alkalmazásspecifikus jogcímek hozzáadása .For](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)details, see Adding application-specific claims . Mentse a módosításokat, ha végzett. 

6. Kattintson a **Mentés** gombra. Az új jogcím megjelenik a táblázatban.

   > [!NOTE]
   > Az AZURE AD-től az alkalmazásig az SAML-token testreszabásának további módjait az alábbi forrásokban.
   >- Egyéni szerepkörök létrehozása az Azure Portalon keresztül, [lásd: Szerepkörjogcímek konfigurálása.](../develop/active-directory-enterprise-app-role-management.md)
   >- A jogcímek testreszabásához a PowerShellen keresztül című témakörben a [Jogcímek testreszabása – PowerShell című témakörben.](../develop/active-directory-claims-mapping.md)
   >- Az alkalmazásjegyzék módosításához az alkalmazás választható jogcímének konfigurálásához olvassa el [a Választható jogcímek konfigurálása című témakört.](../develop/active-directory-optional-claims.md)
   >- A frissítési jogkivonatok, a hozzáférési jogkivonatok, a munkamenet-jogkivonatok és az azonosító jogkivonatok tokenélettartamának beállításához olvassa el a [Token élettartamának konfigurálása](../develop/active-directory-configurable-token-lifetimes.md)című témakört. Vagy ha korlátozni szeretné a hitelesítési munkameneteket az Azure AD feltételes hozzáférésén keresztül, olvassa el a [hitelesítési munkamenetek kezelési képességei című témakört.](https://go.microsoft.com/fwlink/?linkid=2083106)

## <a name="step-3-manage-the-saml-signing-certificate"></a>3. lépés Az SAML aláíró tanúsítvány kezelése

Az Azure AD egy tanúsítványt használ az alkalmazásnak küldött SAML-jogkivonatok aláírásához. Ehhez a tanúsítványhoz az Azure AD és az alkalmazás közötti megbízhatóság beállításához. A tanúsítvány formátumáról az alkalmazás SAML dokumentációjában olvashat. További információt az [Összevont egyszeri bejelentkezés tanúsítványainak kezelése](manage-certificates-for-federated-single-sign-on.md) és az [SAML-jogkivonat Speciális tanúsítvány-aláírási beállításai című témakörben](certificate-signing-options.md)talál.

Az Azure AD-ből letöltheti az aktív tanúsítványt Base64 vagy Raw formátumban közvetlenül a fő **beállítása egyszeri bejelentkezés SAML-lappal.** Azt is megteheti, hogy az aktív tanúsítványt az alkalmazás metaadat-XML-fájljának letöltésével vagy az alkalmazásösszevonás metaadat-URL-címének használatával szerezheti be. A (aktív vagy inaktív) tanúsítványok megtekintéséhez, létrehozásához vagy letöltéséhez kövesse az alábbi lépéseket.

1. Nyissa meg az **SAML aláíró tanúsítvány szakaszt.** 

   ![3. lépés Az SAML aláíró tanúsítvány kezelése](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Ellenőrizze, hogy a tanúsítvány rendelkezik-e:

   - *A kívánt lejárati dátum.* A lejárati dátumot legfeljebb három évre konfigurálhatja a jövőben.
   - *A kívánt tanúsítvány aktív állapota.* Ha az állapot **inaktív,** módosítsa az állapotot **Aktív**állásra. Az állapot módosításához kattintson a jobb gombbal a kívánt tanúsítvány sorára, és válassza a **Tanúsítvány aktívvá váltása parancsot.**
   - *A helyes aláírási lehetőség és algoritmus.*
   - *A helyes értesítési e-mail cím(ek).* Ha az aktív tanúsítvány közel van a lejárati dátumhoz, az Azure AD értesítést küld az ebben a mezőben konfigurált e-mail címre.

2. A tanúsítvány letöltéséhez válassza a Base64 formátum, a Nyers formátum vagy az Összevonási metaadat-XML beállításainak egyikét. Az Azure AD az **App Federation metaadat-url-címét** is biztosítja, ahol `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`az alkalmazásra jellemző metaadatokat a formátumban érheti el.

3. Tanúsítvány kezeléséhez, létrehozásához vagy importálásához jelölje ki a **Szerkesztés** ikont (ceruzát) az **SAML aláíró tanúsítvány** szakasz jobb felső sarkában.

   ![SAML aláíró tanúsítvány](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Tegye az alábbi műveletek egyikét:

   - Új tanúsítvány létrehozásához válassza az **Új tanúsítvány**lehetőséget, jelölje be a **Lejárati dátumot,** majd kattintson a Mentés **gombra.** A tanúsítvány aktiválásához válassza a helyi menüt (**...**) és válassza a **Tanúsítvány aktívvá bírása**lehetőséget.
   - Ha személyes kulccsal és pfx hitelesítő adatokkal szeretne feltölteni egy tanúsítványt, válassza **a Tanúsítvány importálása** lehetőséget, és keresse meg a tanúsítványt. Írja be a **PFX-jelszót**, majd válassza **a Hozzáadás**lehetőséget.  
   - A speciális tanúsítványaláírási beállítások konfigurálásához használja az alábbi beállításokat. A beállítások leírását a [Speciális tanúsítványaláírási beállítások](certificate-signing-options.md) című cikkismerteti.
      - Az **Aláírási lehetőség** legördülő listában válassza az **SamL-válasz aláírása**, **Az SAML-állítás aláírása**vagy az **SAML-válasz és -állítás aláírása**lehetőséget.
      - Az **Aláíró algoritmus** legördülő listában válassza az **SHA-1** vagy az **SHA-256 lehetőséget.**
   - Ha további személyeket szeretne értesíteni, ha az aktív tanúsítvány a lejárati dátuma közelében van, írja be az e-mail címeket az **Értesítés e-mail címek** mezőben.

4. Ha módosításokat hajtott végre, válassza a **Mentés** gombot az **SAML aláíró tanúsítvány** szakasz tetején. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4. lépés Az alkalmazás beállítása az Azure AD használatára

Az ** \<ApplicationName beállítása>** szakasz felsorolja azokat az értékeket, amelyeket konfigurálni kell az alkalmazásban, így saml-identitásszolgáltatóként fogja használni az Azure AD-t. A szükséges értékek az alkalmazástól függően változnak. További információt az alkalmazás SAML dokumentációjában talál. A dokumentáció megkereséséhez nyissa meg az **Alkalmazásnév beállítása \<>** címsort, és válassza a Részletes utasítások megtekintése **lehetőséget.** A dokumentáció a **Bejelentkezés konfigurálása** lapon jelenik meg. Ez a lap végigvezeti a **bejelentkezési URL-** és **Azure-ad-azonosító**és a ** \<kijelentkezési** **URL-értékek** kitöltésében az alkalmazásnév beállítása>címsorban.

1. Görgessen le az **alkalmazásnév \<beállítása>** szakaszig. 
   
   ![4. lépés Az alkalmazás beállítása](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Szükség szerint másolja az értéket az adott szakasz egyes soraiból, és kövesse az alkalmazásspecifikus utasításokat az alkalmazáshoz való hozzáadásához. A galériaalkalmazások esetében a dokumentációt a **Nézet lépésenkénti útmutató**kiválasztásával tekintheti meg. 
   - A **bejelentkezési URL-cím** és **a kijelentkezési URL-értékek** egyaránt feloldódik ugyanazzal a végponttal, amely az Azure AD-példány SAML kérelemkezelő végpontja. 
   - Az **Azure AD-azonosító** **az** alkalmazásnak kiadott SAML-jogkivonat kiállítójának értéke.
2. Miután az összes értéket beillesztette a megfelelő mezőkbe, válassza a **Mentés lehetőséget.**

## <a name="step-5-validate-single-sign-on"></a>5. lépés Egyszeri bejelentkezés ellenőrzése

Miután konfigurálta az alkalmazást, hogy az Azure AD-t SAML-alapú identitásszolgáltatóként használja, tesztelheti a beállításokat, hogy ellenőrizze, hogy az egyszeri bejelentkezés működik-e a fiókjában. 

2. Görgessen az **Egyszeri bejelentkezés <applicationName> ellenőrzése szakaszhoz.**

   ![5. lépés Egyszeri bejelentkezés ellenőrzése](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Válassza az **Érvényesítés** lehetőséget. Megjelennek a tesztelési beállítások.

4. Válassza **a Bejelentkezés jelenlegi felhasználóként**lehetőséget. 

Ha a bejelentkezés sikeres, készen áll arra, hogy felhasználókat és csoportokat rendeljen az SAML-alkalmazáshoz.
Ha hibaüzenet jelenik meg, hajtsa végre az alábbi lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Válassza **a Felbontásútmutató beszereznie**lehetőséget. Megjelenik a kiváltó ok és a felbontásra vonatkozó útmutatás.  Ebben a példában a felhasználó nincs hozzárendelve az alkalmazáshoz.

3. Olvassa el a megoldási útmutatót, majd ha lehetséges, javítsa ki a problémát.

4. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

További információ: [Debug SAML-based single sign-on to applications in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>További lépések

- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus kiépítésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
