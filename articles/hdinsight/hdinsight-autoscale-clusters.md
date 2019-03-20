---
title: Automatikus skálázása az Azure HDInsight-fürtök (előnézet)
description: A HDInsight automatikus méretezési funkciója segítségével fürtök automatikus méretezése
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 28f04f5ab3cf8310a6ee3828405910d34b31591b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227619"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatikus skálázása az Azure HDInsight-fürtök (előnézet)

>[!Important]
>A HDInsight automatikus méretezési funkciója jelenleg előzetes verzióban érhető el. Küldjön egy e-mailek hdiautoscalepm@microsoft.com szeretné, hogy az automatikus méretezés engedélyezve az előfizetésében.

Az Azure HDInsight-fürt automatikus skálázási funkció automatikusan méretezi magát a munkavégző csomópontok számát felfelé és lefelé a fürt egy előre definiált tartományon belüli terhelés alapján. Egy új HDInsight-fürt létrehozásakor a feldolgozó csomópontok minimális és maximális száma megadható. Az automatikus méretezés, majd a figyelők az analytics erőforrásigényeinek betölteni és méretezi a munkavégző csomópontok számát vagy leskálázásakor, ennek megfelelően. Nem jár további költségekkel ennek a funkciónak.

## <a name="getting-started"></a>Első lépések

### <a name="create-a-cluster-with-the-azure-portal"></a>Fürt létrehozása az Azure portal használatával

> [!Note]
> Az automatikus skálázás jelenleg csak a támogatott Azure HDInsight Hive, a MapReduce és a Spark fürt 3.6-os verzióját.

Az automatikus skálázási funkció engedélyezéséhez a következőkre normál Fürtlétrehozási folyamatának részeként:

1. Válassza ki **egyéni (méret, beállítások, alkalmazások)** helyett **Gyorslétrehozás**.
2. A **egyéni** 5. lépés (**fürtméret**) Ellenőrizze a **munkavégző csomópont automatikus skálázási** jelölőnégyzetet.
3. Adja meg a kívánt értékeket a következő tulajdonságokkal:  

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

Engedélyezheti vagy az Azure Portalon keresztül 2019. január 1. után létrehozott HDInsight-fürtök automatikus skálázás letiltása.

## <a name="monitoring"></a>Figyelés

Megtekintheti a fürt felfelé és lefelé méretezési előzményeket a fürtmetrikák részeként. Az elmúlt is listázhatja az összes skálázási műveletek napi, heti vagy hosszabb ideig.

## <a name="how-it-works"></a>Működés

### <a name="metrics-monitoring"></a>Figyelési metrikák

Az automatikus méretezés folyamatosan figyeli a fürt és a következő metrikákat gyűjt:

1. **Összes függőben lévő CPU**: Az összes függőben lévő tárolók végrehajtásának elindítását szolgáló szükséges magok teljes száma.
2. **Függőben lévő memória teljes**: A teljes memória (MB) van szükség az összes függőben lévő tárolók végrehajtásának elindításához.
3. **Ingyenes CPU összesen**: Az aktív feldolgozó csomópontokon futó összes nem használt magok összege.
4. **Teljes szabad memória**: A fel nem használt memóriamennyiség (MB) a aktív feldolgozó csomópontokon futó összege.
5. **Foglalt memória csomópontonként**: A terhelés, a feldolgozó csomóponton. Munkavégző csomópont 10 GB-nyi memóriát használ, akkor tekinthető, mint a használt memória 2 GB-os dolgozó további terhelés alatt.
6. **Csomópontonként alkalmazás főkiszolgálók száma**: A feldolgozó csomóponton futó alkalmazás fő (Csendes) tárolók száma. Futtató két AM tárolók, munkavégző csomópont fontosabb, mint nulla AM tárolókat üzemeltető munkavégző csomópont számít.

A fenti metrikák 60 másodpercenként ellenőrzi. Automatikus skálázási rendszer döntéseket felfelé és lefelé méretezési metrikák alapján.

### <a name="cluster-scale-up"></a>Fürt vertikális felskálázása

A következő feltételek észlelése esetén, az automatikus méretezés a vertikális felskálázás kérelem állít ki:

* Függőben lévő CPU összesen mérete nagyobb teljes szabad CPU-nál több mint 1 perc.
* Függőben lévő memória összesített érték nagyobb, mint a teljes szabad memória a több mint 1 perce.

Mi számít, hogy egy bizonyos számú munkavégző csomópontok az aktuális Processzor- és követelményeinek, és hogyan adhat ki, amely hozzáadja a munkavégző csomópontok számát felfelé kérelem van szükség.

### <a name="cluster-scale-down"></a>Fürt méretezése

A következő feltételek észlelése esetén, automatikus skálázási kérelem állít ki:

* Függőben lévő CPU összesen 10 percnél hosszabb ideig nem éri el a teljes szabad CPU.
* Függőben lévő memória összesen 10 percnél hosszabb ideig nem éri el a teljes szabad memória.

/ Csomópont és a jelenlegi CPU és a memóriaigény AM tárolók száma alapján, az automatikus méretezés ki távolítsa el a csomópontokon, bizonyos számú kérelem adja meg, melyik csomópontokon lehetséges eltávolítása a deduplikációra. Alapértelmezés szerint két csomópont egy ciklus fog szerepelni.

## <a name="next-steps"></a>További lépések

* Olvassa el manuálisan a fürtök méretezés ajánlott eljárásai [skálázása – ajánlott eljárások](hdinsight-scaling-best-practices.md)
