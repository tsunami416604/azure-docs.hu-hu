---
title: Egyéni rendszerképek és képletek a DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Egyéni rendszerképek és képletek közötti különbségekről további, a virtuális gép alapjait, így eldöntheti, melyik leginkább megfelelő a környezet.
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
ms.openlocfilehash: 37288fd4a9c7558d05728b8ce03df505117e0232
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611270"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Egyéni rendszerképek és a DevTest Labs szolgáltatásban létrehozott képletek összehasonlítása
Mindkét [egyéni rendszerképek](devtest-lab-create-template.md) és [képletek](devtest-lab-manage-formulas.md) alapjait, használható [létrehozott új virtuális gépek](devtest-lab-add-vm.md). Azonban a kulcs megkülönböztetése egyéni rendszerképek és képletek, hogy egy egyéni rendszerkép egyszerűen alapuló rendszerképet a virtuális merevlemez, egy rendszerképet a virtuális merevlemez alapján pedig egy képlet *mellett* előre konfigurált beállítások – például a virtuális gép méretét, a virtuális hálózathoz, alhálózat és összetevőket. Ezek a beállítások előre konfigurált alapértelmezett értékekkel, amely a virtuális gép létrehozásakor felülbírálható beállítása. Ez a cikk ismerteti azokat a (szakemberek számára) előnyeit és hátrányait (hátrányai) használatával egyéni képek és képletek használatával.

## <a name="custom-image-pros-and-cons"></a>Egyéni rendszerkép és hátrányai
Egyéni rendszerképek adjon meg statikus, nem módosítható úgy hozhat létre virtuális gépeket a kívánt környezetre. 

**Szakemberek számára**

* Virtuális gépek üzembe helyezésekor egyéni rendszerképpel az gyors, mivel e tekintetben semmilyen változást után a virtuális gép a lemezkép alapján működésbe. Más szóval nem beállításokat a rendszer a alkalmazni, mivel az egyéni rendszerkép csak a vonatkozó beállítások kihagyásával kép. 
* Egy egyéni lemezkép alapján létrehozott virtuális gépek megegyeznek.

**Hátrányai**

* Ha bizonyos elemeit az egyéni rendszerképet frissíteni kell, a lemezkép létre kell hozni.  

## <a name="formula-pros-and-cons"></a>Képlet és hátrányai
Képleteket hozhat létre virtuális gépeket a kívánt konfigurációs beállítások dinamikus lehetőséget biztosíthat.

**Szakemberek számára**

* A környezet változásai összetevők menet rögzíthetők. Például ha szeretné, a legújabb elemeket a kibocsátási folyamat a telepített virtuális gép vagy bejegyzést elhelyezni a legújabb kódot az adattárból, egyszerűen megadhatja egy összetevő, amely telepíti a legújabb elemeket, vagy a legújabb kódot és a egy cél alapja a képletben részlege kép. Amikor ez a képlet segítségével virtuális gépeket hozhat létre, a legújabb bit/kód, a virtuális gépre telepítve és bejegyezve. 
* Képletek adhatja meg az alapértelmezett beállításokat, amelyek egyéni rendszerképek nem biztosítanak – például a Virtuálisgép-méretek és a virtuális hálózati beállításait. 
* A képlet mentett beállításokat alapértelmezett értékek jelennek meg, de módosítható, ha a virtuális gép jön létre. 

**Hátrányai**

* A képlet egy virtuális gép létrehozása egyéni rendszerképből egy virtuális gép létrehozása több időt is igénybe vehet.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)