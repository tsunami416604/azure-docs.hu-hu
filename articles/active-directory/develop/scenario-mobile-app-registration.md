---
title: Webes API-kat meghívó mobil alkalmazás – alkalmazás kódjának konfigurációja | Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (az alkalmazás kódjának konfigurációja)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326124"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó mobil alkalmazás – alkalmazás regisztrálása

Ez a cikk az alkalmazás regisztrációs utasításait tartalmazza a mobileszközök létrehozásához.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A Mobile Applications által támogatott fióktípus függ az engedélyezni kívánt felhasználói élménytől, és az alkalmazás által megcélzott felhasználóknak.

## <a name="platform-configuration-and-redirect-uris"></a>Platform konfigurálása és URI-k átirányítása  

A mobil alkalmazások létrehozásakor a legkritikusabb regisztrációs lépés az átirányítási URI. Ez a hitelesítés panelen állítható be a [platform-konfiguráción](https://aka.ms/MobileAppReg)keresztül.

Ez a felhasználói élmény lehetővé teszi az alkalmazás számára, hogy egyszeri bejelentkezést (SSO) kapjon a Microsoft Authenticator (és az Androidon Intune Céges portál), valamint az eszköz-felügyeleti házirendek támogatásával.

Ha manuálisan szeretné konfigurálni az átirányítási URI-t, ezt az alkalmazás Jegyzékfájlján keresztül teheti meg. Az ajánlott formátum a következő:

- ***iOS***:`msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Az androidos aláírás kivonata a Key Tool parancs használatával hozható létre a kiadási vagy a hibakeresési kulcsokkal.

## <a name="api-permissions"></a>API-engedélyek

A Mobile Applications a bejelentkezett felhasználó nevében hívja meg az API-kat. Az alkalmazásnak delegált engedélyeket kell igényelnie, más néven hatóköröknek kell lennie. A kívánt élménytől függően ez statikusan végezhető el a Azure Portalon vagy dinamikusan, futásidőben. Az engedélyek statikus regisztrálása lehetővé teszi, hogy a rendszergazdák egyszerűen jóváhagyják az alkalmazást, és ajánlottak legyenek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token beszerzése](scenario-mobile-acquire-token.md)
