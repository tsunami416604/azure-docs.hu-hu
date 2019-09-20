---
title: Azure-beli virtuális gépek meglévő rendelkezésre állási csoportba való felvételének támogatása | Microsoft Docs
description: Azure-beli virtuális gépek meglévő rendelkezésre állási csoportba való felvételének támogatása.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155456"
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
