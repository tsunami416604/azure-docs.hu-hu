---
title: Microsoft Identity platform & SAML-tulajdonos állítási folyamata | Azure
description: Megtudhatja, hogyan kérhet le adatokat a Microsoft Graphról anélkül, hogy az SAML-tulajdonosra vonatkozó állítási folyamatot használó hitelesítő adatokat kérjen a felhasználótól.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: e0db5bec00ce864536b3559eda160acdada5e157
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114693"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity platform és OAuth 2,0 SAML-tulajdonos állítási folyamata
A OAuth 2,0 SAML-tulajdonos állítási folyamata lehetővé teszi, hogy egy SAML-kijelentéssel igényeljen egy OAuth hozzáférési tokent, ha az ügyfélnek meglévő megbízhatósági kapcsolatot kell használnia. Az SAML-kijelentésre alkalmazott aláírás a hitelesítő alkalmazás hitelesítését biztosítja. Az SAML-jogcímek egy, az identitás-szolgáltató által kiadott és a szolgáltató által használt XML biztonsági jogkivonat. A szolgáltató a tartalomra támaszkodva azonosítja az állítás tárgyát a biztonsággal kapcsolatos célokra.

Az SAML-jogkivonatot a rendszer az OAuth jogkivonat-végponton küldi el.  A végpont dolgozza fel az állítást, és egy hozzáférési jogkivonatot bocsát ki az alkalmazás előzetes jóváhagyása alapján. Az ügyfélnek nem kell frissítési tokent használnia vagy tárolnia, sem a jogkivonat-végpontnak átadandó ügyfél-titkos kulcsot.

Az SAML-tulajdonosi állítási folyamat akkor hasznos, ha Microsoft Graph API-ból származó adatokat kér le (amelyek csak a delegált engedélyeket támogatják) anélkül, hogy a felhasználónak kellene megadnia a hitelesítő adatokat. Ebben a forgatókönyvben az ügyfél hitelesítő adatainak megadása, amely a háttérben futó folyamatok esetében ajánlott, nem működik.

Olyan alkalmazások esetében, amelyek interaktív, böngészőalapú bejelentkezést biztosítanak az SAML-érvényesítéshez, majd hozzá kívánják adni a hozzáférést egy OAuth által védett API-hoz (például Microsoft Graph), létrehozhat egy OAuth kérelmet az API hozzáférési jogkivonatának beszerzéséhez. Ha a böngésző átirányítja az Azure AD-ba a felhasználó hitelesítéséhez, a böngésző az SAML-bejelentkezésből veszi fel a munkamenetet, és a felhasználónak nem kell megadnia a hitelesítő adatait.

A OAuth SAML-tulajdonos állítási folyamata is támogatott az olyan identitás-szolgáltatókkal való hitelesítést végző felhasználók számára, mint a Active Directory összevonási szolgáltatások (AD FS) (ADFS) összevont Azure Active Directory.  Az Active Directory összevonási szolgáltatásból beszerzett SAML-állítás a felhasználó hitelesítéséhez használható OAuth-folyamatokban.

![OAuth folyamat](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Gráf hívása az SAML-tulajdonosi állítással
Most tudassa velünk, hogy miként lehet ténylegesen beolvasni az SAML-állítások programozott módon. Ezt a megközelítést az ADFS teszteli. Ez azonban minden olyan identitás-szolgáltatóval működik, amely támogatja az SAML-programozott módon visszaküldését. Az alapszintű folyamat: SAML-jogcímek beszerzése, hozzáférési token beszerzése és Microsoft Graph elérése.

### <a name="prerequisites"></a>Előfeltételek

Hozzon létre megbízhatósági kapcsolatot az engedélyezési kiszolgáló/környezet (Microsoft 365) és az identitás-szolgáltató, illetve az SAML 2,0 tulajdonosi jogcíme (ADFS) számára. Az ADFS egyszeri bejelentkezéshez és identitás-szolgáltatóként való konfigurálásához tekintse meg [ezt a cikket](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Az alkalmazás regisztrálása a [portálon](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Jelentkezzen be a [portál alkalmazás-regisztráció](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) paneljére (vegye figyelembe, hogy a Graph API v 2.0-s végpontját használjuk, ezért regisztrálni kell az alkalmazást ebben a portálon. Ellenkező esetben a regisztrációkat használták az Azure Active Directoryban. 
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait: 
    1. **Név** – az alkalmazás felhasználói számára megjelenített, kifejező alkalmazásnevet adjon meg.
    1. **Támogatott fióktípusok** – válassza ki, hogy mely fiókokat szeretné támogatni az alkalmazásában.
    1. **Átirányítási URI (nem kötelező)** – válassza ki az Ön által felépített, webes vagy nyilvános ügyfélprogramot (Mobile & Desktop), majd adja meg az alkalmazás ÁTirányítási URI-ját (vagy válasz URL-címét).
    1. Miután végzett, válassza a **Regisztrálás** lehetőséget.
1. Jegyezze fel az alkalmazás (ügyfél) AZONOSÍTÓját.
1. A bal oldali ablaktáblán válassza a **tanúsítványok & Secrets**elemet. Az **ügyfél titkai** szakaszban kattintson az **új ügyfél titka** elemre. Másolja az új ügyfél titkos kulcsát, és nem fogja tudni lekérni a panel elhagyásakor.
1. A bal oldali panelen válassza az **API-engedélyek** lehetőséget, majd **adjon hozzá egy engedélyt**. Válassza a **Microsoft Graph**, majd a **delegált engedélyek**elemet, majd válassza a **feladatok lehetőséget. olvassa el** , mivel az Outlook Graph APIt szeretné használni. 

Telepítse a [Poster](https://www.getpostman.com/)eszközt, amely a mintavételi kérelmek teszteléséhez szükséges.  Később átalakíthatja a kéréseket a kódra.

### <a name="get-the-saml-assertion-from-adfs"></a>SAML-állítás beszerzése az ADFS-ből
Hozzon létre egy POST-kérelmet az ADFS-végponthoz SOAP-boríték használatával az SAML-kijelentés beolvasásához:

![SAML-kijelentés beolvasása](./media/v2-saml-bearer-assertion/2.png)

Fejléc értékei:

![Fejléc értékei](./media/v2-saml-bearer-assertion/3.png)

ADFS-kérelem törzse:

![ADFS-kérelem törzse](./media/v2-saml-bearer-assertion/4.png)

A kérelem sikeres közzétételekor az ADFS-től származó SAML-állítást kell kapnia. Csak az **SAML: Kijelentési** kódelemek szükségesek, a további kérések használatához alakítsa át Base64 kódolásra.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Az OAuth2-jogkivonat beszerzése az SAML-kijelentéssel 
Ebben a lépésben egy OAuth2 jogkivonatot kell beolvasnia az ADFS-kijelentési válasz használatával.

1. Hozzon létre egy POST-kérelmet az alább látható módon a fejléc értékeivel:

    ![POST kérelem](./media/v2-saml-bearer-assertion/5.png)
1. A kérelem törzsében cserélje le a **client_id**, **client_secret**és az **állítást** (a Base64 kódolású SAML-állítás az előző lépést szerezte be):

    ![A kérés törzse](./media/v2-saml-bearer-assertion/6.png)
1. Sikeres kérelem esetén hozzáférési jogkivonatot fog kapni az Azure Active Directoryból.

### <a name="get-the-data-with-the-oauth-token"></a>Az OAuth jogkivonattal rendelkező adatlekérdezés

A hozzáférési jogkivonat kézhezvétele után hívja meg a Graph API-kat (ebben a példában az Outlook-feladatok). 

1. Hozzon létre egy GET kérelmet az előző lépésben beolvasott hozzáférési jogkivonattal:

    ![GET kérés](./media/v2-saml-bearer-assertion/7.png)

1. A sikeres kérelem után JSON-választ fog kapni.

## <a name="next-steps"></a>További lépések

További információ a különböző [hitelesítési folyamatokról és alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md).