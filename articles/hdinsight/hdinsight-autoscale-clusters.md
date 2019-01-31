---
title: Automatikus skálázása az Azure HDInsight-fürtök (előnézet)
description: A HDInsight automatikus méretezési funkciója segítségével fürtök automatikus méretezése
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: bd1ffcfd915fe9ece683ec88d27f54b3a9214621
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475677"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatikus skálázása az Azure HDInsight-fürtök (előnézet)

Az Azure HDInsight-fürt automatikus skálázási funkció automatikusan méretezi magát a munkavégző csomópontok számát felfelé és lefelé a fürt egy előre definiált tartományon belüli terhelés alapján. Egy új HDInsight-fürt létrehozásakor a feldolgozó csomópontok minimális és maximális száma megadható. Az automatikus méretezés, majd a figyelők az analytics erőforrásigényeinek betölteni és méretezi a munkavégző csomópontok számát vagy leskálázásakor, ennek megfelelően. Nem jár további költségekkel ennek a funkciónak.

## <a name="getting-started"></a>Első lépések

### <a name="create-a-cluster-with-the-azure-portal"></a>Fürt létrehozása az Azure portal használatával

> [!Note]
> Az automatikus skálázás jelenleg csak a támogatott Azure HDInsight Hive, a MapReduce és a Spark fürt 3.6-os verzióját.

Az automatikus skálázási funkció engedélyezéséhez a normál Fürtlétrehozási folyamat részeként a következőket tegye:

1. Válassza ki **egyéni (méret, beállítások, alkalmazások)** helyett **Gyorslétrehozás**.
2. A **egyéni** 5. lépés (**fürtméret**) Ellenőrizze a **munkavégző csomópont automatikus skálázási** jelölőnégyzetet.
3. Adja meg a kívánt:  

    * Kezdeti **száma a munkavégző csomópontok**.  
    * **Minimális** munkavégző csomópontok száma.  
    * **Maximális** munkavégző csomópontok száma.  

![Munkavégző csomópont automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

A munkavégző csomópontok kezdeti száma között minimális és maximális tartományba kell esnie. Ez az érték határozza meg a fürt kezdeti mérete, a létrehozásakor. A feldolgozó csomópontok minimális száma csak nullánál nagyobbnak kell lennie.

Miután kiválasztotta az egyes csomóponttípusok VM-típus, lesz megtekintheti a becsült költség esik az egész fürt számára. Ezek a beállítások a költségvetéshez igazodó majd módosíthatja.

Az előfizetés minden olyan régió esetében kapacitás kvótával rendelkezik. Kombinálva a feldolgozó csomópontok maximális száma a fő csomópontok magok teljes száma nem haladhatja meg a kapacitás kvótát. Azonban ez a kvóta-e egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyen növelni álljon.

> [!Note]  
> Ha túllépi a teljes magkvótájának határértékét, kapni fog egy hibaüzenet közli, hogy "a csomópontok maximális túllépte a rendelkezésre álló magok ebben a régióban, válasszon egy másik régióban, vagy forduljon az támogatási a kvóta növeléséhez."

További információ a HDInsight-fürt létrehozása az Azure portal használatával: [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása a Resource Manager-sablonnal

Egy Azure Resource Manager-sablonnal, hozzon létre egy HDInsight-fürtöt, adjon hozzá egy `autoscale` csomópont a `computeProfile`  >  `workernode` szakasz a tulajdonságokkal `minInstanceCount` és `maxInstanceCount` az alábbi json-kódrészletben látható módon.

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Engedélyezheti vagy letilthatja az automatikus skálázási futó fürt

Az automatikus méretezés engedélyezése egy futó fürt privát előzetes verzió ideje alatt nem támogatott. Engedélyezni kell a fürt létrehozásakor.

Automatikus skálázás letiltása, vagy egy futó fürt automatikus méretezési beállításainak módosítása nem támogatott privát előzetes verzióban érhető el. Törölje a fürtöt kell, és hozzon létre egy új fürtöt törölni vagy módosítani a beállításokat.

## <a name="monitoring"></a>Figyelés

Megtekintheti a fürt vertikális felskálázási és vertikális leskálázás előzményei a fürtmetrikák részeként. Az elmúlt is listázhatja az összes skálázási műveletek napi, heti vagy hosszabb ideig.

## <a name="how-it-works"></a>Működés

### <a name="metrics-monitoring"></a>Figyelési metrikák

Az automatikus méretezés folyamatosan figyeli a fürt és a következő metrikákat gyűjt:

1. **Összes függőben lévő CPU**: Az összes függőben lévő tárolók végrehajtásának elindítását szolgáló szükséges magok teljes száma.
2. **Függőben lévő memória teljes**: A teljes memória (MB) van szükség az összes függőben lévő tárolók végrehajtásának elindításához.
3. **Ingyenes CPU összesen**: Az aktív feldolgozó csomópontokon futó összes nem használt magok összege.
4. **Teljes szabad memória**: A fel nem használt memóriamennyiség (MB) a aktív feldolgozó csomópontokon futó összege.
5. **Foglalt memória csomópontonként**: A terhelés, a feldolgozó csomóponton. Munkavégző csomópont 10 GB-nyi memóriát használ, akkor tekinthető, mint a használt memória 2 GB-os dolgozó további terhelés alatt.
6. **Csomópontonként alkalmazás főkiszolgálók száma**: A feldolgozó csomóponton futó alkalmazás fő (Csendes) tárolók száma. 2 AM tárolókat üzemeltető munkavégző csomópont fontosabb, mint 0 óra tárolókat üzemeltető munkavégző csomópont számít.

A fenti metrikák 60 másodpercenként ellenőrzi. Automatikus méretezés méretezési alkotják, és a vertikális leskálázás metrikák alapján döntéseket hozhat.

### <a name="cluster-scale-up"></a>Fürt vertikális felskálázási

A következő feltételek észlelése esetén, automatikus skálázási vertikális felskálázási kérelmet bocsát ki:

* Függőben lévő CPU összesen mérete nagyobb teljes szabad CPU-nál több mint 1 perc.
* Függőben lévő memória összesített érték nagyobb, mint a teljes szabad memória a több mint 1 perce.

Mi számít, hogy egy bizonyos számú munkavégző csomópontok aktuális Processzor- és követelményeinek, és hogyan adhat ki vertikális felskálázási kérelmet, amely hozzáadja a munkavégző csomópontok száma van szükség.

### <a name="cluster-scale-down"></a>Fürt vertikális leskálázási

A következő feltételek észlelése esetén, az automatikus méretezés a vertikális leskálázási kérelem állít ki:

* Függőben lévő CPU összesen 10 percnél hosszabb ideig nem éri el a teljes szabad CPU.
* Függőben lévő memória összesen 10 percnél hosszabb ideig nem éri el a teljes szabad memória.

/ Csomópont, valamint az aktuális Processzor- és követelmények AM tárolók száma alapján, az automatikus méretezés állít ki távolítsa el a csomópontokon, bizonyos számú kérelem adja meg, melyik csomópontokon lehetséges eltávolítása a deduplikációra. Alapértelmezés szerint két csomópont egy ciklus fog szerepelni.

## <a name="next-steps"></a>További lépések

* Olvassa el manuálisan a fürtök méretezés ajánlott eljárásai [skálázása – ajánlott eljárások](hdinsight-scaling-best-practices.md)
