---
title: Apache Spark az Azure Synapse Analytics szolgáltatásban – Alapfogalmak
description: Ez a cikk bemutatja az Apache Spark ot az Azure Synapse Analytics szolgáltatásban, és a különböző fogalmakat.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423669"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark az Azure Synapse Analytics alapkoncepcióiban

Az Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-analitikus alkalmazások teljesítményének növelése érdekében. Az Apache Spark az Azure Synapse Analytics szolgáltatásban az Apache Spark felhőbeli microsoftos implementációinak egyike. 

Az Azure Synapse megkönnyíti a Spark-képességek létrehozását és konfigurálását az Azure-ban. Az Azure Synapse a Spark-képességek eltérő megvalósítását biztosítja, amelyek itt vannak dokumentálva.

## <a name="spark-pools-preview"></a>Spark-készletek (előzetes verzió)

Spark-készlet (előzetes verzió) jön létre az Azure Portalon. Ez a Spark-készlet definíciója, amely példányosításkor egy Spark-példány, amely feldolgozza az adatokat. Spark-készlet létrehozásakor csak metaadatként létezik; nem használnak fel, nem számítanak fel erőforrásokat, nem számítanak fel, illetve nem számítanak fel díjat. A Spark-készlet egy spark-példány jellemzőit vezérlő tulajdonságok sorozatával rendelkezik; ezek a jellemzők közé tartozik, de nem kizárólagosan a név, méret, méretezési viselkedés, az élethez való idő.

Mivel a Spark-készletek létrehozásához nincs dollár vagy erőforrásköltség, tetszőleges számú különböző konfigurációval tetszőleges számú szám hozható létre. Az engedélyek a Spark-készletekre is alkalmazhatók, így a felhasználók csak néhányhoz férhetnek hozzá, másokhoz nem.

Ajánlott eljárás: kisebb Spark-készletek létrehozása, amelyek fejlesztési és hibakeresés, majd nagyobbak éles számítási feladatok futtatásához.

Elolvashatja, hogyan hozhat létre spark-készletet, és tekintse meg az összes tulajdonságát itt [A Spark-készletek használata a Synapse Analytics szolgáltatásban című témakörben.](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Spark-példányok

Spark-példányok jönnek létre, amikor csatlakozik egy Spark-készlet, hozzon létre egy munkamenetet, és fuss egy feladat. Mivel több felhasználó férhet hozzá egy Spark-készlethez, egy új Spark-példány jön létre minden egyes felhasználó, amely csatlakozik. 

Amikor küld egy második feladatot, majd ha van kapacitás a készletben, a meglévő Spark-példány is rendelkezik kapacitással, majd a meglévő példány feldolgozza a feladatot; ha nem, és van kapacitás a készlet szintjén, majd egy új Spark-példány jön létre.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1. példa

- Létrehoz egy SP1 nevű Spark-készletet; 20 csomópont rögzített fürtmérete van.
- Küld egy jegyzetfüzet-feladat, J1, amely 10 csomópontot használ, egy Spark-példány, SI1 jön létre a feladat feldolgozásához.
- Most küld egy másik feladat, J2, amely 10 csomópontot használ, mert még mindig van kapacitás a készletben, és a például, a J2, si1 feldolgozása.
- Ha j2 kért 11 csomópontot, nem lett volna kapacitás sp1 vagy SI1. Ebben az esetben, ha a J2 egy jegyzetfüzetből származik, akkor a feladat elutasításra kerül; ha j2 származik egy kötegelt feldolgozás, akkor lesz sorban.

### <a name="example-2"></a>2. példa

- Hozzon létre egy Spark-készlet hívás SP2; ez birtokol egy autoscale engedélyezve 10 -20 csomópontok
- Küld egy jegyzetfüzet-feladat, J1, amely 10 csomópontot használ, egy Spark-példány, SI1, jön létre a feladat feldolgozásához.
- Most küldegy másik feladat, J2, amely 10 csomópontot használ, mert még mindig van kapacitás a készletben a példány automatikus nő 20 csomópontok és a J2 folyamatok.

### <a name="example-3"></a>3. példa

- Létrehoz egy SP1 nevű Spark-készletet; 20 csomópont rögzített fürtmérete van.
- Küld egy jegyzetfüzet-feladat, J1, amely 10 csomópontot használ, egy Spark-példány, SI1 jön létre a feladat feldolgozásához.
- Egy másik felhasználó, u2, beküld egy feladat, J3, amely 10 csomópontot használ, egy új Spark-példány, SI2 jön létre a feladat feldolgozásához.
- Most küld egy másik feladat, J2, amely 10 csomópontot használ, mert még mindig van kapacitás a készletben, és a példát, J2, si1 dolgozza fel.

## <a name="next-steps"></a>További lépések

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.4/)
