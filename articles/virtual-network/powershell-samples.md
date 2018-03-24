---
title: Virtuális hálózat az Azure PowerShell-példák |} Microsoft Docs
description: Az Azure PowerShell-példák virtuális hálózat.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: c9cb8da2a6a7512daa6721af90d5b21c6ba5e8e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Virtuális hálózat az Azure PowerShell-példák

A következő táblázat az Azure Powershell-parancsfájlok mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [A többrétegű alkalmazások virtuális hálózat létrehozása](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat felé irányuló forgalom korlátozódik HTTP, közben az adatforgalom a háttér-alhálózathoz korlátozott, az SQL-1433-as port. |
| [A két partner virtuális hálózatok](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Hoz létre, és két virtuális hálózat ugyanabban a régióban kapcsolódik. |
| [A hálózati virtuális készülék-útvonal forgalmát](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Hoz létre egy virtuális hálózati előtér- és alhálózatok és virtuális gép, amely képes irányíthatja a forgalmat a két alhálózat között. |
| [Bejövő és kimenő virtuális gép hálózati forgalom szűrésére](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat bejövő hálózati forgalmát a HTTP és HTTPS korlátozódik. Kimenő forgalom az internethez, a háttér-alhálózatból nem engedélyezett. |
