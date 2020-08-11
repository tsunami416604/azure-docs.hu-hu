---
title: A Azure NetApp Files tárolási hierarchiája | Microsoft Docs
description: A cikk a tárhely-hierarchiát ismerteti, beleértve az Azure NetApp Files-fiókokat, -kapacitáskészleteket és -köteteket.
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
ms.topic: overview
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066474"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>A Azure NetApp Files tárolási hierarchiája

Mielőtt létrehozna egy kötetet az Azure NetApp Filesban, meg kell vásárolnia és be kell állítania egy készletet a kiosztott kapacitáshoz.  Kapacitáskészlet beállításához rendelkeznie kell NetApp-fiókkal. Az Azure NetApp Files tárhely-hierarchiájának megismerése segítségére lehet az Azure NetApp Files-erőforrások beállítása és kezelése során.

> [!IMPORTANT] 
> A Azure NetApp Files jelenleg nem támogatja az előfizetések közötti erőforrás-áttelepítést.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-fiókok

- A NetApp-fiókok a kapacitáskészlet-tagok felügyeleti csoportosítására szolgálnak.  
- A NetApp-fiók nem egyezik meg az általános Azure Storage-fiókkal. 
- A NetApp-fiók regionális hatókörrel rendelkezik.   
- Több NetApp-fiókkal is rendelkezhet egy régióban, de minden NetApp-fiók egyetlen régióhoz van társítva.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapacitáskészletek

- A kapacitáskészletek mérése a kiosztott kapacitás alapján történik.  
- A kapacitást a megvásárolt rögzített SKU-nak (például egy 4 TiB-kapacitásnak) kell kiépíteni.
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
- Mindegyik kapacitási készlet csak egy NetApp-fiókhoz tartozhat. Egy NetApp-fiókon belül azonban több kapacitású készlet is rendelkezhet.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  
- A kapacitási készlet nem törölhető, amíg a kapacitás-készletben lévő összes kötetet nem törölték.

## <a name="volumes"></a><a name="volumes"></a>Kötetek

- A köteteket a logikai kapacitások fogyasztása méri, és méretezhető. 
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
- [Regisztrálás az Azure NetApp Filesra](azure-netapp-files-register.md)
