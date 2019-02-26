---
title: Azure Service Fabric parancssori felület (sfctl) szkript-üzembehelyezési minta
description: Alkalmazás üzembe helyezése egy Azure Service Fabric-fürtön az Azure Service Fabric parancssori felület használatával
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 1723bdd29bf85b11bf0a5d86d51cb813abd6e6b4
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804093"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön

Ez a példaszkript egy alkalmazáscsomagot másol a fürt lemezképtárolójába, regisztrálja az alkalmazás típusát a fürtben, és létrehozza az alkalmazás egy példányát az alkalmazástípusból. Ekkor jönnek létre az alapértelmezett szolgáltatások is.

Amennyiben szükséges, telepítse a [Service Fabric parancssori felületet](../service-fabric-cli.md).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Ha ezzel elkészült, a [remove](cli-remove-application.md) szkripttel eltávolíthatja az alkalmazást. A remove szkript eltávolítja az alkalmazáspéldányt, törli az alkalmazástípus regisztrációját, és eltávolítja az alkalmazáscsomagot a lemezképtárolóból.

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
