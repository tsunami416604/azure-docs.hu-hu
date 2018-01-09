---
title: "Rendelkezésre állási zónák áttekintése |} Microsoft Docs"
description: "Ez a cikk áttekintést nyújt a rendelkezésre állási zónák az Azure-ban."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: c3a1758ab965d22eabfe44a410e9f72420e0148b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>A rendelkezésre állási zónák (előzetes verzió) Azure-ban – áttekintés

Rendelkezésre állási zónák a Súgó gombra a datacenter-szintű hibák elleni védelemben. Egy Azure-régiót belül találhatók, és mindegyiket egy rendelkezik, saját független forrás-, hálózati és hűtési kapcsolja. Rugalmasság biztosítása érdekében legalább három különálló zónákra minden engedélyezett régióban van. A fizikai és logikai elkülönítése a rendelkezésre állási zónák régión belül alkalmazások és adatok zónaszintű hibák védelmet nyújt. 

![egy zóna régióban fog konceptuális ábrázolása](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató régiók

- USA 2. keleti régiója
- Nyugat-Európa
- Közép-Franciaország

## <a name="services-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató szolgáltatások

Azure-szolgáltatás, amely támogatja a rendelkezésre állási zónák a következők:

- Linux rendszerű virtuális gépek
- Windows rendszerű virtuális gépek
- Zonal virtuálisgép-méretezési csoportok
- Felügyelt lemezek
- Load Balancer
- Nyilvános IP-cím

## <a name="get-started-with-the-availability-zones-preview"></a>Ismerkedjen meg a rendelkezésre állási zónák előzetes verzió

A rendelkezésre állási zónák preview USA keleti régiója 2. régiója, Nyugat-Európa, és az adott Azure-szolgáltatásokhoz központi Franciaország régiók érhető el. 

1. [Bejelentkezés a rendelkezésre állási zónák előzetes](http://aka.ms/azenroll). 
2. Jelentkezzen be az Azure-előfizetéshez.
3. Válasszon egy régiót, amely támogatja a rendelkezésre állási zónák.
4. Az alábbi hivatkozások valamelyikével indíthatja a rendelkezésre állási zónák a szolgáltatással. 
    - [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Hozzon létre egy virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Adjon hozzá egy felügyelt lemezt PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Terheléselosztó](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](http://aka.ms/azqs)
