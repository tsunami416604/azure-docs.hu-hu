---
title: CPU Core kvóta-növelési kérelem – Azure HDInsight
description: Ismerje meg az előfizetéshez lefoglalt CPU-magok növelésének folyamatát.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: a708ad9626f4d6750de64097a7e1e9177c350e5e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83717808"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Kvóta-növelés az Azure HDInsight

A CPU Core-kvóták segítségével biztosítható, hogy az erőforrás-használat méltányos legyen az adott Azure-régióban lévő összes ügyfél között. Bizonyos esetekben azonban előfordulhat, hogy az üzleti követelmények több fürterőforrás-t igényelnek, mint a jelenlegi kvótája. Ilyen esetekben kérheti a CPU Core-kvóta növelését, hogy olyan fürtöket telepítsen, amelyek megfelelnek az adatfeldolgozási követelményeknek.

Ha eléri a kvóta korlátját, nem telepíthet új fürtöket, illetve nem bővítheti a meglévő fürtöket további feldolgozói csomópontok hozzáadásával. Az egyetlen kvóta a CPU magok kvótája, amely az egyes előfizetések régiójának szintjén található. Az előfizetés például az USA keleti régiójában 30 CPU-korláttal rendelkezhet, és az USA 2. keleti régiójában további 30 CPU-magot is használhat.

## <a name="gather-required-information"></a>A szükséges információk összegyűjtése

Ha olyan hibaüzenetet kapott, amely azt jelzi, hogy elérte a kvóta korlátját, az ebben a szakaszban ismertetett eljárással fontos információkat gyűjthet, és kvóta-növelési kérést küldhet.

1. Határozza meg a fürt kívánt virtuális gép méretét, méretezését és típusát.
1. Győződjön meg arról, hogy az előfizetésének aktuális kvóta-kapacitási korlátai vannak. Az elérhető magok vizsgálatához hajtsa végre a következő lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
    1. Navigáljon a HDInsight-fürt **áttekintő** oldalára.
    1. A bal oldali menüben válassza a **kvóta korlátai**lehetőséget. Az oldal megjeleníti a használatban lévő magok számát, az elérhető magok számát és a magok teljes számát.

A kvóta növeléséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A lap bal alsó részén kattintson a **Súgó és támogatás** elemre.

    ![Súgó és támogatás gomb](./media/quota-increase-request/help-support-button.png)

1. Válassza az **Új támogatási kérelem** lehetőséget.
1. Az **Alapvető beállítások** lapon található **Új támogatási kérelem** oldalon válassza ki a következő beállításokat:

   - **Probléma típusa**: **szolgáltatás-és előfizetési korlátok (kvóták)**
   - **Előfizetés**: a módosítani kívánt előfizetés
   - **Kvóta típusa**: **HDInsight**

     ![Támogatási kérelem létrehozása az HDInsight Core-kvóta növeléséhez](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Válassza a **tovább lehetőséget: megoldások >>**.
1. A **részletek** lapon adja meg a probléma leírását, válassza ki a probléma súlyosságát, az előnyben részesített kapcsolattartási módszert és az egyéb kötelező mezőket. Az alább felsorolt sablonnal biztosíthatja a szükséges információk megadását. A kvóta-növelési kérelmeket az Azure Capacity csapat értékeli ki, nem pedig a HDInsight termék csapata. Minél több teljes információt ad meg, annál valószínűbb, hogy a kérést jóvá fogja hagyni.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![probléma részletei](./media/quota-increase-request/problem-details.png)

1. Válassza a **Tovább: Áttekintés + >>létrehozása **elemet.
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.

> [!NOTE]  
> Ha a HDInsight alapszintű kvótát egy privát régióban szeretné megemelni, [küldjön el egy engedélyezési kérelmet](https://aka.ms/canaryintwhitelist).

[Az ügyfélszolgálattól kérheti a kvóta növelését](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Vannak rögzített kvóták. Például egyetlen Azure-előfizetés legfeljebb 10 000 magot tartalmazhat. A korlátokkal kapcsolatos további információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök beállítása a Apache Hadoop-, Spark-, Kafka-és egyéb szolgáltatásokkal](hdinsight-hadoop-provision-linux-clusters.md): megtudhatja, hogyan állíthatja be és konfigurálhatja a fürtöket a HDInsight-ben.
* A [fürt teljesítményének figyelése](hdinsight-key-scenarios-to-monitor.md): megismerheti a HDInsight-fürt azon főbb forgatókönyveit, amelyek hatással lehetnek a fürt kapacitására.