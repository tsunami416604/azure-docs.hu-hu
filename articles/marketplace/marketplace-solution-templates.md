---
title: Az Azure-alkalmazások megoldás sablon ajánlat közzétételi útmutató
description: Ez a cikk ismerteti a megoldássablon közzététele az Azure piactéren.
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
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: 8e8bcc43a3bf26a40055e4258bf73bb047e61ef9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737603"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Az Azure-alkalmazások: Megoldás sablon az ajánlat közzétételi útmutató

Megoldássablonok a fő módon lehet a megoldás a piactéren közzétett tartoznak. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Az Azure-alkalmazást használja: a megoldáshoz szükséges túl egyetlen virtuális gép további telepítési és konfigurációs automation megoldás sablon ajánlat típusát. Előfordulhat, hogy automatizálja az Azure-alkalmazások használata egy vagy több virtuális gép üzembe helyezése: megoldássablonok. Hálózati és tárolási erőforrásokat is üzembe helyezése. Az Azure apps: megoldássablonok ajánlat típus automation értékelemeket biztosít a virtuális gépekre, és a teljes IaaS-alapú megoldásokat.

A megoldás sablonok tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."


## <a name="requirements-for-solution-templates"></a>Megoldássablonok követelményei

| **Követelmények** | **Részletek**  |
| ---------------  | -----------  |
|Számlázással és méréssel    |  Az erőforrások lesznek üzembe helyezve, az ügyfél Azure-előfizetésében. Használatalapú fizetés (ÁTTÉRÉSHEZ) virtuális gépek az ügyfélnél, Microsoft, a számlázás az ügyfél az Azure-előfizetés (ÁTTÉRÉSHEZ) keresztül lesz kell feldolgozva.  <br/> Esetén bring-your-saját licenc használata (BYOL) amíg az ügyfél-előfizetés infrastrukturális költségeit a Microsoft ki, fog transact a szoftverlicenc-díjak, az ügyfél közvetlenül.   |
|Azure-kompatibilis virtuális merevlemez (VHD)  |   Windows vagy Linux rendszerű virtuális gépek kell épül.  További információ [lásd az Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Ügyfél használati megnevezése | Az Azure piactéren közzétett minden megoldássablonok ügyfél használati tesznek elérhetővé; ilyenek engedélyezése szükséges. Az ügyfél használati tesznek elérhetővé; ilyenek és engedélyezését a további információkért lásd: [Azure-partneri ügyfél használati tesznek elérhetővé; ilyenek](./azure-partner-customer-usage-attribution.md).  |
|  |  |


## <a name="next-steps"></a>További lépések
Ha ezt még nem tette meg, [regisztrálása](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van, és hozza létre egy új ajánlat vagy az dolgozik egy meglévő, jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot.
