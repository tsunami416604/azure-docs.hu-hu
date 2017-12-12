---
title: "Állítsa be a klasszikus Linux virtuális gép végpontok |} Microsoft Docs"
description: "Ismerje meg, hozzon létre egy Linux virtuális Gépet az Azure portálon végpontjainak és a Linux virtuális gépek közötti kommunikáció biztosítása az Azure-ban"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: bb82bfc4c5c85b9cacce907578326cab4e518766
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Linux klasszikus virtuális gépre az Azure-végpontok beállítása
Az Azure klasszikus telepítési modellel létrehozott, az összes Linux virtuális gépek automatikusan kommunikálhat más virtuális gépekkel a felhőalapú szolgáltatás- vagy virtuális hálózati magánhálózati csatornán keresztül. Azonban a számítógépek az interneten vagy más virtuális hálózatok végpontok át tudja irányítani a bejövő hálózati forgalmat a virtuális gép szükséges. Ez a cikk érhető el is [Windows virtuális gépek](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az a **erőforrás-kezelő** üzembe helyezési modelljével végpontok használatával konfigurálhatók **hálózati biztonsági csoportokkal (NSG-k)**. További információkért lásd: [portok, valamint a végpontok](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Linux virtuális gép létrehozása az Azure portálon, ha a Secure Shell (SSH) végpont általában, automatikusan létrejön. További végpontokat konfigurálhat a virtuális gép létrehozása során vagy azt követően szükség szerint.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Következő lépések
* A virtuális gép végpont használatával is létrehozhat a [Azure parancssori felület](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Futtassa a **azure virtuálisgép-végpont létrehozása** parancsot.
* Ha létrehozott egy virtuális gépet a Resource Manager üzembe helyezési modellel, az Azure parancssori felület használható erőforrás-kezelő módban [hálózati biztonsági csoportok létrehozása](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) a virtuális gép forgalmának ellenőrzésére.
