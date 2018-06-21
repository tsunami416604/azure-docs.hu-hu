---
title: Azure Service Fabric parancssori felület (sfctl) szkript-üzembehelyezési minta
description: Alkalmazás üzembe helyezése egy Azure Service Fabric-fürtön az Azure Service Fabric parancssori felület használatával
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 7e2bd212e88b3efcf7f3aad3ef1555b3fc48ef1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641730"
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
