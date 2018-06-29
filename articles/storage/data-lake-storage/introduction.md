---
title: Az Azure Data Lake tárolási Gen2 Preview bemutatása
description: Azure Data Lake tárolási Gen2 előzetes áttekintése
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0631b1d0c8da925858f0b7fb1d778cb1161eb737
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061524"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Bevezetés az Azure Data Lake tárolási Gen2 előzetes

Az Azure Data Lake tárolási Gen2 előzetes kijelölve a big data elemzésre szolgáló, beépített a képességek egy készletét, [Azure Blob Storage tárolóban](../blobs/storage-blobs-introduction.md). Lehetővé teszi mindkét fájl rendszer és objektum-tárolási mintáknak használata esetén az adatok az illesztő. Így Data Lake tárolási Gen2 a csak felhőalapú kombinált társzolgáltatás, lehetővé téve analytics értékének kinyerése az összes adat.

Data Lake tárolási Gen2 összes minőségű analitikai adatok a teljes életciklusát a szükséges funkciókat. Ez a két meglévő tárolási szolgáltatások lehetőségeinek zajlik eredménye. A szolgáltatások [Azure Data Lake tárolási Gen1](../../data-lake-store/index.md), a fájlrendszer szemantikáját, például fájlszintű biztonsági, és a skála alacsony költségű, rétegzett tárolás, a magas rendelkezésre állás/vész-helyreállítási képességek és a nagy SDK/tooling együtt az ökoszisztéma [Azure Blob Storage tárolóban](../blobs/storage-blobs-introduction.md). A Data Lake tárolási Gen2 objektum tároló összes minőség addig maradnak, amíg az analytics hozzáadása egy fájl rendszer felületet előnyei optimalizált munkaterhelések.

## <a name="designed-for-enterprise-big-data-analytics"></a>A vállalati big data koncepción tervezett

Data Lake tárolási Gen2 a legalapvetőbb társzolgáltatás felépítéséhez a vállalati adatok tavakat (EDL) az Azure-on. A start átviteli Gigabit több száz fenntartása közben adatok több petabájt kiszolgálásához úgy tervezték, Data Lake tárolási Gen2 lehetővé teszi az kezeléséhez nagy mennyiségű adatot egyszerűen.

A Data Lake tárolási Gen2 alapvető jellemzője hozzáadása egy [hierarchikus névtér](./namespace.md) a Blob storage szolgáltatás, amely rendezi objektumok/fájlokat performant adatelérési címtárak hierarchiába. A hierarchikus névtér azt is lehetővé teszi, hogy a Data Lake tárolási Gen2 mindkét objektum store támogatása, és a rendszer mintáknak fájlt egy időben. Például közös objektum tároló elnevezési használatával perjeleket a nevében a hierarchikus struktúrájának utánozzák. Ez a struktúra válik a Data Lake tárolási Gen2 valós. Műveletek, például a átnevezése vagy törlése egy könyvtárat egy atomi metaadatok műveletek a directory helyett számbavétele és annak a könyvtárnak a nevét előtagot használó összes objektumok feldolgozása vált.

A múltban felhőalapú analytics kellett a teljesítmény, felügyeleti és biztonsági hibát okoz. Data Lake tárolási Gen2 címek ezeket az jellemzőket mindegyikének a következőképpen:

- **Teljesítmény** megfelelően lett optimalizálva, mert nem másolja vagy adatok átalakítása elemzéshez előfeltételként szükséges. A hierarchikus névtér jelentősen javítja a könyvtár-felügyeleti műveletek, ami javítja az általános feladat teljesítményt.

- **Felügyeleti** oka egyszerűbb rendszerezheti és a fájlkezelő keresztül könyvtárak és alkönyvtárak.

- **Költség hatékonyságának** lehetséges legyen, a Data Lake tárolási Gen2 épül az alacsony költségű [Azure Blob Storage tárolóban](../blobs/storage-blobs-introduction.md). A további szolgáltatások további alacsonyabb a teljes tulajdonosi költség, Azure big data-elemzések futtatására.

## <a name="key-features-of-data-lake-storage-gen2"></a>A Data Lake tárolási Gen2 a legfontosabb jellemzők

> [!NOTE]
> A Data Lake tárolási Gen2 nyilvános előzetes az alább felsorolt funkciók némelyike változhat a rendelkezésre állásuk. Új szolgáltatásokat és régiók kiadott során a programot, a tájékoztatni kell.
> [Regisztráció](https://aka.ms/adlsgen2signup) a nyilvános előzetes verziójára Data Lake tárolási Gen2.  

- **Hadoop-kompatibilis hozzáférés**: Data Lake tárolási Gen2 lehetővé teszi, hogy ugyanúgy, mint az adatok elérése és kezelésére egy [Hadoop elosztott fájlrendszerrel (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS illesztőprogram](./abfs-driver.md) elérhető összes Apache Hadoop-környezetekben, beleértve a [Azure HDInsight](../../hdinsight/index.yml) és [Azure Databricks](../../azure-databricks/index.yml) a Data Lake tárolási adataihoz hozzáférést Gen2.

- **Többprotokollos és kombinált adatelérési**: Data Lake tárolási Gen2 tekinthető egy **több modális** , mert a társzolgáltatás biztosít objektum tároló és a fájl ugyanazokat az adatokat a rendszer felületek **ugyanazon idő**. Ez az érhető el, amely férhetnek hozzá ugyanazokhoz az adatokhoz több protokollvégpontokat megadásával. 

    Más analytics megoldásoktól eltérően a Data Lake tárolási Gen2 adataihoz nem kell áthelyezni vagy elemzőeszközök számos futtatása előtt kell alakítani. Hagyományos keresztül az adatok eléréséhez [Blob storage API-kkal](../blobs/storage-blobs-introduction.md) (például: adatok keresztül [Event Hubs rögzítése](../../event-hubs/event-hubs-capture-enable-through-portal.md)) és a HDInsight- vagy Azure Databricks használatával egyszerre adatok feldolgozása. 

- **Költséghatékony**: Data Lake tárolási Gen2 szolgáltatások alacsony költségű tárolási kapacitás és a tranzakciók. A teljes életciklusuk átmenetek adatok, mint számlázási díjszabás módosítsa karbantartási költségek keresztül beépített szolgáltatásai legalább például [Azure Blob storage életciklus](../common/storage-lifecycle-managment-concepts.md).

- **A Blob storage-eszközökkel, keretrendszerek és alkalmazásokkal együttműködve**: Data Lake tárolási Gen2 továbbra is fennáll, az eszközök, keretrendszerek és alkalmazásokat, amelyek a Blob Storage ma létezik széles köréről együttműködni.

- **Optimalizált illesztőprogram**: A `abfs` illesztőprogram [kifejezetten optimalizált](./abfs-driver.md) a big data koncepción. A megfelelő REST API-kon keresztül illesztett a `dfs` végpont, `dfs.core.windows.net`.

## <a name="scalability"></a>Méretezhetőség

Az Azure Storage egy méretezhető úgy lett kialakítva, hogy Ön érhetnek el Data Lake tárolási Gen2 vagy a Blob storage-felületekkel. Képes tárolni és ki tudja szolgálni *adatok sok exabájtjain való elemzést*. Ezt a mennyiséget, a tárolás Gigabit / másodperc (Gbps) a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) magas szintű mért átviteli érhető el. Csak adatmegőrzési túl feldolgozási végrehajtja kérelmenkénti közelében állandó késéseket okoz, amelyek alapján a szolgáltatás, a fiók és a fájl szintjén.

## <a name="cost-effectiveness"></a>Költséghatékonyság

Data Lake tárolási Gen2 felépítése fölött Azure Blob storage számos előnyének egyike a [alacsony költségű](https://azure.microsoft.com/pricing/details/storage) tárolási kapacitás és a tranzakciók. Egyéb tárolási felhőszolgáltatások, ellentétben a Data Lake tárolási Gen2 költségeket csökkenti a mert adatok áthelyezése vagy át legyenek-e elemzés végrehajtása előtt fel kell.

Továbbá, szolgáltatások, például a a [hierarchikus névtér](./namespace.md) sok analytics-feladatok általános teljesítménye jelentősen növelhető. Ez a teljesítmény fokozása azt jelenti, hogy a szükséges kevesebb számítási teljesítményt feldolgozni azonos adatmennyiséget, ami tulajdonosi (TCO) egy alacsonyabb költségek a végpont analytics-feladat.

## <a name="next-steps"></a>További lépések

A következő cikkek ismertetik azokat a főbb fogalmakat Data Lake tárolási Gen2 és részletes tárolására, hozzáférni, kezelése és az adataiból információt kaphat:

* [Hierarchikus névtér](./namespace.md)
* [Tárfiók létrehozása](./quickstart-create-account.md)
* [HDInsight-fürtök létrehozása az Azure Data Lake tárolási Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Használhatja az Azure Data Lake tárolási Gen2 fiókot Azure Databricks](./quickstart-create-databricks-account.md) 