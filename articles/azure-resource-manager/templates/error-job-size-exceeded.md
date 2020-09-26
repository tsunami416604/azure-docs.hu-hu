---
title: A feladatok mérete túllépte a hibát.
description: Leírja, hogyan lehet elhárítani a hibákat, amikor a feladatok mérete vagy a sablon túl nagy.
ms.topic: troubleshooting
ms.date: 09/25/2020
ms.openlocfilehash: 06645561964d9634d93061b3be4d100a578cc7e7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373172"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Túllépte a feladatokhoz tartozó hibák elhárítását

Ez a cikk a **JobSizeExceededException** és a **DeploymentSizeExceededException** hibák elhárítását ismerteti.

## <a name="symptom"></a>Hibajelenség

A sablonok telepítésekor hibaüzenetet kap, amely túllépte az üzemelő példány korlátait.

## <a name="cause"></a>Ok

Ez a hiba akkor jelenik meg, ha a sablon mérete meghaladja a 4 MB-ot. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután kibővítettük azokat az erőforrás-definíciókat, amelyek számos példány létrehozásához a [másolást](copy-resources.md) használják. A végső állapot a változók és paraméterek megoldott értékeit is tartalmazza.

A telepítési feladathoz a kérelemmel kapcsolatos metaadatok is tartoznak. Nagyméretű sablonok esetén a sablonnal összevont metaadatok meghaladják a feladatok megengedett méretét.

A sablon egyéb korlátai a következők:

* 256 paraméterek
* 256 változók
* 800-erőforrások (beleértve a példányszámot)
* 64 kimeneti értékek
* 24 576 karakter egy sablon kifejezésében

## <a name="solution-1---simplify-template"></a>1. megoldás – sablon leegyszerűsítése

Az első lehetőség a sablon egyszerűsítése. Ez a beállítás akkor működik, ha a sablon számos különböző erőforrástípust telepít. Érdemes lehet a sablont [csatolt sablonokba](linked-templates.md)osztani. Az erőforrástípusok logikai csoportokba oszthatók, és minden csoporthoz hozzá kell adni egy csatolt sablont. Ha például sok hálózati erőforrást kell központilag telepítenie, akkor ezeket az erőforrásokat egy csatolt sablonba helyezheti át.

A társított sablontól függőként más erőforrásokat is beállíthat, és [értékeket kaphat a csatolt sablon kimenetéről](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---use-serial-copy"></a>2. megoldás – soros másolás használata

A második lehetőség a másolási hurok [párhuzamosról soros feldolgozásra](copy-resources.md#serial-or-parallel)való módosítása. Ezt a lehetőséget csak akkor használja, ha azt gyanítja, hogy a hiba nagy számú erőforrásnak a másolással történő központi telepítését eredményezi. Ez a változás jelentősen növelheti a központi telepítési időt, mert az erőforrások párhuzamosan nincsenek telepítve.
