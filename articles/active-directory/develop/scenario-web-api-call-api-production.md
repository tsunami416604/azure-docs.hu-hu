---
title: Webes API-hívás webes API-k áthelyezése az éles környezetbe | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan helyezhet át olyan webes API-t, amely webes API-kat hív meg éles környezetben.
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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753342"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webes API: áthelyezés éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k hívásához, áthelyezheti az alkalmazást az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Részletek

Most, hogy már ismeri a webes API-k saját webes API-ból való meghívásának alapjait, a következő oktatóanyag érdekli, amely leírja a webes API-kat meghívó védett webes API-k létrehozásához használt kódot.

| Sample | Platform | Leírás |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 1. fejezet | ASP.NET Core webes API, asztali (WPF) | ASP.NET Core webes API-hívások Microsoft Graph, amelyet a Microsoft Identity platform használatával hívhat meg egy WPF-alkalmazásból. |
