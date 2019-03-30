---
title: Service Fabric parancssori felület példaszkriptje – Fürtön lévő alkalmazás frissítése
description: Service Fabric parancssori felület példaszkriptje – Alkalmazás frissítése egy új verzióval. Ez a példa egy üzembe helyezett alkalmazást is frissít az új elemekkel.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: ffc60279ae414055c893c024d0ffd98267e6655f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662941"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz

Ez a példaszkript egy meglévő alkalmazás új verzióját tölti fel, majd frissíti az üzembe helyezett alkalmazást az új elemekkel.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
