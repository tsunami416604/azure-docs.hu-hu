---
title: Az adatkezelési átjáró kibocsátási megjegyzései |} A Microsoft Docs
description: Data Management Gateway címe kibocsátási megjegyzései
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 865bfdae199bca7ebee888be527db239d34511d1
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017616"
---
# <a name="release-notes-for-data-management-gateway"></a>Az adatkezelési átjáró kibocsátási megjegyzései
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [saját üzemeltetésű integrációs modul a v2-ben](../create-self-hosted-integration-runtime.md).

A legnagyobb kihívás a modern Adatintegráció az adatok importálására és a helyszínről a felhőbe. A Data Factory lehetővé teszi, hogy ez az integráció az adatkezelési átjáró, amely egy ügynök, mint a helyszíni hibrid adatáthelyezés lehetővé tételével is telepíthető.

Az adatkezelési átjáró, és hogyan használható a kapcsolatos részletes információkat a következő cikkekben talál:

*  [Adatkezelési átjáró](data-factory-data-management-gateway.md)
*  [Adatok áthelyezése helyszíni között, és a felhőbeli Azure Data Factory használatával](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLIS VERZIÓ 
A Microsoft több karbantartása a kibocsátási megjegyzéseket itt. Legújabb kibocsátási megjegyzések lekérése [Itt](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Korábbi verziók
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Fejlesztések-
- Hozzáadhat DNS-bejegyzéseket engedélyezési helyett engedélyezze a service bus az összes Azure IP-címek a tűzfal a (ha szükséges). Az Azure Portalon is megtalálhatja a saját DNS-bejegyzés (Data Factory "Fejlesztés és üzembe helyezés" -> -> "Átjárók" a "szolgáltatási" (a JSON-) ->
- HDFS-összekötő mostantól támogatja az önaláírt nyilvános tanúsítvánnyal azáltal, hogy hagyja ki az SSL-érvényesítést.
- Kijavítva: Adja ki az átjáró offline (miatt torzulása formátum) frissítése közben.


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Fejlesztések-
-   Hozzáadhat DNS-bejegyzéseket az engedélyezési listára engedélyezési helyett a Service Bus az összes Azure IP-címek a tűzfal a (ha szükséges). Itt további részleteket.
-   Így és-tárolókról egyetlen adatok másolása blob 4,75 TB-ig, amely blokkblob maximális támogatott mérete. (a korábbi 195 GB volt).
-   Kijavítva: Elfogyott a memória probléma történt a kicsomagolás során több kis méretű fájlt a másolási tevékenység során.
-   Kijavítva: Egy helyszíni SQL Server idempotens szolgáltatással a dokumentum-adatbázis másolása közben az index tartományon kívül esik a probléma.
-   Kijavítva: SQL-karbantartási parancsprogramot a helyszíni SQL Server, a másolás varázsló nem működik.
-   Kijavítva: A másolási tevékenység nem működik a végén területtel rendelkező oszlop neve.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Fejlesztések-
- Kijavítva: Probléma hiányoznak a hitelesítő adatok, az átjáró gép újraindítására.
- Kijavítva: A regisztráció során átjáró probléma állítsa vissza a biztonságimásolat-fájl használatával.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Fejlesztések-
- Kijavítva: Helytelen olvasási forrásként Oracle decimális null érték.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Újdonságok
- Ügyfelek visszajelzést adhat az átjáró regisztrálása a felhasználói élményt.
- Egy új tömörítési formátumot támogatja: A ZIP (Deflate)

### <a name="enhancements-"></a>Fejlesztések-
- A teljesítmény javítása a fogadó Oracle, HDFS-forrás.
- Az átjáró automatikus hibajavítás frissíti, az átjáró párhuzamos feldolgozási kapacitást.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Fejlesztések
- Jobb és még robusztusabbá átjáró regisztrációs élmény – most átjáró regisztráció során, ami lehetővé teszi a regisztráció során dinamikusabb követheti a folyamat állapotát.
- Fokozása az átjáró visszaállítási folyamat -, de ilyenkor is helyreállíthatók az átjáró, akkor is, ha nem rendelkezik az átjáró biztonságimásolat-fájl ezzel a frissítéssel. Ehhez arra lenne szükség, hogy új társított szolgáltatás hitelesítő adatai a portálon.
- Hibajavítás.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Újdonságok

- Most már az adatforrás hitelesítő adatainak helyileg is tárolhatja. A hitelesítő adatok titkosítása. Az adatforrás hitelesítő adatainak helyreállíthatók legyenek, és vissza is exportálhatók, a meglévő átjárót, az összes helyi biztonságimásolat-fájl használatával.

### <a name="enhancements-"></a>Fejlesztések-

- Jobb és még robusztusabbá átjáró alkalmazásregisztrációs folyamatot.
- Az automatikus észlelés QuoteChar konfiguráció támogatása szöveges formátumban, a másolás varázsló, és általános formátuma észlelési pontosságának.

## <a name="2361002"></a>2.3.6100.2

- A helyi fájlrendszer és a HDFS szöveges fájlok másolása varázsló támogatja a firstRowAsHeader és a SkipLineCount automatikus észlelése.
- Átjáró és a Service Bus közötti hálózati kapcsolat stabilitásának növelése
- Néhány hibajavítások


## <a name="2260721"></a>2.2.6072.1

*  HTTP-proxyt az átjáró a Gateway Configuration Managerrel beállítást támogatja. Ha konfigurálva, az Azure Blob, az Azure Table, az Azure Data Lake és dokumentum-adatbázis HTTP-proxyn keresztül érhetők el.
*  Támogatja a fejléc kezelése a textformat használatát bemutató, ha másol adatokat, és az Azure Blobba, az Azure Data Lake Store, a helyi fájlrendszer és a helyszíni HDFS.
*  Az adatok másolása a hozzáfűző Blob és a már támogatott a Blokkblobok és Lapblobok támogatja.
*  Bemutatja egy új átjáró állapota **Online (korlátozott)**, ami azt jelenti, hogy az átjáró fő funkcióit működik, kivéve az interaktív művelet támogatása a másolás varázsló.
*  Fokozza a megbízhatóságát, az átjáró regisztrálása regisztrációs kulcsát használja.

## <a name="216040"></a>2.1.6040.

*  DB2-illesztőprogram most már az átjáró telepítési csomag tartalmazza. így nem kell külön telepítenie.
*  DB2-illesztőprogram mostantól támogatja a – z/OS- és DB2 for i (AS / 400) együtt a már támogatott platformokhoz (Linux, Unix és Windows).
*  Azure Cosmos DB használatával a forrás vagy cél helyszíni adattárakban támogatja
*  Adatok másolása Azure blobból vagy ritka elérésű/ritkáról gyakori elérésű támogatja a blob storage már támogatott általános célú tárfiókkal együtt.
*  Lehetővé teszi, hogy a helyszíni SQL Server távoli bejelentkezési jogokkal átjárón keresztül kapcsolódhat.  

## <a name="2060131"></a>2.0.6013.1

*  Kiválaszthatja a nyelvet/kulturális környezetet manuális telepítés során az átjáró által használandó.

*  Ha az átjáró nem működnek megfelelően, válassza ki átjáró naplók az elmúlt hét nap küldeni a Microsoftnak a probléma a hibaelhárítás megkönnyítése érdekében. Ha az átjáró nincs csatlakoztatva a felhőszolgáltatáshoz, lehet váltani, mentse, és az átjáró naplói archiválása.  

*  Felhasználói felület fejlesztései a gateway configuration manager:

    *  Átjáró állapota több láthatóvá tegye a kezdőlapon.

    *  Átszervezett és egyszerűsített felügyelettel.

    *  Adatok másolása a storage használatával a [kódmentes fájlmásoló eszközt](data-factory-copy-data-wizard-tutorial.md). Lásd: [szakaszos Másolás](data-factory-copy-activity-performance.md#staged-copy) részleteit, ez a funkció általános.
*  Az adatkezelési átjáró bejövő adatok közvetlenül a egy helyszíni SQL Server-adatbázisból az Azure Machine Learningbe is használhatja.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Séma és az előzetes verzió az SQL Server megtekintése a kódmentes copy eszköz a teljesítmény javítása.

## <a name="11259531"></a>1.12.5953.1

*  Hibajavítások

## <a name="11159181"></a>1.11.5918.1

*  Az átjáró eseménynapló maximális mérete 40 MB-ra nőtt az 1 MB.

*  Egy figyelmeztető párbeszédpanel jelenik meg abban az esetben, ha újraindításra van szükség, átjáró automatikus frissítése során. Ha szeretné, indítsa újra közvetlenül ezután vagy újabb.

*  Abban az esetben az automatikus frissítés nem sikerül, az átjáró telepítőjének újrapróbálkozik háromszor a maximális automatikus frissítése.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Javíthatja a teljesítményt nagy táblák betöltése kódmentes másolása a forgatókönyvben a helyi kiszolgálóról.

*  Hibajavítások

## <a name="11058921"></a>1.10.5892.1

*  Teljesítménnyel kapcsolatos fejlesztések

*  Hibajavítások

## <a name="1958652"></a>1.9.5865.2

*  Nulla touch automatikus frissítési funkció
*  Az átjáró Állapotjelzők új tálcai ikonja
*  Az ügyfél "Frissítés most" lehetőség
*  Lehetővé teszi a frissítési ütemezés idő beállítása
*  PowerShell-szkript és kikapcsolni az automatikus frissítés be-vagy kikapcsolása
*  JSON-formátum támogatása  
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

## <a name="1858221"></a>1.8.5822.1

*  Hibaelhárítási élmény javítása érdekében
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

*  A Configuration Manager támogatja a diagnosztikai eszközök
*  A táblázat oszlopaihoz támogatja a táblázatos adatforrások esetén az Azure Data Factoryhoz
*  Az Azure Data Factory az SQL DW támogatása
*  Az Azure Data Factory a BlobSource és Fájlforrásba Reclusive támogatása
*  A CopyBehavior – MergeFiles PreserveHierarchy és FlattenHierarchy BlobSink és bináris másolással FileSink támogatása az Azure Data Factory
*  Jelentéskészítés folyamatban van az Azure Data Factory másolási tevékenység támogatásához
*  Az Azure Data Factory támogatási adatok forrás kapcsolat érvényesítése
*  Hibajavítások

### <a name="1656721"></a>1.6.5672.1

*  Táblanév ODBC-adatforrás támogatja az Azure Data Factoryhoz
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656581"></a>1.6.5658.1

*  Az Azure Data Factory fogadó támogatási fájl
*  A bináris másolat megőrző hierarchia támogatása az Azure Data Factory
*  Az Azure Data Factory másolási tevékenység Idempotens támogatása
*  Hibajavítások

### <a name="1656401"></a>1.6.5640.1

*  3 további adatforrások támogatása az Azure Data Factory (ODBC, OData, HDFS)
*  Az Azure Data Factoryhoz idézőjel karaktert, a csv-elemző támogatása
*  A tömörítés támogatása (BZip2)
*  Hibajavítások

### <a name="1556121"></a>1.5.5612.1

*  Öt relációs adatbázisok támogatása az Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata és Sybase)
*  (A Gzip és a Deflate) tömörítési támogatása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1455491"></a>1.4.5549.1

*  Adja hozzá az Oracle adatforrás-támogatást az Azure Data Factoryhoz
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1454921"></a>1.4.5492.1

*  Egyesített bináris fájlt, amely támogatja a Microsoft Azure Data Factory és az Office 365 Power BI szolgáltatást
*  A konfigurációs felhasználói Felületet és a regisztrációs folyamat finomítása
*  Az Azure Data Factory – Azure bejövő és kimenő támogatása az SQL Server-adatforrás

### <a name="1253031"></a>1.2.5303.1

*  Időtúllépés problémamegoldáshoz további időigényes adatforrás-kapcsolatok támogatásához.

### <a name="1155268"></a>1.1.5526.8

*  .NET-keretrendszer 4.5.1-es verzióját előfeltételként szükséges a telepítés során.

### <a name="1051442"></a>1.0.5144.2

*  Nincs változás, amely hatással vannak az Azure Data Factory-forgatókönyvekhez.
