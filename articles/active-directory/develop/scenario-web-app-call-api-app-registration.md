---
title: Webes API-kat meghívjaó webalkalmazás regisztrálása – Microsoft identity platform | Azure
description: További információ a webes API-kat meghívjaó webalkalmazások regisztrálásáról
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759057"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Webes API-kat meghívjaó webalkalmazás: Alkalmazásregisztráció

A webes API-kat meghívó webalkalmazás regisztrációja megegyezik a felhasználókat bejelentkező webalkalmazásregisztrációval. Tehát kövesse az utasításokat [egy webapp, amely aláírja a felhasználók: App regisztráció](scenario-web-app-sign-user-app-registration.md).

Mivel azonban a webalkalmazás most antól webes API-kat is meghív, bizalmas ügyfélalkalmazássá válik. Ezért van szükség további regisztrációra. Az alkalmazásnak meg kell osztania az ügyfél hitelesítő adatait vagy *titkos titkait*a Microsoft identitásplatformmal.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A webalkalmazások a bejelentkezett felhasználó nevében api-kat hívnak meg. Ehhez *delegált engedélyeket*kell kérniük. További információt a [Webes API-k hozzáférési engedélyeinek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)című témakörben talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívjaó webalkalmazás: Kódkonfiguráció](scenario-web-app-call-api-app-configuration.md)
