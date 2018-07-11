---
title: Klasszikus Linuxos virtuális gép létrehozása az Azure CLI 1.0 |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép az Azure CLI 1.0-t a klasszikus üzemi modell használatával
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
ms.openlocfilehash: 13d0ef93c3828c514e46e37494a66f7003eac827
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931614"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Klasszikus Linuxos virtuális gép létrehozása az Azure CLI 1.0-s
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Az erőforrás-kezelő verzióját, lásd: [Itt](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a témakör ismerteti hogyan hozhat létre egy Linux rendszerű virtuális gép (VM) az Azure CLI 1.0 használatával a klasszikus üzemi modellt. Az elérhető egy Linuxos rendszerképet használunk **LEMEZKÉPEK** az Azure-ban. Az Azure CLI 1.0 parancsainak adja meg a következő konfigurációs beállításokkal, többek között:

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

