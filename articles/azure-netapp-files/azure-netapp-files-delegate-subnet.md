---
title: Egy alhálózatot az Azure Files-NetApp delegálása |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy alhálózatot az Azure Files-NetApp delegálására.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711816"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Alhálózat delegálása az Azure NetApp Fileshoz 

Az Azure Files-NetApp egy alhálózatot kell delegálni.   Kötet létrehozásakor kell a delegált alhálózatot adjon meg.

## <a name="considerations"></a>Megfontolandó szempontok
* Új alhálózat egy/24, az alapértelmezett érték létrehozására szolgáló varázsló hálózati maszk, amely 251 elérhető IP-címek biztosít. Egy/28-as használatával hálózati maszk, amely a 16 használható IP-címet biztosít, a szolgáltatás elegendő.
* Az egyes Azure virtuális hálózatok (Vnet) csak egy alhálózatot az Azure Files-NetApp delegálható.
* Nem egy hálózati biztonsági csoportot kijelölje vagy szolgáltatási végpont delegált alhálózaton. Ekkor a alhálózati delegálás sikertelen lesz.
* A kötet egy globálisan társított virtuális hálózaton való hozzáférés jelenleg nem támogatott.
* Létrehozás [felhasználó által definiált egyéni útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) címmel rendelkező Virtuálisgép-alhálózatok előtag (cél) egy alhálózathoz, az Azure Files-NetApp delegált használata nem támogatott. Ez hatással lesz a virtuális gépek kapcsolatai.

## <a name="steps"></a>Lépések 
1.  Nyissa meg a **virtuális hálózatok** panel az Azure Portalon, majd válassza a virtuális hálózatot, amelyet szeretne használni az Azure NetApp fájlokhoz.    

1. Válassza ki **alhálózatok** , a virtuális hálózat paneljén, majd kattintson a **+ alhálózat** gombra. 

1. Hozzon létre egy új alhálózatot használni az Azure NetApp fájlokhoz; Ehhez hajtsa végre a következő kötelező mezőket az alhálózat hozzáadása oldalon:
    * **Név**: Adja meg az alhálózat neve.
    * **Címtartomány**: Adja meg az IP-címtartományt.
    * **Alhálózat delegálás**: Válassza ki **Microsoft.NetApp/volumes**. 

      ![Alhálózat delegálás](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Is hozhat létre, és egy alhálózatot delegálása amikor Ön [hozzon létre egy kötetet a NetApp Azure-fájlok](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>További lépések  
* [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)
* [Virtuális hálózat integrációja Azure-szolgáltatások ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


