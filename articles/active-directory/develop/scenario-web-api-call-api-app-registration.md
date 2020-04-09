---
title: Webes API-kat megnevező webes API regisztrálása – Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-t, amely meghívja az alsóbb rétegbeli webes API-kat (alkalmazásregisztráció).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885123"
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
