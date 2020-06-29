---
title: Alhálózat delegálása Azure NetApp Filesra | Microsoft Docs
description: Leírja, hogyan delegálhat egy alhálózatot Azure NetApp Filesra.
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
ms.topic: how-to
ms.date: 05/04/2020
ms.author: b-juche
ms.openlocfilehash: 713a72b0a406d2038d56dc6fcc41e169d02c54eb
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483618"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Alhálózat delegálása az Azure NetApp Fileshoz 

Az alhálózatot Azure NetApp Filesre kell delegálni.   Kötet létrehozásakor meg kell adnia a delegált alhálózatot.

## <a name="considerations"></a>Megfontolandó szempontok
* A varázsló egy új alhálózat alapértelmezett értékének egy/24 hálózati maszkban való létrehozására szolgál, amely 251 elérhető IP-címekkel rendelkezik. A/28 hálózati maszk használatával, amely 16 használható IP-címet biztosít, elegendő a szolgáltatáshoz.
* Minden egyes Azure-Virtual Network (VNet) esetében csak egy alhálózat delegálható Azure NetApp Fileshoz.   
   Az Azure lehetővé teszi, hogy több delegált alhálózatot hozzon létre egy VNet.  Az új kötetek létrehozására tett kísérletek azonban sikertelenek lesznek, ha egynél több delegált alhálózatot használ.  
   Egy VNet csak egyetlen delegált alhálózat tartozhat. A NetApp-fiókok több virtuális hálózatok is telepíthetnek köteteket, amelyek mindegyike saját delegált alhálózattal rendelkezik.  
* A delegált alhálózatban nem lehet hálózati biztonsági csoportot vagy szolgáltatási végpontot kijelölni. Ennek hatására az alhálózat-delegálás sikertelen lesz.
* A globálisan összetartozó virtuális hálózatokból származó kötetek hozzáférése jelenleg nem támogatott.
* A [felhasználó által definiált egyéni útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) létrehozása a (z) előtaggal rendelkező virtuálisgép-alhálózatokon a következőhöz delegált alhálózatok nem támogatottak: Azure NetApp files. Ez hatással lesz a virtuális gép kapcsolatára.

## <a name="steps"></a>Lépések 
1.  Lépjen a Azure Portal **virtuális hálózatok** paneljére, és válassza ki a Azure NetApp Files használni kívánt virtuális hálózatot.    

1. Válassza ki az **alhálózatok** elemet a virtuális hálózat panelen, és kattintson a **+ alhálózat** gombra. 

1. Hozzon létre egy új alhálózatot, amelyet a Azure NetApp Files használhat az alhálózat hozzáadása lapon a következő kötelező mezők kitöltésével:
    * **Név**: adja meg az alhálózat nevét.
    * **Címtartomány**: az IP-címtartomány megadására szolgáló tartomány.
    * **Alhálózat delegálása**: válassza a **Microsoft. NetApp/kötetek**lehetőséget. 

      ![Alhálózat delegálása](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Létrehozhat és delegálhat egy alhálózatot is, amikor [kötetet hoz létre a Azure NetApp Fileshoz](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>További lépések  
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


