---
title: Mi az a Delta-tó
description: A Delta-tó és annak az Azure Synapse Analytics részeként való működésének áttekintése
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429199"
---
# <a name="what-is-delta-lake"></a>Mi az a Delta-tó?

Az Azure Synapse Analytics kompatibilis a Linux Foundation Delta Lake szolgáltatással. A Delta Lake egy nyílt forráskódú tárolási réteg, amely acid (atomi, konzisztencia, elkülönítés és tartósság) tranzakciókat hoz az Apache Sparkés a big data számítási feladatokhoz.

## <a name="key-features"></a>A legfontosabb jellemzők

| Szolgáltatás | Leírás |
| --- | --- |
| **SAVAS tranzakciók** | Az adattavak at általában több folyamat és folyamat segítségével töltik fel, amelyek közül néhány olvasással egyidejűleg írja az adatokat. A Delta-tó előtt és a tranzakciók hozzáadása előtt az adatmérnököknek manuális hibaérzékeny folyamaton kellett átesnie az adatok integritásának biztosítása érdekében. A Delta Lake ismerős ACID tranzakciókat hoz az adattavakhoz. Szerializálást biztosít, a legerősebb szintű elkülönítési szintet. Tudjon meg többet a [Búvárkodás a Delta-tó: Kicsomagolás a tranzakciós napló](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Méretezhető metaadatok kezelése** | A big data-ban még maga a metaadatok is "big data" lehetnek. A Delta Lake ugyanúgy kezeli a metaadatokat, mint az adatokat, kihasználva a Spark elosztott feldolgozási erejét az összes metaadat kezeléséhez. Ennek eredményeképpen a Delta Lake képes kezelni a petabyte-méretű táblákat több milliárd partícióval és fájllal. |
| **Időutazás (adatverziószámozás)** | A tranzakciók egyik legfontosabb jellemzője az a képesség, hogy "visszavonja" a módosításokat, vagy visszatérjen egy korábbi verzióra. A Delta Lake pillanatképeket biztosít az adatokról, amelyek lehetővé teszik, hogy visszaállítsa az adatok korábbi verzióit auditok, visszaállítások vagy kísérletek reprodukálása érdekében. További [információ: Bemutatkozás Delta Lake Time Travel for Large Scale Data Lakes](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Formátum megnyitása** | Az Apache Parquet a Delta-tó alapformátuma, amely lehetővé teszi a formátumban őshonos hatékony tömörítési és kódolási sémák kihasználását. |
| **Egyesített kötegelt és streamelési forrás és -fogadó** | A Delta-tó ban lévő tábla kötegelő tábla, valamint egy streamelési forrás és a fogadó. Az adatok streamelése, a kötegelt korábbi háttérkitöltés és az interaktív lekérdezések csak úgy kidolgoznak a dobozból. |
| **Séma kényszerítése** | A sémakényszerítés segít biztosítani, hogy az adattípusok helyesek legyenek, és a szükséges oszlopok jelen vannak, megakadályozva, hogy a hibás adatok adatinkonzisztenciát okozzanak. További információ: [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Séma evolution** | A Delta Lake lehetővé teszi, hogy az automatikusan alkalmazható táblaséma módosításai az áttelepítési DDL írása nélkül is ellátható módosításokat hajtson végre. További információ: [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Naplózási előzmények** | A Delta Lake tranzakciós naplója rögzíti az adatok on-át végzett minden módosításának részleteit, amely a módosítások teljes naplózási nyomvonalát biztosítja. |
| **Frissítések és törlések** | A Delta Lake támogatja a Scala / Java / Python és SQL API-kat a különböző funkciókhoz. Az egyesítési, frissítési és törlési műveletek támogatása segít a megfelelőségi követelmények teljesítésében. További információ: [A Delta-tó bejelentése 0.4.0 kiadás](https://delta.io/news/delta-lake-0-4-0-released/) és [egyszerű, megbízható upserts és törlések a Delta Lake-táblák python API-k használatával,](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)amely tartalmazza a kódrészletek egyesítése, frissítése és törlése DML-parancsok. |
| **100%-ban kompatibilis az Apache Spark API-val** | A fejlesztők minimális módosítással használhatják a Delta Lake-et a meglévő adatfolyamataikkal, mivel teljes mértékben kompatibilisek a meglévő Spark-implementációkkal. |

A teljes dokumentációt a [Delta Lake dokumentációs oldalán találja.](https://docs.delta.io/latest/delta-intro.html)

További információ: [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>További lépések

- [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
