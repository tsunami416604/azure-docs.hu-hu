---
title: Klasszikus Windows virtuális gép végpontok beállítása |} A Microsoft Docs
description: Ismerje meg, ha engedélyezi a kommunikációt egy Windows virtuális gépek az Azure-ban az Azure Portalon klasszikus Windows virtuális gép végpontok beállítása.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957165"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Windows virtuális gép végpontok beállítása a klasszikus üzemi modell használatával
Windows virtuális gépek (VM) hoz létre az Azure-ban a klasszikus üzemi modell használatával automatikusan képes kommunikálni a többi virtuális gép ugyanazon a felhőszolgáltatáson vagy virtuális hálózati magánhálózati csatornán keresztül. Azonban a számítógépek az interneten vagy más virtuális hálózatokhoz szükséges végpontok a bejövő hálózati forgalmat egy virtuális Gépet. 

Emellett beállíthat végpontok a [Linux rendszerű virtuális gépek](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az a **Resource Manager** -alapú üzemi modellben végpontok segítségével konfigurálhatók **hálózati biztonsági csoportok (NSG)**. További információkért lásd: [a virtuális gép külső hozzáférés engedélyezése az Azure portal használatával](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Windows virtuális gép létrehozásakor az Azure Portalon közös végpontot, a távoli asztal és a Windows PowerShell-távelérést, a végpontok például általában automatikusan létrehozza a Ön számára. Később igény szerint beállíthatja további végpontokat.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>További lépések
* Az Azure PowerShell-parancsmag segítségével állítsa be a Virtuálisgép-végpont, lásd: [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* A végpont ACL kezelése az Azure PowerShell-parancsmag használatával, lásd: [PowerShell-lel kezelése hozzáférés-vezérlési listák az végpontok (ACL)](../../../virtual-network/virtual-networks-acl-powershell.md).
* Ha a Resource Manager-alapú üzemi modellben létrehozott virtuális gép, használhatja az Azure PowerShell-lel [hálózati biztonsági csoportok létrehozása](../../../virtual-network/tutorial-filter-network-traffic.md) a forgalom szabályozására a virtuális géphez.
