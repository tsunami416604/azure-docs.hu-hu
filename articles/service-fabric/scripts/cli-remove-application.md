---
title: Azure Service Fabric parancssori felület (sfctl) eltávolítási példaszkriptje
description: Alkalmazás eltávolítása az Azure Service Fabric fürtből az Azure Service Fabric parancssori felület használatával
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7c3739a2e5e15e77cb88ffb9d3effe9abdd0b848
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592235"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Alkalmazás eltávolítása egy Service Fabric fürtről

Ez a példaszkript törli a Service Fabric-alkalmazás egy éppen futó példányát, majd törli egy alkalmazástípus- és verzió regisztrációját a fürtről.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Ezt követően az alkalmazásfájlok is törlődnek a rendszerképtárolóból. 

Amennyiben szükséges, telepítse a [Service Fabric parancssori felületet](../service-fabric-cli.md).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
