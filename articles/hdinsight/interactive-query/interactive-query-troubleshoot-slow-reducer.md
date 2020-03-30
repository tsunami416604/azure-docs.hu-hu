---
title: A szűkítő lassú az Azure HDInsightban
description: A szűkítő lassú az Azure HDInsightban a lehetséges adatdöntéstől
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895166"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Forgatókönyv: A szűkítő lassú az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtök interaktív lekérdezési összetevőinek használatával kapcsolatos problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Lekérdezés futtatásakor, `insert into table1 partition(a,b) select a,b,c from table2` például a lekérdezési terv elindul egy csomó szűkítők, de az adatok minden partíció megy egyetlen szűkítő. Ez azt eredményezi, hogy a lekérdezés olyan lassú, mint a legnagyobb partíció szűkítője által eltöltött idő.

## <a name="cause"></a>Ok

Nyissa meg a [beeline-t,](../hadoop/apache-hadoop-use-hive-beeline.md) és ellenőrizze a készlet `hive.optimize.sort.dynamic.partition`értékét.

Ennek a változónak az értékét az adatok jellege alapján igaz/hamis értékre kell állítani.

Ha a bemeneti tábla partíciói kisebbek (mondjuk kevesebb, mint 10), és így a `true`kimeneti partíciók száma is, és a változó be van állítva, akkor az adatok globálisan rendezve és írásra kerülpartíciónként egyetlen szűkítő használatával. Még akkor is, ha a rendelkezésre álló szűkítők száma nagyobb, néhány szűkítő késés ben maradhat az adatok ferdesége miatt, és a maximális párhuzamosság nem érhető el. Ha a `false`rendszerre módosítja, egynél több szűkítő is kezelhet egy partíciót, és több kisebb fájl is kilesz írva, ami gyorsabb beszúrást eredményez. Ez azonban a kisebb fájlok jelenléte miatt további lekérdezésekre is hatással lehet.

`true` A partíciók száma nagyobb és az adatok elferdítése esetén van értelme. Ilyen esetekben a térképfázis eredménye úgy lesz kiírva, hogy minden partíciót egyetlen szűkítő kezeljen, ami jobb későbbi lekérdezési teljesítményt eredményez.

## <a name="resolution"></a>Megoldás:

1. Próbálja meg újrapartiálni az adatokat, hogy normalizálják több partícióra.

1. Ha #1 nem lehetséges, állítsa a konfiguráció értékét hamisra a beeline munkamenetben, és próbálkozzon újra a lekérdezéssel. `set hive.optimize.sort.dynamic.partition=false`. Az érték fürtszinten hamis értékre állítása nem ajánlott. Az érték `true` optimális, és állítsa be a paramétert szükség szerint az adatok és a lekérdezés jellege alapján.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse át az Azure-támogatási kérelem létrehozása című, [továbbcímű tájékoztatót.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
