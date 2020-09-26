---
title: Apache Spark alapvető fogalmak
description: Az Azure szinapszis Analytics és a különböző fogalmak Apache Sparkának bemutatása.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 74e85906742207d6cde0b7c4cc5c021c23ee4c7b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260138"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark az Azure szinapszis Analytics alapvető fogalmakkal

A Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatanalitikai alkalmazások teljesítményének növelése érdekében. Az Azure szinapszis Analytics Apache Spark a Microsoft egyike a Felhőbeli Apache Spark megvalósításának. 

Az Azure szinapszis megkönnyíti a Spark-képességek létrehozását és konfigurálását az Azure-ban. Az Azure szinapszis ezen Spark-képességek eltérő implementációját biztosítja, amelyek dokumentálva vannak itt.

## <a name="spark-pools-preview"></a>Spark-készletek (előzetes verzió)

A rendszer létrehoz egy Spark-készletet (előzetes verzió) a Azure Portal. Ez egy olyan Spark-készlet definíciója, amely a példányok létrehozásakor egy olyan Spark-példány létrehozására szolgál, amely az adatfeldolgozást végzi. Spark-készlet létrehozásakor a rendszer csak metaadatként működik, és nem használja fel, nem futtatja és nem számlázza fel az erőforrásokat. A Spark-készlet olyan tulajdonságokkal rendelkezik, amelyek a Spark-példány jellemzőit vezérlik. Ezek a tulajdonságok közé tartoznak például a név, a méret, a skálázási viselkedés, az élettartam.

Mivel a Spark-készletek létrehozásához nem jár dollár vagy erőforrás-díj, tetszőleges számú különböző konfigurációval hozható létre. Az engedélyek olyan Spark-készletekre is alkalmazhatók, amelyek lehetővé teszik a felhasználók számára, hogy mások számára is hozzáférjenek.

Az ajánlott eljárás az, hogy olyan kisebb Spark-készleteket hozzon létre, amelyek fejlesztési és hibakeresési célokra használhatók, majd nagyobbak a termelési munkaterhelések futtatásához is.

Megtudhatja, hogyan hozhat létre egy Spark-készletet, és hogyan tekintheti meg az összes tulajdonságot itt [megkezdheti a Spark-készletek a szinapszis Analyticsben](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Spark-példányok

A Spark-példányok akkor jönnek létre, amikor egy Spark-készlethez csatlakozik, létrehoz egy munkamenetet, és futtat egy feladatot. Mivel több felhasználó is hozzáférhet egyetlen Spark-készlethez, az összes csatlakozó felhasználóhoz létrejön egy új Spark-példány. 

Ha egy második feladatot küld el, ha van kapacitás a készletben, akkor a meglévő Spark-példány kapacitása is rendelkezésre áll. Ezután a meglévő példány feldolgozza a feladatot. Ellenkező esetben, ha a kapacitás rendelkezésre áll a készlet szintjén, a rendszer új Spark-példányt hoz létre.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

- Létrehoz egy SP1 nevű Spark-készletet; a fürt rögzített mérete 20 csomópont.
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1 a feladatok feldolgozásához.
- Most elküld egy másik feladatot, a J2, amely 10 csomópontot használ, mivel a készletben és a példányban is van kapacitás, a J2 a SI1 dolgozza fel.
- Ha a J2 11 csomópontra kérték, a SP1 vagy a SI1 nem volt kapacitása. Ebben az esetben, ha a J2 egy jegyzetfüzetből származik, a rendszer elutasítja a feladatot. Ha a J2 egy batch-feladatokból származik, az várólistára kerül.

### <a name="example-2"></a>2\. példa

- Létrehoz egy Spark Pool-hívást SP2; engedélyezve van a 10 – 20 csomópontos autoskálázás
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1-t, amely a feladatok feldolgozásához jön létre.
- Most elküld egy másik, 10 csomópontot használó feladatot, a J2, mert a készletben továbbra is kapacitás áll rendelkezésre, így a példányok automatikusan 20 csomópontra növekednek, és J2 a folyamatokat.

### <a name="example-3"></a>3\. példa

- Létrehoz egy SP1 nevű Spark-készletet; a fürt rögzített mérete 20 csomópont.
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1 a feladatok feldolgozásához.
- Egy másik felhasználó, a U2, a J3, amely 10 csomópontot használ, egy új Spark-példányt hoz létre a SI2, amely feldolgozza a feladatot.
- Most elküld egy másik feladatot, a J2, amely 10 csomópontot használ, mivel a készletben továbbra is rendelkezésre áll a kapacitása, a J2 pedig a SI1 dolgozza fel.

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.4/)
