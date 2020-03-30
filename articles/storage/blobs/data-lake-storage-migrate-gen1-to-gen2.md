---
title: Az Azure Data Lake Storage áttelepítése Gen1-ről Gen2-re
description: Az Azure Data Lake Storage áttelepítése Gen1-ről Gen2-re.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533283"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Az Azure Data Lake Storage áttelepítése Gen1-ről Gen2-re

Áttelepítheti az adatokat, a számítási feladatokat és az alkalmazásokat a Data Lake Storage Gen1-ről a Data Lake Storage Gen2-re.

Az Azure Data Lake Storage Gen2 [az Azure Blob storage-ra](storage-blobs-introduction.md) épül, és a big data-elemzéshez szükséges képességeket biztosítja. [A Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) egyesíti [az Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)szolgáltatásait, például a fájlrendszer szemantikáját, a könyvtárat és a fájlszintű biztonságot és skálázást az Azure Blob [storage](storage-blobs-introduction.md)alacsony költségű, többszintű tárolásával, magas rendelkezésre állású/vész-helyreállítási képességekkel.

> [!NOTE]
> A könnyebb olvasás érdekében ez a cikk a *Gen1* kifejezést használja az Azure Data Lake Storage Gen1 kifejezésre, és a *Gen2* kifejezés az Azure Data Lake Storage Gen2 kifejezésre.

## <a name="recommended-approach"></a>Ajánlott megközelítés

A Gen2-re való áttelepítéshez a következő megközelítést javasoljuk.

:heavy_check_mark: 1. lépés: A készültség felmérése

:heavy_check_mark: 2. lépés: Felkészülés az áttelepítésre

:heavy_check_mark: 3. lépés: Adatok és alkalmazás-munkaterhelések áttelepítése

:heavy_check_mark: 4. lépés: Átvágás Gen1-ről Gen2-re

> [!NOTE]
> A Gen1 és a Gen2 különböző szolgáltatások, nincs helybeni frissítési élmény, szándékos áttelepítési erőfeszítés szükséges. 

### <a name="step-1-assess-readiness"></a>1. lépés: A készültség felmérése

1. További információ a [Data Lake Storage Gen2 ajánlatról;](https://azure.microsoft.com/services/storage/data-lake-storage/) ez előnyök, költségek, és az általános építészet. 

2. [Hasonlítsa össze a](#gen1-gen2-feature-comparison) Gen1 képességeit a Gen2 képességeivel. 

3. Tekintse át az [ismert problémák](data-lake-storage-known-issues.md) listáját a működési hiányosságok felméréséhez.

4. A Gen2 támogatja a Blob storage-funkciókat, például [a diagnosztikai naplózást,](../common/storage-analytics-logging.md) [a hozzáférési szinteket](storage-blob-storage-tiers.md)és a [Blob-tárolási életciklus-kezelési házirendeket.](storage-lifecycle-management-concepts.md) Ha érdekes a funkciók bármelyikének használata, tekintse át [a támogatás jelenlegi szintjét.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)

5. Tekintse át az [Azure-ökoszisztéma-támogatás](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) jelenlegi állapotát, és győződjön meg arról, hogy a Gen2 támogatja a megoldásoktól függő szolgáltatásokat.

### <a name="step-2-prepare-to-migrate"></a>2. lépés: Felkészülés az áttelepítésre

1. Azonosítsa az áttelepítendő adatkészleteket.

   Használja ki ezt az alkalmat, hogy megtisztítsa a már nem használt adatkészleteket. Hacsak nem tervezi az összes adat egyszerre való áttelepítését, A fázisokban áttelepíthető logikai adatcsoportok azonosításához szánjon időt.
   
2. Határozza meg, hogy az áttelepítés milyen hatással lesz a vállalkozásra.

   Fontolja meg például, hogy megengedheti-e magának az állásidőt az áttelepítés ideje alatt. Ezek a szempontok segíthetnek a megfelelő áttelepítési minta azonosításában és a legmegfelelőbb eszközök kiválasztásában.

3. Áttelepítési terv létrehozása. 

   Ezeket a [migrációs mintákat](#migration-patterns)ajánljuk. Választhat ezek közül a minták közül, kombinálhatja őket, vagy egyéni mintát tervezhet.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3. lépés: Adatok, munkaterhelések és alkalmazások áttelepítése

Adatok, munkaterhelések és alkalmazások áttelepítése a kívánt minta használatával. Azt javasoljuk, hogy a forgatókönyvek növekményes érvényesítése.

1. [Hozzon létre egy tárfiókot,](data-lake-storage-quickstart-create-account.md) és engedélyezze a hierarchikus névtér funkciót. 

2. Telepítse át az adatokat. 

3. Konfigurálja [a számítási feladatok ban lévő szolgáltatásokat](data-lake-storage-integrate-with-azure-services.md) úgy, hogy a Gen2 végpontra mutassanak. 
   
4. Frissítse az alkalmazásokat a Gen2 API-k használatára. Lásd: útmutatók [a .NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) és [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Parancsfájlok frissítése a Data Lake Storage Gen2 [PowerShell-parancsmagok](data-lake-storage-directory-file-acl-powershell.md)és [az Azure CLI-parancsok](data-lake-storage-directory-file-acl-cli.md)használatához.
   
6. Keresse meg a kódfájlokban, databricks-jegyzetfüzetekben, Apache Hive HQL-fájlokban vagy a számítási feladatok részeként használt bármely más fájlban lévő karakterláncot `adl://` tartalmazó URI-hivatkozásokat. Cserélje le ezeket a hivatkozásokat az új tárfiók [Gen2 formátumú](data-lake-storage-introduction-abfs-uri.md) URI-jára. Például: a Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` URI: válhat `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Konfigurálja úgy a fiók biztonságát, hogy az tartalmazza [a szerepköralapú hozzáférés-vezérlési (RBAC) szerepköröket,](../common/storage-auth-aad-rbac-portal.md) [a fájl- és mappaszintű biztonságot,](data-lake-storage-access-control.md)valamint az [Azure Storage tűzfalait és virtuális hálózatait.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4. lépés: Átvágás Gen1-ről Gen2-re

Miután biztos abban, hogy az alkalmazások és a munkaterhelések stabilak a Gen2-en, elkezdheti használni a Gen2-t az üzleti forgatókönyvek kielégítésére. Kapcsolja ki a Gen1-en futó többi folyamatot, és szerelje le a Gen1-fiókot. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2 képességek

Ez a táblázat a Gen1 képességeit hasonlítja össze a Gen2 képességeivel.

|Terület |1.   |Gen2 (1988– |
|---|---|---|
|Adatszervezés|[Hierarchikus névtér](data-lake-storage-namespace.md)<br>Fájl- és mappatámogatás|[Hierarchikus névtér](data-lake-storage-namespace.md)<br>Tároló, fájl és mappa támogatása |
|Georedundancia| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS,](../common/storage-redundancy.md#zone-redundant-storage) [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Hitelesítés|[AAD által felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Szolgáltatásnévmegtagok](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD által felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Szolgáltatásnévmegtagok](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Megosztott hozzáférési kulcs](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Engedélyezés|Menedzsment - [RBAC](../../role-based-access-control/overview.md)<br>Adatok – [ACL-ek](data-lake-storage-access-control.md)|Menedzsment – [RBAC](../../role-based-access-control/overview.md)<br>Adatok - [ACL-ek](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Titkosítás – Inaktív adatok|Szerveroldal – [Microsoft által felügyelt](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy ügyfél által [felügyelt](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kulcsokkal|Szerveroldal – [Microsoft által felügyelt](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy ügyfél által [felügyelt](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kulcsokkal|
|A VNET támogatása|[VNET-integráció](../../data-lake-store/data-lake-store-network-security.md)|[Szolgáltatásvégpontok](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [privát végpontok (nyilvános előzetes verzió)](../common/storage-private-endpoints.md)|
|Fejlesztői élmény|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md) (Nyilvános előzetes verzióban)|
|Diagnosztikai naplók|Klasszikus naplók<br>[Integrált Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klasszikus naplók](../common/storage-analytics-logging.md) (nyilvános előzetes verzióban)<br>Azure monitor integráció – idővonal TBD|
|Ökoszisztéma|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 vagy újabb)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 vagy újabb)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 a Gen2 minták

Válasszon egy áttelepítési mintát, majd szükség szerint módosítsa azt.

|||
|---|---|
|**Emelés és váltás**|A legegyszerűbb minta. Ideális, ha az adatfolyamatok megengedhetik maguknak az állásidőt.|
|**Növekményes másolat**|Hasonló a *lifthez és a váltáshoz,* de kevesebb állásidővel. Ideális nagy mennyiségű adat másolásához.|
|**Kettős csővezeték**|Ideális olyan csővezetékekhez, amelyek nem engedhetik meg maguknak az állásidőt.|
|**Kétirányú szinkronizálás**|Hasonló a *kettős csővezetékhez*, de egy szakaszosabb megközelítéssel, amely alkalmas a bonyolultabb csővezetékek számára.|

Vessünk egy közelebbi pillantást az egyes minták.
 
### <a name="lift-and-shift-pattern"></a>Emelési és váltási minta

Ez a legegyszerűbb minta.

1. Állítsa le az összes írást a Gen1-nek.

2. Adatok áthelyezése a Gen1-ről a Gen2-re. Az [Azure Data Factory használatát](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)javasoljuk. Az ACL-ok az adatokkal együtt másolnak.

3. A műveletek és a munkaterhelések a Gen2-be történő letöltési műveleteket és számítási feladatokat.

4. Gen1 leszerelés.

> [!div class="mx-imgBorder"]
> ![emelési és váltási minta](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>A lift és a shift minta használatának szempontjai

:heavy_check_mark: A Gen1-ről a Gen2-re történő átépítés az összes számítási feladathoz egyszerre.

:heavy_check_mark: Állásidő rekedésre az áttelepítés és az átállási időszak alatt.

:heavy_check_mark: Ideális olyan csővezetékekhez, amelyek megengedhetik maguknak az állásidőt, és az összes alkalmazás egyszerre frissíthető.

### <a name="incremental-copy-pattern"></a>Növekményes másolási minta

1. Kezdje meg az adatok áthelyezését a Gen1-ről a Gen2-re. Az [Azure Data Factory használatát](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)javasoljuk. Az ACL-ok az adatokkal együtt másolnak.

2. Új adatok fokozatos másolása a Gen1 programból.

3. Az összes adat másolása után állítsa le az összes írást a Gen1-be, és irányítsa a számítási feladatokat a Gen2-re.

4. Gen1 leszerelés.

> [!div class="mx-imgBorder"]
> ![Növekményes másolási minta](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>A növekményes másolási minta használatával kapcsolatos szempontok:

:heavy_check_mark: A Gen1-ről a Gen2-re történő átépítés az összes számítási feladathoz egyszerre.

:heavy_check_mark: Csak az átvágási időszakban várjon állásidőt.

:heavy_check_mark: Ideális olyan folyamatokhoz, ahol az összes alkalmazás egyszerre frissített, de az adatok másolása több időt igényel.

### <a name="dual-pipeline-pattern"></a>Kettős csővezeték-minta

1. Adatok áthelyezése a Gen1-ről a Gen2-re. Az [Azure Data Factory használatát](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)javasoljuk. Az ACL-ok az adatokkal együtt másolnak.

2. Új adatok betöltése a Gen1 és a Gen2 rendszerbe is.

3. Számítási feladatok pont a Gen2.

4. Állítsa le az összes írást a Gen1-nek, majd szerelje le a Gen1-et.

> [!div class="mx-imgBorder"]
> ![Kettős csővezeték-minta](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>A kettős csővezeték-minta használatával kapcsolatos szempontok:

:heavy_check_mark: A Gen1 és a Gen2 csővezetékek egymás mellett futnak.

:heavy_check_mark: Támogatja a nulla állásidőt.

:heavy_check_mark: Ideális olyan helyzetekben, amikor a számítási feladatok és alkalmazások nem engedhetik meg maguknak az állásidőt, és mindkét tárfiókba betöltést.

### <a name="bi-directional-sync-pattern"></a>Kétirányú szinkronizálási minta

1. Kétirányú replikáció beállítása a Gen1 és a Gen2 között. Javasoljuk [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Ez felajánl egy javítási funkció a meglévő adatokat.

3. Ha minden lépés befejeződött, állítsa le az összes írást a Gen1-be, és kapcsolja ki a kétirányú replikációt.

4. Gen1 leszerelés.

> [!div class="mx-imgBorder"]
> ![Kétirányú minta](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>A kétirányú szinkronizálási minta használatának szempontjai:

:heavy_check_mark: Ideális olyan összetett forgatókönyvekhez, amelyek nagyszámú folyamatot és függőséget foglalnak magukban, ahol a szakaszos megközelítésnek több értelme lehet.  

:heavy_check_mark: A migrációs erőfeszítések magasak, de egymás mellett támogatják a Gen1 és a Gen2 programot.

## <a name="next-steps"></a>További lépések

- Ismerje meg a tárfiók biztonságának beállításának különböző részeit. Lásd: [Azure Storage biztonsági útmutató.](../common/storage-security-guide.md)
- Optimalizálja a teljesítményt a Data Lake Store-hoz. Lásd: [Az Azure Data Lake Storage Gen2 optimalizálása a teljesítményért](data-lake-storage-performance-tuning-guidance.md)
- Tekintse át a Data Lake Áruház kezelésével kapcsolatos gyakorlati tanácsokat. Tekintse meg [az Azure Data Lake Storage Gen2 használatának gyakorlati tanácsait](data-lake-storage-best-practices.md)

