---
title: Az adatfolyamatok Azure Data Factory hibáinak megoldása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory az adatfolyamokkal kapcsolatos problémákat.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5cf4773ac781ae51a60ef7d987c3dc324c125d95
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387726"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Azure Data Factory adatfolyamatok hibáinak megoldása

Ez a cikk a Azure Data Factory adatforgalmának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Hibaüzenet: DF-SYS-01: árnyékolt. databricks. org. Apache. Hadoop. fs. Azure. AzureException: com. microsoft. Azure. Storage. StorageException: a megadott tároló nem létezik.

- **Tünetek**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása meghiúsul, mert a tároló nem létezik.

- **OK**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban

- **Megoldás**: Győződjön meg arról, hogy az adatkészletben hivatkozott tároló létezik

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Hibaüzenet: DF-SYS-01: Java. lang. AssertionError: az érvényesítés sikertelen volt: a rendszer ütköző címtár-struktúrákat észlelt. Gyanús elérési utak

- **Tünetek**: Ha helyettesítő karaktereket használ a forrás-átalakításhoz a Parquet-fájlokkal

- **OK**: helytelen vagy érvénytelen helyettesítő karakteres szintaxis

- **Megoldás**: a forrás-átalakítási beállításokban használt helyettesítő karakteres szintaxist tekintse meg

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Hibaüzenet: DF-SRC-002: "Container" (tároló neve) szükséges

- **Tünetek**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása meghiúsul, mert a tároló nem létezik.

- **OK**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban

- **Megoldás**: Győződjön meg arról, hogy az adatkészletben hivatkozott tároló létezik

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Hibaüzenet: DF-UNI-001: a PrimaryKeyValue nem kompatibilis típusú IntegerType és StringType rendelkezik

- **Tünetek**: az adatelőnézet, a hibakeresés és a folyamat adatforgalmának végrehajtása meghiúsul, mert a tároló nem létezik.

- **OK**: akkor történik, amikor helytelen elsődleges kulcs típusú adattípust próbál beszúrni az adatbázis-tárolóba

- **Megoldás**: használjon származtatott oszlopot az adatfolyamatban az elsődleges kulcshoz használt oszlopnak a céladatbázis adattípusának megfelelő értékre való továbbításához.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Hibaüzenet: DF-SYS-01: com. microsoft. SQLServer. JDBC. SQLServerException: a (z) 1433-es xxxxx.database.windows.net-porthoz tartozó TCP/IP-csatlakozás meghiúsult. Hiba: "xxxx.database.windows.net. Ellenőrizze a kapcsolatok tulajdonságait. Győződjön meg arról, hogy SQL Server fut a gazdagépen, és fogadja a TCP/IP-kapcsolatokat a porton. Győződjön meg arról, hogy a porthoz tartozó TCP-kapcsolatokat nem blokkolja tűzfal. "

- **Tünetek**: nem sikerült az adatelőnézet vagy a folyamat végrehajtása adatbázis-forrással vagy fogadóval

- **OK**: az adatbázist tűzfal védi

- **Megoldás**: Nyissa meg a tűzfal hozzáférését az adatbázishoz

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Hibaüzenet: DF-SYS-01: com. microsoft. SQLServer. JDBC. SQLServerException: már létezik "xxxxxx" nevű objektum az adatbázisban.

- **Tünetek**: a fogadó nem tud létrehozni táblázatot

- **OK**: már létezik egy meglévő táblanév a célként megadott adatbázisban a forrásban vagy az adatkészletben definiált névvel

- **Megoldás**: módosítsa a létrehozni kívánt tábla nevét

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató

1. Keresse meg az adatkészlet kapcsolatainak állapotát. Minden forrás-és fogadó-átalakításban keresse fel a társított szolgáltatást minden Ön által használt adatkészlet esetében, és tesztelje a kapcsolatokat.
2. Győződjön meg róla, hogy a fájl és a tábla kapcsolatainak állapota az adatfolyam-tervezőben található. Kapcsolja be a hibakeresést, és kattintson az adatelőnézetre a forrás-átalakításokban, és győződjön meg arról, hogy képes hozzáférni az adataihoz.
3. Ha minden jól látható az adatok előnézetében, ugorjon a folyamat-tervezőbe, és helyezze át az adatfolyamatot egy folyamat tevékenységbe. Egy végpontok közötti teszt folyamatának hibakeresése.

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Az ADF-leképezés adatforgalmának teljesítmény-útmutatója](concepts-data-flow-performance.md)
