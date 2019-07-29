---
title: Bevezetés a Deep learning virtuális gépre – Azure | Microsoft Docs
description: Főbb elemzési forgatókönyvek és összetevők a Deep learning Virtual Machineshoz.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, adatelemzési eszközök, Linux-adatelemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: ddf1169d94c3e71b92e8b1835040ab5738bf94b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591893"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>A Deep learning virtuális gép bemutatása

## <a name="why-deep-learning-virtual-machine"></a>Miért érdemes a Deep learning virtuális gépet? 

Egyre többek között a mélyreható tanulási algoritmusok és a mélyreható neurális hálózatok a gépi tanulási problémák számos népszerű módszerének egyike lesz. Ezek különösen a gépi megismerési feladatok, például a képek, a szövegek, a hang-és videó-felismerések, amelyek gyakran megközelítik az emberi kognitív szinteket bizonyos tartományokban a fejlett mély neurális hálózati architektúrákkal, és nagy mennyiségű adathoz férnek hozzá a modellek betanításához. A Deep learning nagy mennyiségű számítási teljesítményt igényel a modellek ezen nagyméretű adatkészletekkel való betanításához. A grafikus feldolgozási egységek (GPU-k) felhővel és rendelkezésre állásával olyan kifinomult mély neurális architektúrák hozhatók létre, amelyek nagy mennyiségű, a felhőben található hatékony számítástechnikai infrastruktúrában vannak betanítva.  A [Data Science Virtual Machine](overview.md) eszközök és minták széles választékát adta meg az adatelőkészítéshez, a gépi tanuláshoz és a mély tanuláshoz. A felhasználók által felmerülő kihívások egyike azonban az, hogy felfedezzék az eszközöket és a mintákat olyan konkrét forgatókönyvekhez, mint például a Deep learning, és a GPU-alapú virtuálisgép-példányok könnyebben is üzembe helyezhetők. Ez a Deep learning virtuális gép (DLVM) ezeket a kihívásokat kezeli. 

## <a name="what-is-deep-learning-virtual-machine"></a>Mi a Deep learning virtuális gép? 
A Deep learning virtuális gép a [Data Science Virtual Machine](overview.md) (DSVM) egy speciálisan konfigurált változata, amely megkönnyíti a GPU-alapú virtuálisgép-példányok használatát a Deep learning-modellek betanításához. Ez a Windows 2016 és az Ubuntu Data Science Virtual Machine esetén támogatott.  Ugyanazokat az alapvető virtuálisgép-rendszerképeket (és így az összes gazdag eszközkészletet) megosztja a DSVM, de úgy van beállítva, hogy a mélyebb tanulást is megkönnyítse. A képek és a szövegek megismerése érdekében teljes körű mintákat is biztosítunk, amelyek számos valós idejű AI-forgatókönyvre alkalmazhatók. A Deep learning virtuális gép emellett a DSVM található eszközök és minták gazdag készletét is könnyebben felderíthetővé teszi a virtuális gépen lévő eszközök és minták katalógusával. Az eszközök kiszolgálása szempontjából a Deep learning virtuális gép számos népszerű Deep learning-keretrendszert kínál, valamint eszközöket biztosít a képek és a szöveges adatok előzetes feldolgozásához. Az eszközök átfogó listáját a [Data Science Virtual Machine áttekintés lapjára](overview.md#whats-included-in-the-data-science-vm)kattintva tekintheti meg. 

## <a name="next-steps"></a>További lépések

Ismerkedés a Deep learning virtuális géppel a következő lépésekkel:

* [Mély tanulási virtuális gép kiépítése](provision-deep-learning-dsvm.md)
* [A Deep learning virtuális gép használata](use-deep-learning-dsvm.md)
* [Eszköz leírása](dsvm-deep-learning-ai-frameworks.md)
* [Példák](dsvm-samples-and-walkthroughs.md)
