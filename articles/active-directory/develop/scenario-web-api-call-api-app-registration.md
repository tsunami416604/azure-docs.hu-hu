---
title: Webes API-kat megnevező webes API regisztrálása – Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-t, amely meghívja az alsóbb rétegbeli webes API-kat (alkalmazásregisztráció).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701790"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó webes API: Alkalmazásregisztráció

Egy webes API-kat, amely meghívja az alsóbb rétegbeli webes API-k at, ugyanaz a regisztráció, mint egy védett webes API-k. Ezért követnie kell a [Védett webes API: Alkalmazásregisztráció utasításait.](scenario-protected-web-api-app-registration.md)

Mivel a webalkalmazás mostantól webes API-kat hív meg, bizalmas ügyfélalkalmazássá válik. Ezért van szükség további regisztrációs adatokra: az alkalmazásnak meg kell osztania a titkos kulcsokat (ügyfélhitelesítő adatokat) a Microsoft identitásplatformmal.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A webalkalmazások api-kat hívnak olyan felhasználók nevében, akikszámára a tulajdonosi jogkivonat érkezett. A webalkalmazásoknak delegált engedélyeket kell kérnie. További információt a [Webes API-k hozzáférési engedélyeinek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)című témakörben talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívja: Kódkonfiguráció](scenario-web-api-call-api-app-configuration.md)
