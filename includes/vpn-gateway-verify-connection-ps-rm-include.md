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
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418024"
---
Ellenőrizheti, hogy a kapcsolat sikeresen létrejött, a "Get-AzVirtualNetworkGatewayConnection" parancsmag használatával, vagy anélkül:-Debug ". 

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az „A” lehetőséget az összes futtatásához. A példában a „-Name” a tesztelni kívánt kapcsolat nevére utal.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot „Csatlakoztatva”, és láthatja a bemenő és kimenő bájtokat.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```