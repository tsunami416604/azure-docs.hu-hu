---
title: Mi az Azure NetApp Files tárolási hierarchiája | Microsoft dokumentumok
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
ms.date: 02/27/2020
ms.author: b-juche
ms.openlocfilehash: 70d3a2a501952a5e20b1ff8e99f48f4d7aefce8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78163964"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Mi az Azure NetApp-fájlok tárolási hierarchiája?

Mielőtt létrehozna egy kötetet az Azure NetApp Filesban, meg kell vásárolnia és be kell állítania egy készletet a kiosztott kapacitáshoz.  Kapacitáskészlet beállításához rendelkeznie kell NetApp-fiókkal. Az Azure NetApp Files tárhely-hierarchiájának megismerése segítségére lehet az Azure NetApp Files-erőforrások beállítása és kezelése során.

> [!IMPORTANT] 
> Az Azure NetApp Files jelenleg nem támogatja az erőforrások közötti áttelepítést az előfizetések között.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-fiókok

- A NetApp-fiókok a kapacitáskészlet-tagok felügyeleti csoportosítására szolgálnak.  
- A NetApp-fiók nem egyezik meg az általános Azure Storage-fiókkal. 
- A NetApp-fiók regionális hatókörrel rendelkezik.   
- Több NetApp-fiókkal is rendelkezhet egy régióban, de minden NetApp-fiók egyetlen régióhoz van társítva.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapacitáskészletek

- A kapacitáskészletek mérése a kiosztott kapacitás alapján történik.  
- A kapacitást a megvásárolt rögzített sk-ek (például egy 4-TiB kapacitás) építik ki.
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
- Minden kapacitáskészlet csak egy NetApp-fiókhoz tartozhat. Egy NetApp-fiókon belül azonban több kapacitáskészlet is lehet.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  
- A kapacitáskészlet nem törölhető, amíg a kapacitáskészleten belüli összes kötetet nem törölték.

## <a name="volumes"></a><a name="volumes"></a>Kötetek

- A kötetet logikai kapacitásfelhasználással mérik, és skálázható. 
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 
- Kötet nem helyezhető át kapacitáskészletek között. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Az [alábbi, a tárolási hierarchia koncepcionális diagramjában](#conceptual_diagram_of_storage_hierarchy) például nem helyezheti át a köteteket az 1-es kapacitáskészletből a 2-es kapacitáskészletbe.
- Egy kötet nem törölhető, amíg az összes pillanatképét nem törölték.

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
- [Regisztrálás az Azure NetApp Filesra](azure-netapp-files-register.md)
