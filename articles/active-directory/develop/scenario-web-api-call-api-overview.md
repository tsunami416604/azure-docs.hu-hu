---
title: Webes API, amely az alárendelt webes API-kat hívja meg (áttekintés) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre olyan webes API-t, amely meghívja az alárendelt webes API-kat (áttekintés).
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852495"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Forgatókönyv: Webes API-kat hívó webes API

A webes API-kat meghívó webes API-k létrehozásához szükséges tudnivalók.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben a webes API-kat meghívó védett webes API-k a "webes API-k védelme" forgatókönyvre épülnek. Ha többet szeretne megtudni erről az alapvető forgatókönyvről, tekintse meg a [Protected web API – első forgatókönyv](scenario-protected-web-api-overview.md) című témakört.

## <a name="overview"></a>Áttekintés

- Egy ügyfél (web, Desktop, mobil vagy egylapos alkalmazás) – az alábbi ábrán nem szerepel – meghívja a védett webes API-t, és egy JWT tulajdonosi jogkivonatot biztosít az "engedélyezési" http-fejlécben.
- A védett webes API ellenőrzi a jogkivonatot, és az `AcquireTokenOnBehalfOf` MSAL metódus használatával kéri le (az Azure ad-ből) egy másik tokent, hogy a felhasználó nevében meghívjon egy második webes API-t (az alárendelt webes API néven).
- A védett webes API ezt a tokent használja egy alsóbb rétegbeli API meghívásához. Azt is megteheti, hogy később meghívja `AcquireTokenSilent`a tokeneket más alsóbb rétegbeli API-khoz (de még ugyanazon felhasználó nevében). `AcquireTokenSilent`szükség esetén frissíti a jogkivonatot.

![Webes API-t hívó webes API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazások regisztrációjának része a klasszikus. Az alkalmazás konfigurációja magában foglalja a OAuth 2,0-as verziójának használatát a JWT tulajdonosi jogkivonatának az alsóbb rétegbeli API-hoz való cseréjéhez. Ezt a tokent a rendszer hozzáadja a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API vezérlői között, és az alsóbb rétegbeli API-k meghívásához a tokent csendesen is beszerezheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
