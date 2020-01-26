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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759057"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Webes API-kat meghívó webalkalmazás: alkalmazás regisztrálása

A webes API-kat meghívó webalkalmazások ugyanazzal a regisztrációval rendelkeznek, mint egy webalkalmazás, amely aláírja a felhasználókat a alkalmazásban. Ezért kövesse a következő témakör utasításait [: alkalmazások regisztrációja a webalkalmazásban](scenario-web-app-sign-user-app-registration.md).

Mivel azonban a webalkalmazás most is meghívja a webes API-kat, a szolgáltatás bizalmas ügyfélalkalmazás lesz. Ezért további regisztrációra van szükség. Az alkalmazásnak meg kell osztania az ügyfél hitelesítő adatait vagy *titkos kulcsait*a Microsoft Identity platformmal.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-engedélyek

A Web Apps a bejelentkezett felhasználó nevében hívja meg az API-kat. Ehhez *delegált engedélyeket*kell igényelnie. Részletekért lásd: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webalkalmazás: kód konfigurálása](scenario-web-app-call-api-app-configuration.md)
