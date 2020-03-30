---
title: Az Azure-beli virtuális gépek meglévő rendelkezésre állási készlethez való hozzáadásának támogatása | Microsoft dokumentumok
description: Ez a cikk egy támogatási mátrixot tartalmaz arról, hogy melyik virtuálisgép-sorozatot keverheti ugyanabban a rendelkezésre állási készletben
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122921"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Az Azure-beli virtuális gépek meglévő rendelkezésre állási készlethez való hozzáadásának támogatása

Előfordulhat, hogy korlátozásokkal jár, amikor új virtuális gépeket (VM-eket) ad hozzá egy meglévő rendelkezésre állási csoporthoz. A következő diagram részletezi, hogy melyik virtuálisgép-sorozatot keverheti ugyanabban a rendelkezésre állási csoportban.

Itt van a támogathatósági mátrix a különböző típusú virtuális gépek keveréséhez:

Sorozat & rendelkezésre állási készlet|Második virtuális gép|A|Av2 között|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Első virtuális gép|||||||
|A||OK|OK|OK|OK|OK|
|Av2 között||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Az összes többi sorozat nem lehet ugyanabban a rendelkezésre állási csoportban, mert egy adott hardvert igényelnek.

Az A8/A9 virtuális gép mérete nem keverhető a dedikált RDMA háttérhálózat követelménye miatt.
