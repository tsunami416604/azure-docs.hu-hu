---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 591da67e6411d0e859076f0a3c3c38afc1ebe1f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197890"
---
Ellenőrizheti, hogy sikerült-e a kapcsolat a "Get-AzureVNetConnection" parancsmag használatával.

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Szóközöket tartalmaz, a virtuális hálózat nevét idézőjelek között kell lennie.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapota jeleníti meg, mint a "Csatlakoztatott", és láthatja a bemenő és kimenő bájtokat.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal