---
title: Webes API-kat meghívó webalkalmazás regisztrálása – Microsoft Identity platform | Azure
description: Útmutató webes API-kat meghívó webalkalmazás regisztrálásához
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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701671"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó webalkalmazás – alkalmazás regisztrálása

A webes API-kat hívó webalkalmazások ugyanazzal a regisztrációval rendelkeznek, mint a webalkalmazás-bejelentkezési felhasználók. Ezért követnie kell a [webalkalmazásban a bejelentkező felhasználók – alkalmazás regisztrációja](scenario-web-app-sign-user-app-registration.md) című témakör utasításait.

Mivel azonban a webalkalmazás most meghívja a webes API-kat, a szolgáltatás bizalmas ügyfélalkalmazás lesz. Ezért van szükség további regisztrációra: meg kell osztania a titkos kulcsokat (az ügyfél hitelesítő adatait) a Microsoft Identity platformmal.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A webalkalmazások a bejelentkezett felhasználó nevében hívjanak API-kat. Delegált engedélyeket kell kérniük. További részletek: a [webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-web-app-call-api-app-configuration.md)
