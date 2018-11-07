---
title: Használati eset – Ügyfélprofil-készítés
description: Ismerje meg, hogyan használható az Azure Data Factory adatvezérelt munkafolyamat létrehozása (folyamat) játék ügyfelek profilját.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5345ca3a9c2f35b45669a3a54ecb42ca627cde32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259819"
---
# <a name="use-case---customer-profiling"></a>Használati eset – Ügyfélprofil-készítés
Az Azure Data Factory a Cortana Intelligence Suite-megoldásgyorsítók megvalósításához használt számos szolgáltatás egyike.  Cortana Intelligence kapcsolatos további információkért látogasson el [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). Ez a dokumentum egyszerű használati esetek segítenek megérteni, hogyan tudja oldani az Azure Data Factory analytics kapcsolatos gyakori problémák a ismertetünk.

## <a name="scenario"></a>Forgatókönyv
Contoso egy játékokkal foglalkozó vállalatot, amely létrehozza a játékok, több platformon: játékok konzolok, az aktuális birtokolt eszközök és személyes számítógépek. Lejátszók ezek játékokat, mivel nagy mennyiségű napló jön létre, amely nyomon követi a használati mintákat, játék stílusának és a felhasználói beállítások.  Demográfiai, regionális, kombinálva és termékadatokat, Contoso játékos élmény fokozásával kapcsolatos nyújt számukra az analitikai hajthat végre, és azokat, frissítéseket és a játékbeli cél vásárol. 

A Contoso cél azonosítása a játékosok játék előzményei alapján akár-értékesítési és keresztértékesítési lehetőségeket, és meggyőző funkciót hozzáadhat az üzleti növekedés elősegítése érdekében, és jobb felhasználói élményt nyújtson az ügyfeleknek. Jelen esetben minden a egy játékokkal foglalkozó vállalat üzleti példaként használjuk. A vállalat úgy kívánja optimalizálni a játékok játékos viselkedés alapján. Minden olyan cég, amely körül a termékek és szolgáltatások ügyfeleinek és javíthatja a felhasználói élményt szeretné alkalmazni ezeket az alapelveket.

Ebben a megoldásban a Contoso biztosítani szeretné kiértékelni a nemrég elindította marketingkampány hatékonyságának. Azt indítsa el a nyers játék naplók, feldolgozása és bővítését őket a földrajzi helyadatokat, csatlakozik, a referenciaadatok hirdetési és végül másolja őket egy Azure SQL Database a kampány hatás elemzése.

## <a name="deploy-solution"></a>Megoldás üzembe helyezése
Elérheti és kipróbálhatja ezt a egyszerű használati esetet, mindössze egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/), egy [Azure Blob storage-fiók](../../storage/common/storage-quickstart-create-account.md), és a egy [Azure SQL Database](../../sql-database/sql-database-get-started.md). Az Ügyfélprofil-készítés a folyamat üzembe a **folyamatok minta** csempére az adat-előállító kezdőlapja.

1. Adat-előállító létrehozása, vagy nyisson meg egy meglévő data factoryt. Lásd: [adatok másolása Blob Storage-ból az SQL Database használatával a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) az adat-előállító létrehozásához szükséges lépéseket.
2. Az a **adat-előállító** a data Factory panelen kattintson a **folyamatok minta** csempére.

    ![Minta folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. Az a **folyamatok minta** panelen kattintson a **Ügyfélprofil-készítés** , amely számára telepíteni kívánja.

    ![Minta folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az Azure storage-fiók neve és a kulcs, az Azure SQL-kiszolgáló neve, adatbázis, felhasználói azonosító és jelszó.

    ![Mintául szolgáló panelt](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadása gombra **létrehozás** létrehozása és üzembe helyezéséhez a mintában a folyamatok és a társított szolgáltatások/táblák az egyes folyamatok által használt.
6. Központi telepítés állapotát az minta csempéjén, amire kattintott korábban tekintse meg a **folyamatok minta** panelen.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **üzembe helyezés sikeres** jelenik meg, zárja be a mintához a csempe a **folyamatok minta** panelen.  
8. A **adat-előállító** panelen láthatja, hogy a társított szolgáltatások, adatkészletek és folyamatok kerülnek az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Megoldás áttekintése
Ebben az esetben egyszerű, hogyan használhatja az Azure Data Factory a betöltési, előkészítése, átalakíthatja, elemezheti és adatok közzététele példaként használható.

![Teljes körű munkafolyamat](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ez az ábra bemutatja, hogyan az adatfolyamatok jelennek meg az Azure Portalon a telepítés után.

1. A **PartitionGameLogsPipeline** játék nyers események beolvassa a blob storage-ból, és év, hónap és nap alapján hoz létre.
2. A **EnrichGameLogsPipeline** földrajzi kód referenciaadatok particionált játék események csatlakozik, és bővíti az adatok IP-címek leképezi a megfelelő földrajzi helyekre.
3. A **AnalyzeMarketingCampaignPipeline** folyamat képi elemekben gazdag adatokat használja, és feldolgozza őket a hirdetés adatokkal hozhat létre, amely tartalmazza a marketing kampány hatékonyságát a végeredmény.

Ebben a példában a Data Factory segítségével, amely bemeneti adatokat, átalakítási és a folyamat az adatok másolása, és a végső soron az adatok egy Azure SQL Database kimeneti tevékenységek szervezését.  Megjelenítheti a hálózaton az adatfolyamatok, kezelheti azokat, valamint figyelheti azok állapotát a felhasználói felületen.

## <a name="benefits"></a>Előnyök
Saját felhasználói profil analytics optimalizálása, és azt üzleti céljaihoz igazodó, játékokkal foglalkozó vállalatot is képes gyorsan használati minták összegyűjtheti és elemezheti a saját digitálismarketing-kampányok hatékonyságát.

