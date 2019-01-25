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
ms.openlocfilehash: 977466d77bee2d6fe49c0438aa1d9d4489f8eb5f
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888226"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatikus skálázása az Azure HDInsight-fürtök (előnézet)

Az Azure HDInsight-fürt automatikus skálázási funkció automatikusan méretezi magát a munkavégző csomópontok számát felfelé és lefelé a fürt egy előre definiált tartományon belüli terhelés alapján. Egy új HDInsight-fürt létrehozásakor a feldolgozó csomópontok minimális és maximális száma megadható. Az automatikus méretezés, majd a figyelők az analytics erőforrásigényeinek betölteni és méretezi a munkavégző csomópontok számát vagy leskálázásakor, ennek megfelelően. Nem jár további költségekkel ennek a funkciónak.

## <a name="getting-started"></a>Első lépések

### <a name="create-cluster-with-azure-portal"></a>Fürt létrehozása az Azure Portalon

> [!Note]
> Az automatikus skálázás jelenleg csak a támogatott Azure HDInsight Hive, a MapReduce és a Spark fürt 3.6-os verzióját.

Teljes HDInsight fürt létrehozásának lépései az Azure Portal használatával található [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).  A létrehozási folyamat során az automatikus méretezés engedélyezése szükséges néhány gyakorlattól eltérő a szokásos telepítési lépéseket.  

1. Válassza ki **egyéni (méret, beállítások, alkalmazások)** helyett **Gyorslétrehozás**.
2. 5. lépés az egyéni **fürtméret**, ellenőrizze a **munkavégző csomópont automatikus skálázási** jelölőnégyzetet.
3. Adja meg a kívánt:  
  &#8226;Kezdeti **száma a munkavégző csomópontok**.  
  &#8226;**Minimális** munkavégző csomópontok száma.  
  &#8226;**Maximális** munkavégző csomópontok száma.  

![Munkavégző csomópont automatikus skálázási beállítás engedélyezése](./media/hdinsight-autoscale-clusters/usingAutoscale.png)


A munkavégző csomópontok kezdeti száma között minimális és maximális tartományba kell esnie. Ez az érték határozza meg a fürt kezdeti mérete, a létrehozásakor. A feldolgozó csomópontok minimális száma csak nullánál nagyobbnak kell lennie.

Miután kiválasztotta az egyes csomóponttípusok VM-típus, lesz megtekintheti a becsült költség esik az egész fürt számára. Ezek a beállítások a költségvetéshez igazodó majd módosíthatja.

Az előfizetés minden olyan régió esetében kapacitás kvótával rendelkezik. Kombinálva a feldolgozó csomópontok maximális száma a fő csomópontok magok teljes száma nem haladhatja meg a kapacitás kvótát. Azonban ez a kvóta-e egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyen növelni álljon.

> [!Note]  
> Ha túllépi a teljes magkvótájának határértékét, kapni fog egy hibaüzenet közli, hogy "a csomópontok maximális túllépte a rendelkezésre álló magok ebben a régióban, válasszon egy másik régióban, vagy forduljon az támogatási a kvóta növeléséhez."

### <a name="create-cluster-with-an-resource-manager-template"></a>Fürt létrehozása a Resource Manager-sablon

Teljes HDInsight fürt létrehozása Resource Manager-sablonok használatával lépéseket található [Apache Hadoop-fürtök létrehozása a HDInsight használatával a Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  Egy HDInsight-fürtön az Azure Resource Manager-sablon létrehozásakor kell a "computeProfile" a "workernode" szakaszban adja hozzá a következő beállításokat, és ennek megfelelően szerkesztésére:

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

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Engedélyezése és letiltása az automatikus skálázási futó fürt

Az automatikus méretezés engedélyezése egy futó fürt privát előzetes verzió ideje alatt nem támogatott. Engedélyezni kell a fürt létrehozásakor.

Automatikus skálázás letiltása, vagy egy futó fürt automatikus méretezési beállításainak módosítása nem támogatott privát előzetes verzióban érhető el. Törölje a fürtöt kell, és hozzon létre egy új fürtöt törölni vagy módosítani a beállításokat.

## <a name="monitoring"></a>Figyelés

A fürt vertikális felfelé és a fürt metrikák részeként előzmények tekintheti meg. Az elmúlt listázhatja az összes skálázási műveletek napi, heti vagy hosszabb ideig.

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

Mi számít, hogy a munkavégző csomópontok N aktuális Processzor- és követelményeinek, és hogyan adhat ki egy méretezési kérelem kérve N munkavégző csomópontok van szükség.

### <a name="cluster-scale-down"></a>Fürt vertikális leskálázási

A következő feltételek észlelése esetén, az automatikus méretezés a vertikális leskálázási kérelem állít ki:

* Függőben lévő CPU összesen 10 percnél hosszabb ideig nem éri el a teljes szabad CPU.
* Függőben lévő memória összesen 10 percnél hosszabb ideig nem éri el a teljes szabad memória.

/ Csomópont, valamint az aktuális Processzor- és követelmények AM tárolók száma alapján, az automatikus méretezés fog adjon ki egy kérelmet eltávolítása N, adja meg, melyik csomópontokon lehetséges eltávolítása a deduplikációra. Alapértelmezés szerint két csomópont egy ciklus fog szerepelni.

## <a name="next-steps"></a>További lépések

* Olvassa el manuálisan a fürtök méretezés ajánlott eljárásai [skálázása – ajánlott eljárások](hdinsight-scaling-best-practices.md)
