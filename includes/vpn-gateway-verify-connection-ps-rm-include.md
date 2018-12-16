---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444152"
---
A sikeres kapcsolat ellenőrzéséhez használja a Get-AzureRmVirtualNetworkGatewayConnection parancsmagot a -Debug argumentummal vagy anélkül. 

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az „A” lehetőséget az összes futtatásához. A példában a „-Name” a tesztelni kívánt kapcsolat nevére utal.

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot „Csatlakoztatva”, és láthatja a bemenő és kimenő bájtokat.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```