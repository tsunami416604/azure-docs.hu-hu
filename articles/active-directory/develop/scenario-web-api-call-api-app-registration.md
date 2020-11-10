---
title: Webes API-kat meghívó webes API regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely az alárendelt webes API-kat hívja meg (az alkalmazások regisztrációját).
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
ms.openlocfilehash: 790580160ec236d1923dc28b9990d2675c253b44
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442684"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó webes API: alkalmazás regisztrálása

Az alárendelt webes API-kat meghívó webes API-k ugyanazzal a regisztrációval rendelkeznek, mint a védett webes API-k. Ezért kövesse a következő témakör utasításait [: Protected web API (alkalmazás regisztrálása](scenario-protected-web-api-app-registration.md)).

Mivel a webalkalmazás most meghívja a webes API-kat, a szolgáltatás bizalmas ügyfélalkalmazás lesz. Ezért további regisztrációs információkra van szükség: az alkalmazásnak meg kell osztania a titkos kulcsokat (az ügyfél hitelesítő adatait) a Microsoft Identity platformmal.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A Web Apps hívja az API-kat azon felhasználók nevében, akik számára a tulajdonosi jogkivonat érkezett. A webalkalmazásoknak delegált engedélyeket kell kérniük. További információ: a [webes API eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>További lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben, az [alkalmazás kódjának konfigurációjában](scenario-web-api-call-api-app-configuration.md).
