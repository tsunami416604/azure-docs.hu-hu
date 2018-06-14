---
title: Azure virtuális gépeket ad hozzá egy meglévő rendelkezésre állási támogatásának beállítása |} Microsoft Docs
description: Támogatás az Azure virtuális gépek hozzáadása egy meglévő rendelkezésre állási csoportot.
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
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: 8bf2a55563772e26239445732b2b08df677436ef
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
ms.locfileid: "23987720"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Támogatás az Azure virtuális gépek hozzáadása egy meglévő rendelkezésre állási csoportot

Alkalmanként korlátozások során felmerülő új virtuális gépek (VM) hozzáadása egy meglévő rendelkezésre állási csoportba. Az alábbi táblázat részletezi mely kombinálhatja a rendelkezésre állási ugyanazokat a Virtuálisgép-sorozat.

Virtuális gépek különböző típusú kombinálhatók támogatási mátrix itt található:

Adatsorozat & rendelkezésre állási csoport|Második virtuális gép|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Első virtuális gép|||||||
|A||OKÉ|OKÉ|OKÉ|OKÉ|OKÉ|
|Av2||OKÉ|OKÉ|OKÉ|OKÉ|OKÉ|
|D||OKÉ|OKÉ|OKÉ|OKÉ|OKÉ|
|Dv2||OKÉ|OKÉ|OKÉ|OKÉ|OKÉ|
|Dv3||OKÉ|OKÉ|OKÉ|OKÉ|OKÉ|

Minden adatsorozatban azonos rendelkezésre állási csoportban, mert azok megkövetelik, hogy egy adott hardverekhez nem sikerült.

VM a8/A9 csomag mérete miatt requirment RDMA dedikált háttér-hálózat nem keverhetők.
