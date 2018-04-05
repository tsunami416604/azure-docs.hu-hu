---
title: Állítsa be a klasszikus windowsos virtuális gép végpontja |} Microsoft Docs
description: Ismerje meg, hozzon létre végpontok a klasszikus Windows virtuális gépek Azure-portálon az Azure-ban és Windows rendszerű virtuális gép közötti kommunikáció biztosítása.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
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
ms.openlocfilehash: d64feff341e389df4079c0603a414f0d40b754e7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>A klasszikus Azure-ban Windows rendszerű virtuális gép végpontja beállítása
Minden Windows virtuális gépek az Azure klasszikus telepítési modellel létrehozott képes automatikusan egy olyan magánhálózat protokollt használó kommunikációra hálózati csatornát a felhőalapú szolgáltatás- vagy virtuális hálózati más virtuális gépekkel. Azonban a számítógépek az interneten vagy más virtuális hálózatok végpontok át tudja irányítani a bejövő hálózati forgalmat a virtuális gép szükséges. Ez a cikk érhető el is [Linux virtuális gépek](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az a **erőforrás-kezelő** üzembe helyezési modelljével végpontok használatával konfigurálhatók **hálózati biztonsági csoportokkal (NSG-k)**. További információkért lásd: [külső férhessenek hozzá a virtuális Gépet az Azure portál használatával](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Amikor egy Windows virtuális gép létrehozása az Azure portálon, például a távoli asztal és a Windows PowerShell távoli eljáráshívás közös végpontok általában meg automatikusan létrejönnek. További végpontokat konfigurálhat a virtuális gép létrehozása során vagy azt követően szükség szerint.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>További lépések
* Az Azure PowerShell parancsmag használatával állítson be egy virtuális gép végpontjának, lásd: [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Az Azure PowerShell-parancsmag használatával felügyeli a végponti ACL, lásd: [kezelése hozzáférés listák (ACL) végpontok PowerShell használatával](../../../virtual-network/virtual-networks-acl-powershell.md).
* Ha egy virtuális gépet a Resource Manager üzembe helyezési modellel létrehozott, használhatja az Azure PowerShell [hálózati biztonsági csoportok létrehozása](../../../virtual-network/tutorial-filter-network-traffic.md) a virtuális gép forgalmának ellenőrzésére.
