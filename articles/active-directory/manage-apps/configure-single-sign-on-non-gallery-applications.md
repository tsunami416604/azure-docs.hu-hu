---
title: SAML egyszeri bejelentkezés – nem katalógusbeli alkalmazások – Microsoft Identity platform | Microsoft Docs
description: Egyszeri bejelentkezés (SSO) konfigurálása a nem katalógusbeli alkalmazásokhoz a Microsoft Identity platformon (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cee2b9a0ea32a3b331849263c8a97f55930542d
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024231"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>A katalógusban nem szereplő alkalmazásokba történő SAML-alapú egyszeri bejelentkezés konfigurálása

Ha [egy Gallery-alkalmazást](add-gallery-app.md) vagy egy [nem Gallery-webalkalmazást](add-non-gallery-app.md) AD hozzá az Azure ad vállalati alkalmazásaihoz, az egyik elérhető egyszeri bejelentkezési lehetőség az [SAML-alapú egyszeri bejelentkezés](what-is-single-sign-on.md#saml-sso). Ha lehetséges, az SAML-protokollok egyikével hitelesítő alkalmazások esetében válassza az SAML lehetőséget. Az SAML egyszeri bejelentkezéssel az Azure AD hitelesíti az alkalmazást a felhasználó Azure AD-fiókjának használatával. Az Azure AD a bejelentkezési adatokat kapcsolati protokollon keresztül továbbítja az alkalmazásnak. A felhasználókat az SAML-jogcímek által meghatározott szabályok alapján rendelheti hozzá adott alkalmazási szerepkörökhöz. Ez a cikk azt ismerteti, hogyan konfigurálhatja az SAML-alapú egyszeri bejelentkezést egy nem katalógusbeli alkalmazáshoz. 

> [!NOTE]
> Katalógusalkalmazást szeretne hozzáadni? A [SaaS app oktatóanyagok listájában](../saas-apps/tutorial-list.md) részletes beállítási utasításokat talál

Ha az SAML egyszeri bejelentkezést egy nem katalógusbeli alkalmazáshoz szeretné beállítani kód írása nélkül, rendelkeznie kell Azure AD-előfizetéssel, és az alkalmazásnak támogatnia kell az SAML 2,0-et. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el az [Azure ad díjszabására](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás nem lett hozzáadva az Azure AD-bérlőhöz, tekintse meg [a nem Gallery-alkalmazás hozzáadása](add-non-gallery-app.md)című témakört.

## <a name="step-1-edit-the-basic-saml-configuration"></a>1. lépés Az alapszintű SAML-konfiguráció szerkesztése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) Felhőbeli alkalmazás-rendszergazdaként vagy az Azure ad-bérlőhöz tartozó alkalmazás-rendszergazdaként.

2. Navigáljon **Azure Active Directory**  >  **vállalati alkalmazások** elemre, és válassza ki az alkalmazást a listából. 
   
   - Az alkalmazás kereséséhez az **alkalmazás típusa** menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra. Adja meg az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

3. A **kezelés** szakaszban válassza az **egyszeri bejelentkezés**lehetőséget. 

   - Vegye figyelembe, hogy vannak olyan helyzetek, amikor az **egyszeri bejelentkezési** beállítás nem lesz jelen. Ha például az alkalmazás a **Alkalmazásregisztrációk** használatával lett regisztrálva, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint a OIDC OAuth használatára van beállítva. Ebben az esetben az **egyszeri bejelentkezési** lehetőség nem jelenik meg a **vállalati alkalmazások**alatt a navigációban. Ha a **Alkalmazásregisztrációk** használatával adja hozzá az egyéni alkalmazást, a jegyzékfájlban konfigurálhatja a beállításokat. További információ a jegyzékfájlról: ( https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) . További információ az SSO-szabványokról: ( https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) . Egyéb forgatókönyvek, amelyekben az **egyszeri bejelentkezés** hiányzik a navigálásból, ha egy alkalmazás egy másik bérlőn fut, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás-rendszergazda vagy az egyszerű szolgáltatásnév tulajdonosa). Az engedélyek olyan eseteket is okozhatnak, ahol megnyithatja az **egyszeri bejelentkezést** , de nem fogja tudni menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Válassza az **SAML**lehetőséget. Megjelenik az **egyszeri bejelentkezés beállítása az SAML-előnézettel** oldalon.

   ![1. lépés az alapszintű SAML-konfiguráció szerkesztése](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Az alapszintű SAML-konfigurációs beállítások szerkesztéséhez kattintson a **szerkesztési** ikonra (egy ceruza) az **alapszintű SAML-konfigurációs** szakasz jobb felső sarkában.

1. Adja meg az alábbi beállításokat. Az alkalmazás gyártójától kell beolvasnia az értékeket. Manuálisan is megadhatja az értékeket, vagy feltöltheti a metaadatokat a mezők értékének kinyeréséhez.

    | Alapszintű SAML konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Description |
    |:--|:--|:--|:--|
    | **Azonosító (entitásazonosító)** | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedileg azonosítja az alkalmazást. Az Azure AD elküldi az azonosítót az alkalmazásnak az SAML-jogkivonat célközönségi paramétereként. Az alkalmazásnak el kell érvényesíteni. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik. Adjon meg egy URL-címet, amely a következő mintát használja: "https:// <subdomain> . contoso.com". *Ez az érték az alkalmazás által elküldhető **AuthnRequest** (SAML-kérelem) **kiállító** elemeként is megkereshető* . |
    | **Válasz URL-címe** | Kötelező | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. A további válasz URL-címek mezővel több válasz URL-címet is megadhat. Előfordulhat például, hogy több altartományhoz is szüksége van további válasz URL-címekre. Vagy tesztelési célból egyszerre több válasz URL-címet (helyi gazdagépet és nyilvános URL-címeket) is megadhat. |
    | **Bejelentkezési URL-cím** | Kötelező | Nincs megadva | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-cím használatával indítja el az alkalmazást az Office 365 vagy az Azure AD hozzáférési paneljén. Ha üres, az Azure AD identitásszolgáltató-alapú bejelentkezést hajt végre, amikor egy felhasználó elindítja az alkalmazást az Office 365, az Azure AD hozzáférési paneljén vagy az Azure AD SSO URL-címén.|
    | **Továbbítási állapot** | Választható | Választható | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe. Néhány alkalmazás azonban eltérő módon használja ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.
    | **Kijelentkezési URL-cím** | Választható | Választható | Az SAML-kijelentkezési válaszok visszaküldésére szolgál az alkalmazásnak.

További információ: [egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>2. lépés Felhasználói attribútumok és jogcímek konfigurálása 

Amikor egy felhasználó hitelesíti magát az alkalmazásban, az Azure AD egy SAML-jogkivonatot bocsát ki a felhasználótól, amely egyedileg azonosítja azokat. Alapértelmezés szerint ez az információ tartalmazza a felhasználó felhasználónevét, e-mail-címét, utónevét és vezetéknevét. Előfordulhat, hogy testre kell szabnia ezeket a jogcímeket, ha például az alkalmazásnak konkrét jogcím-értékeket vagy a felhasználónévtől eltérő **nevet** kell megadni. A Gallery-alkalmazásokra vonatkozó követelményeket az [alkalmazásspecifikus oktatóanyagok](../saas-apps/tutorial-list.md)ismertetik, vagy megkérheti az alkalmazás gyártóját is. A felhasználói attribútumok és jogcímek konfigurálásának általános lépései alább olvashatók.

1. A **felhasználói attribútumok és jogcímek** szakaszban válassza a jobb felső sarokban található **Szerkesztés** ikont (ceruza).

   ![2. lépés – felhasználói attribútumok és jogcímek konfigurálása](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Ellenőrizze a **név azonosító értékét**. Az alapértelmezett érték a *User. egyszerű név*. A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

3. A **név azonosító értékének**módosításához válassza a **név-azonosító érték** mező **Szerkesztés** ikonját (ceruza). Szükség szerint végezze el a megfelelő módosításokat az azonosító formátumán és a forráson. Részletekért lásd: [NameId szerkesztése](../develop/active-directory-saml-claims-customization.md#editing-nameid). Mentse a módosításokat, ha elkészült. 
 
4. A csoportos jogcímek konfigurálásához válassza a jogcím mezőben **visszaadott csoportok** **Szerkesztés** ikonját. Részletekért lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md).

5. Jogcímek hozzáadásához válassza az **új jogcím hozzáadása** lehetőséget az oldal tetején. Adja meg a **nevet** , és válassza ki a megfelelő forrást. Ha kiválasztja az **attribútum** forrását, ki kell választania a használni kívánt **forrás-attribútumot** . Ha kiválasztja a **fordítási** forrást, ki kell választania a használni kívánt **átalakítást** és **1. paramétert** . Részletekért lásd: [alkalmazásspecifikus jogcímek hozzáadása](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Mentse a módosításokat, ha elkészült. 

6. Kattintson a **Mentés** gombra. Az új jogcím megjelenik a táblában.

   > [!NOTE]
   > Az SAML-token Azure AD-ből az alkalmazásba való testreszabásának további módjaiért lásd az alábbi forrásokat.
   >- Ha egyéni szerepköröket szeretne létrehozni a Azure Portal keresztül, tekintse meg a [szerepkör-jogcímek konfigurálása](../develop/active-directory-enterprise-app-role-management.md)című részt.
   >- A jogcímek PowerShell használatával történő testreszabásával kapcsolatban lásd: [jogcímek testreszabása – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Ha módosítani szeretné az alkalmazás jegyzékfájlját az alkalmazás választható jogcímeinek konfigurálásához, tekintse meg a [választható jogcímek konfigurálása](../develop/active-directory-optional-claims.md)című témakört.
   >- A jogkivonat élettartamára vonatkozó szabályzatok frissítési jogkivonatok, hozzáférési tokenek, munkamenet-tokenek és azonosító tokenek beállításával kapcsolatban lásd: [jogkivonat-élettartamok konfigurálása](../develop/active-directory-configurable-token-lifetimes.md). Ha az Azure AD feltételes hozzáférés használatával szeretné korlátozni a hitelesítési munkameneteket, tekintse meg a [hitelesítési munkamenetek kezelési képességeit](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>3. lépés Az SAML aláíró tanúsítvány kezelése

Az Azure AD egy tanúsítványt használ az alkalmazásnak küldött SAML-tokenek aláírásához. Erre a tanúsítványra van szüksége az Azure AD és az alkalmazás közötti megbízhatósági kapcsolat beállításához. A tanúsítvány formátumával kapcsolatos részletekért tekintse meg az alkalmazás SAML-dokumentációját. További információ: [tanúsítványok kezelése összevont egyszeri bejelentkezéshez](manage-certificates-for-federated-single-sign-on.md) és [speciális tanúsítvány-aláírási beállítások az SAML-jogkivonatban](certificate-signing-options.md).

Az Azure AD-ből az aktív tanúsítvány Base64 vagy RAW formátumban tölthető le közvetlenül az **SAML-t tartalmazó fő set egyszeri bejelentkezéssel** . Azt is megteheti, hogy az aktív tanúsítványt letölti az alkalmazás metaadatainak XML-fájljából, vagy az alkalmazás-összevonási metaadatok URL-címét használja. Ha szeretné megtekinteni, létrehozni vagy letölteni a tanúsítványokat (aktív vagy inaktív), kövesse az alábbi lépéseket.

1. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt. 

   ![3. lépés az SAML aláíró tanúsítvány kezelése](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Ellenőrizze, hogy a tanúsítvány rendelkezik-e:

   - *A kívánt lejárati dátum.* A lejárati dátumot akár három évig is beállíthatja a jövőbe.
   - *A kívánt tanúsítvány aktív állapotának állapota.* Ha az állapot **inaktív**, módosítsa az állapotot **aktív**értékre. Az állapot módosításához kattintson a jobb gombbal a kívánt tanúsítvány sorára, majd válassza a **tanúsítvány aktívvá tétele**lehetőséget.
   - *A helyes aláírási beállítás és algoritmus.*
   - *A helyes értesítő e-mail-cím (ek).* Ha az aktív tanúsítvány közel van a lejárati dátumhoz, az Azure AD értesítést küld az ebben a mezőben konfigurált e-mail-címre.

2. A tanúsítvány letöltéséhez válassza ki a Base64 formátum, a nyers formátum vagy az összevonási metaadatok XML-fájljának egyik lehetőségét. Az Azure AD az alkalmazás- **összevonási metaadatok URL-címét** is tartalmazza, ahol az alkalmazáshoz tartozó metaadatokat a következő formátumban érheti el: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

3. A tanúsítványok kezeléséhez, létrehozásához vagy importálásához válassza az **SAML aláíró tanúsítvány** szakasz jobb felső sarkában található **Szerkesztés** ikont (ceruza).

   ![SAML-aláíró tanúsítvány](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Végezze el az alábbi műveletek bármelyikét:

   - Új tanúsítvány létrehozásához válassza az **új tanúsítvány**lehetőséget, válassza ki a **lejárati dátumot**, majd kattintson a **Mentés**gombra. A tanúsítvány aktiválásához válassza a helyi menüt (**...**), majd válassza a **tanúsítvány aktívvá tétele**lehetőséget.
   - Titkos kulccsal és pfx hitelesítő adatokkal rendelkező tanúsítvány feltöltéséhez válassza a **tanúsítvány importálása** lehetőséget, és keresse meg a tanúsítványt. Adja meg a **pfx-jelszót**, majd kattintson a **Hozzáadás**gombra.  
   - A speciális tanúsítvány-aláírási beállítások konfigurálásához használja a következő beállításokat. Ezen beállítások leírását a [speciális tanúsítvány-aláírási beállítások](certificate-signing-options.md) című cikkben találja.
      - Az **aláírási lehetőség** legördülő listában válassza az **SAML-válasz aláírása**, az **SAML-érvényesítés**aláírása vagy az SAML- **Válasz és-állítás aláírása**lehetőséget.
      - Az **aláírási algoritmus** legördülő listában válassza az **SHA-1** vagy az **SHA-256**elemet.
   - Ha további személyeket szeretne értesíteni, amikor az aktív tanúsítvány a lejárati dátum közelében van, adja meg az e-mail címeket az **értesítő e-mail címek** mezőben.

4. Ha módosította a módosításokat, válassza a **Mentés** lehetőséget az **SAML aláíró tanúsítványa** szakasz tetején. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4. lépés Az alkalmazás beállítása az Azure AD használatára

A **beállítás \<applicationName> ** szakasz felsorolja azokat az értékeket, amelyeket konfigurálni kell az alkalmazásban, hogy az Azure ad-t SAML-identitás-szolgáltatóként fogja használni. A szükséges értékek az alkalmazástól függően változnak. Részletekért tekintse meg az alkalmazás SAML-dokumentációját. A dokumentáció megkereséséhez nyissa **meg a címsor \<application name> beállítása** elemet, és válassza a **részletes utasítások megtekintése**lehetőséget. A dokumentáció a bejelentkezési oldal **konfigurálása** lapon jelenik meg. Ezen a lapon megtekintheti a **bejelentkezési URL-cím**, az **Azure ad-azonosító**és a **kijelentkezési URL-** ** \<application name> ** értékek kitöltését a beállítás fejlécében.

1. Görgessen le a **beállítás \<applicationName> ** szakaszhoz. 
   
   ![4. lépés az alkalmazás beállítása](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Szükség szerint másolja a szakasz egyes sorainak értékét, és kövesse az alkalmazáshoz tartozó érték hozzáadására vonatkozó alkalmazásspecifikus útmutatást. A Gallery-alkalmazások esetében megtekintheti a dokumentációt a **részletes utasítások megtekintése**lehetőség kiválasztásával. 
   - A **bejelentkezési URL-cím** és a **KIJELENTKEZÉSI URL-cím** mindkét esetben ugyanazt a végpontot oldja fel, amely az Azure ad-példány SAML-kérelmének kezelési végpontja. 
   - Az **Azure ad-azonosító** az alkalmazás számára kiállított SAML-token **kiállítójának** értéke.
2. Amikor beillesztette az összes értéket a megfelelő mezőkbe, válassza a **Mentés**lehetőséget.

## <a name="step-5-validate-single-sign-on"></a>5. lépés Egyszeri bejelentkezés ellenőrzése

Ha úgy konfigurálta az alkalmazást, hogy az Azure AD-t SAML-alapú identitás-szolgáltatóként használja, a beállítások tesztelésével ellenőrizheti, hogy az egyszeri bejelentkezés működik-e a fiókjával. 

2. Görgessen az **egyszeri bejelentkezés <applicationName> ellenőrzése** szakaszhoz.

   ![5. lépés: az egyszeri bejelentkezés ellenőrzése](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Válassza az **Érvényesítés** lehetőséget. Megjelennek a tesztelési beállítások.

4. Válassza **a bejelentkezés aktuális felhasználóként**lehetőséget. 

Ha a bejelentkezés sikeres, készen áll a felhasználók és csoportok hozzárendelésére az SAML-alkalmazáshoz.
Ha hibaüzenet jelenik meg, hajtsa végre a következő lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Válassza a **megoldás lekérése útmutatást**. Megjelenik az alapvető ok és a megoldási útmutató.  Ebben a példában a felhasználó nem lett hozzárendelve az alkalmazáshoz.

3. Olvassa el a megoldási útmutatót, és ha lehetséges, javítsa ki a problémát.

4. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

További információ: [SAML-alapú egyszeri bejelentkezés hibakeresése Azure Active Directory-alkalmazásokban](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>További lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
