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
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701722"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webes API: áthelyezés éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k hívásához, áthelyezheti az alkalmazást az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>További információk

Most, hogy már ismeri a webes API-k saját webes API-ból való meghívásának alapjait, a következő oktatóanyag érdekli, amely leírja a webes API-kat meghívó védett webes API-k létrehozásához használt kódot.

| Minta | Platform | Leírás |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2,2 webes API, asztali (WPF) | ASP.NET Core 2,2 web API hív Microsoft Graph, amelyet a Microsoft Identity platform (v 2.0) használatával hívhat meg egy WPF-alkalmazásból. |
