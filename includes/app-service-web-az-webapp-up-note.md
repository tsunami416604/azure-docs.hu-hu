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
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559487"
---
> [!NOTE]
> Az `az webapp up` parancs a következő műveleteket hajtja végre:
>
>- Hozzon létre egy alapértelmezett [erőforráscsoportot](/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Hozzon létre egy alapértelmezett [app Service-csomagot](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Hozzon létre egy alkalmazást](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a megadott névvel.
>
>- A [zip telepíti](../articles/app-service/deploy-zip.md) a fájlokat az aktuális munkakönyvtárból az alkalmazásba.
>