---
title: Egyéni képek és képletek összehasonlítása a DevTest Labs-ben | Microsoft dokumentumok
description: Ismerje meg az egyéni képek és képletek virtuálisgép-alapként való közötti különbségeket, így eldöntheti, hogy melyik felel meg leginkább a környezetének.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680298"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Egyéni képek és képletek összehasonlítása a DevTest Labs-ben
Mind [az egyéni lemezképek,](devtest-lab-create-template.md) mind [a képletek](devtest-lab-manage-formulas.md) használhatók a [létrehozott új virtuális gépek](devtest-lab-add-vm.md)alapjaiként. Az egyéni lemezképek és a képletek közötti legfontosabb különbségtétel azonban az, hogy az egyéni lemezkép egyszerűen egy virtuális merevlemezen alapuló lemezkép, míg a képlet az előre konfigurált beállítások – például a virtuális gép mérete, a virtuális hálózat, az alhálózat és az összetevők *– mellett* egy virtuális merevlemezen alapuló lemezkép. Ezek az előre konfigurált beállítások olyan alapértelmezett értékekkel vannak beállítva, amelyek felülbírálhatók a virtuális gép létrehozásakor. Ez a cikk ismerteti az egyéni képek és képletek használatának előnyeit (előnyeit) és hátrányait (hátrányait).

## <a name="custom-image-pros-and-cons"></a>Egyéni kép érvek és ellenérvek
Az egyéni lemezképek statikus, változatlan módot biztosítanak a virtuális gépek kívánt környezetből történő létrehozására. 

**Profik**

* Virtuális gép kiépítése egy egyéni rendszerkép gyors, mivel semmi sem változik, miután a virtuális gép megpördült fel a rendszerképből. Más szóval, nincsenek olyan beállítások, amelyeket alkalmazni kell, mivel az egyéni kép csak egy beállítás nélküli kép. 
* Az egyetlen egyéni lemezképből létrehozott virtuális gépek azonosak.

**Hátránya**

* Ha frissítenie kell az egyéni lemezkép bizonyos aspektusait, a lemezképet újra létre kell hoznia.  

## <a name="formula-pros-and-cons"></a>Formula előnyei és hátrányai
A képletek dinamikus módot biztosítanak a virtuális gépek létrehozásához a kívánt konfigurációból/beállításokból.

**Profik**

* A környezet változásait menet közben rögzítheti a műtermékek. Ha például azt szeretné, hogy egy virtuális gép telepítve legyen a kiadási folyamat legújabb bitjeivel, vagy a legújabb kódot vegye igénybe a tárházból, egyszerűen megadhat egy műtárgyat, amely telepíti a legújabb biteket, vagy a legújabb kódot a képletben egy célbázissal együtt Kép. Amikor ezt a képletet virtuális gépek létrehozásához használja, a legújabb bitek/kód telepítése/a virtuális géphez való besorozása történik. 
* A képletek olyan alapértelmezett beállításokat határozhatnak meg, amelyeket az egyéni lemezképek nem tudnak megadni – például a virtuális gép méreteit és a virtuális hálózati beállításokat. 
* A képletben mentett beállítások alapértelmezett értékekként jelennek meg, de a virtuális gép létrehozásakor módosíthatók. 

**Hátránya**

* Virtuális gép létrehozása egy képlettöbb időt vesz igénybe, mint egy virtuális gép létrehozása egy egyéni lemezképből.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)