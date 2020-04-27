---
title: Egyéni rendszerképek és képletek összehasonlítása a DevTest Labs szolgáltatásban | Microsoft Docs
description: Ismerje meg az egyéni lemezképek és a képletek közötti különbséget virtuálisgép-bázisként, így eldöntheti, hogy melyik a legmegfelelőbb a környezetéhez.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "64680298"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Egyéni rendszerképek és képletek összehasonlítása a DevTest Labs szolgáltatásban
Az [Egyéni rendszerképek](devtest-lab-create-template.md) és [képletek](devtest-lab-manage-formulas.md) is használhatók a [létrehozott új virtuális gépek](devtest-lab-add-vm.md)alapjaként. Az egyéni lemezképek és a képletek közötti különbség azonban az, hogy az egyéni rendszerkép egyszerűen egy virtuális merevlemezen alapuló rendszerkép, míg a képlet egy, az előre konfigurált beállításokon (például a virtuális GÉPEN, a virtuális hálózaton, az alhálózaton és az összetevőkön) *kívüli* virtuális merevlemezen alapuló rendszerkép. Ezek az előre konfigurált beállítások alapértelmezett értékekkel vannak beállítva, amelyek felülbírálják a virtuális gépek létrehozásakor. Ez a cikk az Egyéni rendszerképek és a képletek használatának előnyeit (előnyeit) és hátrányait (hátrányait) ismerteti.

## <a name="custom-image-pros-and-cons"></a>Egyéni rendszerkép-előnyök és hátrányok
Az egyéni lemezképek statikus, nem változtatható módon hozhatnak létre virtuális gépeket a kívánt környezetből. 

**Szakemberek**

* A virtuális gép üzembe helyezése egy egyéni rendszerképből gyorsan történik, mivel a virtuális gép a rendszerképből való áttelepítése után nem változik. Más szóval nem kell alkalmazni az egyéni rendszerképeket, csak a beállítások nélküli rendszerképek. 
* Az egyetlen egyéni rendszerképből létrehozott virtuális gépek azonosak.

**Hátránya**

* Ha frissítenie kell az egyéni rendszerkép bizonyos aspektusait, újra létre kell hoznia a rendszerképet.  

## <a name="formula-pros-and-cons"></a>A Formula előnyei és hátrányai
A képletek dinamikus módszert biztosítanak a virtuális gépek létrehozásához a kívánt konfiguráció/beállítások alapján.

**Szakemberek**

* A környezet változásai menet közben is rögzíthetők. Ha például azt szeretné, hogy egy virtuális gép a kiadási folyamat legújabb BITS szolgáltatásával legyen telepítve, vagy a legfrissebb kódot kívánja felvenni a tárházból, egyszerűen megadhat egy olyan összetevőt, amely központilag telepíti a legújabb biteket, vagy a képletben szereplő legújabb kódot egy célként megadott alapképpel együtt helyezi el. Ha ez a képlet virtuális gépek létrehozására szolgál, a rendszer telepíti a virtuális gépre a legújabb biteket/kódokat. 
* A képletek meghatározhatják az egyéni lemezképek által nem megadható alapértelmezett beállításokat – például a virtuálisgép-méreteket és a virtuális hálózati beállításokat. 
* A képletekben mentett beállítások alapértelmezett értékként jelennek meg, de a virtuális gép létrehozásakor módosíthatók. 

**Hátránya**

* A virtuális gépek képletből való létrehozása több időt vehet igénybe, mint a virtuális gép létrehozása egyéni rendszerképből.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)