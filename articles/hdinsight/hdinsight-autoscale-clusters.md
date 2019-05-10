---
title: Automatikus skálázása az Azure HDInsight-fürtök (előnézet)
description: A HDInsight automatikus méretezési funkciója segítségével fürtök automatikus méretezése
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: f8803a498e62958a5488f2ac8830137c37533e54
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413699"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatikus skálázása az Azure HDInsight-fürtök (előnézet)

Az Azure HDInsight-fürt automatikus méretezési funkciója automatikusan méretezi magát a munkavégző csomópontok számát a fürtben található felfelé és lefelé. A fürtben található csomópontok más típusú jelenleg nem lehet méretezni.  Egy új HDInsight-fürt létrehozásakor a feldolgozó csomópontok minimális és maximális száma megadható. Automatikus skálázási ezután figyeli a analytics terhelés erőforrásigényeinek és méretezi a munkavégző csomópontok számát felfelé vagy lefelé. Nem jár további költségekkel ennek a funkciónak.

## <a name="cluster-compatibility"></a>Fürt-kompatibilitás

> [!Important]
> Az automatikus skálázási funkció csak a nyilvános rendelkezésre állás a funkció a 2019. május után létrehozott fürtök esetében működik. Mindez nem működik a már meglévő fürtök esetében.

A következő táblázat ismerteti a fürt típusát és verziókat, amelyek kompatibilisek az automatikus méretezési funkciója.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6-os ESP nélkül | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 4.0 ESP nélkül | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 3.6-ot az ESP | Igen | Igen | Nem | Nem | Nem | Nem | Nem |
| HDInsight 3.6-ot az ESP | Igen | Igen | Nem | Nem | Nem | Nem | Nem |

## <a name="how-it-works"></a>A szolgáltatás működése

Lehetősége van a terhelés-alapú méretezés, vagy a HDInsight-fürt méretezése ütemezésalapú. Betöltés-alapú méretezés módosítja egy Ön által beállított, győződjön meg, hogy optimális CPU-kihasználtság, és minimalizálja a költségeket futó tartományon belül, a fürt csomópontjainak számát.

Ütemezésalapú skálázási módosítások a fürtben található csomópontok számát, amelyek a megadott időpontban életbe feltételek alapján. Ezek a feltételek a kívánt számú csomópontot fürt méretezése.

### <a name="metrics-monitoring"></a>Figyelési metrikák

Az automatikus méretezés folyamatosan figyeli a fürt és a következő metrikákat gyűjt:

* **Összes függőben lévő CPU**: Az összes függőben lévő tárolók végrehajtásának elindítását szolgáló szükséges magok teljes száma.
* **Függőben lévő memória teljes**: A teljes memória (MB) van szükség az összes függőben lévő tárolók végrehajtásának elindításához.
* **Ingyenes CPU összesen**: Az aktív feldolgozó csomópontokon futó összes nem használt magok összege.
* **Teljes szabad memória**: A fel nem használt memóriamennyiség (MB) a aktív feldolgozó csomópontokon futó összege.
* **Foglalt memória csomópontonként**: A terhelés, a feldolgozó csomóponton. Munkavégző csomópont 10 GB-nyi memóriát használ, akkor tekinthető, mint a használt memória 2 GB-os dolgozó további terhelés alatt.
* **Csomópontonként alkalmazás főkiszolgálók száma**: A feldolgozó csomóponton futó alkalmazás fő (Csendes) tárolók száma. Futtató két AM tárolók, munkavégző csomópont fontosabb, mint nulla AM tárolókat üzemeltető munkavégző csomópont számít.

A fenti metrikák 60 másodpercenként ellenőrzi. Az automatikus méretezés lehetővé teszi a felfelé és lefelé méretezési döntések metrikák alapján.

### <a name="load-based-cluster-scale-up"></a>Betöltés-alapú fürt vertikális felskálázása

A következő feltételek észlelése esetén, az automatikus méretezés a vertikális felskálázás kérelem állít ki:

* Függőben van a Processzor teljes mérete nagyobb teljes szabad CPU-nál több mint 3 perc alatt.
* Függőben lévő memória teljes mérete nagyobb teljes szabad memória-nál több mint 3 perc alatt.

A HDInsight szolgáltatás kiszámítja a munkavégző csomópontok számát a jelenlegi CPU és memória követelmények teljesítéséhez szükséges, és ezt követően kiállított adja hozzá a szükséges csomópontok számát felfelé irányuló.

### <a name="load-based-cluster-scale-down"></a>Betöltés-alapú fürt méretezése

A következő feltételek észlelése esetén, automatikus skálázási kérelem állít ki:

* Függőben lévő CPU összesen 10 percnél hosszabb ideig nem éri el a teljes szabad CPU.
* Függőben lévő memória összesen 10 percnél hosszabb ideig nem éri el a teljes szabad memória.

/ Csomópont és a jelenlegi CPU és a memóriaigény AM tárolók száma alapján, az automatikus méretezés kibocsát egy kérelmet, bizonyos számú csomópont eltávolítása. A szolgáltatás is észleli, amelyek csomópontok deduplikációra alapján a jelenlegi feladat-végrehajtási eltávolítása. A vertikális leskálázási művelet először decommissions a csomópontokat, és eltávolítja a fürtből.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Egy fürt létrehozása a betöltés-alapú automatikus skálázás

Ahhoz, hogy az automatikus méretezési funkciója a terhelés-alapú méretezés, a következő lépéseket a normál Fürtlétrehozási folyamat részeként:

1. Válassza ki **egyéni (méret, beállítások, alkalmazások)** helyett **Gyorslétrehozás**.
1. A **egyéni** 5. lépés (**fürtméret**), ellenőrizze a **munkavégző csomópont automatikus skálázási** jelölőnégyzetet.
1. Válassza a **terhelés-alapú** alatt **automatikus skálázási típus**.
1. Adja meg a kívánt értékeket a következő tulajdonságokkal:  

    * Kezdeti **száma a munkavégző csomópontok**.  
    * **Minimális** munkavégző csomópontok száma.  
    * **Maximális** munkavégző csomópontok száma.  

    ![Munkavégző csomópont terhelés-alapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

A munkavégző csomópontok kezdeti száma között minimális és maximális tartományba kell esnie. Ez az érték határozza meg a fürt kezdeti mérete, a létrehozásakor. A feldolgozó csomópontok minimális száma csak nullánál nagyobbnak kell lennie.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Ütemezésalapú automatikus skálázást egy fürt létrehozása

A méretezéssel ütemezésalapú automatikus skálázási funkció engedélyezéséhez a következő lépéseket a normál Fürtlétrehozási folyamat részeként:

1. Válassza ki **egyéni (méret, beállítások, alkalmazások)** helyett **Gyorslétrehozás**.
1. A **egyéni** 5. lépés (**fürtméret**), ellenőrizze a **munkavégző csomópont automatikus skálázási** jelölőnégyzetet.
1. Adja meg a **száma a munkavégző csomópontok**, amely szabályozza, hogy a fürt vertikális felskálázása a korlátot.
1. Válassza a **ütemezésalapú** alatt **automatikus skálázási típus**.
1. Kattintson a **konfigurálása** megnyitásához a **automatikus skálázási konfigurációjának** ablak.
1. Válassza ki az időzóna, és kattintson a **+ feltétel hozzáadása**
1. Válassza ki a hét azon napjai, az új feltétel kell vonatkozniuk.
1. Szerkessze az idő a feltételt vesz igénybe, a hatás és a fürt kell méretezhetők a csomópontok számát.
1. Ha szükséges, hozzáadhat további feltételeket.

    ![Munkavégző csomópont ütemezésalapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Csomópontok száma 1 és feltételek hozzáadása előtt a megadott munkavégző csomópontok között kell lennie.

### <a name="final-creation-steps"></a>Utolsó létrehozásának lépései

Betöltés mind ütemezésalapú méretezését, válassza ki a virtuális gép munkavégző csomópontok kattintva **velikost Pracovního uzlu** és **csomópontméret Átjárócsomópont**. Miután kiválasztotta az egyes csomóponttípusok VM-típus, láthatja a becsült költség esik az egész fürt számára. Módosítsa a virtuális gépek típusai a költségvetéshez igazodó.

![Munkavégző csomópont ütemezésalapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Az előfizetés minden olyan régió esetében kapacitás kvótával rendelkezik. Kombinálva a feldolgozó csomópontok maximális száma a fő csomópontok magok teljes száma nem haladhatja meg a kapacitás kvótát. Azonban ez a kvóta-e egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyen növelni álljon.

> [!Note]  
> Ha túllépi a teljes magkvótájának határértékét, kapni fog egy hibaüzenet közli, hogy "a csomópontok maximális túllépte a rendelkezésre álló magok ebben a régióban, válasszon egy másik régióban, vagy forduljon az támogatási a kvóta növeléséhez."

További információ a HDInsight-fürt létrehozása az Azure portal használatával: [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása a Resource Manager-sablonnal

#### <a name="load-based-autoscaling"></a>Betöltés-alapú automatikus skálázás

Létrehozhat egy HDInsight-fürt és az automatikus skálázás terhelés-alapú egy Azure Resource Manager-sablon hozzáadásával egy `autoscale` a csomópontot a `computeProfile`  >  `workernode` szakasz a tulajdonságokkal `minInstanceCount` és `maxInstanceCount` , az alábbi json-kódrészletben látható.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
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

A Resource Manager-sablonok-fürtök létrehozásával kapcsolatos további információkért, lásd: [Apache Hadoop-fürtök létrehozása a HDInsight használatával a Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Ütemezésalapú automatikus méretezés

Létrehozhat egy HDInsight-fürt és az ütemezés alapú automatikus skálázás egy Azure Resource Manager-sablon hozzáadásával egy `autoscale` a csomópontot a `computeProfile`  >  `workernode` szakaszban. A `autoscale` csomópont tartalmaz egy `recurrence` , amely rendelkezik egy `timezone` és `schedule` , amely azt ismerteti, amikor a módosítás történik.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Engedélyezheti vagy letilthatja az automatikus skálázási futó fürt

Egy futó fürt automatikus skálázás engedélyezéséhez jelölje be **fürtméret** alatt **beállítások**. Kattintson a **automatikus skálázás engedélyezése**. Válassza ki az automatikus méretezés, hogy szeretné, és adja meg a terhelés alapján vagy ütemezés-alapú méretezésének lehetőségeiről. Végül kattintson **mentése**.

![Munkavégző csomópont ütemezésalapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="monitoring"></a>Figyelés

### <a name="cluster-status"></a>Fürt állapota

A fürt állapotát, az Azure Portalon szereplő segíthet az automatikus méretezés tevékenységek figyelése.

![Munkavégző csomópont terhelés-alapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

A fürt állapotüzeneteket, amelyeket láthat minden mutatjuk be az alábbi lista.

| Fürt állapota | Magyarázat |
|---|---|
| Futtatás | A fürt megfelelően működik. Az összes automatikus skálázási előző tevékenység sikeresen befejeződött. |
| Frissítés  | A fürt automatikus skálázási konfigurációjának frissítése folyamatban van.  |
| HDInsight-konfiguráció  | Fürt vertikális felskálázás vagy vertikális leskálázási művelet folyamatban van.  |
| Frissítési hiba  | HDInsight problémákat észlelt az automatikus skálázási konfigurációjának frissítése során. Ügyfelek eldönthetik, próbálkozzon újra a frissítést, vagy az automatikus skálázás letiltása.  |
| Hiba  | Hiba található a fürt, és már nem használható. Törli ezt a fürtöt, és hozzon létre egy újat.  |

A fürtben található csomópontok jelenlegi száma megtekintéséhez nyissa meg a **fürtméret** a diagram a **áttekintése** oldalon a fürt számára, vagy kattintson **fürtméret** alatt  **Beállítások**.

### <a name="operation-history"></a>A művelet előzmények

Megtekintheti a fürt felfelé és lefelé méretezési előzményeket a fürtmetrikák részeként. Az elmúlt nap, hét vagy más időn keresztül is listázhatja az összes skálázási műveletet.

Válassza ki **metrikák** alatt **figyelési**. Kattintson a **metrika hozzáadása** és **aktív feldolgozók száma** származó a **metrika** legördülő menüből. A gombra kattintva módosíthatja az időtartományt.

![Munkavégző csomópont ütemezésalapú automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>További lépések

* Olvassa el manuálisan a fürtök méretezés ajánlott eljárásai [skálázása – ajánlott eljárások](hdinsight-scaling-best-practices.md)
