---
title: Az Azure SQL Data Warehouse – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk felsorolja az Azure SQL Data Warehouse – gyakori kérdések az ügyfelek és -fejlesztőket ki
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 7a570dfa6e0a2812a8dd3a25b8903c70fe07befc
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971703"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse gyakori kérdések

## <a name="general"></a>Általános kérdések

VÁLASZOK. Mi az SQL DW kínál a biztonsági adatok?

A. SQL dw-ben például a TDE-adatok védelme és a naplózás több megoldásokat kínál. További információkért lásd: [Biztonság].

VÁLASZOK. Hol találhatok ki, hogy milyen jogi vagy üzleti szabványok SQL DW megfelelő?

A. Látogasson el a [A Microsoft megfelelőségi] különböző megfelelőségi ajánlattal, például az SOC és ISO termék oldalán. Először válasszon megfelelőségi kilobájtban, majd bontsa ki az Azure a Microsoft releváns cloud services szakasz a jobb oldalon az oldal, hogy mely szolgáltatások az Azure-szolgáltatások rendszer megfelelő.

VÁLASZOK. A Power bi képes csatlakozni?

A. Igen! Bár a Power bi támogatja a közvetlen lekérdezés az SQL dw-vel, nem célja a felhasználók vagy a valós idejű adatok nagy számú. Üzemi használatra, a Power bi javasoljuk, a Power bi épülő Azure Analysis Services vagy az Analysis Service IaaS használatával. 

VÁLASZOK. Mik az SQL Data Warehouse kapacitás korlátai?

A. Tekintse meg az aktuális [kapacitáskorlátait] lapot. 

VÁLASZOK. Miért érdemes a méretezési csoport/szüneteltethet és folytathat tart sokáig?

A. Számos tényező befolyásolhatja a számítási műveletek időpontját. Egy közös használatieset-tranzakciós visszaállítása hosszú ideig futó műveletek van. Egy méretezési csoport vagy a szüneteltetési művelet elindításakor blokkolja az összes bejövő munkamenetek és lekérdezések vannak ürítve. Annak érdekében, hogy a rendszer stabil állapotban hagyásához tranzakciók kell őket állítani egy művelet megkezdése előtt. A nagyobb számának és nagyobb a napló méretét a tranzakciók, minél hosszabb a művelet lesz elakadt stabil állapotba állítja vissza a rendszer.

## <a name="user-support"></a>Felhasználó támogatása

VÁLASZOK. Van valamilyen funkcióra vonatkozó kérést, ahol küldje el azt?

A. Ha valamilyen funkcióra vonatkozó kérést, küldje el a az [UserVoice-on] lap

VÁLASZOK. Hogyan tehetem x?

A. Az SQL Data Warehouse fejlesztésébe segítségért a kérdéseit teheti fel az [Stack Overflow] lapot. 

VÁLASZOK. Hogyan küldhetek be támogatási jegy?

A. [Támogatási jegy megjelenítése] Azure-portálon keresztül lehet benyújtani.

## <a name="sql-languagefeature-support"></a>Az SQL nyelv/funkciók támogatása 

VÁLASZOK. Milyen adattípusokat támogatja az SQL Data Warehouse?

A. Tekintse meg az SQL Data Warehouse [adattípusok].

VÁLASZOK. Milyen tábla funkciókat támogatja?

A. Az SQL Data Warehouse számos funkciót támogatja, míg az egyes nem támogatottak, és vannak dokumentálva [A tábla nem támogatott funkciók].

## <a name="tooling-and-administration"></a>Eszközök és felügyelet

VÁLASZOK. Támogatott adatbázis-projekt a Visual Studióban.

A. Jelenleg nem támogatott adatbázis-projekt a Visual Studióban az SQL Data warehouse-hoz. Ha szeretné leadott szavazattal lekérni ezt a szolgáltatást, látogasson el a User Voice [Adatbázis-funkcióigénylés projektek].

VÁLASZOK. Támogatja az SQL Data Warehouse a REST API-kat?

A. Igen. A lehető legtöbb REST-funkció, amely használható az SQL Database az SQL Data Warehouse is érhető el. Belül dokumentációs oldalát érintő REST API-t információkat vagy [MSDN].


## <a name="loading"></a>Betöltés

VÁLASZOK. Milyen ügyfél illesztőprogramokat támogatja?

A. Illesztőprogram-támogatás a DW találhatók a [Kapcsolati sztringek] lap

Kérdés: milyen fájlformátumok az SQL Data Warehouse a PolyBase által támogatott?

V: Orc, RC, Parquet és egybesimított karakterrel elválasztott szöveg

K: milyen I kapcsolódhat a PolyBase az SQL DW? 

V: [Azure Data Lake Store] és [Azure Storage Blobs]

K: van számítási legördülő lista lehetséges, amikor csatlakozik az Azure Storage Blobból vagy az ADLS? 

V: nem, az SQL DW PolyBase csak a tároló-összetevők kommunikál. 

K: Csatlakozhatok HDI?

V: HDI ADLS vagy WASB használhat a HDFS-összetevővel. Ha pedig a HDFS-réteget, majd betöltheti az adatokat az SQL DW-be. Azonban nem hozható létre legördülő lista számítási a HDI-példányhoz. 

## <a name="next-steps"></a>További lépések
Teljes az SQL Data Warehouse további információkért lásd: a [Áttekintés] lapot.


<!-- Article references -->
[UserVoice-on]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Kapcsolati sztringek]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Támogatási jegy megjelenítése]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Biztonság]: ./sql-data-warehouse-overview-manage-security.md
[A Microsoft megfelelőségi]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitáskorlátait]: ./sql-data-warehouse-service-capacity-limits.md
[adattípusok]: ./sql-data-warehouse-tables-data-types.md
[A tábla nem támogatott funkciók]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Adatbázis-funkcióigénylés projektek]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Áttekintés]: ./sql-data-warehouse-overview-faq.md
