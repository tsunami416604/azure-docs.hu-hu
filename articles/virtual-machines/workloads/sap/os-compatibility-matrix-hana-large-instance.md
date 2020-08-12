---
title: SAP HANA operációs rendszer kompatibilitási mátrixa (nagyméretű példányok) | Microsoft Docs
description: A kompatibilitási mátrix a különböző típusú operációs rendszerek (nagyméretű példányok) különböző verzióinak kompatibilitását jelenti.
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce82193b1af07b993b02319397a00b802589579f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88116410"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibilis operációs rendszerek a HANA nagyméretű példányaihoz

## <a name="hana-large-instance-type-i"></a>HANA nagyméretű példány típusa I     
  | Operációs rendszer | Rendelkezésre állás        | Termékváltozatok                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Többé nem ajánlott | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Elérhető           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Elérhető           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7,6         | Elérhető           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Állandó memória SKU-i
  | Operációs rendszer | Rendelkezésre állás | Termékváltozatok                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Elérhető    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA nagyméretű példány típusa II     
  |  Operációs rendszer       | Rendelkezésre állás        | Termékváltozatok                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Többé nem ajánlott | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Elérhető           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | Elérhető           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP5             | Elérhető           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Kapcsolódó dokumentumok

- További információ az [elérhető SKU](hana-available-skus.md) -ról
- [Az operációs rendszer frissítésének](os-upgrade-hana-large-instance.md) megismerése
  

  
  
