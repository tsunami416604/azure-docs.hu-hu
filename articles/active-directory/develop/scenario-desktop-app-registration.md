---
title: Webes API-kat hívó asztali alkalmazások regisztrálása – Microsoft identity platform | Azure
description: Megtudhatja, hogy miként hozhat létre webes API-kat meghívja (alkalmazásregisztráció)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885157"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Webes API-kat meghívjaó asztali alkalmazás: Alkalmazásregisztráció

Ez a cikk az asztali alkalmazások alkalmazásregisztrációs sajátosságait ismerteti.

## <a name="supported-account-types"></a>Támogatott fióktípusok

Az asztali alkalmazásokáltal támogatott fióktípusok a kivilágítani kívánt élménytől függenek. E kapcsolat miatt a támogatott fióktípusok a használni kívánt folyamatoktól függenek.

### <a name="audience-for-interactive-token-acquisition"></a>Interaktív tokenek megszerzésének célközönsége

Ha asztali alkalmazása interaktív hitelesítést használ, bármilyen [fióktípusból](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)bejelentkezhet a felhasználókhoz.

### <a name="audience-for-desktop-app-silent-flows"></a>Az asztali alkalmazások csendes folyamatainak célközönsége

- Az integrált Windows-hitelesítés vagy felhasználónév és jelszó használatához az alkalmazásnak be kell jelentkeznie a saját bérlőjében lévő felhasználókhoz, például ha ön üzletági (LOB) fejlesztő. Vagy az Azure Active Directory-szervezetekben az alkalmazásnak be kell jelentkeznie a saját bérlője, ha egy isv-forgatókönyv. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókok.
- Ha eszközkód-folyamatot szeretne használni, még nem jelentkezhet be a felhasználóka Microsoft-személyes fiókjukkal.
- Ha olyan közösségi identitással rendelkező felhasználókat jelentkezbe be, amelyek megfelelnek egy üzleti és kereskedelmi (B2C) jogosultságnak és házirendnek, csak az interaktív és felhasználónév-jelszó hitelesítést használhatja.

## <a name="redirect-uris"></a>Uri-k átirányítása

Az asztali alkalmazásokban használandó átirányítási URI-k a használni kívánt folyamattól függenek.

- Ha interaktív hitelesítést vagy eszközkód-áramlást használ, használja a használatát. `https://login.microsoftonline.com/common/oauth2/nativeclient` A konfiguráció eléréséhez válassza ki a megfelelő URL-címet az alkalmazás **hitelesítése** szakaszban.
  
  > [!IMPORTANT]
  > Ma MSAL.NET alapértelmezés szerint egy másik átirányítási URI-t használ a Windows rendszeren futó asztali alkalmazásokban (`urn:ietf:wg:oauth:2.0:oob`). A jövőben módosítani szeretnénk ezt az alapértelmezett beállítást, ezért `https://login.microsoftonline.com/common/oauth2/nativeclient`javasoljuk, hogy használja a használatát.

- Ha natív Objective-C vagy Swift alkalmazást hoz létre macOS-hez, regisztrálja az átirányítási URI-t az alkalmazás csomagazonosítója alapján a következő formátumban: msauth.<your.app.bundle.id>://auth. Cserélje le <your.app.bundle.id> az alkalmazás csomagazonosítójára.
- Ha az alkalmazás csak integrált Windows-hitelesítést vagy felhasználónevet és jelszót használ, nem kell átirányítási URI-t regisztrálnia az alkalmazáshoz. Ezek a folyamatok egy oda-vissza a Microsoft identity platform v2.0 végpont. Az alkalmazás nem lesz visszahívva egy adott URI-n.
- Az eszközkód-áramlás, az integrált Windows-hitelesítés, valamint a felhasználónév és a jelszó megkülönböztetéséhez egy olyan bizalmas ügyfélalkalmazás-folyamattól, amely nem rendelkezik átirányítási URI-k (a démonalkalmazásokban használt ügyfélhitelesítő adatfolyam) megkülönböztetéséhez, ki kell fejeznie, hogy az alkalmazás nyilvános ügyfélalkalmazás. A konfiguráció eléréséhez nyissa meg az alkalmazás **hitelesítése** szakaszt. A **Speciális beállítások** alszakasz **Alapértelmezett ügyféltípus** bekezdésében válassza az **Igen** a **Kezelés alkalmazáshoz nyilvános ügyfélként**lehetőséget.

  ![Nyilvános ügyfél engedélyezése](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-engedélyek

Az asztali alkalmazások a bejelentkezett felhasználó API-jait hívják meg. Delegált engedélyeket kell kérniük. Nem kérhetnek alkalmazásengedélyeket, amelyeket csak [démonalkalmazásokban](scenario-daemon-overview.md)kezelnek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás: Alkalmazás konfigurációja](scenario-desktop-app-configuration.md)
