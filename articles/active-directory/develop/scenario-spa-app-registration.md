---
title: Single-page application (alkalmazás regisztrációja) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (alkalmazásregisztráció)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074830"
---
# <a name="single-page-application---app-registration"></a>Egyoldalas alkalmazás – alkalmazás regisztrálása

Ezen a lapon egy egyoldalas alkalmazás (SPA)-alkalmazás regisztrációja adatait ismerteti.

Kövesse a lépéseket a [egy új alkalmazás regisztrálása a Microsoft identity Platform](quickstart-register-app.md), és válassza ki az alkalmazás a támogatott fiókokat. Az SPA-forgatókönyv fiókokkal történő hitelesítés is támogatja a szervezet vagy a bármely szervezet és a személyes Microsoft-fiókok.

Következőként ismerje meg, hogy az alkalmazás regisztrációja konkrét elemeit, amely a alkalmazni a egyoldalas alkalmazások.

## <a name="register-a-redirect-uri"></a>Átirányítási URI-t regisztrálása

Az implicit folyamatot küldi a jogkivonatok átirányítási az egyoldalas alkalmazást a böngészőben. Ezért célszerű regisztrálni az átirányítási URI, ahol az alkalmazás fogadhat-e a tokenek egy fontos követelmény. Győződjön meg arról, hogy az átirányítási URI-t az alkalmazás URI-azonosítójú pontosan megegyezik.

A a [az Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908), a regisztrált egy alkalmazást, nyissa meg a **hitelesítési** az alkalmazásnak, válassza az oldal a **webes** platform, és adja meg az értékét a átirányítási URI-ban az alkalmazás a **átirányítási URI-t** mező.

## <a name="enable-the-implicit-flow"></a>Az implicit folyamat engedélyezése

Az azonos **hitelesítési** lap **speciális beállítások**, is engedélyeznie kell a **típusú Implicit engedélyezés**. Az alkalmazás csak jelentkezzen be a felhasználók és az azonosító jogkivonatok lekérésének lépéseiről teljesítményének vizsgálatához, hogy elegendő ahhoz, hogy **azonosító-jogkivonatokat** jelölőnégyzetet.

Ha az alkalmazásnak is kell beolvasni a hozzáférési jogkivonatok az API-kat, ügyeljen arra, hogy engedélyezze a **hozzáférési jogkivonatokat** jelölőnégyzetet is. További információkért lásd: [azonosító-jogkivonatokat](./id-tokens.md) és [hozzáférési jogkivonatokat](./access-tokens.md).

## <a name="api-permissions"></a>API-engedélyek

Egyoldalas alkalmazások hívhatják a API-k a bejelentkezett felhasználó nevében. Delegált engedélyek kéréséhez szükséges. További információkért lásd: [webes API-k elérésére vonatkozó engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-spa-app-configuration.md)
