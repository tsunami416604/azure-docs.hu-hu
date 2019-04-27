---
title: Service Fabric parancssori felületi példaszkript – Fürtön lévő alkalmazások listázása
description: Service Fabric parancssori felületi példaszkript – Service Fabric-fürtön lévő alkalmazások listázása.
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/13/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: ''
ms.openlocfilehash: 8fd83190f3cf92ef0f88ff0fb2a807e03199a5c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621930"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric-fürtön futó alkalmazások listázása

Ez a példaszkript egy Service Fabric-fürthöz csatlakozik, és az összes üzembe helyezett alkalmazást felsorolja.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Példaszkript

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Retrieve all applications from the cluster
sfctl application list
```

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
<!--Update_Description: update meta properties -->