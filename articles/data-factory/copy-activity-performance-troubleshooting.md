---
title: A másolási tevékenység teljesítményével kapcsolatos hibák
description: További információ a másolási tevékenység teljesítményének hibakereséséről a Azure Data Factory.
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
ms.date: 06/10/2020
ms.openlocfilehash: d339e68dcf49c74c508029fda3e7eb548ec92588
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84770953"
---
# <a name="troubleshoot-copy-activity-performance"></a>A másolási tevékenység teljesítményével kapcsolatos hibák

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan lehet elhárítani a másolási tevékenység teljesítményével kapcsolatos problémát Azure Data Factoryban. 

A másolási tevékenység futtatása után összegyűjtheti a Futtatás eredményét és a teljesítmény statisztikáit a [másolási tevékenység figyelése](copy-activity-monitoring.md) nézetben. Például:

![A másolási tevékenység futtatási részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Teljesítmény-finomhangolási tippek

Bizonyos helyzetekben, amikor másolási tevékenységet futtat Data Factoryban, a fenti példában látható **"Performance tuning tippek"** láthatók. A tippekből megtudhatja, hogy az ADF milyen szűk keresztmetszetet észlelt ehhez a másolási menethez, valamint javaslatot tesz a másolási teljesítmény növelésére. Próbálja megismételni a módosításokat, majd futtassa újra a másolást.

A teljesítmény-hangolási tippek jelenleg a következő esetekben nyújtanak javaslatokat:

| Kategória              | Teljesítmény-finomhangolási tippek                                      |
| --------------------- | ------------------------------------------------------------ |
| Adattár-specifikus   | Adatok betöltése az **Azure Synpase analyticsbe (korábban SQL DW)**: javasolt a Base vagy a copy utasítás használata, ha nincs használatban. |
| &nbsp;                | Adatok másolása innen/ből **Azure SQL Database**: Ha a DTU magas kihasználtságú, javasoljuk, hogy magasabb szintre frissítsen. |
| &nbsp;                | Adatok másolása a (z)/rendszerről **Azure Cosmos DBre**: Ha ru magas kihasználtsággal rendelkezik, javasoljuk, hogy nagyobb ru-re frissítsen. |
|                       | Adatok másolása az **SAP-táblából**: nagy mennyiségű adatok másolása esetén az SAP-összekötő partíciós lehetőségének kihasználása lehetővé teszi a párhuzamos betöltést, és növeli a maximális partíció számát. |
| &nbsp;                | Az **Amazon vöröseltolódásról**származó adatok beolvasása |
| Adattár-szabályozás | Ha az adattár a másolás során több írási/olvasási műveletet is szabályoz, akkor az adattároló megengedett kérelmi arányának ellenőrzését és növelését, illetve az egyidejű munkaterhelés csökkentését javasolja. |
| Integration Runtime  | Ha saját üzemeltetésű **Integration Runtime (IR)** és másolási tevékenységet használ a várólistán mindaddig, amíg az IR nem rendelkezik a végrehajtáshoz rendelkezésre álló erőforrással, javasoljuk, hogy bővítse/felskálázást hajtson végre az IR-ben. |
| &nbsp;                | Ha olyan **Azure Integration Runtime** használ, amely nem optimális régióban van, ami lassú olvasási/írási kísérletet eredményez, akkor azt javasoljuk, hogy egy másik régióban lévő IR használatára konfigurálja a konfigurálást. |
| Hibatűrés       | Ha a hibatűrést konfigurálja, és a nem kompatibilis sorok kihagyása lassú teljesítményt eredményez, a forrás-és fogadói adat kompatibilitásának biztosítása javasolt. |
| Előkészített másolás           | Ha a szakaszos másolás konfigurálva van, de nem hasznos a forrás-fogadó pár számára, javasoljuk, hogy távolítsa el azt. |
| Folytatás                | Ha a másolási tevékenység az utolsó meghibásodási pontról folytatódik, de az eredeti Futtatás után módosítja a DIU beállítást, vegye figyelembe, hogy az új DIU beállítás nem lép érvénybe. |

## <a name="understand-copy-activity-execution-details"></a>A másolási tevékenység végrehajtási részleteinek ismertetése

A másolási tevékenység figyelése nézet alján a végrehajtás részletei és időtartama a másolási tevékenység lépéseit ismerteti (lásd a cikk elején található példát), amely különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási Futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg az alábbi táblázatot az egyes fázisok definíciójában, és Ismerje meg, hogyan lehet [elhárítani a másolási tevékenységet a Azure IR](#troubleshoot-copy-activity-on-azure-ir) és a [másolási tevékenységet a saját](#troubleshoot-copy-activity-on-self-hosted-ir) üzemeltetésű integrációs modulban az ilyen adatokkal.

| Fázis           | Leírás                                                  |
| --------------- | ------------------------------------------------------------ |
| Várólista           | Az az eltelt idő, amíg a másolási tevékenység ténylegesen nem indul el az integrációs modulban. |
| Másolás előtti parancsfájl | A másolási tevékenység és a másolási tevékenység közötti eltelt idő a fogadó adattárban lévő előmásolási parancsfájl végrehajtásának befejezése után. Az adatbázis-nyelők előmásolási parancsfájljának konfigurálásakor alkalmazza, például amikor az adatírást Azure SQL Database az új Adatmásolás előtt törli az adatbevitelt. |
| Átvitel        | Az előző lépés vége és a forrás és a fogadó közötti összes adatok átvitele között eltelt idő. <br/>Figyelje meg, hogy az átvitel alatt álló allépések párhuzamosan futnak, és egyes műveletek nem jelennek meg, pl. a fájlformátum elemzése/létrehozása.<br><br/>- **Első bájtig eltelt idő:** Az előző lépés vége és az az idő, amikor az IR megkapja az első bájtot a forrás adattárból. A nem fájl alapú forrásokra vonatkozik.<br>- **Listaelem forrása:** A forrásfájlok vagy az adatpartíciók számbavételére fordított idő mennyisége. Az utóbbi akkor érvényes, ha az adatbázis-források partíciós beállításait konfigurálja, például az adatok olyan adatbázisokból való másolásakor, mint például az Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Olvasás a forrástól:** Az adatok forrás adattárból való beolvasásához felhasznált idő mennyisége.<br/>- **Írás a** fogadóba: Az adattárolási adattárba való adatírás során eltöltött idő mennyisége. Megjegyzés: egyes összekötők jelenleg nem rendelkeznek ezzel a metrikával, beleértve az Azure Cognitive Search, az Azure Adatkezelő, az Azure Table Storage, az Oracle, a SQL Server, a Common Data Service, a Dynamics 365, a Dynamics CRM, a Salesforce/Salesforce Service Cloud szolgáltatást. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR másolási tevékenységének hibáinak megoldása

Kövesse a [teljesítmény-hangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. 

Ha a másolási tevékenység teljesítménye nem felel meg az elvárásoknak, a Azure Integration Runtime futó egypéldányos másolási tevékenységek hibáinak megoldása érdekében alkalmazza a javaslatot a másolás figyelése nézetben, és próbálkozzon újra. [performance tuning tips](#performance-tuning-tips) Ellenkező esetben olvassa el a [másolási tevékenység végrehajtásának részleteit](#understand-copy-activity-execution-details), tekintse meg a **leghosszabb** időtartamú szakaszt, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- **"A másolás előtti parancsfájl" hosszú** ideig tart: az azt jelenti, hogy a fogadó adatbázison futó másolás előtti parancsfájl hosszú ideig tart. A teljesítmény növelése érdekében állítsa be a megadott előmásolási parancsfájl-logikát. Ha további segítségre van szüksége a parancsfájl fejlesztéséhez, forduljon az adatbázis-csapathoz.

- **"Átvitel – az első bájtig eltelt idő" hosszú ideig tartó munkavégzést**eredményezett: Ez azt jelenti, hogy a forrás lekérdezése sokáig tart, és az adatok visszaadása A lekérdezés vagy a kiszolgáló keresése és optimalizálása. Ha további segítségre van szüksége, vegye fel a kapcsolatot az adattár csapatával.

- **"A továbbítási lista forrása" hosszú munkaidőtartamot tapasztalt**: a forrásfájlok vagy a forrás-adatbázis adatpartícióinak számbavétele lassú.
  - Ha fájl-alapú forrásból másol adatokat, ha **helyettesítő szűrőt** használ a mappa elérési útján vagy a fájlnéven ( `wildcardFolderPath` vagy `wildcardFileName` ), vagy a **fájl utolsó módosításának időszűrőjét** ( `modifiedDatetimeStart` vagy) használja `modifiedDatetimeEnd` , vegye figyelembe, hogy az ilyen szűrő a másolási tevékenységet fogja eredményezni a megadott mappában lévő összes fájlnak a kliens oldalára való listázásakor, majd alkalmazza a szűrőt. Ilyenkor előfordulhat, hogy a fájl enumerálása különösen szűk keresztmetszetet jelenthet, ha csak a fájlok kis halmaza felel meg a szűrési szabálynak.

    - Győződjön meg arról, hogy [a fájlok másolását a DateTime partíciós fájl elérési útja vagy neve alapján](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)végezheti el. Ez a módszer nem jelent terhet a forrás oldal listázására.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, azaz az Amazon S3 és az Azure Blob**előtagját**. Az előtag-szűrő egy adattár-Kiszolgálóoldali szűrő, amely sokkal jobb teljesítményt nyújt.

    - Vegye figyelembe, hogy az egyetlen nagyméretű adathalmazt több kisebb adatkészletre osztja szét, és lehetővé teszi, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [több tárolóból származó fájlok másolását](solution-template-copy-files-multiple-containers.md) , vagy az [Amazon S3-ból származó adatok átimportálását](solution-template-migration-s3-azure.md) általános példaként ADLS Gen2 megoldási sablonokra.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forrásnál, vagy ha az adattár magas kihasználtságú állapotban van. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - Használja a Azure IR a forrás adattároló-régiójában lévő vagy azzal megegyezően.

- **"A forrástól való olvasás" hosszú munkaidőtartamot tapasztalt**: 

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az [Amazon vöröseltolódásról](connector-amazon-redshift.md)másol be Adatmásolást, konfigurálja a következőt: a vöröseltolódás eltávolításának használata.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forráson, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha a másolási minta támogatja a 4 adatintegrációs egységet (DIUs), tekintse meg [ezt a szakaszt](copy-activity-performance-features.md#data-integration-units) a részletek részben, és a jobb teljesítmény érdekében próbálkozzon a DIUs növelésével. 

    - Ellenkező esetben érdemes lehet egy nagyméretű adathalmazt több kisebb adatkészletbe bontani, és lehetővé kell tennie, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [fájlok több tárolóból való másolását](solution-template-copy-files-multiple-containers.md), az [Amazon S3-ból történő adatáttelepítést ADLS Gen2](solution-template-migration-s3-azure.md), vagy egy általános példaként egy vezérlőelem-tábla megoldási sablonjaival történő [tömeges másolást](solution-template-bulk-copy-with-control-table.md) .

  - Használja a Azure IR a forrás adattároló-régiójában lévő vagy azzal megegyezően.

- **"Átvitel – írás a fogadóba" – hosszú munkavégzés időtartama**:

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az [Azure szinapszis analyticsbe](connector-azure-sql-data-warehouse.md) (korábbi NEVÉN SQL DW) másol Adatmásolást, használja a Base vagy a copy utasítást. 

  - Ellenőrizze, hogy az ADF jelent-e szabályozási hibát a fogadón, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha a másolási minta támogatja a 4 adatintegrációs egységet (DIUs), tekintse meg [ezt a szakaszt](copy-activity-performance-features.md#data-integration-units) a részletek részben, és a jobb teljesítmény érdekében próbálkozzon a DIUs növelésével. 

    - Ellenkező esetben, fokozatosan hangolja a [párhuzamos másolatokat](copy-activity-performance-features.md), vegye figyelembe, hogy túl sok párhuzamos másolat is megsértheti a teljesítményt.

  - Használja Azure IR a fogadó adattároló-régiójának azonos vagy záró területére.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>A másolási tevékenység a saját üzemeltetésű integrációs modulban – problémamegoldás

Kövesse a [teljesítmény-hangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. 

Ha a másolási teljesítmény nem felel meg az elvárásoknak, a Azure Integration Runtimeon futó egypéldányos másolási tevékenységek [performance tuning tips](#performance-tuning-tips) hibáinak megoldása érdekében alkalmazza a javaslatot a másolás figyelése nézetben, és próbálkozzon újra. Ellenkező esetben olvassa el a [másolási tevékenység végrehajtásának részleteit](#understand-copy-activity-execution-details), tekintse meg a **leghosszabb** időtartamú szakaszt, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- A **"várólista" hosszú időtartamot észlelt:** az azt jelenti, hogy a másolási tevékenység hosszú ideig várakozik a várólistán, amíg a saját üzemeltetésű integrációs modulnak erőforrást nem kell végrehajtania. Tekintse át az IR-kapacitást és-használatot, és a számítási feladatok alapján [fel-vagy kibővítheti](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

- **"Átvitel – az első bájtig eltelt idő" hosszú ideig tartó munkavégzést**eredményezett: Ez azt jelenti, hogy a forrás lekérdezése sokáig tart, és az adatok visszaadása A lekérdezés vagy a kiszolgáló keresése és optimalizálása. Ha további segítségre van szüksége, vegye fel a kapcsolatot az adattár csapatával.

- **"A továbbítási lista forrása" hosszú munkaidőtartamot tapasztalt**: a forrásfájlok vagy a forrás-adatbázis adatpartícióinak számbavétele lassú.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a forrás-adattárhoz. Ha a forrás az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régióban, annál kevesebb a jobb.

  - Ha fájl-alapú forrásból másol adatokat, ha **helyettesítő szűrőt** használ a mappa elérési útján vagy a fájlnéven ( `wildcardFolderPath` vagy `wildcardFileName` ), vagy a **fájl utolsó módosításának időszűrőjét** ( `modifiedDatetimeStart` vagy) használja `modifiedDatetimeEnd` , vegye figyelembe, hogy az ilyen szűrő a másolási tevékenységet fogja eredményezni a megadott mappában lévő összes fájlnak a kliens oldalára való listázásakor, majd alkalmazza a szűrőt. Ilyenkor előfordulhat, hogy a fájl enumerálása különösen szűk keresztmetszetet jelenthet, ha csak a fájlok kis halmaza felel meg a szűrési szabálynak.

    - Győződjön meg arról, hogy [a fájlok másolását a DateTime partíciós fájl elérési útja vagy neve alapján](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)végezheti el. Ez a módszer nem jelent terhet a forrás oldal listázására.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, azaz az Amazon S3 és az Azure Blob**előtagját**. Az előtag-szűrő egy adattár-Kiszolgálóoldali szűrő, amely sokkal jobb teljesítményt nyújt.

    - Vegye figyelembe, hogy az egyetlen nagyméretű adathalmazt több kisebb adatkészletre osztja szét, és lehetővé teszi, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [több tárolóból származó fájlok másolását](solution-template-copy-files-multiple-containers.md) , vagy az [Amazon S3-ból származó adatok átimportálását](solution-template-migration-s3-azure.md) általános példaként ADLS Gen2 megoldási sablonokra.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forrásnál, vagy ha az adattár magas kihasználtságú állapotban van. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

- **"A forrástól való olvasás" hosszú munkaidőtartamot tapasztalt**: 

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a forrás-adattárhoz. Ha a forrás az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régiók számára, annál kevesebb a jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép rendelkezik-e elegendő bejövő sávszélességgel az adatok hatékony olvasásához és átviteléhez. Ha a forrásoldali adattár az Azure-ban található, az [eszköz](https://www.azurespeed.com/Azure/Download) használatával ellenőrizze a letöltési sebességet.

  - Tekintse meg a saját üzemeltetésű integrációs modul CPU-és memóriahasználat-trendjét a Azure Portal-> a > – áttekintés lapot. Ha a CPU-használat magas vagy kevés a rendelkezésre álló memória, vegye fontolóra a vertikális [felskálázást](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Például:

    - Az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), a [Teradata](connector-teradata.md#teradata-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source)és az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) adatainak másolása során az adatpartíciós beállítások lehetővé teszik az adatok párhuzamos másolását.

    - Az adatok [HDFS](connector-hdfs.md)való másolása esetén konfigurálja a DistCp használatát.

    - Az [Amazon vöröseltolódásból](connector-amazon-redshift.md)történő adatmásoláskor konfigurálja a következőt: a vöröseltolódás eltávolításának használata.

  - Ellenőrizze, hogy az ADF jelent-e szabályozási hibát a forráson, illetve hogy az adattár magas kihasználtságú-e. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha az adatok másolása a partícióról engedélyezett adattárakból, a [párhuzamos másolatok](copy-activity-performance-features.md)fokozatos finomhangolása érdekében vegye figyelembe, hogy túl sok párhuzamos másolat is megsértheti a teljesítményt.

    - Ellenkező esetben érdemes lehet egy nagyméretű adathalmazt több kisebb adatkészletbe bontani, és lehetővé kell tennie, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [fájlok több tárolóból való másolását](solution-template-copy-files-multiple-containers.md), az [Amazon S3-ból történő adatáttelepítést ADLS Gen2](solution-template-migration-s3-azure.md), vagy egy általános példaként egy vezérlőelem-tábla megoldási sablonjaival történő [tömeges másolást](solution-template-bulk-copy-with-control-table.md) .

- **"Átvitel – írás a fogadóba" – hosszú munkavégzés időtartama**:

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az [Azure szinapszis analyticsbe](connector-azure-sql-data-warehouse.md) (korábbi NEVÉN SQL DW) másol Adatmásolást, használja a Base vagy a copy utasítást. 

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a fogadó adattárhoz. Ha a fogadója az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régióban, annál kevesebb a jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép rendelkezik-e elegendő kimenő sávszélességgel az adatok hatékony átviteléhez és írásához. Ha a fogadó adattár az Azure-ban található, az [eszköz](https://www.azurespeed.com/Azure/UploadLargeFile) használatával ellenőrizze a feltöltési sebességet.

  - Ellenőrizze, hogy a saját üzemeltetésű integrációs modul CPU-és memóriahasználat-trendje Azure Portal-> a saját adatfeldolgozó-> – áttekintés lapot. Ha a CPU-használat magas vagy kevés a rendelkezésre álló memória, vegye fontolóra a vertikális [felskálázást](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - Ellenőrizze, hogy az ADF jelent-e szabályozási hibát a fogadón, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - Gondolja át, hogy fokozatosan hangolja a [párhuzamos másolatokat](copy-activity-performance-features.md), és ne feledje, hogy túl sok párhuzamos másolat is sérült a teljesítmény.

## <a name="other-references"></a>Egyéb referenciák

Az alábbiakban a támogatott adattárak némelyikének teljesítmény-figyelési és hangolási referenciái találhatók:

* Azure Blob Storage: a blob Storage-hoz szükséges [méretezhetőségi és teljesítményi célok](../storage/blobs/scalability-targets.md) , valamint a blob Storage-hoz kapcsolódó [teljesítmény-és méretezhetőségi ellenőrzőlista](../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: a táblázatos tároláshoz [szükséges méretezhetőségi](../storage/tables/scalability-targets.md) és teljesítményi célok, [valamint a Table Storage teljesítményére és méretezhetőségére vonatkozó ellenőrzőlista](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: nyomon követheti [a teljesítményt](../sql-database/sql-database-single-database-monitor.md) , és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos arányát.
* Azure SQL Data Warehouse: a képesség mérése adatraktár-egységekben (DWU) történik. Lásd: [a számítási teljesítmény kezelése Azure SQL Data Warehouseban (áttekintés)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Azure Cosmos db teljesítmény szintjei](../cosmos-db/performance-levels.md).
* SQL Server: [a teljesítmény figyelése és finomhangolása](https://msdn.microsoft.com/library/ms189081.aspx).
* Helyszíni fájlkiszolgáló: a [fájlkiszolgálók teljesítményének finomhangolása](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>További lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató](copy-activity-performance.md)
- [Másolási tevékenység teljesítményének optimalizálási funkciói](copy-activity-performance-features.md)
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
