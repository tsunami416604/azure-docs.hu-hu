---
title: Azure-beli virtuális gépek meglévő rendelkezésre állási csoportba való felvételének támogatása | Microsoft Docs
description: Ez a cikk egy támogatási mátrixot biztosít arról, hogy mely virtuálisgép-sorozatokat lehet összekeverni ugyanabban a rendelkezésre állási csoportba
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77122921"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Azure-beli virtuális gépek meglévő rendelkezésre állási csoportba való felvételének támogatása

Időnként előfordulhat, hogy a meglévő rendelkezésre állási csoportba új virtuális gépek (VM-EK) hozzáadásakor bizonyos korlátozásokkal találkozhat. A következő diagram részletezi, hogy mely virtuálisgép-sorozatokat lehet összekeverni ugyanabban a rendelkezésre állási csoportba.

Itt látható a támogatási mátrix, amely különböző típusú virtuális gépeket kever:

Adatsorozat & rendelkezésre állási csoport|Második virtuális gép|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Első virtuális gép|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Az összes többi sorozat nem lehet ugyanabban a rendelkezésre állási csoportban, mert egy adott hardverre van szükségük.

Az A8-as/A9-es virtuális gép mérete nem keverhető a dedikált RDMA háttér-hálózatra vonatkozó követelmény miatt.
