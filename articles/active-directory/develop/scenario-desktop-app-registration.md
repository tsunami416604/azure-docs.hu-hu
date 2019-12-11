---
title: Webes API-kat meghívó asztali alkalmazás regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (alkalmazás-regisztráció)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f7f2dfdbf404a092773857a0f7727618cd429a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965534"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó asztali alkalmazás – alkalmazás regisztrálása

Ez a cikk az asztali alkalmazások alkalmazás-regisztrálási sajátosságait ismerteti.

## <a name="supported-accounts-types"></a>Támogatott fiókok típusai

Az asztali alkalmazásban támogatott fióktípus a felvenni kívánt élménytől függ. A kapcsolat miatt a támogatott fióktípus a használni kívánt folyamatoktól függ.

### <a name="audience-for-interactive-token-acquisition"></a>Célközönség az interaktív jogkivonat-beszerzéshez

Ha az asztali alkalmazás interaktív hitelesítést használ, bármilyen [fióktípus](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)használatával bejelentkezhet a felhasználókba.

### <a name="audience-for-desktop-app-silent-flows"></a>Hallgatóság az asztali alkalmazások csendes folyamataihoz

- Ha integrált Windows-hitelesítést vagy felhasználónevet/jelszót szeretne használni, az alkalmazásnak be kell jelentkeznie a saját bérlő (LOB-fejlesztő) vagy az Azure Active Directory-szervezetek (ISV-forgatókönyv) felhasználói számára. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókjaiban.
- Ha az eszköz kódját szeretné használni, a felhasználók nem jelentkezhetnek be a Microsoft személyes fiókjaival.
- Ha a felhasználók a B2C-hatóságot és a szabályzatot áthaladó közösségi identitásokkal jelentkeznek be, akkor csak az interaktív és a username-Password hitelesítést használhatja.

## <a name="redirect-uris"></a>Átirányítási URI azonosítók

Az asztali alkalmazásban használandó átirányítási URI-k a használni kívánt folyamattól függenek.

- Ha az **interaktív hitelesítés** vagy az **eszköz kódjának folyamatát**használja, érdemes `https://login.microsoftonline.com/common/oauth2/nativeclient`használni. Ezt a konfigurációt úgy érheti el, ha a megfelelő URL-címre kattint az alkalmazás **hitelesítés** szakaszában.
  
  > [!IMPORTANT]
  > A mai MSAL.NET egy másik átirányítási URI-t használ alapértelmezés szerint a Windows rendszeren futó asztali alkalmazásokban (`urn:ietf:wg:oauth:2.0:oob`). A jövőben módosítani szeretnénk ezt az alapértelmezett értéket, ezért javasoljuk, hogy használja a `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Ha macOS-hez készült natív Objective-C vagy SWIFT-alkalmazást készít, a redirectUri az alkalmazás köteg-azonosítója alapján kell regisztrálnia a következő formátumban: **msauth. < a. app. Bundle. id >://auth** (cserélje le < a. app. Bundle. id > az alkalmazás Bundle azonosítójával)
- Ha az alkalmazás csak az integrált Windows-hitelesítést vagy-felhasználónevet/jelszót használja, nem kell regisztrálnia az alkalmazás átirányítási URI-JÁT. Ezek a folyamatok a Microsoft Identity platform 2.0-s verziójának végpontját jelentik, és az alkalmazás nem hívható vissza semmilyen konkrét URI-ra.
- Ha meg szeretné különböztetni az eszköz kódjának folyamatát, az integrált Windows-hitelesítést, valamint egy olyan bizalmas ügyfélalkalmazás-folyamathoz tartozó felhasználónevet és jelszót, amely nem rendelkezik átirányítási URI-k használatával (a démon-alkalmazásokban használt ügyfél-hitelesítési folyamat), meg kell adnia, hogy az alkalmazás egy nyilvános ügyfélalkalmazás. A konfiguráció eléréséhez nyissa meg az alkalmazás **hitelesítés** szakaszát. Ezt követően a **Speciális beállítások** alszakasz **alapértelmezett ügyfél típusa** csoportjában válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként való kezelésére**szolgáló kérdésnél.

  ![Nyilvános ügyfél engedélyezése](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-engedélyek

Asztali alkalmazások hívás API-kat a bejelentkezett felhasználó számára. Delegált engedélyeket kell kérniük. Azonban nem igényelhetnek alkalmazás-engedélyeket, amelyek csak [Daemon-alkalmazásokban](scenario-daemon-overview.md)vannak kezelve.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás konfigurálása](scenario-desktop-app-configuration.md)
