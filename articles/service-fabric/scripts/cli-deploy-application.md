---
title: "Az Azure Service Fabric CLI parancsfájl mintaalkalmazás telepítését."
description: "Alkalmazás üzembe helyezése az Azure Service Fabric-fürt, az Azure Service Fabric parancssori felület használatával"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: a9b7b2e3a8355aa0da0069bd27d2f61d8b5b8028
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>A Service Fabric-fürt alkalmazás központi telepítése

Ez a parancsfájlpélda másolja egy alkalmazáscsomagot a fürt lemezképtárolóhoz, az alkalmazástípus regisztrálása a fürtben, és létrehoz egy alkalmazáspéldányt az alkalmazástípus. Alapértelmezett szolgáltatások is létrejön, most.

Szükség esetén telepítse a [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Mintaparancsfájl

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Ha elkészült, a [eltávolítása](cli-remove-application.md) parancsfájl segítségével távolítsa el az alkalmazást. A remove-parancsfájl törli az alkalmazáspéldány, az alkalmazástípus regisztrációjának törlése, és az alkalmazáscsomag törlése az image store.

## <a name="next-steps"></a>Következő lépések

További információkért lásd: a [Service Fabric CLI dokumentáció](../service-fabric-cli.md).

Azure Service Fabric további Service Fabric CLI-példák találhatók a [Service Fabric CLI minták](../samples-cli.md).
