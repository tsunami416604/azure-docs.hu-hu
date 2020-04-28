---
title: Webes API-kat meghívó webes API létrehozása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre olyan webes API-t, amely meghívja az alárendelt webes API-kat (áttekintés).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885072"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webes API

Ismerje meg, hogy mit kell tudnia a webes API-kat meghívó webes API-k létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ez a forgatókönyv, amelyben a védett webes API meghívja a webes API-kat, a "webes API-k védelme" forgatókönyvre épül. Ha többet szeretne megtudni erről az alapvető forgatókönyvről, tekintse meg a [forgatókönyv: védett webes API](scenario-protected-web-api-overview.md)című témakört.

## <a name="overview"></a>Áttekintés

- A webes, asztali, mobil vagy egyoldalas alkalmazás ügyfélprogram (amely nem szerepel a csatolt ábrán) meghívja a védett webes API-t, és egy JSON Web Token (JWT) tulajdonosi jogkivonatot biztosít az "engedélyezési" HTTP-fejlécben.
- A védett webes API ellenőrzi a jogkivonatot, és a Microsoft Authentication Library (MSAL `AcquireTokenOnBehalfOf` ) metódust használja a Azure Active Directory (Azure ad) egy másik jogkivonatának igényléséhez, hogy a védett webes API meghívhat egy második webes API-t, vagy a felhasználó nevében egy másodlagos webes API-t.
- A védett webes API később is meghívhat `AcquireTokenSilent`, hogy ugyanazon felhasználó nevében más alsóbb rétegbeli API-kra is igényeljen jogkivonatokat. `AcquireTokenSilent`szükség esetén frissíti a jogkivonatot.

![Webes API-t hívó webes API diagramja](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazás-regisztrációs rész klasszikus. Az alkalmazás konfigurációja magában foglalja a OAuth 2,0-as verziójának használatát a JWT tulajdonosi jogkivonatának az alsóbb rétegbeli API-hoz való cseréjéhez. Ezt a tokent a rendszer hozzáadja a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API vezérlői között, és a tokent az alsóbb rétegbeli API-k meghívásához csendesen is beszerezheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
