---
title: Webes API-t, hogy hívások alsóbb rétegbeli webes API-k (alkalmazásregisztráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre a webes API-k a hívások alsóbb rétegbeli webes API-k (alkalmazásregisztráció)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075385"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Webes API-t, hogy a hívások webes API-k – alkalmazás regisztrálása

Webes API-t, amely meghívja az alsóbb rétegbeli webes API-k ugyanazt a regisztrációt, mint egy védett webes API-t tartalmaz. Ezért kell kövesse a [védett webes API - alkalmazás regisztrációja](scenario-protected-web-api-app-registration.md).

Azonban óta a webalkalmazás most hívások webes API-kat, lesz egy bizalmas ügyfélalkalmazás. Ezért van szükséges további regisztrációs adatai: az alkalmazás (ügyfél-hitelesítő adatok) titkos kulcsok megoszthatja a Microsoft identity platform kell.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

Webes alkalmazások API-k hívása a tulajdonosi jogkivonat érkezett, amely a felhasználó nevében. Delegált engedélyek kéréséhez szükséges. További információkért lásd: [webes API-k elérésére vonatkozó engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-web-api-call-api-app-configuration.md)
