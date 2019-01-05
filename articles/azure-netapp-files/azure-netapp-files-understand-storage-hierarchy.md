---
title: Az Azure NetApp Files tárhely-hierarchiájának ismertetése | Microsoft Docs
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
ms.topic: get-started-article
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: e6792d994c01fb77739ad7a044bc7193e350e173
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053789"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Az Azure NetApp Files tárhely-hierarchiájának ismertetése

Mielőtt létrehozna egy kötetet az Azure NetApp Filesban, meg kell vásárolnia és be kell állítania egy készletet a kiosztott kapacitáshoz.  Kapacitáskészlet beállításához rendelkeznie kell NetApp-fiókkal. Az Azure NetApp Files tárhely-hierarchiájának megismerése segítségére lehet az Azure NetApp Files-erőforrások beállítása és kezelése során.

## <a name="azure_netapp_files_account"></a>NetApp-fiókok

- A NetApp-fiókok a kapacitáskészlet-tagok felügyeleti csoportosítására szolgálnak.  
- A NetApp-fiók nem egyezik meg az általános Azure Storage-fiókkal. 
- A NetApp-fiók regionális hatókörrel rendelkezik.   
- Több NetApp-fiókkal is rendelkezhet egy régióban, de minden NetApp-fiók egyetlen régióhoz van társítva.

## <a name="capacity_pools"></a>Kapacitáskészletek

- A kapacitáskészletek mérése a kiosztott kapacitás alapján történik.  
- A kapacitás a rögzített termékváltozatok (például egy 4-TiB kapacitás) vásárolt lett kiépítve.
- Egyetlen kapacitás készlet minimális mérete 4 Tib-ra, és a maximális mérete 500 Tib-ra. 
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
  Jelenleg csak a Prémium szolgáltatásszint érhető el.
- Minden kapacitás címkészlet NetApp csak egy fiók is tartozik. Azonban akkor is több kapacitás készletek NetApp-fiókon belül.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  

## <a name="volumes"></a>Kötetek

- A kötet logikai kapacitásfogyasztásának mérjük és skálázható. Egy kötetet minimális mérete 100 GB, és a maximális méretet 92 Tib-ra.
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
-   Legfeljebb 100 kötetek Azure-előfizetésenként és régiónként rendelkezhet. 
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 
- Ugyanazon a NetApp-fiókon belül áthelyezheti a köteteket az egyik készletből egy másikba.    
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például áthelyezheti a köteteket az 1. kapacitáskészletből a 2. kapacitáskészletbe.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Files-NetApp erőforráskorlátok](azure-netapp-files-resource-limits.md)
- [Regisztráljon a Azure NetApp-fájlok](azure-netapp-files-register.md)
