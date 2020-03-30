---
title: Az adatkezelési átjáró kibocsátási megjegyzései
description: Adatkezelési átjáró tory kiadási megjegyzések
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065002"
---
# <a name="release-notes-for-data-management-gateway"></a>Az adatkezelési átjáró kibocsátási megjegyzései
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [önkiszolgáló integrációs futásidejű v2-ben című témakört.](../create-self-hosted-integration-runtime.md)

A modern adatintegráció egyik kihívása az adatok áthelyezése a helyszíni és a felhőbe. A Data Factory ezt az integrációt az Adatkezelési átjáróval integrálja, amely egy olyan ügynök, amelyet a hibrid adatáthelyezés lehetővé tétele érdekében a helyszínen telepíthet.

Az adatkezelési átjáróval és annak használatával kapcsolatos részletes információkat az alábbi cikkekben talál:

*  [Adatkezelési átjáró](data-factory-data-management-gateway.md)
*  [Adatok áthelyezése a helyszíni és a felhőbeli adatok között az Azure Data Factory használatával](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>JELENLEGI VERZIÓ 
Mi nem több fenntartása release megjegyzések itt. A legújabb kiadási megjegyzések [itt szerezhetik](https://go.microsoft.com/fwlink/?linkid=853077) be




## <a name="earlier-versions"></a>Korábbi verziók
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Fejlesztések-
- Dns-bejegyzéseket adhat hozzá az engedélyezési szolgáltatásbuszhoz ahelyett, hogy a tűzfalról (ha szükséges) az összes Azure-IP-címet beszeretné késni. A megfelelő DNS-bejegyzést az Azure Portalon találja (Data Factory -> 'Author and Deploy' -> 'Gateways' -> "serviceUrls" (JSON-ban)
- A HDFS-összekötő mostantól támogatja az önaláírt nyilvános tanúsítványt azáltal, hogy kihagyja a TLS-érvényesítést.
- Javítva: Probléma az átjáróval kapcsolat nélküli módban a frissítés során (óradöntés miatt)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Fejlesztések-
-   Dns-bejegyzéseket adhat hozzá az engedélyezési listához, ahelyett, hogy a tűzfalról (ha szükséges) az összes Azure-IP-címet beiktatna. További részleteket itt talál.
-   Most már másolhat adatokat, vagy egyetlen blokk blob legfeljebb 4,75 TB, amely a blokk blob maximális támogatott mérete. (korábbi korlát 195 GB volt).
-   Javítva: Kevés a memória, miközben több kis fájlt kibontott a másolási tevékenység során.
-   Javítva: Index elhárította a tartományon kívüli problémát, miközben a DB dokumentumból egy helyszíni SQL Server kiszolgálóra másolt idempotencia-szolgáltatással.
-   Javítva: Az SQL-karbantartó parancsfájl nem működik a helyszíni SQL Server alkalmazással a Másolás varázslóból.
-   Rögzített: Oszlop neve szóköz a végén nem működik a másolási tevékenység.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Fejlesztések-
- Javítva: Probléma a hiányzó hitelesítő adatokkal az átjárószámítógép újraindítása során.
- Javítva: Hiba történt a regisztrációval kapcsolatban az átjáró biztonsági másolata segítségével történő visszaállítás során.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Fejlesztések-
- Javítva: A decimális null érték helytelen olvasása az Oracle-től forrásként.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Újdonságok
- Az ügyfelek visszajelzést adhatnak az átjárók regisztrálási élményéhez.
- Új tömörítési formátum támogatása: ZIP (Deflate)

### <a name="enhancements-"></a>Fejlesztések-
- Teljesítményjavulás az Oracle Sink, HDFS-forrás számára.
- Hibajavítás az átjáró automatikus frissítéséhez, az átjáró párhuzamos feldolgozási kapacitásához.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Erősítés
- Továbbfejlesztett és robusztusabb átjáróregisztrációs élmény– Most már nyomon követheti az előrehaladás állapotát az átjáró regisztrációs folyamata során, ami a regisztrációs élményt válaszkészebbé teszi.
- Az átjáró-visszaállítási folyamat javítása– Akkor is helyreállíthatja az átjárót, ha nem rendelkezik az átjáró biztonsági másolatfájljával a frissítéssel. Ehhez alaphelyzetbe kell állítania a csatolt szolgáltatás hitelesítő adatait a Portálon.
- Hibajavítás.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Újdonságok

- Most már helyileg is tárolhatja az adatforráshitelesítő adatokat. A hitelesítő adatok titkosítva vannak. Az adatforrás hitelesítő adatai visszaállíthatók és visszaállíthatók a meglévő átjáróból exportálható biztonságimásolat-fájllal, amely az összes helyszíni adatszolgáltató.

### <a name="enhancements-"></a>Fejlesztések-

- Továbbfejlesztett és robusztusabb átjáróregisztrációs élmény.
- Támogatja a Szövegformátum QuoteChar-konfigurációjának automatikus észlelését a másolás varázslóban, és javíthatja az általános formátumészlelési pontosságot.

## <a name="2361002"></a>2.3.6100.2

- Támogatja a firstRowAsHeader és SkipLineCount automatikus észlelést a másolás varázslóban a helyszíni fájlrendszerben és a HDFS fájlrendszerben lévő szövegfájlokhoz.
- Az átjáró és a Service Bus közötti hálózati kapcsolat stabilitásának javítása
- Néhány hibajavítás


## <a name="2260721"></a>2.2.6072.1

*  Támogatja az átjáró HTTP-proxyjának beállítását az átjáró konfigurációkezelőjével. Ha konfigurálva van, az Azure Blob, az Azure Table, az Azure Data Lake és a Document DB HTTP-proxyn keresztül érhetők el.
*  Támogatja a TextFormat fejléckezelését az Azure Blobból, az Azure Data Lake Store-ból, a helyszíni fájlrendszerből és a helyszíni HDFS-ből történő másoláskor.
*  Támogatja az adatok másolását a blob hozzáfűző és a lapblob, valamint a már támogatott blokk blob.
*  Új átjáróállapot **Online (Limited)**( alkalmazás ) bevezetése, amely azt jelzi, hogy az átjáró fő funkciói működnek, kivéve a Másolás varázsló interaktív művelettámogatását.
*  Javítja az átjáróregisztráció robusztusságát a regisztrációs kulcs használatával.

## <a name="216040"></a>2.1.6040.

*  A DB2 illesztőprogram most már szerepel az átjáró telepítőcsomagjában. így nem kell külön telepítenie.
*  A DB2 illesztőprogram mostantól támogatja a z/OS és a DB2 for i (AS/400) rendszert a már támogatott platformokkal (Linux, Unix és Windows).
*  Támogatja az Azure Cosmos DB használatát a helyszíni adattárak forrásaként vagy célállomásaként
*  Támogatja az adatok másolását/a hideg/meleg blob storage együtt a már támogatott általános célú tárfiók.
*  Lehetővé teszi, hogy távoli bejelentkezési jogosultságokkal rendelkező átjárón keresztül csatlakozzon a helyszíni SQL Server kiszolgálóhoz.  

## <a name="2060131"></a>2.0.6013.1

*  Kiválaszthatja az átjáró által a manuális telepítés során használandó nyelvet/kultúrát.

*  Ha az átjáró nem a várt módon működik, a probléma elhárításának megkönnyítése érdekében elküldheti az elmúlt hét nap átjárónaplóit a Microsoftnak. Ha az átjáró nem csatlakozik a felhőszolgáltatáshoz, választhatja az átjárónaplók mentését és archiválását.  

*  A felhasználói felület fejlesztései az átjárókonfiguráció-kezelőhöz:

    *  Az átjáró állapotának láthatóbbá tétele a Kezdőlap lapon.

    *  Átszervezett és egyszerűsített vezérlők.

    *  A [kódmentes másolóeszközzel](data-factory-copy-data-wizard-tutorial.md)adatokat másolhat egy tárolóból. A funkcióval kapcsolatos részletekért lásd a [Szakaszos másolás](data-factory-copy-activity-performance.md#staged-copy) című témakört.
*  Az Adatkezelési átjáró segítségével adatokat vihet be közvetlenül egy helyszíni SQL Server-adatbázisból az Azure Machine Learningbe.

*  Teljesítménnyel kapcsolatos fejlesztések

    * A Séma/előnézet SQL Server rendszeren való megtekintésének teljesítményének javítása kódmentes másolási eszközben.

## <a name="11259531"></a>1.12.5953.1

*  Hibajavítások

## <a name="11159181"></a>1.11.5918.1

*  Az átjáró eseménynaplójának maximális mérete 1 MB-ról 40 MB-ra nőtt.

*  Figyelmeztető párbeszédpanel jelenik meg arra az esetre, ha az átjáró automatikus frissítése során újra kell indítani. Választhat, hogy újraindul majd akkor vagy később.

*  Abban az esetben, ha az automatikus frissítés sikertelen, az átjárótelepítő háromszor próbálja újrafrissíteni az automatikus frissítést.

*  Teljesítménnyel kapcsolatos fejlesztések

    * A nagy méretű táblák helyszíni kiszolgálóról való betöltésének teljesítményének javítása kódmentes másolási forgatókönyv esetén.

*  Hibajavítások

## <a name="11058921"></a>1.10.5892.1

*  Teljesítménnyel kapcsolatos fejlesztések

*  Hibajavítások

## <a name="1958652"></a>1.9.5865.2

*  Érintésmentes automatikus frissítési képesség
*  Új tálcaikon átjáróállapot-jelzőkkel
*  Képesség a "Frissítés most" az ügyféltől
*  A frissítésütemezési idő beállításának lehetősége
*  PowerShell-parancsfájl az automatikus frissítés be- és kikapcsolására
*  A JSON formátum támogatása  
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

## <a name="1858221"></a>1.8.5822.1

*  A hibaelhárítási élmény javítása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757951"></a>1.7.5795.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757641"></a>1.7.5764.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1657351"></a>1.6.5735.1

*  Helyszíni HDFS-forrás/-fogadó támogatása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656961"></a>1.6.5696.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656761"></a>1.6.5676.1

*  Diagnosztikai eszközök támogatása a Configuration Manageren
*  Táblázatoszlopok támogatása táblázatos adatforrásokhoz az Azure Data Factory számára
*  Az SQL DW támogatása az Azure Data Factory-hoz
*  A BlobSource és a FileSource támogatása az Azure Data Factory-hoz
*  CopyBehavior támogatása – MergeFiles, PreserveHierarchy és FlattenHierarchy a BlobSink ben és a FileSink az Azure Data Factory bináris másolásával
*  Tevékenységmásolási jelentéskészítési folyamat támogatása az Azure Data Factory számára
*  Az Azure Data Factory adatforrás-kapcsolatának érvényesítése
*  Hibajavítások

### <a name="1656721"></a>1.6.5672.1

*  Az Azure Data Factory ODBC adatforrásának támogatási táblaneve
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656581"></a>1.6.5658.1

*  Fájlfogadó támogatása az Azure Data Factory számára
*  A hierarchia megőrzésének támogatása az Azure Data Factory bináris példányában
*  Az Azure Data Factory másolási tevékenységének támogatása
*  Hibajavítások

### <a name="1656401"></a>1.6.5640.1

*  További 3 adatforrás támogatása az Azure Data Factory hoz (ODBC, OData, HDFS)
*  Az Azure Data Factory ajánlati karakterének támogatása a csv-elemzőben
*  Tömörítéstámogatása (BZip2)
*  Hibajavítások

### <a name="1556121"></a>1.5.5612.1

*  Öt relációs adatbázis támogatása az Azure Data Factory számára (MySQL, PostgreSQL, DB2, Teradata és Sybase)
*  Tömörítési támogatás (Gzip és Deflate)
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1455491"></a>1.4.5549.1

*  Oracle adatforrás-támogatás hozzáadása az Azure Data Factory-hoz
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1454921"></a>1.4.5492.1

*  Egyesített bináris fájl, amely támogatja a Microsoft Azure Data Factory és az Office 365 Power BI-szolgáltatásokat is
*  A konfigurációs felhasználói felület és a regisztrációs folyamat finomítása
*  Azure Data Factory – Az Azure Ingress és egress támogatása az SQL Server adatforráshoz

### <a name="1253031"></a>1.2.5303.1

*  Javítsa ki az időtúlkiszolgáló problémát, hogy támogassa az időigényesebb adatforrás-kapcsolatokat.

### <a name="1155268"></a>1.1.5526.8

*  A telepítés előfeltétele a .NET Framework 4.5.1.-es rendszer szükséges.

### <a name="1051442"></a>1.0.5144.2

*  Nincs olyan módosítás, amely hatással van az Azure Data Factory-forgatókönyvek.
