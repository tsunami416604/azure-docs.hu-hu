---
title: Webes API-k webes API-k áthelyezése éles környezetbe – Microsoft identity platform | Azure
description: Ismerje meg, hogyan helyezhet át webes API-kat, amelyek webes API-kat hívnak éles környezetbe.
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
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885055"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webes API:Ugrás éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k hívásához, áthelyezheti az alkalmazást éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Részletek

Most, hogy már ismeri a web API-k hívásának alapjait a saját webes API-król, a következő oktatóanyag érdekelheti, amely ismerteti a webes API-kat meghívó védett webes API-k létrehozásához használt kódot.

| Sample | Platform | Leírás |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Webes API, Desktop (WPF) | ASP.NET Core 2.2 Web API a Microsoft Graph-ot hívja meg, amelyet egy WPF-alkalmazásból hív meg a Microsoft identity platform (2.0- es verzió) használatával. |
