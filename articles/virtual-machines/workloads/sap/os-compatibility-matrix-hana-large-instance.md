---
title: Az SAP HANA operációsrendszer-kompatibilitási mátrixa (nagy példányok)| Microsoft dokumentumok
description: A kompatibilitási mátrix az operációs rendszer különböző verzióinak kompatibilitását jelöli a különböző hardvertípusokkal (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675646"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibilis operációs rendszerek hana nagy példányok

## <a name="hana-large-instance-type-i"></a>HANA I. típusú HANA példány     
  | Operációs rendszer | Rendelkezésre állás        | Termékváltozatok                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Már nem ajánlják fel. | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Elérhető           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Elérhető           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>Állandó memória SKUs
  | Operációs rendszer | Rendelkezésre állás | Termékváltozatok                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Elérhető    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA II típusú hana példány     
  |  Operációs rendszer       | Rendelkezésre állás        | Termékváltozatok                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Már nem ajánlják fel. | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Elérhető           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Kapcsolódó dokumentumok

- További tudnivalók az [elérhető ska-król](hana-available-skus.md)
- Tudniaz [operációs rendszer frissítéséről](os-upgrade-hana-large-instance.md)
  

  
  
