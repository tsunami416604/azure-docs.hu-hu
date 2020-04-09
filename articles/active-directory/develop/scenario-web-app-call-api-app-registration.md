---
title: Webes API-kat meghívjaó webalkalmazás regisztrálása – Microsoft identity platform | Azure
description: További információ a webes API-kat meghívjaó webalkalmazások regisztrálásáról
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881876"
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
