---
title: Asztali alkalmazás, hogy a hívások webes API-k (alkalmazásregisztráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, hogy a hívások webes API-k (alkalmazásregisztráció)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076060"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Asztali alkalmazás, hogy a hívások webes API-k – alkalmazás regisztrálása

Ez a cikk egy asztali alkalmazás az alkalmazás regisztrációs sajátosságainak tartalmazza.

## <a name="supported-accounts-types"></a>Támogatott fiókok típusok

A fióktípus esetében támogatott az asztali alkalmazás érheti el a kívánt világos készül függenek, és a folyamatok ezért szeretné használni.

### <a name="audience-for-interactive-token-acquisition"></a>Interaktív token beszerzése a célközönség

Ha az asztali alkalmazás interaktív hitelesítést használ, bejelentkezhet bármely felhasználó [fiók típusa](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Asztali alkalmazás beavatkozás nélküli folyamatok célközönség

- Integrált Windows-hitelesítés vagy a felhasználónév/jelszó használni szeretne, ha az alkalmazása szükséges, a felhasználók a saját bérlőben (ÜZLETÁGI fejlesztő), vagy az Azure Active directory-szervezet (ISV-forgatókönyv). Ezek a hitelesítési folyamatok személyes Microsoft-fiókok nem támogatottak.
- Ha az eszköz kódfolyamat használni kívánt, nem tud bejelentkezni a személyes Microsoft-fiókkal rendelkező felhasználók még
- Ha a felhasználó bejelentkezik egy szolgáltató B2C-szabályzat, és közösségi identitású, csak a a interaktív és a felhasználónév-jelszó-hitelesítést használjon.

## <a name="redirect-uris"></a>Átirányítási URI azonosítók

Az átirányítási URI-k, az asztali alkalmazás használatához újra a használni kívánt folyamat függ.

- Az interaktív hitelesítéshez használja, ha szeretné használni `https://login.microsoftonline.com/common/oauth2/nativeclient`. Ez a konfiguráció megfelelő URL-cím kattintva érhet el a **hitelesítési** szakaszban az alkalmazás
  
  > [!IMPORTANT]
  > Még ma MSAL.NET használ egy másik átirányítási URI-t alapértelmezés szerint a Windows rendszerű asztali alkalmazások (`urn:ietf:wg:oauth:2.0:oob`). A későbbiekben szeretnénk lesz az alapértelmezés módosításáról, és ezért azt javasoljuk, hogy használja `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Ha az alkalmazás csak integrált Windows-hitelesítés, felhasználónév/jelszó vagy eszköz kód Flow, nem kell regisztrálnia az alkalmazás átirányítási URI-t. Sőt ezek a folyamatok ne adatváltási, a Microsoft identity platform v2.0-végpont, és az alkalmazás vissza nem hívható meg minden olyan egyedi URI. Annak érdekében, hogy megkülönböztetni azokat a bizalmas ügyfél alkalmazás folyamatot, amely nem rendelkezik átirányítási URI-k vagy (az ügyfél hitelesítő adat flow démon alkalmazásokban használt), express, hogy az alkalmazás-e nyilvános az ügyfélalkalmazások kell. Címen érhető el ebben a konfigurációban a **hitelesítési** az alkalmazáshoz, és a szakasz a **speciális beállítások** alszakaszt, válassza a **Igen**, kérdésre **Kezelni alkalmazás nyilvános ügyfél** (az a **ügyféltípus alapértelmezett** bekezdés)

  ![Nyilvános ügyfél engedélyezése](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-engedélyek

Asztali alkalmazások API-k hívása a bejelentkezett felhasználó nevében. Delegált engedélyek kéréséhez szükséges. Alkalmazásengedélyek nem kérnek (amely csak kezeli [démon alkalmazások](scenario-daemon-overview.md))

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás - alkalmazások konfigurálása](scenario-desktop-app-configuration.md)
