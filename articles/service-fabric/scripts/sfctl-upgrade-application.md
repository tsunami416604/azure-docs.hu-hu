---
title: "Service Fabric CLI parancsfájl minta - fürt alkalmazás frissítése"
description: "Service Fabric CLI parancsfájl minta - alkalmazás egy új verziójával frissíti. Ebben a példában az új bitként a központilag telepített alkalmazás is frissíti."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: f91ea8c2f8c6e9a6ce38cf72be2c61d9e5218b23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Az alkalmazás tanúsítványának felvétele a Service Fabric-fürt

Ez a parancsfájlpélda feltölt egy meglévő alkalmazás új verzióját, és ezután frissíti az új bitként a központilag telepített alkalmazás.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Következő lépések

További információkért lásd: a [Service Fabric CLI dokumentáció](../service-fabric-cli.md).

Azure Service Fabric további Service Fabric CLI-példák találhatók a [Service Fabric CLI minták](../samples-cli.md).
