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
ms.openlocfilehash: c1b5560e16b68565c37365ac9c2cba217d9b1b90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150059"
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