---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 8b5be0a438d9c5bb1fd0596368327c53a2d6c31f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "67179795"
---
> [!NOTE]
> Az `az webapp up` parancs a következő műveleteket hajtja végre:
>
>- Hozzon létre egy alapértelmezett [erőforráscsoportot](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Hozzon létre egy alapértelmezett [app Service-csomagot](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Hozzon létre egy alkalmazást](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a megadott névvel.
>
>- A [zip telepíti](https://docs.microsoft.com/azure/app-service/deploy-zip) a fájlokat az aktuális munkakönyvtárból az alkalmazásba.
>
