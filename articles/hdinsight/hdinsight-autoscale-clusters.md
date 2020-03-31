---
title: Az Azure HDInsight-fürtök automatikus méretezése
description: Az Azure HDInsight automatikus skálázási funkciójával automatikusan méretezhet fürtöket
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399714"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök automatikus méretezése

> [!Important]
> 2019. november 7-én jelent meg a Spark és a Hadoop-fürtök általános elérhetőségére az Azure HDInsight automatikus skálázási funkció, és olyan fejlesztéseket tartalmazott, amelyek nem érhetők el a szolgáltatás előzetes verziójában. Ha 2019. november 7-e előtt létrehozott egy Spark-fürtöt, és szeretné használni az Automatikus skálázás funkciót a fürtön, az ajánlott elérési út egy új fürt létrehozása, és az automatikus skálázás engedélyezése az új fürtön.
>
> Az interaktív lekérdezés (LLAP) és a HBase-fürtök automatikus skálázása még előzetes verzióban érhető el. Az automatikus skálázás csak Spark, Hadoop, Interactive Query és HBase-fürtökön érhető el.

Az Azure HDInsight fürtautomatikus skálázási szolgáltatása automatikusan méretezi a fürt ben lévő feldolgozócsomópontok számát fel-le. A fürt más típusú csomópontjai jelenleg nem méretezhetők.  Egy új HDInsight-fürt létrehozása során beállítható a munkavégző csomópontok minimális és maximális száma. Automatikus skálázás majd figyeli az elemzési terhelés erőforrás-követelményeit, és felskálázja a munkavégző csomópontok fel-vagy le. A funkcióért nem számítunk fel további díjat.

## <a name="cluster-compatibility"></a>Fürtkompatibilitás

Az alábbi táblázat az automatikus skálázási szolgáltatással kompatibilis fürttípusokat és -verziókat ismerteti.

| Verzió | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 ESP nélkül | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 4.0 ESP nélkül | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 3.6 ESP-vel | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 4.0 ESP-vel | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |

\*A HBase-fürtök csak ütemezésalapú skálázáshoz konfigurálhatók, a terhelésalapúakhoz nem.

## <a name="how-it-works"></a>Működés

Választhat terhelésalapú méretezés vagy ütemezés-alapú méretezés a HDInsight-fürthöz. A terhelésalapú skálázás módosítja a fürt csomópontjainak számát a beállított tartományon belül, hogy biztosítsa az optimális CPU-kihasználtságot és minimalizálja a működési költségeket.

Az ütemezésalapú skálázás az adott időpontokban érvénybe lépő feltételek alapján módosítja a csomópontok számát a fürtben. Ezek a feltételek a fürtöt a kívánt számú csomópontra méretezi.

### <a name="metrics-monitoring"></a>Metrikák figyelése

Az automatikus skálázás folyamatosan figyeli a fürtöt, és a következő mutatókat gyűjti:

|Metrika|Leírás|
|---|---|
|Összes függőben lévő PROCESSZOR|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges magok teljes száma.|
|Teljes függőben lévő memória|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges teljes memória (MB-ban).|
|Teljes ingyenes CPU|Az aktív munkavégző csomópontokon lévő összes fel nem használt mag összege.|
|Teljes szabad memória|A nem használt memória összege (MB-ban) az aktív munkavégző csomópontokon.|
|Használt memória csomópontonként|A munkavégző csomópont terhelése. A munkavégző csomópont, amelyen 10 GB memóriát használ, nagyobb terhelés nek minősül, mint egy 2 GB-os használt memóriával rendelkező dolgozó.|
|Alkalmazásminták száma csomópontonként|A munkavégző csomóponton futó Alkalmazáskezelő főkiszolgáló (AM) tárolók száma. A két AM-tárolót tároló munkavégző csomópont fontosabbnak számít, mint a nulla AM-tárolókat tároló feldolgozócsomópont.|

A fenti mutatók 60 másodpercenként kerülnek ellenőrzésre. Az automatikus skálázás ezek a metrikák alapján skálázási és leskálázási döntéseket hoz.

### <a name="load-based-scale-conditions"></a>Terhelésalapú méretezési feltételek

A következő feltételek észlelésekor az automatikus skálázás méretezési kérelmet ad ki:

|Vertikális felskálázás|Kicsinyített|
|---|---|
|Teljes függőben lévő CPU nagyobb, mint a teljes szabad CPU több mint 3 percig.|Teljes függőben lévő CPU kevesebb, mint a teljes szabad CPU több mint 10 percig.|
|A függőben lévő teljes memória több mint 3 percig nagyobb, mint a teljes szabad memória.|A függőben lévő teljes memória több mint 10 percig kevesebb, mint a teljes szabad memória.|

A nagylemezzés hez a HDInsight szolgáltatás kiszámítja, hogy hány új munkavégző csomópontra van szükség az aktuális processzor- és memóriakövetelmények teljesítéséhez, majd egy skálázási kérelmet ad ki a szükséges számú csomópont hozzáadásához.

A csomópontonkénti AM-tárolók száma, valamint az aktuális processzor- és memóriakövetelmények alapján az automatikus skálázás bizonyos számú csomópont eltávolítására vonatkozó kérést ad ki. A szolgáltatás azt is észleli, hogy mely csomópontok az aktuális feladat-végrehajtás alapján eltávolításra jelöltek. A leskálázási művelet először leszereli a csomópontokat, majd eltávolítja őket a fürtből.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Fürt létrehozása terhelésalapú automatikus skálázással

Az Automatikus skálázás szolgáltatás terhelésalapú skálázással való engedélyezéséhez hajtsa végre a következő lépéseket a normál fürtlétrehozási folyamat részeként:

1. A **Konfiguráció + árképzés** lapon jelölje be az **Automatikus skálázás engedélyezése** jelölőnégyzetet.
1. Válassza **a Terhelésalapú** lehetőséget az **Automatikus skálázás típusa mezőben.**
1. Adja meg a kívánt értékeket a következő tulajdonságokhoz:  

    * A **dolgozó** **csomópont csomópontjainak** kezdeti száma .
    * A dolgozói csomópontok **száma.**
    * **A** munkavégző csomópontok maximális száma.

    ![Munkavégző csomópont terhelésalapú automatikus skálázásának engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

A munkavégző csomópontok kezdeti számának a minimális és a maximális közé kell esnie, beleértve a számlálót is. Ez az érték határozza meg a fürt kezdeti méretét a létrehozásakor. A munkavégző csomópontok minimális számát három vagy többre kell állítani. Ha a fürtöt háromnál kevesebb csomópontra méretezi, az elégtelen fájlreplikáció miatt csökkentett módban elakadhat.  További információ: [Elakadás csökkentett módban.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Fürt létrehozása ütemezésalapú automatikus skálázással

Az automatikus skálázás szolgáltatás ütemezésalapú skálázással való engedélyezéséhez hajtsa végre a következő lépéseket a normál fürtlétrehozási folyamat részeként:

1. A **Konfiguráció + árképzés** lapon jelölje be az **Automatikus skálázás engedélyezése** jelölőnégyzetet.
1. Adja meg a **feldolgozócsomópontok** **csomópontjainak számát,** amely szabályozza a fürt felskálázásának korlátját.
1. Válassza az **Ütemezés alapú** beállítást az Automatikus **skálázás típusa csoportban.**
1. Az **Automatikus skálázási konfigurációs** ablak megnyitásához válassza a **Konfigurálás** lehetőséget.
1. Válassza ki az időzónát, majd kattintson **a + Feltétel hozzáadása gombra**
1. Válassza ki a hét azon napjait, amelyekre az új feltétel vonatkoznia kell.
1. A feltétel érvénybe lépésének időpontja és a fürt által méretezhető csomópontok száma.
1. Szükség esetén adjon hozzá további feltételeket.

    ![Munkavégző csomópont ütemezésalapú létrehozásának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

A csomópontok számának 3 és a feltételek hozzáadása előtt megadott munkavégző csomópontok maximális száma között kell lennie.

### <a name="final-creation-steps"></a>Végső létrehozási lépések

Mind a terhelés-alapú, mind az ütemezés-alapú skálázás hoz válassza ki a munkavégző csomópontok virtuálisgép-típusát a **csomópontméret**alatti legördülő listából. Miután kiválasztotta a virtuális gép típusát az egyes csomóponttípusokhoz, láthatja a teljes fürt becsült költségtartományát. Állítsa be a virtuálisgép-típusokat a költségkerethez igazítva.

![Munkavégző csomópont ütemezésalapú automatikus skálázási csomópontméretének engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Az előfizetés kapacitáskvótával rendelkezik az egyes régiókhoz. A fejcsomópontok magjainak teljes száma a munkavégző csomópontok maximális számával kombinálva nem haladhatja meg a kapacitáskvótát. Ez a kvóta azonban puha korlát; mindig létrehozhat egy támogatási jegyet, hogy könnyen megnőjön.

> [!Note]  
> Ha túllépi a teljes alapkvóta-korlátot, hibaüzenetet kap, amely szerint "a maximális csomópont meghaladta a régióban rendelkezésre álló magokat, válasszon másik régiót, vagy lépjen kapcsolatba a támogatással a kvóta növeléséhez.".

A HDInsight-fürt azure-portálon történő létrehozásáról további információt a [Linux-alapú fürtök létrehozása a HDInsightban az Azure Portalon keresztül című témakörben](hdinsight-hadoop-create-linux-clusters-portal.md)talál.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása Erőforrás-kezelő sablonnal

#### <a name="load-based-autoscaling"></a>Terhelésalapú automatikus méretezés

Hozhat létre egy HDInsight-fürt terhelés-alapú automatikus skálázás egy `autoscale` Azure Resource `computeProfile`  >  `workernode` Manager-sablon, `minInstanceCount` `maxInstanceCount` egy csomópont hozzáadása a szakasza a tulajdonságokat, és az alábbi json kódrészlet látható.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

A fürtök Resource Manager-sablonokkal történő létrehozásáról az [Apache Hadoop-fürtök létrehozása a HDInsightban erőforrás-kezelősablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md)című témakörben olvashat bővebben.  

#### <a name="schedule-based-autoscaling"></a>Ütemezésalapú automatikus skálázás

Hozhat létre egy HDInsight-fürt ütemezésalapú automatikus skálázás egy Azure `autoscale` Resource Manager `computeProfile`  >  `workernode` sablon, egy csomópont hozzáadása a szakaszhoz. A `autoscale` csomópont tartalmaz `recurrence` egy, `timezone` `schedule` és amely leírja, hogy mikor kerül sor a módosításra.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Futó fürt automatikus skálázásának engedélyezése és letiltása

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha engedélyezni szeretné az automatikus skálázást egy futó fürtön, válassza a **Fürtméretet** a **Beállítások csoportban.** Ezután válassza **az Automatikus skálázás engedélyezése**lehetőséget. Válassza ki a kívánt automatikus skálázás típusát, és adja meg a terhelésalapú vagy ütemezésalapú méretezés beállításait. Végül válassza a **Mentés**gombot.

![A feldolgozói csomópont ütemezésalapú automatikus skálázásának engedélyezése fürtön](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>A REST API használata

Ha engedélyezni vagy letiltani szeretné az automatikus skálázást egy futó fürtön a REST API használatával, küldjön postakérést az automatikus skálázási végpontra az alábbi kódrészletben látható módon:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Használja a megfelelő paramétereket a kérelem hasznos adatában. Az alábbi json-hasznos adat használható az automatikus skálázás engedélyezéséhez. Használja a `{autoscale: null}` hasznos adat letiltásához automatikus skálázás.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Tekintse meg az előző [szakaszban a terhelés-alapú automatikus skálázás engedélyezéséről](#load-based-autoscaling) az összes hasznos adatparaméter teljes leírását.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Terhelésalapú vagy ütemezésalapú méretezés kiválasztása

Mielőtt döntést hozna a kívánt mód kiválasztásáról, vegye figyelembe a következő tényezőket:

* Engedélyezze az automatikus skálázást a fürt létrehozása során.
* A csomópontok minimális számának legalább háromnak kell lennie.
* Terheléseltérés: a fürt terhelése konzisztens mintát követ bizonyos időpontokban, adott napokon. Ha nem, a terhelésalapú ütemezés jobb megoldás.
* SLA-követelmények: Automatikus skálázás idlóhelyett a prediktív reaktív. Lesz-e elegendő késleltetés a terhelés növekedésének megkezdése és a fürt célméretének között? Ha vannak szigorú SLA követelmények és a terhelés egy fix ismert minta, "menetrend alapú" egy jobb választás.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Vegye figyelembe a horizontális felskálázási vagy leskálázási műveletek késését

A skálázási művelet befejezéséhez 10–20 percet is igénybe vehet. A testreszabott ütemezés beállításakor tervezze meg a késleltetést. Ha például 9:00 órakor 20-ra van szüksége a fürtméretre, állítsa be az ütemezési eseményindítót egy korábbi időpontra, például 8:30-ra, hogy a skálázási művelet 9:00-ig befejeződjen.

### <a name="preparation-for-scaling-down"></a>Felkészülés a leskálázásra

A fürt leskálázási folyamata során az automatikus skálázás leszereli a csomópontokat a célméret eléréséhez. Ha ezeken a csomópontokon futó feladatok vannak, az automatikus skálázás megvárja, amíg a feladatok befejeződnek. Mivel minden munkavégző csomópont is szolgál szerepet a HDFS-ben, az ideiglenes adatok a fennmaradó csomópontokra kerülnek. Ezért győződjön meg arról, hogy elegendő hely van a fennmaradó csomópontokon az összes ideiglenes adat üzemeltetéséhez.

A futó feladatok továbbra is futnak és befejeződnek. A függőben lévő feladatok a szokásos módon lesznek ütemezve kevesebb rendelkezésre álló munkavégző csomókkal.

### <a name="minimum-cluster-size"></a>Minimális fürtméret

Ne méretezze le a fürtöt három csomópontnál kevesebbre. Ha a fürtöt háromnál kevesebb csomópontra méretezi, az elégtelen fájlreplikáció miatt csökkentett módban elakadhat.  További információ: [Elakadás csökkentett módban.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

## <a name="monitoring"></a>Figyelés

### <a name="cluster-status"></a>Fürt állapota

Az Azure Portalon felsorolt fürtállapot segítségével figyelheti az automatikus skálázási tevékenységeket.

![A munkavégző csomópont terhelésalapú automatikus skálázású fürtállapotának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Az összes megjelenhetnek fürtállapot-üzenet az alábbi listában olvashatók be.

| Fürt állapota | Leírás |
|---|---|
| Fut | A fürt megfelelően működik. Az összes korábbi automatikus skálázási tevékenység sikeresen befejeződött. |
| Frissítése  | A fürt automatikus skálázási konfigurációja frissítés alatt áll.  |
| HDInsight-konfiguráció  | Fürtskálázási vagy -leskálázási művelet van folyamatban.  |
| Frissítési hiba  | A HDInsight problémákat észlelt az automatikus skálázási konfigurációs frissítés során. Az ügyfelek választhatnak, hogy újra próbálkoznak a frissítéssel, vagy letiltják az automatikus skálázást.  |
| Hiba  | Valami baj van a fürttel, és nem használható. Törölje ezt a fürtöt, és hozzon létre egy újat.  |

A fürt ben lévő csomópontok aktuális számának megtekintéséhez nyissa meg a fürt **Áttekintés lapján** a **Fürtméret** diagramot, vagy válassza a **Fürtméret** lehetőséget a **Beállítások csoportban.**

### <a name="operation-history"></a>Műveleti előzmények

Megtekintheti a fürt felskálázási és lekicsizési előzmények et a fürt metrikáinak részeként. Az elmúlt nap, hét vagy más időszak összes méretezési műveletét is felsorolhatja.

Válassza a **Metrikák lehetőséget a** **Figyelés**csoportban. Ezután válassza **a Metrika hozzáadása** és **az Aktív dolgozók száma** lehetőséget a **Metrika** legördülő lista. Az időtartomány módosításához jelölje ki a jobb felső sarokban lévő gombot.

![Munkavégző csomópont ütemezésalapú automatikus skálázási mérőszámának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>További lépések

A fürtök manuális méretezésével kapcsolatos gyakorlati tanácsok a [méretezésajánlott eljárásokban olvashatók.](hdinsight-scaling-best-practices.md)
