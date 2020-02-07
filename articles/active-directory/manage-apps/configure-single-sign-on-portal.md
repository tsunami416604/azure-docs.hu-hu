---
title: Egyszeri bejelentkezés konfigurálása – Azure Active Directory | Microsoft Docs
description: Ez az oktatóanyag az Azure Portalon konfigurál SAML-alapú egyszeri bejelentkezést egy alkalmazáshoz az Active Directory (Azure AD) használatával.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063492"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés konfigurálása

Miután hozzáadta az alkalmazást az Azure AD vállalati alkalmazásaihoz, konfigurálhatja az egyszeri bejelentkezési beállításokat. Ez a cikk azt ismerteti, hogyan konfigurálhatja az SAML-alapú egyszeri bejelentkezést egy nem katalógusbeli alkalmazáshoz. 

> [!NOTE]
> Katalógusalkalmazást szeretne hozzáadni? A [SaaS app oktatóanyagok listájában](../saas-apps/tutorial-list.md) részletes beállítási utasításokat talál

Ha az egyszeri bejelentkezést egy nem katalógusbeli alkalmazáshoz szeretné beállítani *kód írása nélkül*, előfizetéssel vagy prémium szintű Azure ad kell rendelkeznie, és az alkalmazásnak támogatnia kell az SAML 2,0-et. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el az [Azure ad díjszabására](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Előkészületek

- Ha az alkalmazás nem lett hozzáadva az Azure AD-bérlőhöz, tekintse meg [a Gallery-alkalmazás hozzáadása](add-gallery-app.md) vagy [a nem Gallery-alkalmazás hozzáadása](add-non-gallery-app.md)című témakört.
- Forduljon az alkalmazás gyártójához, és szerezze be a megfelelő információkat a következő beállításokhoz:

    | Alapszintű SAML konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
    |:--|:--|:--|:--|
    | Azonosító (entitásazonosító) | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedi módon azonosítja az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálja. Az Azure AD elküldi az azonosítót az alkalmazásnak az SAML-jogkivonat célközönségi paramétereként. Az alkalmazásnak el kell érvényesíteni. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik. *Ezt az értéket megkeresheti az alkalmazás által elküldhető **AuthnRequest** (SAML-kérelem) **kiállító** elemeként.* |
    | Válasz URL-cím | Optional | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. |
    | Bejelentkezési URL-cím | Kötelező | Nincs megadva | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-cím használatával indítja el az alkalmazást az Office 365 vagy az Azure AD hozzáférési paneljén. Ha üres, az Azure AD az identitás-szolgáltatóra támaszkodva elindítja az egyszeri bejelentkezést, amikor egy felhasználó elindítja az alkalmazást.|
    | Továbbítási állapot | Optional | Optional | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe. Néhány alkalmazás azonban eltérő módon használja ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.
    | Kijelentkezési URL | Optional | Optional | Az SAML-kijelentkezési válaszok visszaküldésére szolgál az alkalmazásnak.

## <a name="step-1-edit-the-basic-saml-configuration"></a>1\. lépés Az alapszintű SAML-konfiguráció szerkesztése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) Felhőbeli alkalmazás-rendszergazdaként vagy az Azure ad-bérlőhöz tartozó alkalmazás-rendszergazdaként.

1. Navigáljon **Azure Active Directory** > **vállalati alkalmazások** elemre, és válassza ki az alkalmazást a listából. 
   
   - Az alkalmazás kereséséhez az **alkalmazás típusa** menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra. Adja meg az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

1. A **kezelés** szakaszban válassza az **egyszeri bejelentkezés**lehetőséget. 

1. Válassza az **SAML**lehetőséget. Megjelenik az **egyszeri bejelentkezés beállítása az SAML-előnézettel** oldalon.

1. Az alapszintű SAML-konfigurációs beállítások szerkesztéséhez kattintson a **szerkesztési** ikonra (egy ceruza) az **alapszintű SAML-konfigurációs** szakasz jobb felső sarkában.

     ![Tanúsítványok konfigurálása](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. A megfelelő mezőkben adja meg az [első lépések](#before-you-begin) szakaszban leírt információkat.

1. A lap tetején válassza a **Mentés**lehetőséget.

## <a name="step-2-configure-user-attributes-and-claims"></a>2\. lépés Felhasználói attribútumok és jogcímek konfigurálása 

Előfordulhat, hogy egy alkalmazás adott felhasználói attribútumokat vagy jogcímeket igényel az Azure AD-től kapott SAML-tokenben, amikor a felhasználó bejelentkezik. Például konkrét jogcím-URI-k vagy jogcím-értékek szükségesek, vagy a **névnek** a Microsoft Identity platformon tárolt felhasználónévtől eltérőnek kell lennie. A Gallery-alkalmazásokra vonatkozó követelményeket az [alkalmazásspecifikus oktatóanyagok](../saas-apps/tutorial-list.md)ismertetik, vagy megkérheti az alkalmazás gyártóját is. A felhasználói attribútumok és jogcímek konfigurálásának általános lépései alább olvashatók.

1. A **felhasználói attribútumok és jogcímek** szakaszban válassza a jobb felső sarokban található **Szerkesztés** ikont (ceruza).

1. Ellenőrizze a **név azonosító értékét**. Az alapértelmezett érték a *User. egyszerű név*. A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

1. A **név azonosító értékének**módosításához válassza a **név-azonosító érték** mező **Szerkesztés** ikonját (ceruza). Szükség szerint végezze el a megfelelő módosításokat az azonosító formátumán és a forráson. Részletekért lásd: [NameId szerkesztése](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Mentse a módosításokat, ha elkészült. 
 
1. A csoportos jogcímek konfigurálásához válassza a jogcím mezőben **visszaadott csoportok** **Szerkesztés** ikonját. Részletekért lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md).

3. Jogcímek hozzáadásához válassza az **új jogcím hozzáadása** lehetőséget az oldal tetején. Adja meg a **nevet** , és válassza ki a megfelelő forrást. Ha kiválasztja az **attribútum** forrását, ki kell választania a használni kívánt **forrás-attribútumot** . Ha kiválasztja a **fordítási** forrást, ki kell választania a használni kívánt **átalakítást** és **1. paramétert** . Részletekért lásd: [alkalmazásspecifikus jogcímek hozzáadása](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Mentse a módosításokat, ha elkészült. 

4. Kattintson a **Mentés** gombra. Az új jogcím megjelenik a táblában.

   > [!NOTE]
   > Az SAML-token Azure AD-ből az alkalmazásba való testreszabásának további módjaiért lásd az alábbi forrásokat.
   >- Ha egyéni szerepköröket szeretne létrehozni a Azure Portal keresztül, tekintse meg a [szerepkör-jogcímek konfigurálása](../develop/active-directory-enterprise-app-role-management.md)című részt.
   >- A jogcímek PowerShell használatával történő testreszabásával kapcsolatban lásd: [jogcímek testreszabása – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Ha módosítani szeretné az alkalmazás jegyzékfájlját az alkalmazás választható jogcímeinek konfigurálásához, tekintse meg a [választható jogcímek konfigurálása](../develop/active-directory-optional-claims.md)című témakört.
   >- A jogkivonat élettartamára vonatkozó szabályzatok frissítési jogkivonatok, hozzáférési tokenek, munkamenet-tokenek és azonosító tokenek beállításával kapcsolatban lásd: [jogkivonat-élettartamok konfigurálása](../develop/active-directory-configurable-token-lifetimes.md). Ha az Azure AD feltételes hozzáférés használatával szeretné korlátozni a hitelesítési munkameneteket, tekintse meg a [hitelesítési munkamenetek kezelési képességeit](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>3\. lépés Az SAML aláíró tanúsítvány kezelése

Az Azure AD egy tanúsítványt használ az alkalmazásnak küldött SAML-tokenek aláírásához. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon megtekintheti vagy letöltheti az aktív tanúsítványt. Emellett tanúsítványt is frissíthet, létrehozhat vagy importálhat. A katalógusbeli alkalmazások esetében a tanúsítvány formátumával kapcsolatos részletek az alkalmazás SAML-dokumentációjában érhetők el (lásd az [alkalmazásra vonatkozó oktatóanyagokat](../saas-apps/tutorial-list.md)). 

1. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt. Az alkalmazás típusától függően megtekintheti a tanúsítvány Base64 formátumban, nyers formátumban vagy az összevonási metaadatok XML-ben való letöltésének lehetőségeit. Az Azure AD az alkalmazás- **összevonási metaadatok URL-címét** is megadja, ahol a `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`formátumban érheti el az alkalmazáshoz tartozó metaadatokat.

1. A tanúsítványok kezeléséhez, létrehozásához vagy importálásához jelölje ki a **szerkesztési** ikont (egy ceruza) az **SAML aláíró tanúsítványa** szakasz jobb felső sarkában, majd tegye a következők egyikét:

   - Új tanúsítvány létrehozásához válassza az **új tanúsítvány**lehetőséget, válassza ki a **lejárati dátumot**, majd kattintson a **Mentés**gombra. A tanúsítvány aktiválásához válassza a helyi menüt ( **...** ), majd válassza a **tanúsítvány aktívvá tétele**lehetőséget.
   - Titkos kulccsal és pfx hitelesítő adatokkal rendelkező tanúsítvány feltöltéséhez válassza a **tanúsítvány importálása** lehetőséget, és keresse meg a tanúsítványt. Adja meg a **pfx-jelszót**, majd kattintson a **Hozzáadás**gombra.  
   - A speciális tanúsítvány-aláírási beállítások konfigurálásához használja a következő beállításokat. Ezen beállítások leírását a [speciális tanúsítvány-aláírási beállítások](certificate-signing-options.md) című cikkben találja.
      - Az **aláírási lehetőség** legördülő listában válassza az **SAML-válasz aláírása**, az **SAML-érvényesítés**aláírása vagy az SAML- **Válasz és-állítás aláírása**lehetőséget.
      - Az **aláírási algoritmus** legördülő listában válassza az **SHA-1** vagy az **SHA-256**elemet.
   - Ha további személyeket szeretne értesíteni, amikor az aktív tanúsítvány a lejárati dátum közelében van, adja meg az e-mail címeket az **értesítő e-mail címek** mezőben.

1. Válassza a **Mentés** lehetőséget az **SAML aláíró tanúsítványa** szakasz tetején. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4\. lépés Az alkalmazás beállítása az Azure AD használatára

A **\<applicationName > beállítása** szakasz azokat az értékeket sorolja fel, amelyeket az alkalmazásban kell konfigurálni, hogy az Azure ad-t SAML-identitás-szolgáltatóként fogja használni. A szükséges értékek az alkalmazástól függően változnak. Részletekért tekintse meg az alkalmazás SAML-dokumentációját.

1. Görgessen le a **\<applicationName > beállítása** szakaszhoz. 
2. Szükség szerint másolja a szakasz egyes sorainak értékét, és kövesse az alkalmazáshoz tartozó érték hozzáadására vonatkozó alkalmazásspecifikus útmutatást. A Gallery-alkalmazások esetében megtekintheti a dokumentációt a **részletes utasítások megtekintése**lehetőség kiválasztásával. 
   - A **bejelentkezési URL-cím** és a **KIJELENTKEZÉSI URL-cím** mindkét esetben ugyanazt a végpontot oldja fel, amely az Azure ad-példány SAML-kérelmének kezelési végpontja. 
   - Az **Azure ad-azonosító** az alkalmazás számára kiállított SAML-token **kiállítójának** értéke.
1. Amikor beillesztette az összes értéket a megfelelő mezőkbe, válassza a **Mentés**lehetőséget.

## <a name="step-5-validate-single-sign-on"></a>5\. lépés Egyszeri bejelentkezés ellenőrzése

Készen áll arra, hogy tesztelje a beállításokat, és ellenőrizze, hogy az egyszeri bejelentkezés működik-e, a rendszergazda.  

1. Nyissa meg az alkalmazás egyszeri bejelentkezési beállításait. 
2. Görgessen az **egyszeri bejelentkezés ellenőrzése <applicationName>** szakaszhoz. Ebben az oktatóanyagban ez a szakasz a **GitHub-test beállítását hívja meg**.
3. Válassza a **teszt**lehetőséget. Megjelennek a tesztelési beállítások.
4. Válassza **a bejelentkezés aktuális felhasználóként**lehetőséget. 

Ha a bejelentkezés sikeres, készen áll a felhasználók és csoportok hozzárendelésére az SAML-alkalmazáshoz.
Ha hibaüzenet jelenik meg, hajtsa végre a következő lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![A megoldási útmutatóhoz használja a "mit csinál a hiba kinézete" mezőt](media/configure-single-sign-on-portal/error-guidance.png)

1. Válassza a **megoldás lekérése útmutatást**. Megjelenik az alapvető ok és a megoldási útmutató.  Ebben a példában a felhasználó nem lett hozzárendelve az alkalmazáshoz.
1. Olvassa el a megoldási útmutatót, és ha lehetséges, javítsa ki a problémát.
1. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
