---
title: Az Azure-alkalmazások megoldás sablon ajánlat közzétételi útmutató
description: Ez a cikk ismerteti a piactéren közzétett megoldássablon követelményei
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059633"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Az Azure-alkalmazások: Megoldás sablon ajánlat közzétételi útmutató

Megoldássablonok a fő módon lehet a megoldás a piactéren közzétett tartoznak. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Ezek a tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."

Az Azure-alkalmazást használja: a megoldáshoz szükséges egy egyszerű virtuális Gépet túl további telepítési és konfigurációs automatizálási megoldás sablon ajánlat típusát. Előfordulhat, hogy automatizálja az Azure-alkalmazások használata egy vagy több virtuális gép üzembe helyezése: megoldássablonok. Hálózati és tárolási erőforrásokat is üzembe helyezése. Az Azure apps: megoldássablonok ajánlat típus automation értékelemeket biztosít a virtuális gépekre, és a teljes IaaS-alapú megoldásokat.

## <a name="requirements-for-solution-templates"></a>Megoldássablonok követelményei

|Követelmények |Részletek  |
|---------|---------|
|Számlázással és méréssel    |  Az erőforrások lesznek üzembe helyezve, az ügyfél Azure-előfizetésében. Használatalapú fizetés (ÁTTÉRÉSHEZ) virtuális gépek lesz feldolgozva, Microsoft, az ügyfél az Azure-előfizetés (ÁTTÉRÉSHEZ) keresztül a számlázás az ügyfélnél 
Esetén bring-your-saját licenc míg infrastrukturális költségeit az ügyfél-előfizetés a Microsoft ki, fog transact a szoftverlicenc-díjak, az ügyfél közvetlenül        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   Windows vagy Linux rendszerű virtuális gépek kell épül.<ul> <li>Linux rendszerű virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Linux-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Windows-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>További lépések
Ha ezt még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot
