---
title: Klasszikus Linux virtuális gép létrehozása az Azure klasszikus parancssori felület |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép az Azure klasszikus parancssori felület, a klasszikus üzemi modell használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 507d9e12a37d7bf187a3e56b04cb47ac0104773d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982041"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Klasszikus Linuxos virtuális gép létrehozása a klasszikus Azure CLI-vel
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Az erőforrás-kezelő verzióját, lásd: [Itt](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a témakör ismerteti a Linux rendszerű virtuális gép (VM) létrehozása az Azure klasszikus parancssori a klasszikus üzemi modell használatával. Az elérhető egy Linuxos rendszerképet használunk **LEMEZKÉPEK** az Azure-ban. A klasszikus Azure CLI-parancsok adja meg a következő konfigurációs beállításokkal, többek között:

* A virtuális gép csatlakoztatása virtuális hálózathoz
* A virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz
* A virtuális gép hozzáadása egy meglévő tárfiókot
* A virtuális gép hozzáadása egy rendelkezésre állási csoport vagy a hely

> [!IMPORTANT]
> Ha azt szeretné, hogy a virtuális gép egy virtuális hálózatot használ, így is kapcsolódhat hozzá közvetlenül az állomásnév vagy állítsa be a létesítmények közötti kapcsolatokat, győződjön meg arról, a virtuális gép létrehozásakor adja meg a virtuális hálózatot. Virtuális gép beállítható úgy, hogy csatlakozzon a virtuális hálózat csak a virtuális gép létrehozásakor. További információ a virtuális hálózatok: [Azure Virtual Network áttekintése](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>A klasszikus üzemi modell használatával Linux virtuális gép létrehozása
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

