---
title: Azure Containers lemezkép-ajánlat | Azure Piactér
description: A tárolóajánlat azure-piactéren való közzétételének folyamatának áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 9c0b4ca6e9a26f13d1539845ca9fb43f31a1a9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281714"
---
# <a name="containers"></a>Containers

<table> <tr> <td>Ez a szakasz bemutatja, hogyan tehet közzé egy tárolórendszerképet az <a href="https://azuremarketplace.microsoft.com">Azure Piactéren.</a>  
A tárolóajánlat típusa támogatja az Azure <a href="https://docs.microsoft.com/azure/aks/index">Kubernetes-szolgáltatás</a> példányai ként vagy <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">az Azure Container Instances-ként</a> kiépített Docker-tárolórendszerképeket, amelyeket egy <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> repository üzemeltet. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Ajánlat-összetevők

Ez a szakasz ismerteti a tároló közzétételének elemeit, és a közzétevő azure piactérre útmutatójaként szolgál. A kiadói tevékenység a következő fő részekre oszlik:

- [Előfeltételek](./cpp-prerequisites.md) – felsorolja a műszaki és üzleti követelmények létrehozása előtt vagy közzététele tároló ajánlat létrehozása előtt.
- [Hozzon létre az ajánlat](./cpp-create-offer.md) -felsorolja a szükséges lépéseket egy új tároló ajánlat bejegyzés a Cloud Partner Portal használatával.
- [Készítse elő a technikai eszközöket](./cpp-create-technical-assets.md) – hogyan hozhat létre a technikai eszközöket egy tárolómegoldáshoz ajánlatként az Azure Marketplace-en.
- [Tegye közzé az ajánlatot](./cpp-publish-offer.md) – hogyan küldheti el az ajánlatot az Azure Marketplace-en való közzétételre.

## <a name="container-publishing-process"></a>Tároló közzétételi folyamata

Az alábbi ábra a virtuális gépajánlat közzétételének magas szintű lépéseit mutatja be.
![Az ajánlat közzétételének lépései](./media/containers-offer-process.png)

A tárolóajánlat közzétételének magas szintű lépései a következők:

1. Az ajánlat létrehozása – Adjon meg részletes információkat az ajánlatról. Ez az információ a következőket tartalmazza: az ajánlat leírása, marketing anyagok, támogatási információk és az eszköz specifikációi.
2. Hozza létre az üzleti és technikai eszközök – hozza létre az üzleti eszközök (jogi dokumentumok és marketinganyagok) és technikai eszközök a kapcsolódó megoldás (a tárolók rendszerképek üzemeltetett egy Azure Container Registry.
3. A termékváltozat létrehozása – az ajánlathoz társított termékváltozatok létrehozása. Minden közzétenni kívánt lemezképhez egyedi termékváltozat szükséges.
4. Az ajánlat hitelesítése és közzététele – Az ajánlat és a technikai eszközök befejezése után benyújthatja az ajánlatot. Ez a beküldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelt, érvényesített, hitelesített, majd "éles" az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Mielőtt megfontolja ezeket a lépéseket, meg kell felelnie a tároló kitágító biztonsági és a Microsoft Azure Piactéren való közzétételéhez [szükséges technikai és üzleti követelményeknek.](./cpp-prerequisites.md)
