---
title: A feladatok mérete túllépte a hibát.
description: Leírja, hogyan lehet elhárítani a hibákat, amikor a feladatok mérete vagy a sablon túl nagy.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610403"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Túllépte a feladatokhoz tartozó hibák elhárítását

Ez a cikk a **JobSizeExceededException** és a **DeploymentJobSizeExceededException** hibák elhárítását ismerteti.

## <a name="symptom"></a>Hibajelenség

A sablonok telepítésekor hibaüzenetet kap, amely túllépte az üzemelő példány korlátait.

## <a name="cause"></a>Ok

Ez a hibaüzenet akkor jelenik meg, ha az üzemelő példány túllépi az engedélyezett határértékek valamelyikét. Ez a hiba általában akkor jelenik meg, ha a sablon vagy a központi telepítést futtató művelet túl nagy.

Az üzembe helyezési feladatok nem haladhatják meg az 1 MB-ot. A feladatban szerepelnek metaadatok a kérelemről. Nagyméretű sablonok esetén a sablonnal összevont metaadatok meghaladják a feladatok megengedett méretét.


A sablon mérete nem haladhatja meg a 4 MB-ot. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután kibővítettük azokat az erőforrás-definíciókat, amelyek számos példány létrehozásához a [másolást](copy-resources.md) használják. A végső állapot a változók és paraméterek megoldott értékeit is tartalmazza.

A sablon egyéb korlátai a következők:

* 256 paraméterek
* 256 változók
* 800-erőforrások (beleértve a példányszámot)
* 64 kimeneti értékek
* 24 576 karakter egy sablon kifejezésében

## <a name="solution-1---simplify-template"></a>1. megoldás – sablon leegyszerűsítése

Az első lehetőség a sablon egyszerűsítése. Ez a beállítás akkor működik, ha a sablon számos különböző erőforrástípust telepít. Érdemes lehet a sablont [csatolt sablonokba](linked-templates.md)osztani. Az erőforrástípusok logikai csoportokba oszthatók, és minden csoporthoz hozzá kell adni egy csatolt sablont. Ha például sok hálózati erőforrást kell központilag telepítenie, akkor ezeket az erőforrásokat egy csatolt sablonba helyezheti át.

A társított sablontól függőként más erőforrásokat is beállíthat, és [értékeket kaphat a csatolt sablon kimenetéről](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>2. megoldás – a név méretének csökkentése

Próbálja megrövidíteni a [paraméterekhez](template-parameters.md), [változókhoz](template-variables.md)és [kimenetekhez](template-outputs.md)használt nevek hosszát. Ha ezeket az értékeket a másolási hurkok használatával ismétli meg, a nagyméretű nevek többször is megszorul.

## <a name="solution-3---use-serial-copy"></a>3. megoldás – soros másolás használata

Érdemes lehet [párhuzamosról soros feldolgozásra](copy-resources.md#serial-or-parallel)módosítani a másolási ciklust. Ezt a lehetőséget csak akkor használja, ha azt gyanítja, hogy a hiba nagy számú erőforrásnak a másolással történő központi telepítését eredményezi. Ez a változás jelentősen növelheti a központi telepítési időt, mert az erőforrások párhuzamosan nincsenek telepítve.
