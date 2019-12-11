---
title: Webes API-kat meghívó webes API létrehozása – Microsoft Identity platform | Azure
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
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965126"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webes API

A webes API-kat meghívó webes API-k létrehozásához szükséges tudnivalók.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben a webes API-kat meghívó védett webes API-k a "webes API-k védelme" forgatókönyvre épülnek. Ha többet szeretne megtudni erről az alapvető forgatókönyvről, tekintse meg a [Protected web API – első forgatókönyv](scenario-protected-web-api-overview.md) című témakört.

## <a name="overview"></a>Áttekintés

- Egy ügyfél (web, Desktop, mobil vagy egylapos alkalmazás) – az alábbi ábrán nem szerepel – meghívja a védett webes API-t, és egy JWT tulajdonosi jogkivonatot biztosít az "engedélyezési" http-fejlécben.
- A védett webes API ellenőrzi a jogkivonatot, és a MSAL `AcquireTokenOnBehalfOf` metódus használatával kéri le (az Azure AD-ből), hogy a felhasználó nevében meghívjon egy második webes API-t (az alárendelt webes API néven).
- A védett webes API ezt a tokent használja egy alsóbb rétegbeli API meghívásához. Azt is meghívhatja `AcquireTokenSilent`később, hogy más alsóbb rétegbeli API-kra is kérjen jogkivonatokat (de ugyanazon felhasználó nevében továbbra is). `AcquireTokenSilent` szükség esetén frissíti a tokent.

![Webes API-t hívó webes API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazások regisztrációjának része a klasszikus. Az alkalmazás konfigurációja magában foglalja a OAuth 2,0-as verziójának használatát a JWT tulajdonosi jogkivonatának az alsóbb rétegbeli API-hoz való cseréjéhez. Ezt a tokent a rendszer hozzáadja a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API vezérlői között, és az alsóbb rétegbeli API-k meghívásához a tokent csendesen is beszerezheti.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
