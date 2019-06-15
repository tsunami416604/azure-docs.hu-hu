---
title: Webes API-t, amely meghívja az alsóbb rétegbeli webes API-k (áttekintés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre a webes API-k a hívások alsóbb rétegbeli webes API-k (áttekintés).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075400"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Forgatókönyv: Webes API-kat hívó webes API

Ismerje meg, hogy az összes, hogy egy webes API-t kell létrehoznia, amely meghívja a webes API-k.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben védett webes API-t, hogy a hívások webes API-k, a "Webes API-k védelme" forgatókönyv épül. Ez a alapvető forgatókönyv kapcsolatos további információkért lásd: [védett webes API - forgatókönyv](scenario-protected-web-api-overview.md) első.

## <a name="overview"></a>Áttekintés

- -Nem jönnek létre az alábbi ábrán a - ügyfél (webes, asztali, mobil, vagy egyoldalas alkalmazás) egy védett webes API-hívások, és az "Engedélyezés" Http-fejlécben JWT tulajdonosi jogkivonattal biztosít.
- A védett webes API érvényesíti a jogkivonatot, és használja az MSAL `AcquireTokenOnBehalfOf` kérhet (Azure AD) egy másik jogkivonatot, hogy képesek is rá, metódus hívása második webes API-k (más néven az alsóbb rétegbeli webes API-k) a felhasználó nevében.
- A védett webes API hívásához egy alsóbb rétegbeli API-t a biztonsági jogkivonattal. Azt is meghívhat `AcquireTokenSilent`újabb szükséges jogkivonatok kérelmezésére más alsóbb rétegbeli API-k (de még mindig ugyanaz a felhasználó nevében). `AcquireTokenSilent` frissíti a tokent, ha szükséges.

![Webes API-k hívása a webes API-k](media/scenarios/web-api.svg)

## <a name="specifics"></a>Tulajdonságairól

Az API-t az engedélyekhez kapcsolódó alkalmazásregisztráció része a klasszikus. Az alkalmazás konfigurációja magában foglalja az OAuth 2.0--meghatalmazásos folyamat segítségével egy token elleni JWT tulajdonosi jogkivonat cserélhet egy alsóbb rétegbeli API-hoz. Ez a token hozzáadódik a token gyorsítótárát, ahol a webes API-vezérlő érhető el, és csendes alsóbb rétegbeli API-k hívása a tokenek beszerzésére is.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
