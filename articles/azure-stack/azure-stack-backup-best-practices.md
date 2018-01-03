---
title: "Infrastruktúra biztonsági mentési szolgáltatás ajánlott eljárások Azure verem |} Microsoft Docs"
description: "Ajánlott eljárásokat követve amikor telepíti és kezeli az Azure-verem segítséget nyújtanak az adatvesztés végzetes hiba esetén az adatközpontban található."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2017
ms.author: mabrigg
ms.openlocfilehash: b9438f3bab92f40a5c79ce7b7a572195c182be45
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>Infrastruktúra biztonsági mentési szolgáltatás gyakorlati tanácsok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Gyakorlati tanácsok követheti, amikor telepíti és kezeli az Azure-verem súlyos meghibásodása az adatvesztéssel csökkentése érdekében az adatközpontban található.

Az ajánlott eljárások ellenőrzése, hogy a telepítés esetén még a következő megfelelőségi módosításai az művelet folyamat rendszeres időközönként tekintse át. Microsoft Support kell bármilyen problémát tapasztal az alábbi gyakorlati tanácsok végrehajtása közben, kérjen segítséget.

## <a name="configuration-best-practices"></a>Technológiának az ajánlott eljárásokkal

### <a name="deployment"></a>Környezet

Infrastruktúra biztonsági mentés engedélyezése minden Azure verem felhő központi telepítése után. Ütemezheti az üzemeltető felügyeleti API végpontra bármely ügyfél-kiszolgáló elérésével készített biztonsági másolat AzureStack eszközök használatával.

### <a name="networking"></a>Hálózat

Az univerzális elnevezési konvenció (UNC) szerinti karakterlánc az elérési utat egy teljesen minősített tartománynevét (FQDN) kell használnia. IP-cím akkor lehet név feloldása nem lehetséges. UNC-karakterláncnak erőforrások, például a megosztott fájlok vagy az eszközök helyét adja meg.

### <a name="encryption"></a>Titkosítás

A titkosítási kulcs, külső tárhelyen, lekérdezi exportált biztonsági mentési adatok titkosítására szolgál. A kulcs AzureStack-eszközök segítségével hozhatók létre. 

![AzureStack-eszközök](media\azure-stack-backup\azure-stack-backup-encryption1.png)

A kulcs (például nyilvános Azure Key Vault titkos kulcs) biztonságos helyen kell tárolni. Ez a kulcs újbóli üzembe helyezése Azure verem során kell használni. 

![A kulcsot biztonságos helyen tárolja.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Gyakorlati tanácsok az üzemeltetéshez

### <a name="backups"></a>Biztonsági másolatok

 - Infrastruktúra biztonsági mentést vezérlő kell az igény szerinti elindul. Javasoljuk, hogy a biztonsági mentés naponta legalább két alkalommal.
 - Biztonsági mentési feladatok végrehajtása közben a rendszer fut, így a felhasználói élmény mellett vagy felhasználó alkalmazások állásidő nélkül. Várja meg a biztonsági mentési feladatok olyan megoldás, amely nagy valószínűséggel terhelésnek van kitéve 20-40 percet vesz igénybe.
 - OEM megadott utasítás használatával, manuálisan mentési hálózati kapcsolók és a hardver életciklus-állomás (HLH) kell tárolni a ugyanazt biztonsági másolatokat tároló megosztáson, ahol a biztonsági mentés vezérlő infrastruktúra tárolók vezérlő sík biztonsági mentési adatokat. Fontolja meg a kapcsoló- és HLH konfigurációk a régió mappában tárolja. Ha több Azure verem ugyanabban a régióban, érdemes egy azonosítót az egyes konfigurációs, amely a méretezési egységhez tartozik.

### <a name="folder-names"></a>Mappanevek

 - Infrastruktúra MASBACKUP mappát automatikusan hozza létre. Ez a Microsoft által felügyelt fájlmegosztás. Létrehozhat megosztások MASBACKUP ugyanazon a szinten. Mappák és a tároló adatait, amely Azure verem nem hoz létre MASBACKUP belül nem ajánlott. 
 -  Felhasználói teljes tartománynév és a mappa nevében régió megkülönböztetni azokat a különböző felhők biztonsági mentési adatait. A teljesen minősített tartományneve (FQDN) az Azure Alkalmazásveremben üzembe és a végpontok a régió és a külső tartománynév paraméter kombinációjából áll. További információkért lásd: [Azure verem datacenter integrációs - DNS](azure-stack-integrate-dns.md).

Például a biztonsági másolatokat tároló megosztáson AzSBackups fileserver01.contoso.com üzemeltet. A fájlmegosztás lehet egy mappa létrehozása minden Azure Alkalmazásveremben üzembe a külső tartománynevet és egy almappát, az a régió nevét használja. 

Teljes tartománynév: contoso.com  
A régióban: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup mappában, ahol az Azure-verem tárolja a biztonsági mentési adatok. Ezt a mappát ne használjon a saját adatok tárolására. OEM vagy bármilyen biztonsági mentési adatok tárolására ne használja ezt a mappát. 

OEM javasolt, hogy az összetevők régió mappában lévő biztonsági mentési adatok tárolására. Minden hálózati kapcsolók, hardver életciklus állomás (HLH), és így tovább a saját almappájában kell tárolni. Példa:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Figyelés

Az alábbi riasztásokat a rendszer által támogatott:

| Riasztás                                                   | Leírás                                                                                     | Szervizelés                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentés nem sikerült, mert a fájlmegosztás nincs elég kapacitás | Fájlmegosztás kapacitás kívül esik, és a biztonsági mentés vezérlő nem tudja exportálni a biztonságimásolat-fájlok helyét. | Adja hozzá a további tárolási kapacitás, és készítsen biztonsági másolatot próbálkozzon újra. Szabadítson fel helyet a törlés meglévő biztonsági másolatok (először indítsa el a legrégebbi).                    |
| Biztonsági mentés csatlakozási problémák miatt nem sikerült.             | Hálózati verem Azure és a fájl között megosztás hibákat észlelt.                          | A hálózati probléma, és próbálkozzon újra a biztonsági mentés.                                                                                            |
| Biztonsági mentés az elérési út egy hiba miatt nem sikerült                | A fájlmegosztás útvonala nem oldható fel                                                          | Képezze le a megosztás egy másik számítógépen annak érdekében, hogy a megosztás érhető el. Szükség lehet frissíteni az elérési út, ha már nem érvényes.       |
| Biztonsági mentés hitelesítési probléma miatt nem sikerült               | A hitelesítő adatok problémát vagy egy hálózati hiba, amely hatással van a hitelesítési lehet.    | Képezze le a megosztás egy másik számítógépen annak érdekében, hogy a megosztás érhető el. Frissítse a hitelesítő adatokat, ha azok már nem érvényes szeretne. |
| Biztonsági mentés nem sikerült, mert egy általános hiba                    | A sikertelen kérelmek egy időszakos probléma okozhatja. Próbálkozzon újra készítsen biztonsági másolatot.                    | az ügyfélszolgálat                                                                                                                               |

## <a name="next-steps"></a>További lépések

 - Tekintse át a vonatkozó referenciaanyagot a [infrastruktúra biztonsági mentési szolgáltatás](azure-stack-backup-reference.md).  
 - Engedélyezze a [infrastruktúra a biztonsági mentési szolgáltatás](azure-stack-backup-enable-backup-console.md).