---
title: Webes API-k áthelyezése webes API-k használatával éles környezetbe – Microsoft Identity platform | Azure
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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518198"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webes API: áthelyezés éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k hívásához, áthelyezheti az alkalmazást az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Tudjon meg többet

Most, hogy már ismeri a webes API-k saját webes API-ból való meghívásának alapjait, a következő oktatóanyag érdekli, amely leírja a webes API-kat meghívó védett webes API-k létrehozásához használt kódot.

| Sample | Platform | Leírás |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 1. fejezet | ASP.NET Core webes API, asztali (WPF) | ASP.NET Core webes API-hívások Microsoft Graph, amely a Microsoft Identity platform (v 2.0) használatával egy WPF-alkalmazásból hívható meg. |
