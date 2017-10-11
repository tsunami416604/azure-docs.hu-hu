---
title: "Klasszikus Linux virtuális gép létrehozása az Azure CLI 1.0 |} Microsoft Docs"
description: "Útmutató: az Azure CLI 1.0 klasszikus telepítési modellel rendelkező Linux virtuális gép létrehozása"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 8ddbacbbb70c0cf1a2537fab4d981a316610a4d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Klasszikus Linux virtuális gép létrehozása az Azure parancssori felület 1.0
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager-verziójáért lásd: [Itt](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a témakör a Linux virtuális gép (VM) létrehozása az Azure CLI 1.0 klasszikus telepítési modellel rendelkező. A Linux-lemezkép az elérhető használjuk **képek** az Azure-on. Az Azure CLI 1.0 parancsok adja meg a következő konfigurációs beállításokkal, többek között:

* A virtuális gép csatlakoztatása a virtuális hálózat
* A virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz
* A virtuális gép hozzáadása egy meglévő tárfiók
* Adja hozzá a virtuális gép egy rendelkezésre állási csoport vagy a hely

> [!IMPORTANT]
> Ha azt szeretné, hogy egy virtuális hálózatot használ, így kapcsolódhat az állomásnév vagy állítsa be a létesítmények közötti kapcsolatok által közvetlenül a virtuális Gépet, győződjön meg arról, hogy a virtuális gép létrehozásakor adja meg a virtuális hálózatot. A virtuális gépek beállítható úgy, hogy csatlakozzon a virtuális hálózat csak a virtuális gép létrehozásakor. A virtuális hálózatokon, lásd: [Azure virtuális hálózat áttekintése](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Linux virtuális gépet a klasszikus telepítési modell létrehozása
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

