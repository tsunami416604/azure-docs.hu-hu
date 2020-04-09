---
title: Webes API-kat megnevező webes API létrehozása – Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-t, amely meghívja az alsóbb rétegbeli webes API-kat (áttekintés).
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885072"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Eset: Webes API-kat meghívja egy webes API-k

Ismerje meg, mit kell tudnia a webes API-kat meghívjaó webes API-k létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ebben a forgatókönyvben, amelyben egy védett webes API-k webes API-k, a "Web API védelme" forgatókönyv alapján épül. Ha többet szeretne megtudni erről az alapvető forgatókönyvről, olvassa el [a Forgatókönyv: Védett webes API.](scenario-protected-web-api-overview.md)

## <a name="overview"></a>Áttekintés

- Egy webes, asztali, mobil vagy egyoldalas alkalmazásügyfél (amely nem szerepel a mellékelt ábrán) védett webes API-t hív meg, és JSON webtoken (JWT) tulajdonosi jogkivonatot biztosít az "Engedélyezés" HTTP fejlécében.
- A védett webes API érvényesíti a jogkivonatot, és `AcquireTokenOnBehalfOf` a Microsoft Authentication Library (MSAL) metódus használatával kér egy másik jogkivonatot az Azure Active Directoryból (Azure AD), hogy a védett webes API-t egy második webes API-t vagy alsóbb rétegbeli webes API-t hívhasson meg a felhasználó nevében.
- A védett webes API-t is hívhatja `AcquireTokenSilent`később, hogy kérje nek jogkivonatokat más alsóbb rétegbeli API-k nevében ugyanaz a felhasználó. `AcquireTokenSilent`szükség esetén frissíti a jogkivonatot.

![Webes API-t hívó webes API-diagram](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazásregisztrációs rész klasszikus. Az alkalmazás konfigurációja magában foglalja az OAuth 2.0 a folyamat nevében a JWT-tulajdonosi jogkivonat cseréje egy jogkivonatot egy alsóbb rétegbeli API-t. Ez a jogkivonat hozzáadódik a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API-vezérlők, és ezután beszerezhet egy jogkivonatot csendben az alsóbb rétegbeli API-k hívásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
