---
title: Áthelyezés az éles környezetbe webes API-kat meghívó webalkalmazások számára – Microsoft Identity platform | Azure
description: Útmutató a webes API-kat meghívó webalkalmazások éles környezetbe való áthelyezéséhez.
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
ms.openlocfilehash: f38755e669a62e9fe74aa6281daccdf39e1f117c
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443568"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webalkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k hívásához, Ismerje meg, hogyan helyezheti át az éles környezetbe.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

További információt a ASP.NET Core Web Apps teljes körű, progresszív oktatóanyagának kipróbálásával foglalkozó témakörben talál. Az oktatóanyag:

- Bemutatja, hogyan lehet a felhasználókat több célközönségbe vagy nemzeti felhőbe, vagy közösségi identitások használatával aláírni.
- Meghívja a Microsoft Graph.
- Több Microsoft API-t hív meg.
- Kezeli a növekményes beleegyezett.
- Meghívja saját webes API-ját.

[ASP.NET Core webalkalmazás-oktatóanyag](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
