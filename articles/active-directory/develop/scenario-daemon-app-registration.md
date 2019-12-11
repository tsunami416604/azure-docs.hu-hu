---
title: Webes API-kat meghívó Daemon-alkalmazás regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre a webes API-kat meghívó Daemon-alkalmazást – alkalmazás-regisztráció
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcdad9b1ea56a41613c5d8d815d1418783fea4ce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965585"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó Daemon-alkalmazás – alkalmazás regisztrálása

Egy Daemon-alkalmazáshoz a következőkre van szükség az alkalmazás regisztrálásakor.

## <a name="supported-account-types"></a>Támogatott fióktípusok

Mivel a Daemon-alkalmazások csak az Azure AD-bérlők számára érthetőek, az alkalmazás létrehozásakor a következőket kell választania:

- **csak ebben a szervezeti könyvtárban található fiókok**. Ez a leggyakoribb eset, mivel a Daemon-alkalmazásokat általában üzletági (LOB) fejlesztők írták.
- vagy **a fiókok bármely szervezeti címtárban**. Ezt a lehetőséget akkor kell megtennie, ha Ön az ISV-t biztosító segédprogramot biztosít ügyfelei számára. Az ügyfél bérlői rendszergazdáinak jóvá kell hagynia.

## <a name="authentication---no-reply-uri-needed"></a>Hitelesítés – nincs szükség válasz URI-ra

Abban az esetben, ha a bizalmas ügyfélalkalmazás **csak** az ügyfél-hitelesítő adatok folyamatát használja, a válasz URI-ját nem kell regisztrálni. Az alkalmazás konfigurációja/építése nem szükséges. Az ügyfél-hitelesítő adatok folyamata nem használja.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-engedélyek – alkalmazás-engedélyek és rendszergazdai engedély

Egy Daemon-alkalmazás csak API-khoz kérheti az alkalmazás engedélyeit (nem delegált engedélyekkel). Az alkalmazás regisztrációjának **API-engedély** lapján az **engedély hozzáadása** és az API-család kiválasztása után válassza az **alkalmazás engedélyei**lehetőséget, majd válassza ki az engedélyeket.

![Alkalmazás-engedélyek és rendszergazdai engedély](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A hívni kívánt webes API-nak meg kell határoznia az **alkalmazás engedélyeit (az alkalmazás szerepkörei)** , a nem delegált engedélyeket. Az ilyen API-k megjelenítésének részletes ismertetését lásd [: védett webes API: alkalmazás regisztrálása – ha a webes API-t egy Daemon-alkalmazás hívja](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app) meg

A Daemon-alkalmazásokhoz a bérlői rendszergazdának előzetes hozzájárulással kell rendelkeznie a webes API-t hívó alkalmazáshoz. Ezt a beleegyezést ugyanabban az **API-engedély** oldalon, egy bérlői rendszergazda adja meg, ha a **rendszergazda beleegyezését választja a *szervezetnek***

Ha Ön egy több-bérlős alkalmazást tartalmazó ISV-t használ, ellenőrizze a [több-bérlős Daemon-alkalmazások telepítését](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) .

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Daemon App – alkalmazás kódjának konfigurálása](./scenario-daemon-app-configuration.md)
