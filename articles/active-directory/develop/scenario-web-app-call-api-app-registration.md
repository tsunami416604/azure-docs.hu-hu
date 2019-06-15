---
title: Webalkalmazás, hogy a hívások webes API-k (alkalmazásregisztráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, hogy a hívások webes API-k (alkalmazásregisztráció)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075190"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webalkalmazás, hogy a hívások webes API-k – alkalmazás regisztrálása

A webes alkalmazás hívó webes API-k ugyanazt a regisztrációt egy webalkalmazás bejelentkezés felhasználói is rendelkezik. Ezért kövesse a kell [Web app, hogy bejelentkezik felhasználók – alkalmazásregisztráció](scenario-web-app-sign-user-app-registration.md)

Azonban óta a webalkalmazás most hívások webes API-kat, lesz egy bizalmas ügyfélalkalmazás. Ezért van egy kis extra regisztráció szükséges: (ügyfél-hitelesítő adatok) titkos kulcsok megoszthatja a Microsoft identity platform kell.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

Webes alkalmazások API-k hívása a bejelentkezett felhasználó nevében. Delegált engedélyek kéréséhez szükséges. További részletekért lásd: [webes API-k elérésére vonatkozó engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-web-app-call-api-app-configuration.md)
