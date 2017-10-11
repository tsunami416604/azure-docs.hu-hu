---
title: "Az egyéni lemezképek és a DevTest Labs szolgáltatásban képletek összehasonlítása |} Microsoft Docs"
description: "További információk a egyéni lemezképek és a formulák közötti különbségeket, a virtuális gép alapjait, eldöntheti, melyik legjobban megfelel a környezetben."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: tarcher
ms.openlocfilehash: ff771abc26c08f0adb977c29739d2f5c91924b21
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Az egyéni lemezképek és a DevTest Labs szolgáltatásban képletek összehasonlítása
Mindkét [egyéni lemezképek](devtest-lab-create-template.md) és [képletek](devtest-lab-manage-formulas.md) alapjait használható [létrehozott új virtuális gépek](devtest-lab-add-vm-with-artifacts.md). Azonban a fő különbség a között egyéni lemezképek és a formulák az, hogy egy egyéni lemezképet egyszerűen csak egy virtuális merevlemez alapján, amíg egy képlete virtuális merevlemez alapuló rendszerképet kép *kívül* előre konfigurált beállítások – például a Virtuálisgép-méretet, a virtuális hálózati, a alhálózati és az összetevők. Ezek előre konfigurált beállítások vannak beállítva, hogy a virtuális gép létrehozásakor felülbírálhatja az alapértelmezett értékekkel. Ez a cikk ismerteti, néhány (szakemberek számára) előnyeit és hátrányait (hátrányait) használatával egyéni lemezképek és képletek alapján.

## <a name="custom-image-pros-and-cons"></a>Kép: egyéni előnyei és hátrányai
Egyéni lemezképek lehetőséget nyújtanak olyan statikus és nem módosítható a kívánt környezetből virtuális gépek létrehozásához. 

**Informatikai szakemberek**

* Virtuális gép kiépítése egyéni lemezképéről esetén gyors, mert semmi nem változik, miután a virtuális gép van hoz létre a lemezképből. Más szóval nincsenek beállítások alkalmazásához, mert az egyéni lemezképet csak a kép vonatkozó beállítások kihagyásával. 
* Egyetlen egyéni lemezkép alapján létrehozott virtuális gépek esetén azonosak.

**Hátrányait**

* Ha módosítania néhány szempontja, hogy az egyéni lemezképet, a lemezkép kell újból létre kell hozni.  

## <a name="formula-pros-and-cons"></a>Képletadat előnyei és hátrányai
Képletek biztosít a virtuális gépek létrehozásához a kívánt konfigurációs beállítások dinamikus módot.

**Informatikai szakemberek**

* A környezet változásai keresztül összetevők parancsprogramok rögzíthetők. Például ha szeretné, hogy egy virtuális Gépet a legújabb bitként a kiadási láncból telepítve, vagy a legújabb kód besorolni a tárházban lévő, egyszerűen megadhat egy összetevő, amely telepíti a legújabb bits vagy felveszi a legújabb kódot a képlet együtt egy cél alapjául szolgáló lemezképhez. A képlet használatával létrehozott virtuális gépet, amikor a legújabb bits kód, telepített/bejegyezve a virtuális Gépet. 
* Képletek alapértelmezett beállításokat, az egyéni lemezképek nem adhatók meg – például a Virtuálisgép-méretek és a virtuális hálózati beállításokat adhat meg. 
* A képlet mentett beállításokat alapértelmezett értékei láthatók, de a virtuális gép létrehozásakor módosítható. 

**Hátrányait**

* Virtuális gép létrehozása egy képletet a virtuális gép létrehozása egy egyéni lemezképből több időt is igénybe vehet.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Következő lépések
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)