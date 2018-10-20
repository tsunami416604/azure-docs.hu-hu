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
ms.openlocfilehash: 03dc6d8bb95a952a77be31f79df36a2c1ddc8ffc
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458639"
---
Ellenőrizheti, hogy a kapcsolat sikeresen létrejött, a "Get-AzureVNetConnection" parancsmag használatával.

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Szóközöket tartalmaz, a a virtuális hálózat nevét idézőjelek között kell lennie.

  ```azurepowershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot "Csatlakoztatva" jeleníti meg, és láthatja a bemenő és kimenő bájtokat.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal