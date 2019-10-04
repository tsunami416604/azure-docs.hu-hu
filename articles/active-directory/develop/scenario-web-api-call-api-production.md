---
title: Webes API-t, hogy hívások webes API-kat (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webes API-t, hogy a hívások aktiválásához webes API-k (Váltás az éles környezetben).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074755"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webes API-t, amely meghívja a webes API-k – átállni éles üzemre

Miután egy jogkivonatot, amellyel meghívhatja a webes API-kat, melyeket beszerezett, továbbléphet az alkalmazás az éles környezetben.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Részletek

Most, hogy megismerte a alapjait, hogyan hívhat meg a saját webes API-t a webes API-kat, talán érdekelheti Ez az oktatóanyag bemutatja a kódot, amely a védett webes API-t készíteni használatban van hívja webes API-k.

| Sample | Platform | Leírás |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2\.2-es ASP.NET Core webes API-t (WPF) asztali | 2\.2 az ASP.NET Core webes API meghívása a Microsoft Graph, maga nevű WPF-alkalmazás használatával a Microsoft identity platform (2.0-s verzió) |
