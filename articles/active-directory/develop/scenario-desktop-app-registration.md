---
title: Webes API-kat meghívó asztali alkalmazások regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (alkalmazás-regisztráció)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885157"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó asztali alkalmazás: alkalmazás regisztrálása

Ez a cikk az asztali alkalmazások alkalmazás-regisztrálási sajátosságait ismerteti.

## <a name="supported-account-types"></a>Támogatott fióktípusok

Az asztali alkalmazásokban támogatott fióktípus attól függ, hogy milyen élményre van szüksége. A kapcsolat miatt a támogatott fióktípus a használni kívánt folyamatoktól függ.

### <a name="audience-for-interactive-token-acquisition"></a>Célközönség az interaktív jogkivonat-beszerzéshez

Ha az asztali alkalmazás interaktív hitelesítést használ, bármilyen [fióktípus](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)használatával bejelentkezhet a felhasználókba.

### <a name="audience-for-desktop-app-silent-flows"></a>Hallgatóság az asztali alkalmazások csendes folyamataihoz

- Ha integrált Windows-hitelesítést vagy felhasználónevet és jelszót szeretne használni, az alkalmazásnak be kell jelentkeznie a saját bérlőben lévő felhasználókba, például ha Ön üzletági (LOB) fejlesztő. Vagy Azure Active Directory szervezetekben az alkalmazásnak saját bérlőben kell bejelentkeznie a felhasználókba, ha az ISV-forgatókönyv. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókjaiban.
- Ha az eszköz kódját szeretné használni, a felhasználók nem jelentkezhetnek be a Microsoft személyes fiókjaival.
- Ha a felhasználók olyan közösségi identitásokkal jelentkeznek be, amelyek átadják a vállalattól a kereskedelmi (B2C) hatóságot és a szabályzatot, akkor csak az interaktív és a Felhasználónév-jelszó típusú hitelesítést használhatja.

## <a name="redirect-uris"></a>Átirányítási URI-k

Az asztali alkalmazásokban használandó átirányítási URI-k a használni kívánt folyamattól függenek.

- Ha az interaktív hitelesítést vagy az eszköz kódjának folyamatát `https://login.microsoftonline.com/common/oauth2/nativeclient`használja, használja a t. A konfiguráció eléréséhez válassza ki a megfelelő URL-címet az alkalmazás **hitelesítés** szakaszában.
  
  > [!IMPORTANT]
  > Napjainkban a MSAL.NET egy másik átirányítási URI-t használ a Windows (`urn:ietf:wg:oauth:2.0:oob`) rendszeren futó asztali alkalmazásokban. A jövőben módosítani fogjuk ezt az alapértelmezett értéket, ezért javasoljuk, hogy használja `https://login.microsoftonline.com/common/oauth2/nativeclient`a következőt:.

- Ha macOS-hez készült natív Objective-C vagy SWIFT alkalmazást hoz létre, regisztrálja az átirányítási URI-t az alkalmazás köteg-azonosítója alapján a következő formátumban: msauth. <your.app.bundle.id>://auth. cserélje le <your.app.bundle.id> az alkalmazás Bundle-azonosítójával.
- Ha az alkalmazás kizárólag integrált Windows-hitelesítést vagy felhasználónevet és jelszót használ, nem kell regisztrálnia az alkalmazás átirányítási URI-JÁT. Ezek a folyamatok a Microsoft Identity platform 2.0-s végpontján keresztül egy oda-vissza. Az alkalmazás nem hívható vissza semmilyen konkrét URI-ra.
- Ha meg szeretné különböztetni az eszköz kódját, az integrált Windows-hitelesítést, valamint egy olyan bizalmas ügyfélalkalmazás-folyamathoz tartozó felhasználónevet és jelszót, amely nem rendelkezik átirányítási URI-azonosítóval (a démon-alkalmazásokban használt ügyfél-hitelesítő adatok folyamata), meg kell adnia, hogy az alkalmazás egy nyilvános ügyfélalkalmazás. A konfiguráció eléréséhez nyissa meg az alkalmazás **hitelesítés** szakaszát. A **Speciális beállítások** alszakasz **alapértelmezett ügyfél típusa** csoportjában válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként való kezelésére**.

  ![Nyilvános ügyfél engedélyezése](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-engedélyek

Asztali alkalmazások hívás API-kat a bejelentkezett felhasználó számára. Delegált engedélyeket kell kérniük. Nem igényelhetnek alkalmazás-engedélyeket, amelyek csak Daemon- [alkalmazásokban](scenario-daemon-overview.md)vannak kezelve.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás: alkalmazás konfigurációja](scenario-desktop-app-configuration.md)
