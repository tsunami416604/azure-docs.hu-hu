---
title: Használati eset – Ügyfél profilkészítés
description: Ismerje meg, hogyan használható az Azure Data Factory egy adatalapú munkafolyamat (folyamat) létrehozásához a játékügyfelek profilzatához.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969294"
---
# <a name="use-case---customer-profiling"></a>Használati eset – Ügyfél profilkészítés
Az Azure Data Factory egyike a Cortana Intelligence Suite megoldásgyorsítók megvalósításához használt számos szolgáltatásnak.  A Cortana Intelligence szolgáltatásról a [Cortana Intelligence csomagban](https://www.microsoft.com/cortanaanalytics)talál további információt. Ebben a dokumentumban egy egyszerű használati esetet ismertünk, amely segít az Azure Data Factory gyakori elemzési problémáinak megoldásában.

## <a name="scenario"></a>Forgatókönyv
A Contoso egy játékgyártó vállalat, amely játékokat hoz létre több platformra: játékkonzolokhoz, kézi eszközökhöz és személyi számítógépekhez.Contoso is a gaming company that creates games for multiple platforms: game consoles, held held devices, and personal computers (PC). Mivel a játékosok játszani ezeket a játékokat, nagy mennyiségű napló adatok keletkeznek, hogy nyomon követi a használati szokások, játék stílusa, és a preferenciák a felhasználó.  Demográfiai, regionális és termékadatokkal kombinálva a Contoso elemzéseket végezhet, hogy irányítsa őket a játékosok élményének növelésére és a frissítésekre és a játékon belüli vásárlásokra való célzásra. 

A Contoso célja, hogy azonosítsa a játékosok játéktörténetén alapuló up-sell/cross-sell lehetőségeket, és lenyűgöző funkciókkal bővítse az üzleti növekedést és jobb élményt nyújtson az ügyfeleknek. Ebben a használati esetben egy szerencsejáték-vállalatot használunk egy vállalkozás példájaként. A cég a játékosok viselkedése alapján szeretné optimalizálni a játékait. Ezek az elvek minden olyan vállalkozásra vonatkoznak, amely ügyfeleit az áruk és szolgáltatások köré kívánja vonni, és növelni kívánja ügyfelei élményét.

Ebben a megoldásban a Contoso szeretné értékelni a nemrégiben elindított marketingkampány hatékonyságát. A nyers játéknaplókkal kezdjük, feldolgozzuk és gazdagítjuk őket földrajzi helymeghatározási adatokkal, összekapcsoljuk a hirdetési referenciaadatokkal, és végül átmásoljuk őket egy Azure SQL Database-be, hogy elemezzük a kampány hatását.

## <a name="deploy-solution"></a>Megoldás telepítése
Ehhez az egyszerű használati esethez mindössze egy [Azure-előfizetésre](https://azure.microsoft.com/pricing/free-trial/), egy [Azure Blob-tárfiókra](../../storage/common/storage-account-create.md)és egy [Azure SQL-adatbázisra](../../sql-database/sql-database-get-started.md)van szüksége. Az ügyfélprofil-készítési folyamatot a **mintafolyamatok** csempéjéről telepíti az adat-előállító kezdőlapján.

1. Hozzon létre egy adat-előállító, vagy nyisson meg egy meglévő adatgyárat. Az adat-előállító létrehozásához lásd: [Adatok másolása a Blob Storage-ból sql-adatbázisba a Data Factory használatával](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben.
2. Az adat-előállító **DATA FACTORY** panelen kattintson a **Mintafolyamatok** csempére.

    ![Mintafolyamatok csempéje](./media/data-factory-samples/SamplePipelinesTile.png)
3. A **Mintafolyamatok** panelen kattintson az üzembe helyezni kívánt **Ügyfél-profilkészítéselemre.**

    ![Mintafolyamatok panelje](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az Azure storage-fiók nevét és kulcsát, az Azure SQL-kiszolgáló nevét, adatbázisát, felhasználói azonosítóját és jelszavát.

    ![Mintapen](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadásával, kattintson a **Létrehozás** gombra a mintafolyamatok és a folyamatok által használt csatolt szolgáltatások/táblák létrehozásához/üzembe helyezéséhez.
6. A **mintafolyamatok** panelen korábban kattintott mintacsempén látható a központi telepítés állapota.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **központi telepítés sikeres** üzenetet a csempe a minta, zárja be a **minta folyamatok** panel.  
8. A **DATA FACTORY** panelen láthatja, hogy a csatolt szolgáltatások, adatkészletek és folyamatok hozzáadódnak az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Megoldási áttekintés
Ez az egyszerű használati eset példaként használható arra, hogyan használhatja az Azure Data Factory segítségével az adatok betöltését, előkészítését, átalakítását, elemzését és közzétételét.

![Teljes körű munkafolyamat](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ez az ábra bemutatja, hogyan jelennek meg az adatfolyamatok az Azure Portalon azok üzembe helyezése után.

1. A **PartitionGameLogsPipeline** beolvassa a nyers játékeseményeket a blob storage-ból, és létrehozza a partíciókat év, hónap és nap alapján.
2. Az **EnrichGameLogsPipeline** geokód referenciaadatokkal csatlakozik a particionált játékeseményekhez, és gazdagítja az adatokat azáltal, hogy ip-címeket a megfelelő földrajzi helyekhez képez le.
3. Az **AnalyzeMarketingCampaignPipeline** folyamat a bővített adatokat használja, és feldolgozza azokat a hirdetési adatokkal a marketingkampány hatékonyságát tartalmazó végső kimenet létrehozásához.

Ebben a példában a Data Factory olyan tevékenységek vezénylésére szolgál, amelyek a bemeneti adatokmásolására, az adatok átalakítására és feldolgozására, valamint a végleges adatok egy Azure SQL-adatbázisba történő kimenetére szolgálnak.  Az adatfolyamatok hálózatát is megjelenítheti, kezelheti, és figyelheti azok állapotát a felhasználói felületről.

## <a name="benefits"></a>Előnyök
A felhasználói profil elemzésének optimalizálásával és az üzleti célokhoz való igazításával a szerencsejáték-vállalat gyorsan összegyűjtheti a használati mintákat, és elemezheti marketingkampányainak hatékonyságát.

