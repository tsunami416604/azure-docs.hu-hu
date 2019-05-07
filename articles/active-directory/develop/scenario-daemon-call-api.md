---
title: Démon alkalmazások hívó webes API-k (hívó webes API-k) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, hogy a hívások webes API-k (hívó webes API-k)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076270"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Démon alkalmazások, hogy a hívások webes API-k – webes API-hívás az alkalmazásból

Egy démon alkalmazás webes API-hívás .NET-démon alkalmazás és számos előre jóváhagyott webes API-k hívása.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Webes API-k hívása a démon .NET-alkalmazás

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Több API-k hívása

Démon alkalmazások esetében a webes API-k hívják meg előre jóváhagyás szükséges. (Nincs felhasználói beavatkozás nélküli) démon alkalmazások bármilyen növekményes jóváhagyás nem lesz. A bérlői rendszergazdának be kell előre jóváhagyást az alkalmazás és az API-engedélyek. Ha szeretné több API-kat, szüksége lesz az egyes erőforrások egy token beszerzéséhez minden egyes alkalommal hívó `AcquireTokenForClient`. Az MSAL az alkalmazás tokengyorsítótárral használatával elkerülheti a felesleges szolgáltatások meghívása.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Démon alkalmazások – áthelyezése éles környezetbe](./scenario-daemon-production.md)