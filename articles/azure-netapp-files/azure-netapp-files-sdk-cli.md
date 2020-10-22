---
title: SDK-k és CLI-eszközök Azure NetApp Files | Microsoft Docs
description: 'Ismerje meg a Azure NetApp Files támogatott SDK-kat és azok közzétett helyét a GitHubon, valamint a támogatott parancssori eszközöket: Azure CLI és PowerShell.'
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: b-juche
ms.openlocfilehash: f7b9cabdc7c2c706ff3b8dd5a0b3b5f7ed3666d6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369029"
---
# <a name="azure-netapp-files-sdks-cli-tools-and-arm-templates"></a>SDK-k, CLI-eszközök és ARM-sablonok Azure NetApp Files

Ez a cikk a Azure NetApp Files által támogatott SDK-kat, parancssori (CLI) eszközöket és Azure Resource Manager (ARM-sablonokat) sorolja fel.

## <a name="supported-sdks"></a>Támogatott SDK-k 

Az alábbi táblázat felsorolja a támogatott SDK-kat.  A támogatott SDK-k részletei a GitHubon közzétett helyükön találhatók.  

|    Nyelv    |    SDK közzétett helye a GitHubon    |
|------------------|--------------------------------------------------------------|
|    .NET  |    [Azure/Azure-SDK-for-net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/netapp)    |
|    Python  |  [Azure/Azure-SDK-for-Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/netapp)    |
|    Indítás    |    [Azure/Azure-SDK-for-go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/netapp)       |
|    Java |     [Azure/Azure-SDK-for-Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/netapp) |
|    JavaScript    |    [Azure/Azure-SDK-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/netapp/arm-netapp)    |
|    Ruby   |    [Azure/Azure-SDK-for-Ruby](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_netapp)    |

## <a name="cli-tools"></a>CLI-eszközök

Az alábbi táblázat felsorolja a támogatott CLI-eszközöket és azok parancs-hivatkozását.   

|    Eszköz    |    A parancs leírása    |
|------------------|--------------------------------------------|
|    Azure CLI  |    [az netappfiles](/cli/azure/netappfiles?view=azure-cli-latest&preserve-view=true)    |
|    PowerShell   |    [Azure NetApp Files Azure PowerShell](/powershell/module/az.netappfiles/?view=azps-2.5.0#netapp_files&preserve-view=true)    |

## <a name="code-samples"></a>Kódminták

Lásd: [Azure NetApp Files-kód mintái](/samples/browse/?filter-products=netapp&products=azure-netapp-files).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok  

A Azure Resource Manager (ARM) lehetővé teszi, hogy az alkalmazásai deklaratív sablonnal legyenek kiépítve. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.   

Tekintse [meg a Azure NetApp Files elérhető ARM-sablonokat](https://azure.microsoft.com/resources/templates/?term=anf).

## <a name="next-steps"></a>Következő lépések   
 [Azure SDK-k letöltése](https://azure.microsoft.com/downloads/)
