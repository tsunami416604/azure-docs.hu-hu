---
title: Ügyfélalkalmazások (Microsoft-hitelesítési tár) |} Az Azure
description: További információ a nyilvános és bizalmas ügyfélprogram alkalmazások a Microsoft hitelesítési tár (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430826"
---
# <a name="public-client-and-confidential-client-applications"></a>Nyilvános ügyfél és a bizalmas ügyfélalkalmazások
A Microsoft-hitelesítési tár (MSAL) határozza meg az ügyfelek kétféle: nyilvános ügyfelek és a bizalmas ügyfelek. A két ügyféltípusok különbözteti meg a biztonságos hitelesítést az engedélyezési kiszolgálón, és bizalmasan ügyfél hitelesítő adataikkal tudnak. Ezzel szemben az Azure AD Authentication Library (ADAL) használ, úgynevezett *hitelesítési környezetet* (amely a kapcsolatot az Azure AD).

- **Bizalmas ügyfélalkalmazások** olyan alkalmazások, amelyeket (webalkalmazások, webes API-alkalmazások vagy akár szolgáltatás/démon alkalmazások) kiszolgálókon futnak. Azokat a rendszer hozzáférést, és ezért nehéz alkalmas arra, hogy egy alkalmazás titkos kulcs. Bizalmas ügyfelek konfigurációs idejű titkos kulcsokat tárolhat. Minden példánya az ügyfél rendelkezik egy különálló konfigurációval (beleértve az ügyfél-Azonosítóját és ügyfélkulcsát). Ezek az értékek nehezen a végfelhasználók számára, csomagolja ki. Webes alkalmazás a leggyakrabban használt bizalmas ügyfél. Az ügyfél-azonosító van közzétéve a webböngészőn keresztül, de a titkos kulcs csak a biztonsági csatorna átadott, és soha nem közvetlenül elérhető.

    Bizalmas ügyfélalkalmazások: <BR>
    ![Webalkalmazás](media/msal-client-applications/web-app.png) ![webes API-t](media/msal-client-applications/web-api.png) ![démon vagy szolgáltatás](media/msal-client-applications/daemon-service.png)

- **Nyilvános ügyfélalkalmazások** eszközök vagy asztali számítógépeken, vagy egy webes böngésző a futó alkalmazások. Ezek még nem megbízható, biztonságos tartani az alkalmazások titkos adatait, így azok csak webes API-k elérésére a felhasználó nevében. (Csak nyilvános client flow támogatja azokat.) Nyilvános ügyfelek nem tartsa, konfiguráció-idő tárolt titkos adatok, hogy ne kelljen ügyfél titkos kulcsok.

    Nyilvános ügyfélalkalmazások: <BR>
    ![Asztali alkalmazás](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilalkalmazás](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Az MSAL.js nem a nyilvános és bizalmas ügyfélalkalmazások szétválasztása nem.  MSAL.js felhasználói ügynök-alapú alkalmazásokat, a nyilvános ügyfelek, amelyben az Ügyfélkód hajtja végre a felhasználói ügynök, például egy webes böngésző az ügyfélalkalmazások jelöli. Ezek az ügyfelek ne tároljon titkos kulcsok, mert némelyik érhető el a böngésző környezetet.

## <a name="comparing-the-client-types"></a>Az ügyfél-típusok összehasonlítása
Íme néhány hasonlóságait és különbségeit nyilvános ügyfél és a bizalmas ügyfél alkalmazások:

- Mindkét típusú alkalmazás felhasználói jogkivonat gyorsítótár karbantartása, és lekérheti a jogkivonat csendes (Ha a jogkivonat már a jogkivonat a gyorsítótárban). Bizalmas ügyfélalkalmazások is, hogy egy alkalmazás tokengyorsítótárral jogkivonatokat, amelyek az alkalmazás lehetőségeit.
- Mindkét típusú alkalmazás felhasználói fiókok kezelése és lekérése egy fiókot a felhasználói jogkivonat gyorsítótárból, get-fiók annak azonosítója vagy fiók eltávolítása.
- Nyilvános ügyfélalkalmazások (négy hitelesítési folyamatok) jogkivonat-beszerzési négy módszer lesz. Bizalmas ügyfélalkalmazások háromféleképpen egy token beszerzéséhez (és egyik módja az URL-címét az identitásszolgáltató számítási authorize végponton). További információkért lásd: [-jogkivonatok beszerzésének](msal-acquire-cache-tokens.md).

Ha már használt adal-t, előfordulhat, hogy megfigyelheti, hogy, ellentétben az adal-t a hitelesítési környezetet, a MSAL az ügyfél-azonosító (más néven a *Alkalmazásazonosító* vagy *Alkalmazásazonosító*) átadott egyszer, az alkalmazás felépítése. Ennek nem kell újra átadandó, amikor az alkalmazás tokenbeolvasás. Ez a nyilvános és a bizalmas ügyfél alkalmazásokhoz egyaránt. Az alkalmazások bizalmas ügyfél konstruktorok is továbbítódnak az ügyfél-hitelesítő adatok: a titok identitásszolgáltató osztanak.

## <a name="next-steps"></a>További lépések
További információ:
- [Ügyféloldali alkalmazás konfigurációs beállításai](msal-client-application-configuration.md)
- [Ügyfélalkalmazások hárítható el MSAL.NET használatával](msal-net-initializing-client-applications.md)
- [Ügyfélalkalmazások hárítható el MSAL.js használatával](msal-js-initializing-client-applications.md)
