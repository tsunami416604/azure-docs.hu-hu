---
title: Alhálózat delegálása az Azure NetApp-fájlokba | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként delegálhat alhálózatot az Azure NetApp-fájlokban.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054469"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Alhálózat delegálása az Azure NetApp Fileshoz 

Alhálózatot kell delegálnia az Azure NetApp-fájlokhoz.   Kötet létrehozásakor meg kell adnia a delegált alhálózatot.

## <a name="considerations"></a>Megfontolandó szempontok
* Az új alhálózat létrehozásához használt varázsló alapértelmezés szerint /24 hálózati maszkot használ, amely 251 rendelkezésre álló IP-címet biztosít. A /28 hálózati maszk használata, amely 16 használható IP-címet biztosít, elegendő a szolgáltatáshoz.
* Minden Egyes Azure virtuális hálózat (VNet) csak egy alhálózat delegálható az Azure NetApp-fájlok.   
   Az Azure lehetővé teszi, hogy több delegált alhálózatot hozzon létre egy virtuális hálózatban.  Ha azonban egynél több delegált alhálózatot használ, az új kötet létrehozására tett kísérletek sikertelenek lesznek.
* A delegált alhálózatban nem jelölhet ki hálózati biztonsági csoportot vagy szolgáltatásvégpontot. Ha így tesz, az alhálózati delegálás sikertelen lesz.
* A kötetek globális társviszony-létesítési virtuális hálózatról való hozzáférése jelenleg nem támogatott.
* Az Azure NetApp-fájlok által delegált alhálózathoz címelőtaggal (cél) rendelkező, [felhasználó által definiált egyéni útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) létrehozása nem támogatott. Ha így tesz, hatással lesz a virtuális gép kapcsolatára.

## <a name="steps"></a>Lépések 
1.  Nyissa meg a **virtuális hálózatok** panelt az Azure Portalon, és válassza ki az Azure NetApp-fájlokhoz használni kívánt virtuális hálózatot.    

1. Válassza **az Alhálózatok elemet** a Virtuális hálózat panelen, és kattintson az **+Alhálózat** gombra. 

1. Hozzon létre egy új alhálózatot az Azure NetApp-fájlok számára az Alhálózat hozzáadása lap következő szükséges mezőinek kitöltésével:
    * **Név**: Adja meg az alhálózat nevét.
    * **Címtartomány**: Adja meg az IP-címtartományt.
    * **Alhálózat delegálása**: Válassza a **Microsoft.NetApp/volumes lehetőséget.** 

      ![Alhálózat delegálása](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Alhálózatot is létrehozhat és delegálhat, amikor [kötetet hoz létre az Azure NetApp Files számára.](azure-netapp-files-create-volumes.md) 

## <a name="next-steps"></a>További lépések  
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [További információ az Azure-szolgáltatások virtuális hálózati integrációjáról](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


