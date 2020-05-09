---
title: Azure HDInsight-fürtök automatikus méretezése
description: Az Azure HDInsight automatikus méretezési funkciója segítségével automatikusan Apache Hadoop a fürtök méretezése
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: f41a15fb52698eaa17d6f76b991cbd31a56ba14f
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731973"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight-fürtök automatikus méretezése

Az Azure HDInsight ingyenes automatikus méretezési funkciója a korábban beállított feltételek alapján automatikusan növelheti vagy csökkentheti a fürt munkavégző csomópontjainak számát. A fürt létrehozása során a csomópontok minimális és maximális számát állíthatja be, a méretezési feltételeket a napi ütemterv vagy a megadott teljesítmény-mérőszámok alapján kell meghatározni, és a HDInsight platform a REST-t használja.

## <a name="how-it-works"></a>Működés

Az automatikus méretezési funkció kétféle feltételt használ a skálázási események elindításához: a különböző fürt teljesítményének mérőszámai (úgynevezett *terheléses skálázás*) és az időalapú eseményindítók ( *ütemezett skálázás*) küszöbértékeit. A terhelésen alapuló skálázás megváltoztatja a fürtben lévő csomópontok számát a beállított tartományon belül, így biztosítva az optimális CPU-használatot, és csökkentheti a futtatási költségeket. Az ütemezett skálázás a fürt csomópontjainak számát a megadott dátummal és időpontokkal társított műveletek alapján módosítja.

### <a name="choosing-load-based-or-schedule-based-scaling"></a>A Load-based vagy az Schedule-alapú skálázás kiválasztása

Skálázási típus kiválasztásakor vegye figyelembe a következő tényezőket:

* Betöltési eltérés: a fürt terhelése bizonyos időpontokban következetes mintát követ bizonyos napokon? Ha nem, akkor a betöltés alapú ütemezés jobb megoldás.
* SLA-követelmények: az automatikus skálázás a prediktív végrehajtás helyett reaktív. Elegendő késés lesz a terhelés növekedésének megkezdése és a fürtnek a célként megadott méretnél való megadása között? Ha szigorú SLA-követelmények vannak meghatározva, és a terhelés egy rögzített ismert minta, az "ütemezés-alapú" jobb megoldás.

### <a name="cluster-metrics"></a>Fürt metrikái

Az autoscale folyamatosan figyeli a fürtöt, és a következő metrikákat gyűjti össze:

|Metrika|Leírás|
|---|---|
|Függőben lévő CPU összesen|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges magok teljes száma.|
|Függőben lévő memória összesen|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges teljes memória (MB).|
|Teljes szabad processzor|Az aktív munkavégző csomópontokon fel nem használt magok összege.|
|Szabad memória összesen|A nem használt memória (MB) összege az aktív munkavégző csomópontokon.|
|Felhasznált memória/csomópont|A munkavégző csomópont terhelése. Egy munkavégző csomópontot, amelyen 10 GB memóriát használ, nagyobb terhelésnek számít, mint egy 2 GB-nyi memóriát használó feldolgozó.|
|Alkalmazás-főkiszolgálók száma csomópont szerint|A munkavégző csomóponton futó Application Master (AM) tárolók száma. A két tárolót üzemeltető munkavégző csomópont sokkal fontosabbnak számít, mint a nulla AM tárolókat üzemeltető munkavégző csomópont.|

A fenti mérőszámok 60 másodpercenként vannak ellenőrizve. Ezen mérőszámok bármelyikével beállíthatja a fürt skálázási műveleteit.

### <a name="load-based-scale-conditions"></a>Terhelésen alapuló méretezési feltételek

Ha a következő feltételek észlelhetők, az autoskálázás egy méretezési kérelmet fog kiadni:

|Vertikális felskálázás|Vertikális leskálázás|
|---|---|
|A függőben lévő CPU összesen több mint 3 percnél nagyobb az összes szabad PROCESSZORnál.|A függőben lévő PROCESSZORok száma több mint 10 percnél kevesebb, mint az összes szabad CPU.|
|A függőben lévő memória összesen több mint 3 percnél nagyobb a teljes szabad memóriánál.|A függőben lévő memória mennyisége kevesebb, mint 10 percnél nagyobb a teljes szabad memória.|

A vertikális felskálázáshoz az autoskálázási szolgáltatás kibővíti a szükséges számú csomópont hozzáadására irányuló kérelmet. A felskálázás azon alapul, hogy hány új feldolgozó csomópontra van szükség a jelenlegi CPU-és memória-követelmények teljesítéséhez.

A leskálázáshoz az autoskálázás bizonyos számú csomópont eltávolítására irányuló kérést bocsát ki. A skálázás a csomóponton lévő AM tárolók számától függ. És a jelenlegi CPU-és memória-követelmények. A szolgáltatás azt is észleli, hogy mely csomópontok vannak kiválasztva az eltávolításra a jelenlegi feladatok végrehajtása alapján. A leskálázási művelet először leszereli a csomópontokat, majd eltávolítja őket a fürtből.

### <a name="cluster-compatibility"></a>Fürt kompatibilitása

> [!Important]
> Az Azure HDInsight autoscale funkciójának kiadása a Spark-és Hadoop-fürtökre vonatkozó 2019, november 7-én általánosan elérhető, és a szolgáltatás előzetes verziójában nem érhető el. Ha a Spark-fürtöt a 2019 november 7. előtt hozta létre, és szeretné használni az autoscale funkciót a fürtön, az ajánlott elérési út egy új fürt létrehozása, és az autoskálázás engedélyezése az új fürtön.
>
> Az interaktív lekérdezés (LLAP) és a HBase-fürtök autoskálázása még előzetes verzióban érhető el. Az autoskálázás csak a Spark, a Hadoop, az interaktív lekérdezés és a HBase-fürtökön érhető el.

Az alábbi táblázat az autoscale szolgáltatással kompatibilis fürtök típusát és verzióját ismerteti.

| Verzió | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 ESP nélkül | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 4,0 ESP nélkül | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 3,6, ESP-vel | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |
| HDInsight 4,0, ESP-vel | Igen | Igen | Igen | Igen* | Nem | Nem | Nem |

\*A HBase-fürtök csak az ütemezett skálázáshoz konfigurálhatók, nem pedig a terhelés alapján.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Fürt létrehozása terheléselosztási alapú automatikus skálázással

Ha az automatikus skálázási funkciót terheléselosztásos skálázással szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. A **konfiguráció + díjszabás** lapon jelölje be az automatikus **skálázás engedélyezése** jelölőnégyzetet.
1. Válassza az automatikus **skálázás típusa**alatt a **Betöltés** lehetőséget.
1. Adja meg a kívánt értékeket a következő tulajdonságokhoz:  

    * A **munkavégző csomóponthoz**tartozó **csomópontok kezdeti száma** .
    * A munkavégző csomópontok **minimális** száma.
    * A munkavégző csomópontok **maximális** száma.

    ![Munkavégző csomópont terheléses automatikus méretezésének engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

A munkavégző csomópontok kezdeti számának a minimális és a maximális érték közé kell esnie. Ez az érték határozza meg a fürt kezdeti méretét a létrehozáskor. A munkavégző csomópontok minimális számát három vagy több értékre kell beállítani. Ha a fürtöt kevesebb mint három csomópontra szeretné méretezni, azt eredményezheti, hogy a fájlreplikációs szolgáltatás nem elegendő a biztonságos módban.  További információ: [beragadás csökkentett módban](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Fürt létrehozása Schedule-alapú automatikus skálázással

Ha az automatikus skálázási funkciót ütemezett méretezéssel szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. A **konfiguráció és díjszabás** lapon jelölje be az automatikus **skálázás engedélyezése** jelölőnégyzetet.
1. Adja meg a **munkavégző csomóponthoz**tartozó **csomópontok számát** , amely a fürt skálázásának korlátját vezérli.
1. Válassza az **ütemezett** beállítás lehetőséget az **autoskálázás típusa**területen.
1. Válassza a **Konfigurálás** lehetőséget az **automatikus skálázási konfiguráció** ablak megnyitásához.
1. Válassza ki az időzónát, majd kattintson a **+ feltétel hozzáadása** elemre.
1. Válassza ki a hét azon napjait, amelyeket az új feltételnek alkalmaznia kell.
1. Szerkessze a feltétel érvénybe léptetésének időpontját, valamint azt, hogy a fürt hány csomópontra legyen méretezhető.
1. Szükség esetén további feltételek is felvehetők.

    ![Munkavégző csomópont ütemezett létrehozásának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

A csomópontok számának 3 és a feltételek hozzáadása előtt a munkavégző csomópontok maximális száma között kell lennie.

### <a name="final-creation-steps"></a>Végső létrehozás lépései

Válassza ki a virtuális gép típusát a munkavégző csomópontok számára egy virtuális gép kiválasztásával a legördülő listából a **csomópont mérete**alatt. Miután kiválasztotta a virtuálisgép-típust az egyes csomópont-típusokhoz, a teljes fürt becsült hatókörét láthatja. Állítsa be úgy a virtuális gépek típusait, hogy illeszkedjenek a költségkerethez.

![Munkavégző csomópont ütemezett méretezési csomópontjának engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Az előfizetéshez tartozik egy kapacitási kvóta az egyes régiókban. A fő csomópontok és a maximális munkavégző csomópontok teljes száma nem haladhatja meg a kapacitási kvótát. Ez a kvóta azonban egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyebben megnövelhető legyen.

> [!Note]  
> Ha túllépi a teljes felső korlátot, a következő hibaüzenet jelenik meg: "a maximális csomópont túllépte az elérhető magok számát ebben a régióban, válasszon egy másik régiót, vagy forduljon a támogatási szolgálathoz a kvóta növeléséhez."

További információ a HDInsight-fürtök létrehozásáról a Azure Portal használatával: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása Resource Manager-sablonnal

#### <a name="load-based-autoscaling"></a>Load-alapú automatikus skálázás

Létrehozhat egy HDInsight-fürtöt egy Azure Resource Manager-sablon terheléses automatikus skálázásával, `autoscale` Ha hozzáad egy csomópontot a `computeProfile`  >  `workernode` szakaszhoz a tulajdonságok `minInstanceCount` segítségével, `maxInstanceCount` és az alábbi JSON-kódrészletben látható.

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

#### <a name="schedule-based-autoscaling"></a>Ütemterv-alapú automatikus skálázás

`autoscale` HDInsight-fürtöt úgy hozhat létre, hogy a szakaszhoz hozzáad egy csomópontot a `computeProfile`  >  `workernode` Azure Resource Manager-sablonhoz. A `autoscale` csomópont tartalmaz `recurrence` egy `timezone` - `schedule` t, amely leírja, hogy mikor kerül sor a módosításra.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Az autoskálázás engedélyezése és letiltása egy futó fürtön

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha egy futó fürtön engedélyezni szeretné az autoskálázást, a **Beállítások**területen válassza a **fürt méretét** . Ezután válassza az **autoskálázás engedélyezése**lehetőséget. Válassza ki a kívánt automatikus méretezési típust, és adja meg a terhelés vagy az ütemterv szerinti skálázás beállításait. Végül válassza a **Mentés**lehetőséget.

![Munkavégző csomópont ütemezett méretezésének engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>A REST API használata

Egy futó fürtön a REST API használatával engedélyezheti vagy letilthatja az autoskálázást, ha POST-kérést küld az autoscale végpontnak:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Használja a kérelem hasznos adatainak megfelelő paramétereket. Az alábbi JSON-adattartalom használatával engedélyezhető az autoskálázás. Az autoscale `{autoscale: null}` letiltásához használja a hasznos adatokat.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Tekintse meg az előző szakaszt a [betöltési alapú automatikus skálázás engedélyezéséhez](#load-based-autoscaling) az összes hasznos adat paraméterének teljes leírásához.

## <a name="monitoring-autoscale-activities"></a>Az autoskálázási tevékenységek figyelése

### <a name="cluster-status"></a>Fürt állapota

A Azure Portalban felsorolt fürt állapota segíthet az autoskálázási tevékenységek figyelésében.

![Munkavégző csomópont terheléses automatikus méretezési fürt állapotának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Az alábbi listában az összes olyan fürt állapotüzenetek látható, amelyet látni fog.

| Fürt állapota | Leírás |
|---|---|
| Fut | A fürt rendesen működik. Az összes korábbi autoskálázási tevékenység sikeresen befejeződött. |
| Frissítése  | A fürt automatikus skálázási konfigurációjának frissítése folyamatban van.  |
| HDInsight-konfiguráció  | Egy fürt vertikális fel-vagy leskálázási művelete folyamatban van.  |
| Frissítési hiba  | A HDInsight az automatikus skálázási konfiguráció frissítése során teljesülnek. Az ügyfelek dönthetnek úgy, hogy megpróbálják megismételni a frissítést vagy letiltani az autoskálázást.  |
| Hiba  | Probléma van a fürttel, és nem használható. Törölje a fürtöt, és hozzon létre egy újat.  |

A fürtben lévő csomópontok aktuális számának megtekintéséhez nyissa meg a fürt **áttekintő** lapjának **fürt mérete** diagramját. Vagy válassza ki a **fürt méretét** a **Beállítások**területen.

### <a name="operation-history"></a>Műveleti előzmények

A fürt metrikáinak részeként megtekintheti a fürt vertikális felskálázásának és leskálázásának előzményeit is. Az összes skálázási műveletet az elmúlt nap, a hét vagy más idő alatt is listázhatja.

A **figyelés**területen válassza a **metrikák** lehetőséget. Ezután válassza a metrika **hozzáadása** és az **aktív feldolgozók száma** elemet a **metrika** legördülő listából. Kattintson a jobb felső sarokban lévő gombra az időtartomány módosításához.

![A feldolgozói csomópont Schedule-alapú autoskálázási metrikájának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="other-considerations"></a>Egyéb szempontok

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Vegye figyelembe a vertikális Felskálázási vagy leskálázási műveletek késését

A skálázási művelet befejezéséhez 10 – 20 percet is igénybe vehet. Testreszabott ütemterv beállításakor tervezze meg ezt a késleltetést. Ha például a fürt méretének 20-at kell megadnia a 9:00-es verziónál, állítsa az ütemezett triggert egy korábbi időpontra, például a 8:30-re, hogy a skálázási művelet a 9:00-as értékkel fejeződött be.

### <a name="preparation-for-scaling-down"></a>Felkészülés a méretezésre

A fürt skálázási folyamata során az automatikus skálázás leszereli a csomópontokat a célként megadott méret kielégítése érdekében. Ha a feladatok futnak ezeken a csomópontokon, az automatikusan megvárja a feladatok befejeződését. Mivel az egyes munkavégző csomópontok is a HDFS szerepkört is kiszolgálják, a rendszer a többi csomópontra helyezi át a temp-adatait. Ezért győződjön meg arról, hogy elegendő lemezterület áll rendelkezésre a többi csomóponton az összes Temp-érték üzemeltetéséhez.

A futó feladatok továbbra is folytatódnak. A függőben lévő feladatok a kevesebb rendelkezésre álló munkavégző csomóponttal való ütemezésre várnak.

### <a name="minimum-cluster-size"></a>Fürt minimális mérete

Ne méretezze a fürtöt kevesebb, mint három csomópontra. Ha a fürtöt kevesebb mint három csomópontra szeretné méretezni, azt eredményezheti, hogy a fájlreplikációs szolgáltatás nem elegendő a biztonságos módban.  További információ: [beragadás csökkentett módban](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="next-steps"></a>További lépések

További tudnivalók a fürtök méretezési [útmutatóinak](hdinsight-scaling-best-practices.md) manuális skálázásához
