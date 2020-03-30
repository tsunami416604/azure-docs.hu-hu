---
title: Másolási tevékenység teljesítményének – problémamegoldás
description: Ismerje meg, hogyan háríthatja el a másolási tevékenységek teljesítményének elhárítását az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 963b86852a7df557ad7179e444e7c3a2692f57d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531452"
---
# <a name="troubleshoot-copy-activity-performance"></a>Másolási tevékenység teljesítményének – problémamegoldás

Ez a cikk ismerteti, hogyan háríthatja el a másolási tevékenység teljesítményével kapcsolatos problémát az Azure Data Factoryban. 

A másolási tevékenység futtatása után a futtatási eredmény és a teljesítménystatisztikák [másolhat tevékenységfigyelési](copy-activity-monitoring.md) nézetben gyűjthetik. Például:

![A másolási tevékenység futtatásának részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Teljesítmény-finomhangolási tippek

Bizonyos esetekben, amikor egy másolási tevékenység et futtat a Data Factory, a **"Teljesítményhangolási tippek"** a tetején, amint az a fenti példában látható. A tippek az ADF által az adott példány futtatásához azonosított szűk keresztmetszetet, valamint a másolási átviteli szint növelésére vonatkozó javaslatokat ismertetik. Próbálja meg a várt módosítást, majd futtassa újra a másolatot.

Referenciaként jelenleg a teljesítményhangolási tippek a következő esetekre nyújtanak javaslatokat:

| Kategória              | Teljesítmény-finomhangolási tippek                                      |
| --------------------- | ------------------------------------------------------------ |
| Adattár-specifikus   | Adatok betöltése **az Azure Synpase Analytics (korábban SQL DW)**: javasoljuk a PolyBase vagy copy utasításhasználata, ha nem használják. |
| &nbsp;                | Adatok másolása/az **Azure SQL Database:** ha a DTU magas kihasználtság alatt áll, javasoljon magasabb szintre való frissítést. |
| &nbsp;                | Adatok másolása/az **Azure Cosmos DB:** ha a RU nagy kihasználtság alatt áll, javasoljon nagyobb RU-ra való frissítést. |
| &nbsp;                | Adatok betöltése **az Amazon Redshift:** javasoljuk, hogy unload, ha nem használják. |
| Adattár szabályozása | Ha az adattár a másolás során több olvasási/írási műveletet is szabályoz, javasoljon az adattár engedélyezett kérési arányának ellenőrzését és növelését, vagy csökkentse az egyidejű munkaterhelést. |
| Integrációs futásidejű  | Ha **önkiszolgáló integrációs futásidejű (IR)** és másolási tevékenység hosszú ideig vár a várólistában, amíg az infravörös rendelkezésre álló erőforrás futtatásához, javasoljuk horizontális felskálázás/fel az infravörös. |
| &nbsp;                | Ha egy nem optimális régióban lévő **Azure-integrációs runtime-ot** használ, amely lassú olvasást/írást eredményez, javasoljuk, hogy konfigurálja az infravörös szolgáltatást egy másik régióban. |
| Hibatűrés       | Ha hibatűrést állít be, és a nem kompatibilis sorok kihagyása lassú teljesítményt eredményez, javasoljuk, hogy biztosítsa a forrás- és fogadóadatok kompatibilitását. |
| Szakaszos másolás           | Ha a szakaszos másolás konfigurálva van, de nem hasznos a forrás-fogadó pár, javasoljuk, hogy távolítsa el azt. |
| Folytatás                | Ha a másolási tevékenység az utolsó hibaponttól folytatódik, de az eredeti futtatás után módosítja a DIU-beállítást, vegye figyelembe, hogy az új DIU-beállítás nem lép érvénybe. |

## <a name="understand-copy-activity-execution-details"></a>A másolási tevékenység végrehajtásának részleteinek ismertetése

A végrehajtási részletek és időtartamok alján a másolási tevékenység figyelési nézet ismerteti a legfontosabb fázisait a másolási tevékenység megy keresztül (lásd a cikk elején látható példát), ami különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg az alábbi táblázatot az egyes fázisok definícióját, és [megtudhatja, hogyan hárítsa el a másolási tevékenység az Azure IR](#troubleshoot-copy-activity-on-azure-ir) és [hibaelhárítási másolási tevékenység saját üzemeltetésű ir](#troubleshoot-copy-activity-on-self-hosted-ir) ilyen információkkal.

| Fázis           | Leírás                                                  |
| --------------- | ------------------------------------------------------------ |
| Várólista           | Az eltelt idő, amíg a másolási tevékenység ténylegesen elindul az integrációs futásidejű. |
| Parancsfájl előzetes másolása | Az infravörös kapcsolaton alapuló másolási tevékenység és a fogadó adattárban az előzetes másolási parancsfájl végrehajtása között eltelt idő. Alkalmazza, ha konfigurálja az előzetes másolási parancsfájl adatbázis-fogadók, például amikor adatokat ír az Azure SQL Database-be nem törli az új adatok másolása előtt. |
| Átvitel        | Az előző lépés vége és az összes adat forrásból fogadóba történő átvitele között eltelt idő. Az "Átvitel" alatti allépések párhuzamosan futnak.<br><br>- **Az első bájtig eltöltött idő:** Az előző lépés vége és az az időpont között eltelt idő, amikor az infravörös modul megkapja az első bájtot a forrásadattárból. Nem fájlalapú forrásokra vonatkozik.<br>- **Lista forrása:** A forrásfájlok vagy adatpartíciók számbavételével töltött idő. Ez utóbbi az adatbázis-források partíciós beállításainak konfigurálásakor érvényes, például amikor adatokat másol olyan adatbázisokból, mint az Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Olvasás forrásból:** Az adatok forrásadattárból történő beolvasásával töltött idő.<br/>- **Írás mosogató:** Az adatok írásával töltött idő az adattároló ba. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Másolási tevékenység – problémamegoldás az Azure IR-ben

Kövesse a [Teljesítményhangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a forgatókönyv teljesítménytesztjének megtervezéséhez és elvégzéséhez. 

Ha a másolási tevékenység teljesítménye nem felel meg az elvárásoknak, az Azure Integration Runtime-on futó egypéldányos tevékenység hibaelhárításához, ha [teljesítményhangolási tippeket](#performance-tuning-tips) jelenít meg a másolásfigyelési nézetben, alkalmazza a javaslatot, majd próbálkozzon újra. Ellenkező esetben [ismerje meg a másolási tevékenység végrehajtási részleteit,](#understand-copy-activity-execution-details)ellenőrizze, hogy melyik szakasz rendelkezik a **leghosszabb** időtartammal, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- **"Pre-copy script" tapasztalt hosszú időtartamú:** ez azt jelenti, hogy az előre másolás script futó mosogató adatbázis hosszú időt vesz igénybe. A teljesítmény növelése érdekében hangolja be a megadott előre másolás előtti parancsfájllogikát. Ha további segítségre van szüksége a parancsfájl javításához, forduljon az adatbázis-csapathoz.

- **A "Átvitel – Az első bájtos időhöz szükséges idő" hosszú munkaidőt tapasztalt:** ez azt jelenti, hogy a forráslekérdezés visszaadása hosszú időt vesz igénybe. Ellenőrizze és optimalizálja a lekérdezést vagy a kiszolgálót. Ha további segítségre van szüksége, forduljon az adattár csapatához.

- **A "Transfer - Listing source" hosszú munkaidőtartamot tapasztalt:** ez azt jelenti, hogy a forrásfájlok vagy a forrásadatbázis-adatpartíciók felsorolása lassú.

  - Fájlalapú forrásból történő másoláskor, ha **helyettesítő karaktert** használ a`wildcardFolderPath` `wildcardFileName`mappa elérési útján vagy a fájl nevén ( vagy ), vagy **a fájl utolsó módosításidejű időszűrőjét** (`modifiedDatetimeStart` vagy`modifiedDatetimeEnd`a ) megjegyzéssel, hogy az ilyen szűrő a megadott mappa összes fájlját ügyféloldalra sorolja, majd alkalmazza a szűrőt. Az ilyen fájlszámbavétel szűk keresztmetszetté válhat, különösen akkor, ha csak kis fájlkészlet felelt meg a szűrőszabálynak.

    - Ellenőrizze, hogy [a dátumidőpartiparti fájl elérési útja vagy neve alapján tud-e fájlokat másolni.](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Így nem hoz terhet a tőzsdei forrás oldalon.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, különösen az "**előtagot**" az Amazon S3 és az Azure Blob számára. Az előtagszűrő egy adattárkiszolgálói szűrő, amely sokkal jobb teljesítményt nyújt.

    - Fontolja meg egyetlen nagy adatkészlet felosztása több kisebb adatkészletek, és hagyja, hogy ezek a másolási feladatok futnak egyidejűleg minden foglalkozik az adatok egy részét. Ezt megteheti a Lookup/GetMetadata + ForEach + Copy segítségével. Példaként a [Fájlok másolása több tárolóból](solution-template-copy-files-multiple-containers.md) vagy [az Adatok áttelepítése az Amazon S3-ról az ADLS Gen2](solution-template-migration-s3-azure.md) megoldássablonokba című dokumentumra.

  - Ellenőrizze, hogy az ADF észlel-e bármilyen szabályozási hibát a forráson, vagy hogy az adattár magas kihasználtsági állapotban van-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

  - Használja az Azure IR-t ugyanabban vagy a forrás-adattár-régió közelében.

- **"Transfer - olvasás a forrásból" tapasztalt hosszú munkaidőtartama:** 

  - Fogadja el az összekötő-specifikus adatok betöltése ajánlott eljárás, ha vonatkozik. Például, amikor adatokat másol az [Amazon Redshift,](connector-amazon-redshift.md)konfigurálja a Redshift UNLOAD.

  - Ellenőrizze, hogy az ADF észlel-e bármilyen szabályozási hibát a forráson, vagy hogy az adattár nagy kihasználtság alatt áll-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

  - Ellenőrizze a másolási forrás- és fogadómintát: 

    - Ha a másolási minta támogatja a nagyobb, mint 4 adatintegrációs egységek (DIUs) - olvassa el ezt a [szakaszt](copy-activity-performance.md#data-integration-units) a részleteket, általában megpróbálhatja a DIUs növelése a jobb teljesítmény érdekében. 

    - Ellenkező esetben fontolja meg egyetlen nagy adatkészlet felosztása több kisebb adatkészletek, és hagyja, hogy ezek a másolási feladatok futnak egyidejűleg minden foglalkozik az adatok egy részét. Ezt megteheti a Lookup/GetMetadata + ForEach + Copy segítségével. Példaként a [Fájlok másolása több tárolóból,](solution-template-copy-files-multiple-containers.md) [Adatok áttelepítése az Amazon S3-ról az ADLS Gen2-be](solution-template-migration-s3-azure.md), vagy [például a Hármas példány vezérlőtábla-megoldássablonokkal](solution-template-bulk-copy-with-control-table.md) című dokumentum.

  - Használja az Azure IR-t ugyanabban vagy a forrás-adattár-régió közelében.

- **"Transfer - írás mosogató" tapasztalt hosszú munkaidőtartama:**

  - Fogadja el az összekötő-specifikus adatok betöltése ajánlott eljárás, ha vonatkozik. Például adatok másolásakor az [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (korábban SQL DW), használja polybase vagy COPY utasítás. 

  - Ellenőrizze, hogy az ADF észlel-e bármilyen szabályozáshibát a fogadóban, vagy hogy az adattár magas kihasználtság alatt áll-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

  - Ellenőrizze a másolási forrás- és fogadómintát: 

    - Ha a másolási minta támogatja a nagyobb, mint 4 adatintegrációs egységek (DIUs) - olvassa el ezt a [szakaszt](copy-activity-performance.md#data-integration-units) a részleteket, általában megpróbálhatja a DIUs növelése a jobb teljesítmény érdekében. 

    - Ellenkező esetben fokozatosan hangolja be a [párhuzamos másolatokat](copy-activity-performance-features.md), vegye figyelembe, hogy a túl sok párhuzamos másolat még a teljesítményt is károsíthatja.

  - Használja az Azure IR-t ugyanabban vagy a fogadó adattár-régiójának közelében.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Másolási tevékenység saját üzemeltetésű infravörös szolgáltatással kapcsolatos hibái val

Kövesse a [Teljesítményhangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a forgatókönyv teljesítménytesztjének megtervezéséhez és elvégzéséhez. 

Ha a másolási teljesítmény nem felel meg az elvárásoknak, az Azure Integration Runtime-on futó egypéldányos tevékenység hibaelhárításához, ha [teljesítményhangolási tippeket](#performance-tuning-tips) jelenít meg a másolásfigyelési nézetben, alkalmazza a javaslatot, majd próbálkozzon újra. Ellenkező esetben [ismerje meg a másolási tevékenység végrehajtási részleteit,](#understand-copy-activity-execution-details)ellenőrizze, hogy melyik szakasz rendelkezik a **leghosszabb** időtartammal, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- **"Várólista" tapasztalt hosszú időtartamú:** ez azt jelenti, hogy a másolási tevékenység hosszú ideig vár a várólistában, amíg a saját üzemeltetésű ir erőforrást kell végrehajtani. Ellenőrizze az infravörös kapacitást és a használatot, és a munkaterhelésnek megfelelően [skálázható vagy ki.](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

- **A "Átvitel – Az első bájtos időhöz szükséges idő" hosszú munkaidőt tapasztalt:** ez azt jelenti, hogy a forráslekérdezés visszaadása hosszú időt vesz igénybe. Ellenőrizze és optimalizálja a lekérdezést vagy a kiszolgálót. Ha további segítségre van szüksége, forduljon az adattár csapatához.

- **A "Transfer - Listing source" hosszú munkaidőtartamot tapasztalt:** ez azt jelenti, hogy a forrásfájlok vagy a forrásadatbázis-adatpartíciók felsorolása lassú.

  - Ellenőrizze, hogy a saját üzemeltetésű infravörös rendszer gép alacsony késleltetéssel csatlakozik a forrás-adattárhoz. Ha a forrás az Azure-ban, ezzel az [eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizheti a késést a saját üzemeltetésű infravörös gép az Azure-régióban, annál kevésbé, annál jobb.

  - Fájlalapú forrásból történő másoláskor, ha **helyettesítő karaktert** használ a`wildcardFolderPath` `wildcardFileName`mappa elérési útján vagy a fájl nevén ( vagy ), vagy **a fájl utolsó módosításidejű időszűrőjét** (`modifiedDatetimeStart` vagy`modifiedDatetimeEnd`a ) megjegyzéssel, hogy az ilyen szűrő a megadott mappa összes fájlját ügyféloldalra sorolja, majd alkalmazza a szűrőt. Az ilyen fájlszámbavétel szűk keresztmetszetté válhat, különösen akkor, ha csak kis fájlkészlet felelt meg a szűrőszabálynak.

    - Ellenőrizze, hogy [a dátumidőpartiparti fájl elérési útja vagy neve alapján tud-e fájlokat másolni.](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Így nem hoz terhet a tőzsdei forrás oldalon.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, különösen az "**előtagot**" az Amazon S3 és az Azure Blob számára. Az előtagszűrő egy adattárkiszolgálói szűrő, amely sokkal jobb teljesítményt nyújt.

    - Fontolja meg egyetlen nagy adatkészlet felosztása több kisebb adatkészletek, és hagyja, hogy ezek a másolási feladatok futnak egyidejűleg minden foglalkozik az adatok egy részét. Ezt megteheti a Lookup/GetMetadata + ForEach + Copy segítségével. Példaként a [Fájlok másolása több tárolóból](solution-template-copy-files-multiple-containers.md) vagy [az Adatok áttelepítése az Amazon S3-ról az ADLS Gen2](solution-template-migration-s3-azure.md) megoldássablonokba című dokumentumra.

  - Ellenőrizze, hogy az ADF észlel-e bármilyen szabályozási hibát a forráson, vagy hogy az adattár magas kihasználtsági állapotban van-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

- **"Transfer - olvasás a forrásból" tapasztalt hosszú munkaidőtartama:** 

  - Ellenőrizze, hogy a saját üzemeltetésű infravörös rendszer gép alacsony késleltetéssel csatlakozik a forrás-adattárhoz. Ha a forrás az Azure-ban, ezzel az [eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizheti a késést a saját üzemeltetésű infravörös gép az Azure-régiók, annál kevésbé, annál jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű infravörös gép rendelkezik-e elegendő bejövő sávszélességgel az adatok hatékony olvasásához és átviteléhez. Ha a forrás-adattár az Azure-ban van, [ezzel az eszközzel](https://www.azurespeed.com/Azure/Download) ellenőrizheti a letöltési sebességet.

  - Tekintse meg az önüzemeltetésű infravörös kapcsolat processzor- és memóriahasználati trendjét az Azure Portalon – > az adatgyárat > áttekintő lap. Fontolja meg a [fel-/ki-é-e infravörös skálázást,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) ha a CPU-használat magas vagy a rendelkezésre álló memória alacsony.

  - Fogadja el az összekötő-specifikus adatok betöltése ajánlott eljárás, ha vonatkozik. Példa:

    - Az [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)és SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)adatainak másolásakor engedélyezze az adatpartíció-beállítások párhuzamos másolását.

    - Adatok [HDFS-ből](connector-hdfs.md)történő másolásakor konfigurálja a DistCp használatát.

    - Amikor adatokat másol az [Amazon Redshift-ből,](connector-amazon-redshift.md)állítsa be a Redshift UNLOAD használatát.

  - Ellenőrizze, hogy az ADF jelentett-e szabályozási hibát a forráson, vagy hogy az adattár nagy kihasználtság alatt áll-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

  - Ellenőrizze a másolási forrás- és fogadómintát: 

    - Ha partícióopció-kompatibilis adattárakból másolja az adatokat, fontolja meg a [párhuzamos másolatok](copy-activity-performance-features.md)fokozatos finomhangolását, vegye figyelembe, hogy túl sok párhuzamos másolat még a teljesítményt is ronthatja.

    - Ellenkező esetben fontolja meg egyetlen nagy adatkészlet felosztása több kisebb adatkészletek, és hagyja, hogy ezek a másolási feladatok futnak egyidejűleg minden foglalkozik az adatok egy részét. Ezt megteheti a Lookup/GetMetadata + ForEach + Copy segítségével. Példaként a [Fájlok másolása több tárolóból,](solution-template-copy-files-multiple-containers.md) [Adatok áttelepítése az Amazon S3-ról az ADLS Gen2-be](solution-template-migration-s3-azure.md), vagy [például a Hármas példány vezérlőtábla-megoldássablonokkal](solution-template-bulk-copy-with-control-table.md) című dokumentum.

- **"Transfer - írás mosogató" tapasztalt hosszú munkaidőtartama:**

  - Fogadja el az összekötő-specifikus adatok betöltése ajánlott eljárás, ha vonatkozik. Például adatok másolásakor az [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (korábban SQL DW), használja polybase vagy COPY utasítás. 

  - Ellenőrizze, hogy a saját üzemeltetésű infravörös gép alacsony késleltetéssel csatlakozik-e a fogadó adattárhoz. Ha a fogadó az Azure-ban, ezzel az [eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizheti a késést a saját üzemeltetésű infravörös gép az Azure-régióban, annál kevésbé, annál jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű infravörös gép rendelkezik-e elegendő kimenő sávszélességgel az adatok hatékony átviteléhez és írásához. Ha a fogadó adattár az Azure-ban, [ezzel az eszközzel](https://www.azurespeed.com/Azure/UploadLargeFile) ellenőrizheti a feltöltési sebességet.

  - Ellenőrizze, hogy az Önüzemeltette infravörös rendszer processzor- és memóriahasználati trendje az Azure Portalon –>-e az adatgyárat – > áttekintő lap. Fontolja meg a [fel-/ki-é-e infravörös skálázást,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) ha a CPU-használat magas vagy a rendelkezésre álló memória alacsony.

  - Ellenőrizze, hogy az ADF észlel-e bármilyen szabályozáshibát a fogadóban, vagy hogy az adattár magas kihasználtság alatt áll-e. Ha igen, csökkentse az adattárban lévő számítási feladatokszámát, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával a szabályozási korlát vagy a rendelkezésre álló erőforrás növeléséhez.

  - Fontolja meg, hogy fokozatosan dallam a [párhuzamos másolatok](copy-activity-performance-features.md), vegye figyelembe, hogy túl sok párhuzamos másolatok is fáj a teljesítményt.

## <a name="other-references"></a>Egyéb hivatkozások

Az alábbiakban néhány támogatott adattárak teljesítményfigyelési és hangolási referenciáit olvashatja:

* Azure Blob storage: [Méretezhetőségi és teljesítménycélok blob storage](../storage/blobs/scalability-targets.md) és [teljesítmény- és méretezhetőségi ellenőrzőlista blob storage.](../storage/blobs/storage-performance-checklist.md)
* Azure Table storage: [Méretezhetőségi és teljesítménycélok táblatárolás](../storage/tables/scalability-targets.md) és [teljesítmény- és méretezhetőségi ellenőrzőlista table storage.](../storage/tables/storage-performance-checklist.md)
* Azure SQL Database: [Figyelheti a teljesítményt,](../sql-database/sql-database-single-database-monitor.md) és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos.
* Azure SQL Data Warehouse: A képességét adattárház egységek (DWUs) mérik. Lásd: [Számítási teljesítmény kezelése az Azure SQL Data Warehouse-ban (áttekintés)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Teljesítményszintek az Azure Cosmos DB-ben.](../cosmos-db/performance-levels.md)
* Helyszíni SQL Server: [A teljesítmény figyelése és hangolása](https://msdn.microsoft.com/library/ms189081.aspx).
* Helyszíni fájlkiszolgáló: [Teljesítményhangolás fájlkiszolgálókhoz.](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>További lépések
Lásd a többi másolási tevékenységcikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [A tevékenység teljesítményének és méretezhetőségének másolása útmutató](copy-activity-performance.md)
- [Tevékenységteljesítmény-optimalizálási funkciók másolása](copy-activity-performance-features.md)
- [Az Azure Data Factory segítségével áttelepítheti az adatokat az adattóból vagy az adattárházból az Azure-ba](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
