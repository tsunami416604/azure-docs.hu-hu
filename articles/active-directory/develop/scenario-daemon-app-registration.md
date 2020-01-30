---
title: Webes API-kat meghívó Daemon-alkalmazások regisztrálása – Microsoft Identity platform | Azure
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
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773398"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó Daemon-alkalmazás – alkalmazás regisztrálása

Egy Daemon-alkalmazáshoz az alábbi tudnivalókat kell tudnia az alkalmazás regisztrálásakor.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A Daemon-alkalmazások csak az Azure AD-bérlők számára érthetők. Tehát az alkalmazás létrehozásakor az alábbi lehetőségek közül kell választania:

- **Csak a szervezeti könyvtárban lévő fiókok**. Ez a leggyakoribb megoldás, mert a Daemon-alkalmazásokat általában üzletági (LOB) fejlesztők írták.
- **Fiókok bármely szervezeti címtárban**. Ezt a lehetőséget akkor kell megtennie, ha Ön az ISV-t biztosító segédprogramot biztosít ügyfelei számára. A jóváhagyáshoz szüksége lesz az ügyfelek bérlői rendszergazdái számára.

## <a name="authentication---no-reply-uri-needed"></a>Hitelesítés – nincs szükség válasz URI-ra

Abban az esetben, ha a bizalmas ügyfélalkalmazás *csak* az ügyfél-hitelesítő adatok folyamatát használja, a válasz URI-ját nem kell regisztrálni. Az alkalmazás konfigurációja vagy az építés nem szükséges. Az ügyfél-hitelesítő adatok folyamata nem használja.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-engedélyek – alkalmazás-engedélyek és rendszergazdai engedély

Egy démon alkalmazás csak az API-kra (nem delegált engedélyekre) vonatkozó alkalmazás-engedélyeket igényelhet. Az alkalmazás regisztrálásához az **API-engedélyek** lapon az **engedély hozzáadása** és az API-család kiválasztása után válassza az **alkalmazás engedélyei**lehetőséget, majd válassza ki az engedélyeket.

![Alkalmazás-engedélyek és rendszergazdai engedély](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A hívni kívánt webes API-nak meg kell határoznia az *alkalmazás engedélyeit (az alkalmazás szerepkörei)* , a nem delegált engedélyeket. Az ilyen API-k megjelenítésével kapcsolatos részletekért lásd [: védett webes API: alkalmazás regisztrálása – ha a webes API-t egy Daemon-alkalmazás hívja](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)meg.

A Daemon-alkalmazások megkövetelik, hogy a bérlői rendszergazda előadja a webes API-t hívó alkalmazásnak. A bérlői rendszergazdák megadják ezt a hozzájárulást ugyanarra az **API-jogosultsági** lapra, ha a **rendszergazda beleegyezését adják meg a *szervezetnek***

Ha egy több-bérlős alkalmazást fejlesztő ISV-t használ, olvassa el a több- [bérlős Daemon-alkalmazások telepítésének](scenario-daemon-production.md#deployment---multitenant-daemon-apps)szakaszát.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Daemon App – alkalmazás kódjának konfigurálása](./scenario-daemon-app-configuration.md)
