---
title: A mobilalkalmazás, hogy a hívások webes API-k – az alkalmazás kódja konfigurációját |} A Microsoft identity platform
description: Ismerje meg a mobilalkalmazások, hogy a hívások webes API-k (alkalmazás kód konfigurálása)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962410"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>A mobilalkalmazás, hogy a hívások webes API-k – alkalmazás regisztrálása

Ez a cikk egy mobilalkalmazás létrehozása az alkalmazás regisztrációs utasításokat tartalmazza.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A mobilalkalmazások támogatott fióktípusok attól függ, a felhasználói élményt szeretné engedélyezni, és a felhasználók az alkalmazáshoz be van-e állítva.

## <a name="platform-configuration-and-redirect-uris"></a>Platform konfigurációja és az átirányítási URI-k  

Mobilalkalmazások készítése, kritikus fontosságú lépésben esetén az átirányítási URI-t. E beállítás révén a [platform konfigurációja a hitelesítési panelen](https://aka.ms/MobileAppReg).

Ez a tapasztalat lehetővé teszi az alkalmazás első egyszeri bejelentkezés (SSO) keresztül a Microsoft Authenticator (és az Intune vállalati portál Android rendszeren) támogatás és az eszközfelügyeleti szabályzatokat.

Ha inkább manuálisan konfigurálnia a átirányítási URI-t, ezt az alkalmazásjegyzék keresztül teheti meg. A javasolt formátuma a következő:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - A Android aláírás-kivonatoló kiadási vagy hibakeresési kulcsokkal a KeyTool paranccsal hozhatók létre.

## <a name="api-permissions"></a>API-engedélyek

Mobilalkalmazások API-k hívása a bejelentkezett felhasználó nevében. Az alkalmazás kell kérhetnek delegált engedélyeket, más néven hatókörök. Attól függően, a kívánt környezet ezt megteheti statikusan az Azure Portalon keresztül vagy dinamikusan, futásidőben. Engedélyek statikusan regisztrálása lehetővé teszi, hogy a rendszergazdák egyszerűen hagyja jóvá az alkalmazást, és javasolt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy token beszerzése](scenario-mobile-acquire-token.md)
