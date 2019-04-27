---
title: Service Fabric parancssori felület példaszkriptje – Fürtön lévő alkalmazás frissítése
description: Service Fabric parancssori felület példaszkriptje – Alkalmazás frissítése egy új verzióval. Ez a példa egy üzembe helyezett alkalmazást is frissít az új elemekkel.
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
origin.date: 12/06/2017
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: ''
ms.openlocfilehash: ffc60279ae414055c893c024d0ffd98267e6655f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621706"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz

Ez a példaszkript egy meglévő alkalmazás új verzióját tölti fel, majd frissíti az üzembe helyezett alkalmazást az új elemekkel.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Példaszkript

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Upload the latest bits of an application
sfctl application upload --path ~/app_package_dir_2

# Provision the new application
sfctl application provision --application-type-build-path app_package_dir_2

# Upgrade an existing up with the new version
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.

<!--Update_Description: update meta properties -->