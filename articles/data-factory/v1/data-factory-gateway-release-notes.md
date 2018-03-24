---
title: Az adatkezelési átjáró kibocsátási megjegyzései |} Microsoft Docs
description: Adatok adatkezelési átjáró címe kibocsátási megjegyzései
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 7db5795132f5ca61c65a3cbd2caec1bccbf9a080
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-data-management-gateway"></a>Az adatkezelési átjáró kibocsátási megjegyzései
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [önállóan üzemel a V2 integrációs futásidejű](../create-self-hosted-integration-runtime.md).

A modern Adatintegráció kapcsolatban felmerülő kihívások egyik áthelyezni az adatokat a helyszíni felhőbe érkező vagy oda irányuló. Adat-előállító lehetővé teszi, hogy ez az integráció az adatkezelési átjáró, amely egy olyan ügynök, hogy a helyszíni hibrid adatátvitel engedélyezése telepítheti.

Az adatkezelési átjáró, és hogy miképpen lehet vele kapcsolatos részletes információkat a következő cikkekben talál:

*  [Adatkezelési átjáró](data-factory-data-management-gateway.md)
*  [Adatok áthelyezése között a helyszíni és felhőalapú Azure Data Factory használatával](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLIS VERZIÓ 
Jelenleg nincs több karbantartása, a kibocsátási megjegyzéseket itt. Első a legújabb kibocsátási megjegyzések [Itt](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Korábbi verziói
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Továbbfejlesztett-
- Is hozzáadhat DNS-bejegyzések engedélyezése helyett engedélyezési lista a service bus összes Azure IP-címet a tűzfal (ha szükséges). Azure-portál is megtalálhatja a megfelelő DNS-bejegyzés (Data Factory -> "Fejlesztés és üzembe helyezés" -> "Átjárók" -> "serviceUrls" (a JSON-ban)
- HDFS-összekötő mostantól támogatja a nyilvános önaláírt tanúsítványt azáltal, hogy SSL-ellenőrzésének kihagyására.
- Rögzített: Problémát átjáró kapcsolat nélküli (miatt az óra eltérésére) frissítése közben.


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Továbbfejlesztett-
-   Is hozzáadhat DNS-bejegyzések az engedélyezett engedélyezése helyett a Service Bus összes Azure IP-címet a tűzfal (ha szükséges). Itt további részleteket.
-   Adatok másolása egyetlen blokkot és a blob akár 4.75 TB-ig, amely az a maximális támogatott fájlméret blokkblob most is. (a korábbi korlát 195 GB volt).
-   Rögzített: Kívüli memória probléma során több kisebb fájlt kicsomagolás másolási tevékenység során.
-   Rögzített: Indexe kívül tartomány probléma másolása egy helyi SQL Server idempotencia szolgáltatással Document DB rendszerbe.
-   Rögzített: SQL-karbantartási parancsprogramot nem működik a helyszíni SQL Server a másolása varázsló segítségével.
-   Rögzített: Végén területtel rendelkező oszlopnév nem működik a másolási tevékenység.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Továbbfejlesztett-
- Rögzített: Probléma átjáró újraindítás hitelesítő adatok hiányoznak.
- Rögzített: Regisztráció során átjáró problémát állítsa vissza egy biztonságimásolat-fájl használatával.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Továbbfejlesztett-
- Rögzített: Oracle forrásaként null értéke helytelen olvasható.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Újdonságok
- Az ügyfelek visszajelzést adhat az átjáró regisztrálása a felhasználói élmény.
- Egy új tömörítési formátum támogatja: ZIP (Deflate)

### <a name="enhancements-"></a>Továbbfejlesztett-
- A teljesítmény javítása a Oracle gyűjtése, HDFS forrás.
- Az átjáró automatikus hibajavítás frissítéséhez átjáró párhuzamos feldolgozási kapacitás.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Fejlesztések
- Hatékonyabb és megbízhatóbb átjáró regisztrációs élmény-most átjáró regisztráció során, így gyorsabban tapasztalhat a regisztrációs folyamat állapota tudja követni.
- Növekedés az átjáró visszaállítása folyamat -, de ilyenkor is helyreállíthatók az átjáró, még akkor is, ha nem rendelkezik az átjáró biztonságimásolat-fájl ezzel a frissítéssel. Ehhez szükség lenne állíthatja vissza a társított szolgáltatás hitelesítő adatait a portálon.
- Hiba a javítás.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Újdonságok

- Most már az adatforrás hitelesítő adatainak helyileg tárolhatja. Az azonosító adatok titkosítása. Az adatforrás hitelesítő adatai állíthatók helyre, és a biztonságimásolat-fájl, amely a jelenlegi átjárót, az összes helyszíni exportálható segítségével visszaállítani.

### <a name="enhancements-"></a>Továbbfejlesztett-

- Hatékonyabb és megbízhatóbb átjáró regisztrációs felületet.
- Az automatikus észlelés quotechar tulajdonság-konfiguráció támogatása másolása varázsló a szöveges formátumú, és átfogó formátum észlelési pontosságának javítása.

## <a name="2361002"></a>2.3.6100.2

- Támogatja a helyszíni fájlrendszer és a HDFS firstRowAsHeader és SkipLineCount az automatikus észlelés szövegfájlok másolása varázsló.
- A hálózati kapcsolatot az átjáró és a Service Bus stabilitásának javítása
- Néhány hibajavítások


## <a name="2260721"></a>2.2.6072.1

*  Az átjáró konfigurációkezelőjének használatával az átjáró HTTP-proxy beállítást támogatja. Ha konfigurálva, Azure Blob, az Azure Table, az Azure Data Lake és a Document DB rendszerbe HTTP-proxyn keresztül érhetők el.
*  Támogatja a fejléc kezelése a szöveges, /, az Azure Blob, az Azure Data Lake Store, az adatok másolásakor helyszíni fájlrendszer, és a helyszíni HDFS.
*  Az adatok másolása hozzáfűző Blob és a már támogatott Blokkblob együtt az oldalakra vonatkozó Blob támogatja.
*  Bevezet egy új az átjáró állapotának **Online (korlátozott)**, ami azt jelenti, hogy működik-e az átjáró fő funkcióit kivéve másolása varázsló interaktív művelet támogatása.
*  Fokozza a megbízhatóságát, az átjáró regisztrálása regisztrációs kulccsal.

## <a name="216040"></a>2.1.6040.

*  DB2 illesztőprogram most már szerepel az átjárótelepítési csomag. Nem kell külön telepíteni.
*  DB2-illesztőprogram mostantól támogatja a z/OS- és a DB2 i (AS / 400) együtt a már támogatott platformon (Linux, Unix és a Windows).
*  A cél- és a helyszíni adattárolókhoz Azure Cosmos DB használatával támogatja
*  Támogatja az adatok másolásának/cold/használt adatok rétegére való blob-tároló már támogatott általános célú tárfiók együtt.
*  Lehetővé teszi a helyszíni SQL Server távoli bejelentkezési jogokkal átjárón keresztül csatlakozni.  

## <a name="2060131"></a>2.0.6013.1

*  Kiválaszthatja a nyelvet/kulturális környezet manuális telepítése során egy átjáró által használt.

*  Ha az átjáró nem várt módon működik, dönthet úgy, az elmúlt hét napban átjáró naplókat küldeni a Microsoftnak a problémát a hibaelhárítás elősegítése érdekében. Ha az átjáró nem csatlakozik a felhőszolgáltatáshoz, dönthet úgy, mentse, és átjáró naplók archiválása.  

*  Felhasználói felület fejlesztései az átjáró a configuration manager:

    *  Adja meg az átjáró állapotának több látható a kezdőlap.

    *  Átszervezett és egyszerűsített szabályozza.

    *  Adatok másolása egy tárolási használatával a [kódmentes másolási preview eszköz](data-factory-copy-data-wizard-tutorial.md). Lásd: [előkészített másolási](data-factory-copy-activity-performance.md#staged-copy) részleteiről a szolgáltatás általában.
*  Az adatkezelési átjáró érkező adatokat a helyszíni SQL Server adatbázis-ről Azure Machine Learning is használhatja.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Séma/Preview SQL-kiszolgálón a kódmentes másolási preview eszköz megjelenítéséről teljesítmény javításához.

## <a name="11259531"></a>1.12.5953.1

*  Hibajavítások

## <a name="11159181"></a>1.11.5918.1

*  Az adatátjáró eseménynaplójában maximális mérete 1 MB, 40 MB-ra nőtt.

*  Egy figyelmeztető párbeszédpanel jelenik meg, abban az esetben, ha újraindítás szükséges átjáró automatikus frissítés során. Ha szeretné, indítsa újra a jobb oldali majd vagy újabb.

*  Abban az esetben, ha az automatikus frissítés nem sikerül, a gateway installer háromszor a maximális automatikus frissítési újrapróbálkozik.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Nagy táblák tölt be a helyi kiszolgálóról a kódmentes másolásának esetéhez teljesítményének javítása.

*  Hibajavítások

## <a name="11058921"></a>1.10.5892.1

*  Teljesítménnyel kapcsolatos fejlesztések

*  Hibajavítások

## <a name="1958652"></a>1.9.5865.2

*  Nulla touch automatikus frissítési funkció
*  Az átjáró állapotának mutatók új tálcaikon
*  Az ügyfél képes "Frissítés most"
*  Frissítés időpontjának ütemezése meg
*  PowerShell-parancsfájl az elvégezte az automatikus frissítés be-és kikapcsolása
*  JSON-formátum támogatása  
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

## <a name="1858221"></a>1.8.5822.1

*  Hibaelhárítási élmény javítása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757951"></a>1.7.5795.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757641"></a>1.7.5764.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1657351"></a>1.6.5735.1

*  Támogatja a helyszíni HDFS forrás/fogadó
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656961"></a>1.6.5696.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656761"></a>1.6.5676.1

*  Diagnosztikai eszközök a Configuration Manager támogatására
*  Az Azure Data Factory táblázatos adatforrások táblaoszlopok támogatása
*  Az Azure Data Factory SQL DW támogatása
*  Az Azure Data Factory Reclusive BlobSource és FileSource támogatása
*  BlobSink és FileSink bináris másolatot az Azure Data Factory CopyBehavior – mergefiles típusú PreserveHierarchy és FlattenHierarchy támogatása
*  Támogatja a másolási tevékenység során az Azure Data Factory jelentési
*  Az Azure Data Factory támogatási adatok forrás kapcsolat érvényesítése
*  Hibajavítások

### <a name="1656721"></a>1.6.5672.1

*  Az ODBC-adatforrás az Azure Data Factory támogatási tábla neve
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656581"></a>1.6.5658.1

*  Támogatási fájlt az Azure Data Factory gyűjtése
*  Hierarchia megőrzése bináris másolása az Azure Data Factory támogatási
*  Az Azure Data Factory támogatja a másolási tevékenység idempotencia
*  Hibajavítások

### <a name="1656401"></a>1.6.5640.1

*  3 további adatforrások támogatja az Azure Data Factory (ODBC, OData, HDFS)
*  Az Azure Data Factory idézőjel, a fürt megosztott kötetei szolgáltatás elemző támogatása
*  Tömörítés támogatása (BZip2)
*  Hibajavítások

### <a name="1556121"></a>1.5.5612.1

*  Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata és Sybase) öt relációs adatbázisok támogatása
*  Tömörítés támogatása (Gzip és a Deflate)
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1455491"></a>1.4.5549.1

*  Az Azure Data Factory Oracle data forrás támogatását
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1454921"></a>1.4.5492.1

*  Egyesített bináris, amely támogatja a Microsoft Azure Data Factory és az Office 365 Power BI szolgáltatásokat
*  Pontosítsa a konfigurációs UI felületen és a regisztrációs folyamat
*  Az Azure Data Factory – Azure bemenő és kimenő támogatja az SQL Server-adatforrás

### <a name="1253031"></a>1.2.5303.1

*  További időigényes adatforrás-kapcsolatok támogatásához időtúllépés problémának a megoldásához.

### <a name="1155268"></a>1.1.5526.8

*  .NET-keretrendszer 4.5.1 előfeltételként igényel a telepítés során.

### <a name="1051442"></a>1.0.5144.2

*  Nincs változás, amelyek hatással vannak az Azure Data Factory forgatókönyvek.
