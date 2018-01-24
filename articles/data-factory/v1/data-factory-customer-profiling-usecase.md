---
title: "Használati eset – Ügyfélprofil-készítés"
description: "Ismerje meg, hogyan használható az Azure Data Factory adatvezérelt munkafolyamat létrehozása (adatcsatorna) profilhoz, játék-ügyfelek számára."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3a3c9c1d483ece71ecc38c7eaa364c35464d80f6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="use-case---customer-profiling"></a>Használati eset – Ügyfélprofil-készítés
Az Azure Data Factory a Cortana Intelligence Suite a megoldás gyorsítók végrehajtásához használt sok szolgáltatások egyike.  További információt a Cortana Intelligence [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Ez a dokumentum azt ismerteti egy egyszerű használati eset Ismerkedés a megértése, hogyan Azure Data Factory is közös analytics felmerülő problémák megoldásához nyújt segítséget.

## <a name="scenario"></a>Forgatókönyv
Contoso által létrehozott több platformot játékok játék vállalat esetében: játékok konzolok, kézi eszköz eszközök és személyes számítógépek. Játékosok a játékokat, mivel a naplóadatok nagy mennyiségű, amely nyomon követi a használati szokásokat, játék stílus és a felhasználó jön létre.  Demográfiai, regionális, kombinálva és termék adatok Contoso játékosok élmény fokozásával kapcsolatos kívánt analytics hajthat végre, és azokat a frissítéseket és a game target a későbbi szoftvervásárlások. 

Contoso-cél azonosítása a játékosok játék előzményei alapján felfelé-értékesít/kereszt-értékesítési lehetőségek és meghajtó üzleti növekedési kényszerítő szolgáltatások hozzáadása, és jobb élményt nyújtsanak az ügyfeleinek. A használati eset, az egy játék vállalati stratégiai példaként használjuk. A vállalat szeretne optimalizálja a játékok játékosok viselkedés alapján. Az alapelvek bármely üzleti, hogy az ügyfelek a termékek és szolgáltatások köré és az ügyfelek élmény javítása érdekében próbál alkalmazni.

Ebben a megoldásban Contoso szeretné egy közelmúltban elindította marketingkampányt hatékonyságát. Azt a nyers játék naplók kezdődnie, feldolgozása és kiegészítése őket a földrajzi hely meghatározásának adatokkal, csatlakozik, a referenciaadatok hirdetési és végül másolja őket abba a kampány hatás elemezheti Azure SQL adatbázis.

## <a name="deploy-solution"></a>Megoldás üzembe helyezéséhez
Eléréséhez, és próbálja ki az egyszerű használati eset szüksége van egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/), egy [Azure Blob storage-fiók](../../storage/common/storage-create-storage-account.md#create-a-storage-account), és egy [Azure SQL Database](../../sql-database/sql-database-get-started.md). A profilkészítési csővezeték az ügyfél központi telepítése a **folyamatok minta** csempét a data factory kezdőlapján.

1. Egy adat-előállító létrehozása, vagy nyissa meg valamelyik adat-előállítót. Lásd: [adatok másolása az Blob-tároló az SQL-adatbázis használata a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy adat-előállító létrehozásának lépései.
2. Az a **adat-előállító** az adat-előállítóban paneljén kattintson a **folyamatok minta** csempére.

    ![A minta folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. Az a **folyamatok minta** panelen kattintson a **profilkészítési ügyfél** , amely számára telepíteni kívánja.

    ![Minta folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az az Azure storage-fiók neve és kulcs, Azure SQL-kiszolgáló neve, adatbázis, felhasználói Azonosítót és jelszót.

    ![A minta panel](./media/data-factory-samples/SampleBlade.png)
5. Miután elkészült, a konfigurációs beállítások megadásával, kattintson **létrehozása** létrehozása/a minta folyamatok és telepítéséhez kapcsolódó szolgáltatások, illetve táblákat a folyamatok által használt.
6. A minta csempe kattintott a korábbi központi telepítés állapotát megtekintheti a **folyamatok minta** panelen.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **KözpontiTelepítés sikerült** a csempére a minta, zárja be az üzenetet a **folyamatok minta** panelen.  
8. A **adat-előállító** panelen látja, hogy társított szolgáltatások, adathalmazok és adatcsatornák bekerülnek a data factory.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Megoldás áttekintése
Ez egyszerű használati eset példa bemutatja, hogyan használhatja az Azure Data Factory betöltési, előkészítése, átalakítására, elemzése és adatok közzététele is használható.

![Teljes körű munkafolyamat](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ábra mutatja be, hogyan az adatok folyamatok jelennek meg az Azure-portálon telepítés után.

1. A **PartitionGameLogsPipeline** a nyers játék események beolvassa a blob storage és év, hónap és nap alapján létrehozott partícióknak hoz létre.
2. A **EnrichGameLogsPipeline** földrajzi kód referenciaadatokkal particionált játék események csatlakozik, valamint a következőképpen színesíti az adatok IP-címek leképezi a megfelelő földrajzi helyeken.
3. A **AnalyzeMarketingCampaignPipeline** folyamat használja a bővített adatok, és feldolgozza őket a végső kimenetet, amely tartalmazza a kampány marketingtevékenység hatékonyságát létrehozásához a hirdetés adatokkal.

Ebben a példában a Data Factory kell levezényelni a tevékenységek másolása a bemeneti adatok, az átalakítási és a folyamat az adatokat, és a végső adatokat az Azure SQL Database kimeneti szolgál.  Az adatok adatcsatornák hálózati megjelenítheti, kezelheti azokat, és figyelheti azok állapotát a felhasználói felületen.

## <a name="benefits"></a>Előnyök
A felhasználói profil analytics optimalizálása, és azt az üzleti céljaihoz igazítása, játék vállalati az képes gyorsan gyűjt használati szokásokról, és a marketingkampányok hatékonyságát.

