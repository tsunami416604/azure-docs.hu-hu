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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010990"
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
- A kiosztott kapacitás mértéke a megvásárolt fix termékváltozatoktól függ (pl.: 4 TB-os kapacitás).
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
  Jelenleg csak a Prémium szolgáltatásszint érhető el.
- Minden kapacitáskészlet egyetlen NetApp-fiókhoz tartozik.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  

## <a name="volumes"></a>Kötetek

- A kötetek mérése a logikaikapacitás-fogyasztás alapján történik, és legfeljebb kötetenként 100 TB-ra skálázható.
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 
- Ugyanazon a NetApp-fiókon belül áthelyezheti a köteteket az egyik készletből egy másikba.    
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például áthelyezheti a köteteket az 1. kapacitáskészletből a 2. kapacitáskészletbe.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>További lépések

1. [NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)
2. [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
3. [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)
4. [Exportálási szabályzat konfigurálása kötethez (opcionális)](azure-netapp-files-configure-export-policy.md)