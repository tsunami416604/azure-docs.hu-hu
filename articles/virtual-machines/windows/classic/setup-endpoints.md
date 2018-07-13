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
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618239"
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Az Azure-beli klasszikus Windows virtuális gép végpontok beállítása
Az Azure-ban a klasszikus üzemi modellel létrehozott virtuális gépek automatikusan képes az összes Windows magánhálózaton keresztül kommunikálnak a hálózati csatornát, az ugyanazon a felhőszolgáltatáson vagy virtuális hálózaton lévő más virtuális gépekkel. Azonban a számítógépek az interneten vagy más virtuális hálózatok végpontok a bejövő hálózati forgalmat a virtuális gép szükséges. Ez a cikk érhető el is [Linux rendszerű virtuális gépek](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az a **Resource Manager** -alapú üzemi modellben végpontok használatával konfigurálhatók **hálózati biztonsági csoportok (NSG)**. További információkért lásd: [a virtuális gép az Azure Portallal való külső hozzáférés engedélyezése](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ha egy Windows virtuális gép létrehozása az Azure Portalon, közös végpontok például a távoli asztal és a Windows PowerShell távoli eljáráshívás általában automatikusan létrehozza a Ön számára. További végpontokat a virtuális gép létrehozása során később szükség esetén, illetve konfigurálhatja.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>További lépések
* Az Azure PowerShell-parancsmag segítségével állítsa be a Virtuálisgép-végpont, lásd: [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* A végpont ACL kezelése az Azure PowerShell-parancsmag használatával, lásd: [PowerShell-lel kezelése hozzáférés-vezérlési listák az végpontok (ACL)](../../../virtual-network/virtual-networks-acl-powershell.md).
* Ha a Resource Manager-alapú üzemi modellben létrehozott virtuális gép, használhatja az Azure PowerShell-lel [hálózati biztonsági csoportok létrehozása](../../../virtual-network/tutorial-filter-network-traffic.md) a forgalom szabályozására a virtuális géphez.
