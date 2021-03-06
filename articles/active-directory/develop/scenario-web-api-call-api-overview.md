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
ms.openlocfilehash: ee9d879849a94ac255a0967ad4fbc762417f8cd4
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442650"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webes API

Ismerje meg, hogy mit kell tudnia a webes API-kat meghívó webes API-k létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ez a forgatókönyv, amelyben a védett webes API más webes API-kat hív meg, a következő [forgatókönyvre épül: védett webes API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Áttekintés

- A webes, asztali, mobil vagy egyoldalas alkalmazás ügyfélprogram (amely nem szerepel a csatolt ábrán) meghívja a védett webes API-t, és egy JSON Web Token (JWT) tulajdonosi jogkivonatot biztosít az "engedélyezési" HTTP-fejlécben.
- A védett webes API ellenőrzi a jogkivonatot, és a Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` metódust használja a Azure Active Directory (Azure ad) egy másik jogkivonatának igényléséhez, hogy a védett webes API meghívhat egy második webes API-t, vagy a felhasználó nevében egy másodlagos webes API-t.
- A védett webes API később is meghívhat `AcquireTokenSilent` , hogy ugyanazon felhasználó nevében más alsóbb rétegbeli API-kra is igényeljen jogkivonatokat. `AcquireTokenSilent` szükség esetén frissíti a jogkivonatot.

![Webes API-t hívó webes API diagramja](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazás-regisztrációs rész klasszikus. Az alkalmazás konfigurációja magában foglalja a OAuth 2,0-as verziójának használatát a JWT tulajdonosi jogkivonatának az alsóbb rétegbeli API-hoz való cseréjéhez. Ezt a tokent a rendszer hozzáadja a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API vezérlői között, és a tokent az alsóbb rétegbeli API-k meghívásához csendesen is beszerezheti.

## <a name="next-steps"></a>További lépések

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](scenario-web-api-call-api-app-registration.md)ismerteti.
