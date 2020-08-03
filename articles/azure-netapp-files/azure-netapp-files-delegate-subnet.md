---
title: Alhálózat delegálása Azure NetApp Filesra | Microsoft Docs
description: Megtudhatja, hogyan delegálhat egy alhálózatot Azure NetApp Filesra. Kötet létrehozásakor a delegált alhálózatot kell megadnia.
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
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 9bb3e93b99ce8d5a61501d417a71e5e38753f5ff
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513026"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Alhálózat delegálása az Azure NetApp Fileshoz 

Az alhálózatot Azure NetApp Filesre kell delegálni.   Kötet létrehozásakor meg kell adnia a delegált alhálózatot.

## <a name="considerations"></a>Megfontolandó szempontok
* A varázsló egy új alhálózat alapértelmezett értékének egy/24 hálózati maszkban való létrehozására szolgál, amely 251 elérhető IP-címekkel rendelkezik. A/28 hálózati maszk használatával, amely 11 felhasználható IP-címet biztosít, elegendő a szolgáltatáshoz.
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


