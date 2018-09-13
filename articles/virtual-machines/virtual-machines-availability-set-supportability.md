---
title: Támogatás az Azure virtuális gépeket ad hozzá egy meglévő rendelkezésre állási beállítása |} A Microsoft Docs
description: Támogatás az Azure virtuális gépeket ad hozzá egy meglévő rendelkezésre állási csoportot.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35925999"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Támogatás az Azure virtuális gépeket ad hozzá egy meglévő rendelkezésre állási csoport

Alkalmanként korlátozások során felmerülő új virtuális gépek (VM) hozzáadása egy meglévő rendelkezésre állási csoporthoz. Az alábbi táblázat részletesen melyik kombinálhatja az azonos rendelkezésre állási csoportban lévő Virtuálisgép-sorozatok.

Itt látható a virtuális gépek különböző típusú értékek nagyságrendjeit támogatási mátrix:

Sorozatok és a rendelkezésre állási csoport|Második virtuális gép|A|Av2|D|A Dv2|Dv3|
|---|---|---|---|---|---|---|
|Első virtuális gép|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|A Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Az összes többi sorozat nem sikerült beállítani, mert azok megkövetelik, hogy egy adott hardverekhez, egyazon rendelkezésre állási.

A8/a9-es virtuális gép mérete miatt a dedikált RDMA-háttérhálózatot requirment nem keveredhet.
