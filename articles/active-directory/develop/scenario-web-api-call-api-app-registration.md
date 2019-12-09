---
title: Webes API-kat meghívó webes API regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely az alárendelt webes API-kat hívja meg (alkalmazás-regisztráció)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919800"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó webes API – alkalmazás regisztrálása

Az alárendelt webes API-kat meghívó webes API-k ugyanazzal a regisztrációval rendelkeznek, mint a védett webes API-k. Ezért követnie kell a [védett webes API-alkalmazás regisztrációjának](scenario-protected-web-api-app-registration.md)utasításait.

Mivel azonban a webalkalmazás most meghívja a webes API-kat, a szolgáltatás bizalmas ügyfélalkalmazás lesz. Ezért van szükség további regisztrációs adatokra: az alkalmazásnak meg kell osztania a titkos kulcsokat (az ügyfél hitelesítő adatait) a Microsoft Identity platformmal.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

Webalkalmazások hívjanak API-kat azon felhasználó nevében, akinek a tulajdonosi jogkivonatot fogadták. Delegált engedélyeket kell kérniük. Részletekért lásd: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-web-api-call-api-app-configuration.md)
