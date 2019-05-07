---
title: Démon alkalmazások hívó webes API-k (alkalmazásregisztráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, amely meghívja a webes API-k – alkalmazás regisztrálása
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076240"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Démon alkalmazások, hogy a hívások webes API-k – alkalmazás regisztrálása

Démon alkalmazás esetében itt van szükség, ha regisztrálja az alkalmazást.

## <a name="supported-account-types"></a>Támogatott fióktípusok

Démon alkalmazások csak érthető legyen az adott Azure AD-bérlővel, amikor az alkalmazást hoz létre, hogy választani kell:

- mindkét **fiókok csak a szervezeti könyvtárban található**. Ez a választás a leggyakoribb eset megegyezik démon alkalmazások általában az üzletági (LOB) fejlesztők által írt.
- vagy **bármely szervezeti directory fiókok**. Ez a választás paritásadatok, ha Ön független Szoftverszállító segédprogram eszközt biztosít az ügyfelek számára. Szüksége lesz az ügyfél bérlői rendszergazdák ezt jóvá kell hagynia.

## <a name="authentication---no-reply-uri-needed"></a>Hitelesítés – nincs szükség válasz URI

Abban az esetben, ahol a bizalmas ügyfélalkalmazás **csak** az ügyfél hitelesítő adatai, a válasz URL-cím nem kell regisztrálni. Nincs szükség szerint az alkalmazás konfigurációs/konstrukció. Az ügyfél hitelesítési folyamata nem használható.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-engedélyek – Alkalmazásengedélyek és rendszergazdai jóváhagyás

A démon alkalmazások csak kérhetnek Alkalmazásengedélyek API-k (nem delegált engedélyeket). Az a **API engedély** lapja az alkalmazás regisztrálása után a kiválasztott **adjon hozzá egy engedélyt** , és válassza ki az API-család, **Alkalmazásengedélyek**, majd válassza ki az Ön engedélyei

![Alkalmazásengedélyek és rendszergazdai jóváhagyás](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Démon alkalmazások szükség van egy Bérlői rendszergazda előre beleegyezik abba, hogy az alkalmazás a webes API hívása. A jóváhagyás ugyanazon biztosított **API engedély** lap egy Bérlői rendszergazda kiválasztásával **rendszergazda hozzájárulását *szervezet számára***

Ha Ön független Szoftverszállító egy több-bérlős alkalmazást készít, érdemes ellenőrizni a [üzembe helyezés – kis-és több-bérlős démon alkalmazások](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) bekezdés.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Démon alkalmazások – kód konfigurálása](./scenario-daemon-app-configuration.md)
