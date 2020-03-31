---
title: Microsoft identity platform & SAML bemutatóra állítás folyamat | Azure
description: Megtudhatja, hogy miként olvashat le adatokat a Microsoft Graph-ból anélkül, hogy a felhasználótól hitelesítő adatokat kérne az SAML-tulajdonosi helyességi folyamat használatával.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700209"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft identity platform és OAuth 2.0 SAML-bemutató jatmonthordozó kontinuetási folyamat
Az OAuth 2.0 SAML-bemutató feltételfolyamat lehetővé teszi, hogy egy SamL-feltétel használatával OAuth hozzáférési jogkivonatot kérjen, ha egy ügyfélnek meglévő megbízhatósági kapcsolatot kell használnia. Az SAML-állításra alkalmazott aláírás biztosítja az engedélyezett alkalmazás hitelesítését. Az SAML-feltétel egy identitásszolgáltató által kiadott és a szolgáltató által felhasznált XML-biztonsági jogkivonat. A szolgáltató a tartalomra támaszkodik, hogy a védelem tárgyát biztonsági okokból azonosítsa.

Az SAML-állítás fel van írva az OAuth token végpontra.  A végpont feldolgozza a helyesbítést, és az alkalmazás előzetes jóváhagyása alapján hozzáférési jogkivonatot ad ki. Az ügyfél nem szükséges, hogy egy frissítési jogkivonatot, és nem az ügyfél titkos kulcsot kell átadni a jogkivonat végpontra.

Az SAML-tulajdonosi helyességi feltétel folyamat akkor hasznos, ha adatokat kér a Microsoft Graph API-kból (amelyek csak delegált engedélyeket támogatnak) anélkül, hogy a felhasználótól hitelesítő adatokat kérne. Ebben az esetben az ügyfél hitelesítő adatok megadása, amely előnyben részesíti a háttérfolyamatok, nem működik.

Olyan alkalmazások esetében, amelyek interaktív böngészőalapú bejelentkezéssel kapnak saml-kijelentést, majd hozzáférést szeretnének hozzáadni egy OAuth védett API-hoz (például a Microsoft Graphhoz), oauth-kérelmet tehet ünk fel az API-hoz való hozzáférési jogkivonat lekéréséhez. Amikor a böngésző tavaszat az Azure AD a felhasználó hitelesítéséhez, a böngésző felveszi a munkamenetet az SAML bejelentkezés, és a felhasználónak nem kell megadnia a hitelesítő adatait.

Az OAuth SAML-bemutató helyességi feltétel folyamat is támogatott a felhasználók hitelesítése identitásszolgáltatók, például az Active Directory összevonási szolgáltatások (ADFS) az Azure Active Directory ba.  Az ADFS-ből kapott SAML-állítás oauth-folyamatban használható a felhasználó hitelesítéséhez.

![OAuth-áramlás](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Hívásdiagram SAML-bemutatóra állítással
Most nézzük meg, hogyan tudjuk ténylegesen letölteni SAML állítás programatikusan. Ezt a módszert az ADFS teszteli. Ez azonban minden olyan identitásszolgáltatóval működik, amely támogatja az SAML-helyességi feltétel visszaadását programszerűen. Az alapvető folyamat a következő: saml-állítás lekérése, hozzáférési jogkivonat beszerzése és a Microsoft Graph elérése.

### <a name="prerequisites"></a>Előfeltételek

Hozzon létre megbízhatósági kapcsolatot az engedélyezési kiszolgáló/környezet (Microsoft 365) és az identitásszolgáltató, illetve az SAML 2.0 tulajdonosi feltétel (ADFS) kibocsátója között. Az ADFS egyszeri bejelentkezéshez és identitásszolgáltatóként történő konfigurálásához olvassa el [ezt a cikket.](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)

Regisztrálja az alkalmazást a [portálon:](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
1. Jelentkezzen be a [portál alkalmazásregisztrációs paneljén](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Kérjük, vegye figyelembe, hogy a v2.0 végpontokat használjuk a Graph API-hoz, és ezért regisztrálnia kell az alkalmazást ezen a portálon. Ellenkező esetben használhattuk volna a regisztrációkat az Azure active directoryban). 
1. Válassza **az Új regisztráció lehetőséget.**
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait: 
    1. **Név** – az alkalmazás felhasználói számára megjelenített, kifejező alkalmazásnevet adjon meg.
    1. **Támogatott fióktípusok** – válassza ki, hogy mely fiókokat szeretné támogatni az alkalmazásában.
    1. **Átirányítási URI (nem kötelező)** – Válassza ki az általad épített alkalmazás típusát, a webet vagy a nyilvános ügyfelet (mobil & asztali számítógépen), majd adja meg az alkalmazás átirányítási URI-ját (vagy válasz URL-címét).
    1. Miután végzett, válassza a **Regisztrálás** lehetőséget.
1. Jegyezze fel az alkalmazás (ügyfél) azonosítóját.
1. A bal oldali ablaktáblában válassza **a Tanúsítványok & titkos kulcsok**lehetőséget. Kattintson az **Új ügyféltitok** elemre az **Ügyféltitok** szakaszban. Másolja az új ügyféltitkot, akkor nem lesz képes letölteni, amikor elhagyja a panelt.
1. A bal oldali ablaktáblában válassza az **API-engedélyek lehetőséget,** majd **adja hozzá az engedélyt.** Válassza a **Microsoft Graph**lehetőséget, majd **a delegált engedélyeket,** majd a **Tasks.read** lehetőséget, mivel az Outlook Graph API-t kívánjuk használni. 

Telepítse [a Postman-t,](https://www.getpostman.com/)amely a mintakérelmek teszteléséhez szükséges.  Később a kérelmeket kóddá alakíthatja.

### <a name="get-the-saml-assertion-from-adfs"></a>Az SAML-állítás beszerzése az ADFS-től
Hozzon létre egy POST-kérelmet az ADFS-végponthoz SOAP-borítékhasználatával az SAML-állítás beolvasásához:

![SAML-állítás beszerzése](./media/v2-saml-bearer-assertion/2.png)

Fejlécértékek:

![Fejlécértékek](./media/v2-saml-bearer-assertion/3.png)

ADFS kérelem törzse:

![ADFS-kérelem törzse](./media/v2-saml-bearer-assertion/4.png)

A kérelem sikeres feladása után saml-kijelentést kell kapnia az ADFS-től. Csak az **SAML:Helyességifeltétel-címcím** adatai szükségesek, konvertálja base64 kódolássá, hogy további kérésekben is fellegyen használva.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Az OAuth2 token beszerzése az SAML-állítás használatával 
Ebben a lépésben egy OAuth2-jogkivonatot kell lekérniaz ADFS-kérelemválasz használatával.

1. Hozzon létre egy POST-kérelmet a fejlécértékekkel együtt az alábbiak szerint:

    ![POST a kérés](./media/v2-saml-bearer-assertion/5.png)
1. A kérelem törzsében cserélje ki **a client_id,** **client_secret**és **az állítást** (a base64 kódolású SAML-állítás az előző lépést kapta meg):

    ![A kérés törzse](./media/v2-saml-bearer-assertion/6.png)
1. Sikeres kérésre kap egy hozzáférési jogkivonatot az Azure active directoryból.

### <a name="get-the-data-with-the-oauth-token"></a>Az adatok beszereznie az Oauth-tokenlel

A hozzáférési jogkivonat fogadása után hívja meg a Graph API-kat (ebben a példában az Outlook-feladatok). 

1. Hozzon létre egy GET-kérelmet az előző lépésben lekért hozzáférési jogkivonattal:

    ![GET kérés](./media/v2-saml-bearer-assertion/7.png)

1. Sikeres kérésre JSON-választ kap.

## <a name="next-steps"></a>További lépések

Ismerje meg a különböző [hitelesítési folyamatokat és alkalmazásforgatókönyveket.](authentication-flows-app-scenarios.md)