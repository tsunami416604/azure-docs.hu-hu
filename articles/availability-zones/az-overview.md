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
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 53c5c340dc4e91db29fbbb45893fabe6eec2bc5d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>A rendelkezésre állási zónák (előzetes verzió) Azure-ban – áttekintés

Rendelkezésre állási zónák a Súgó gombra a datacenter-szintű hibák elleni védelemben. Egy Azure-régiót belül találhatók, és mindegyiket egy rendelkezik, saját független forrás-, hálózati és hűtési kapcsolja. Rugalmasság biztosítása érdekében legalább három különálló zónákra minden engedélyezett régióban van. A fizikai és logikai elkülönítése a rendelkezésre állási zónák régión belül alkalmazások és adatok zónaszintű hibák védelmet nyújt. 

![egy zóna régióban fog konceptuális ábrázolása](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató régiók

- USA 2. keleti régiója
- Nyugat-Európa

## <a name="services-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató szolgáltatások

Azure-szolgáltatás, amely támogatja a rendelkezésre állási zónák a következők:

- Linux rendszerű virtuális gépek
- Windows rendszerű virtuális gépek
- Zonal virtuálisgép-méretezési csoportok
- Felügyelt lemezek
- Load Balancer

## <a name="supported-virtual-machine-size-families"></a>Támogatott virtuális gép mérete családok

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>Ismerkedjen meg a rendelkezésre állási zónák előzetes verzió

A rendelkezésre állási zónák preview nem áll rendelkezésre az USA keleti régiója 2 és Nyugat-Európa régiókat adott Azure-szolgáltatásokhoz. 

1. [Bejelentkezés a rendelkezésre állási zónák előzetes](http://aka.ms/azenroll). 
2. Jelentkezzen be az Azure-előfizetéshez.
3. Válasszon egy régiót, amely támogatja a rendelkezésre állási zónák.
4. Az alábbi hivatkozások valamelyikével indíthatja a rendelkezésre állási zónák a szolgáltatással. 
    - [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Hozzon létre egy zonal virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [Adjon hozzá egy felügyelt lemezt PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Terheléselosztó](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Következő lépések
- [Gyorsindítási sablonok](http://aka.ms/azqs)
