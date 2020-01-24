---
title: Webes API-kat meghívó webes API regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely az alárendelt webes API-kat hívja meg (az alkalmazások regisztrációját).
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701790"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó webes API: alkalmazás regisztrálása

Az alárendelt webes API-kat meghívó webes API-k ugyanazzal a regisztrációval rendelkeznek, mint a védett webes API-k. Ezért kövesse a következő témakör utasításait [: Protected web API (alkalmazás regisztrálása](scenario-protected-web-api-app-registration.md)).

Mivel a webalkalmazás most meghívja a webes API-kat, a szolgáltatás bizalmas ügyfélalkalmazás lesz. Ezért további regisztrációs információkra van szükség: az alkalmazásnak meg kell osztania a titkos kulcsokat (az ügyfél hitelesítő adatait) a Microsoft Identity platformmal.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A Web Apps hívja az API-kat azon felhasználók nevében, akik számára a tulajdonosi jogkivonat érkezett. A webalkalmazásoknak delegált engedélyeket kell kérniük. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: kód konfigurálása](scenario-web-api-call-api-app-configuration.md)
