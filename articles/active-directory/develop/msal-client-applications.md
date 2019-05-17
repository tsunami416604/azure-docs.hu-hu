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
ms.openlocfilehash: 9e0300ec0ef4ee67b06acb85514ae898bbd0a830
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544296"
---
# <a name="public-client-and-confidential-client-applications"></a>Nyilvános ügyfél és a bizalmas ügyfélalkalmazások
A Microsoft-hitelesítési tár (MSAL) határozza meg az ügyfelek kétféle: nyilvános ügyfelek és a bizalmas ügyfelek. A két ügyféltípusok különbözteti meg a biztonságos hitelesítést az engedélyezési kiszolgálón, és bizalmasan ügyfél hitelesítő adataikkal tudnak.  Ezzel szemben az Azure AD Authentication Library (ADAL) rendelkezik a hitelesítési környezetet (amely egy kapcsolatot az Azure AD) fogalmát.

- **Bizalmas ügyfélalkalmazások** olyan alkalmazások, a kiszolgálók (webalkalmazások, webes API-t vagy akár szolgáltatás/démon alkalmazások). Nehéz a hozzáférést, és így tudja tartani az alkalmazás titkos minősülnek. Bizalmas ügyfelek képesek konfigurációs idő titkos kulcsok tárolásához. Az ügyfél minden példánya (beleértve a clientId és a titkos kulcsot) egy különálló konfigurációs van. Ezek az értékek nehezen a végfelhasználók számára, csomagolja ki. Webes alkalmazás a leggyakrabban használt bizalmas ügyfél. Az ügyfél-azonosító van közzétéve a webböngészőn keresztül, de a titkos kulcs csak a biztonsági csatorna átadott, és soha nem közvetlenül elérhető.

    Bizalmas ügyfélalkalmazások: <BR>
    ![Webalkalmazás](media/msal-client-applications/web-app.png) ![webes API-t](media/msal-client-applications/web-api.png) ![démon vagy szolgáltatás](media/msal-client-applications/daemon-service.png)

- **Nyilvános ügyfélalkalmazások** olyan alkalmazások, amelyek eszközök vagy asztali gépeken, vagy egy webböngészőben. Nincsenek megbízható, biztonságos tartani a titkos alkalmazáskulcsok, és emiatt csakis azokhoz a webes API-k (csak támogatják a nyilvános client flow) a felhasználó nevében. Nyilvános ügyfelek nem tudnak konfigurációs idő titkos kulcsok tárolására, és ennek eredményeképpen nincs titkos Ügyfélkód.

    Nyilvános ügyfélalkalmazások: <BR>
    ![Asztali alkalmazás](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilalkalmazás](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Az MSAL.js nem a nyilvános és bizalmas ügyfélalkalmazások szétválasztása nem.  MSAL.js jelöli ügyfélalkalmazások felhasználói ügynök-alapú alkalmazásként, amelyben az Ügyfélkód hajtja végre a felhasználó például egy webes böngésző ügynök egy nyilvános ügyfél.  Ezek az ügyfelek ne tárolja a titkos adatait, mert némelyik érhető el a böngésző környezetet.

## <a name="comparing-the-client-types"></a>Az ügyfél-típusok összehasonlítása
Alkalmazások is vannak bizonyos commonalities és nyilvános ügyfél és a bizalmas ügyfél közötti különbségeket:

- Mindkét típusú alkalmazások karbantartásához a felhasználói jogkivonatok gyorsítótárát, és a csendes (azokban az esetekben, ahol a jogkivonatot már szerepel a tokengyorsítótárral) tokenek beszerzésére is. Bizalmas ügyfélalkalmazások is jogkivonatokat, amelyek az alkalmazás maga az alkalmazás tokengyorsítótárral.
- Mindkét felhasználói fiókok kezelése és a fiókok beolvasása a felhasználói jogkivonat gyorsítótárból, get-fiók annak azonosítója vagy fiók eltávolítása.
- Nyilvános ügyfélalkalmazások négy módszert a tokenbeolvasás (négy hitelesítési folyamatok), mivel az ügyfélalkalmazások bizalmas három (és egy módszert az URL-címét az identitásszolgáltató számítási authorize végponton) rendelkezik. További információkért tekintse meg a forgatókönyvek és -jogkivonatok beszerzésének.

Ha korábban használt adal-t, Észreveheti, hogy ellentétesen adal-t a hitelesítési környezetet, MSAL az ügyfél-azonosító (más néven alkalmazás vagy alkalmazás azonosítója) egyszer átadott, a fejlesztés, az alkalmazást, és már nem kell ismételni, ha egy token beszerzése a. Ez a helyzet mindkét nyilvános és a bizalmas ügyfélalkalmazások esetében. Bizalmas ügyfélalkalmazások konstruktorok is továbbítódnak az ügyfél-hitelesítő adatok: a titok identitásszolgáltató osztanak.

## <a name="next-steps"></a>További lépések
További információ:
- [Ügyféloldali alkalmazás konfigurációs beállításai](msal-client-application-configuration.md)
- [Hárítható el az MSAL.NET használatával ügyfélalkalmazások](msal-net-initializing-client-applications.md).
- [Az ügyfélalkalmazások MSAL.js használatával hárítható el](msal-js-initializing-client-applications.md).
