---
title: Klasszikus Linuxos virtuális gép végpontok beállítása |} A Microsoft Docs
description: Ismerje meg, engedélyezi a kommunikációt a Linux rendszerű virtuális gép az Azure-ban Linux rendszerű virtuális gép az Azure Portalon a végpontok beállítása
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295687"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Linux rendszerű klasszikus virtuális gép az Azure-végpontok beállítása
Az Azure-ban a klasszikus üzemi modellel létrehozott összes Linux rendszerű virtuális gépek automatikusan képes kommunikálni az ugyanazon a felhőszolgáltatáson vagy virtuális hálózaton lévő más virtuális gépekkel egy magánhálózati csatornán. Azonban a számítógépek az interneten vagy más virtuális hálózatok végpontok a bejövő hálózati forgalmat a virtuális gép szükséges. Ez a cikk érhető el is [Windows virtuális gépek](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az a **Resource Manager** -alapú üzemi modellben végpontok használatával konfigurálhatók **hálózati biztonsági csoportok (NSG)**. További információkért lásd: [portok és végpontok megnyitása](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Linux rendszerű virtuális gép az Azure Portalon hoz létre, amikor egy végpontot a Secure Shell (SSH) általában, automatikusan létrejön. További végpontokat a virtuális gép létrehozása során később szükség esetén, illetve konfigurálhatja.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>További lépések
* Egy Virtuálisgép-végpont használatával is létrehozhat a [Azure parancssori felület](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Futtassa a **azure virtuálisgép-végpont létrehozása** parancsot.
* Ha a Resource Manager-alapú üzemi modellben létrehozott virtuális gép, használhatja a Resource Manager módban az Azure CLI [hálózati biztonsági csoportok létrehozása](../../../virtual-network/tutorial-filter-network-traffic-cli.md) a forgalom szabályozására a virtuális géphez.
