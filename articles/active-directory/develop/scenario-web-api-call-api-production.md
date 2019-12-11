---
title: Webes API-k áthelyezése webes API-k használatával éles környezetbe – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan helyezhet át olyan webes API-t, amely webes API-kat hív meg éles környezetben.
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965169"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó webes API – áttérés éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k hívásához, áthelyezheti az alkalmazást az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>További információ

Most, hogy ismeri a webes API-k saját webes API-ból való meghívásának alapjait, érdemes lehet ezt az oktatóanyagot használni, amely leírja a védett webes API-t hívó webes API-k létrehozásához használt kódot.

| Minta | Platform | Leírás |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 webes API, asztali (WPF) | ASP.NET Core 2,2 webes API meghívása Microsoft Graph, magát a Microsoft Identity platformot használó WPF-alkalmazásból meghívták (v 2.0) |
