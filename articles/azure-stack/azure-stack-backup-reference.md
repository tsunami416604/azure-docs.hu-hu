---
title: Azure verem infrastruktúra biztonsági mentési szolgáltatás referencia |} Microsoft Docs
description: Ez a cikk az Azure verem infrastruktúra Backup szolgáltatásra vonatkozó referenciaanyagot tartalmazza.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 608f3043e0e4b851820274ca743cbc44d1c8c0f1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075842"
---
# <a name="infrastructure-backup-service-reference"></a>Biztonsági mentési szolgáltatás útmutató infrastruktúrájához

## <a name="azure-backup-infrastructure"></a>Azure biztonságimásolat-infrastruktúra

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Infrastruktúra felület és Azure verem számos olyan szolgáltatás, a portál, Azure Resource Manager alkotó áll. A készülék hasonló kezelhetőséget Azure verem összpontosít csökkenti a megoldás az üzemeltető számára összetettségét.

Infrastruktúra biztonsági mentése az célja, hogy biztonsági másolatának összetettsége internalize, és infrastruktúra-szolgáltatásokat az adatok helyreállításához, biztosítva az operátorok összpontosíthatnak, a megoldás kezelését és karbantartását az SLA a felhasználóknak.

A biztonsági mentési adatok exportálása egy külső megosztás szükséges ne tárolja a biztonsági mentések ugyanazon a rendszeren. Egy külső megosztás igénylő a rendszergazda a rugalmasságot biztosít a meglévő vállalati BC/DR házirendek alapján adatokat tároló helyének meghatározásához. 

### <a name="infrastructure-backup-components"></a>Biztonsági mentés összetevőket

Infrastruktúra biztonsági másolat a következő összetevőket tartalmazza:

 - **Biztonsági mentés vezérlő infrastruktúra**  
 Az infrastruktúra biztonsági mentést vezérlő rendelkező létrejön, és minden Azure verem felhőben található.
 - **Biztonsági mentési erőforrás-szolgáltató**  
 A felhasználói felület és az alkalmazás program felületek (API) s Azure verem infrastruktúra alapszintű biztonsági mentési funkció teszi ki a biztonsági mentési erőforrás-szolgáltató (biztonsági mentés RP) állnak.

#### <a name="infrastructure-backup-controller"></a>Biztonsági mentés vezérlő infrastruktúra

Az infrastruktúra biztonsági mentést vezérlő a Service Fabric szolgáltatás lekérdezi létre egy Azure verem felhőben. Biztonsági mentési erőforrások jönnek létre, egy regionális szintű és rögzítési régióspecifikus szolgáltatás adatokat az Active Directory, a hitelesítésszolgáltató, Azure Resource Manager, a KSZT, az SRP, NRp-ben, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Biztonsági mentési erőforrás-szolgáltató

A biztonsági mentési erőforrás-szolgáltató felhasználói felülete a verem Azure-portálon alapszintű konfigurációs és a biztonsági mentési erőforrások listája mutatja be. Operátor a következő műveletek végezhetők a felhasználói felületen:

 - Biztonsági mentés engedélyezése először, adja meg a külső tárhelyen találhatók, a hitelesítő adatokat és a titkosítási kulcs
 - Befejeződött nézetet létrehozni létrehozása területen állapot erőforrásokat és biztonsági mentés
 - A tárolási hely, ahol a biztonsági mentés vezérlő helyezi biztonsági mentési adatok módosítása
 - Biztonsági mentés vezérlő külső tárolási helyen elérésére használt hitelesítő adatok módosítása
 - A titkosítási kulcs biztonsági mentést vezérlő használó biztonsági mentések titkosításához módosítása 


## <a name="backup-controller-requirements"></a>Biztonsági mentés vezérlő követelményeinek

Ez a szakasz ismerteti a fontos követelmények infrastruktúra biztonsági mentés. Javasoljuk, hogy tekintse át az információkat gondosan előtt biztonsági mentésének engedélyezése az Azure veremben példányra, majd hivatkozik vissza rá szükség szerint üzembe helyezési és a következő művelet során.

A követelmények a következők:

  - **Szoftverkövetelmények** – ismerteti a támogatott tárolási helyét és olvasható méretezési útmutató. 
  - **Hálózati követelmények** – ismerteti a hálózati követelmények eltérőek a tárolási helyekre.  

### <a name="software-requirements"></a>Szoftverkövetelmények

#### <a name="supported-storage-locations"></a>Támogatott tárolóhelyek

| Tárolási hely                                                                 | Részletek                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben tárolóeszközön üzemeltetett SMB-fájlmegosztásra | SMB-megosztás ugyanabban az adatközpontban Azure verem telepítési helyét, vagy egy ugyanabban az adatközpontban. Több Azure verem példányt használhatja ugyanazt a fájlmegosztást. |
| SMB-fájlmegosztás az Azure-on                                                          | Jelenleg nem támogatott.                                                                                                                                 |
| Az Azure BLOB-tároló                                                            | Jelenleg nem támogatott.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Támogatott SMB-verziók

| SMB | Verzió |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Tárolási hely méretezése 

Infrastruktúra biztonsági mentést vezérlő készít biztonsági másolatot adatok az igény szerinti. Az ajánljuk, hogy készítsen biztonsági másolatot legalább kétszer egy nap, és csak a biztonsági mentések nagyobb, mint hét nap. 

| Környezet skálázási | Biztonsági mentés tervezett mérete | A szükséges lemezterület teljes összeg |
|-------------------|--------------------------|--------------------------------|
| 4 – 12 csomópontok        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
| Tárolási hely                                                                 | Részletek                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben tárolóeszközön üzemeltetett SMB-fájlmegosztásra | 445-ös porton szükség, ha az Azure-verem példány helyezkedik el, a tűzfallal ellátott környezetben. Infrastruktúra biztonsági mentést vezérlő az SMB-fájlkiszolgáló egy kapcsolatot kezdeményez a 445-ös porton keresztül. |
| Fájl kiszolgáló teljes Tartományneve használatához a névnek a az EGP feloldhatónak kell lennie             |                                                                                                                                                                                         |

> [!Note]  
> Nincs bejövő portokat kell megnyitni.


## <a name="infrastructure-backup-limits"></a>Infrastruktúra biztonsági mentési korlátok

Ezek a korlátozások megfontolnia tervezése, üzembe helyezéséhez és üzemeltetéséhez a saját Microsoft Azure verem példányait. A következő táblázat ismerteti ezeket a határértékeket.

### <a name="infrastructure-backup-limits"></a>Infrastruktúra biztonsági korlátok
| Korlátazonosító                                                 | Korlát        | Megjegyzések                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentés típusa                                                      | Csak teljes    | Infrastruktúra biztonsági mentést vezérlő csak teljes biztonsági mentést támogatja. Nem támogatja a növekményes biztonsági mentést.                                          |
| Ütemezett biztonsági mentések                                                | Csak a manuális  | Biztonsági mentés vezérlő jelenleg csak támogatja az igény szerinti biztonsági mentések                                                                                 |
| Maximális párhuzamos biztonsági mentési feladatok                                   | 1            | Biztonsági mentés vezérlő-példányonként csak egy aktív biztonsági mentési feladat támogatott.                                                                  |
| Hálózati kapcsoló konfigurációja                                     | Nincs a hatókörben | Rendszergazda készítsen biztonsági másolatot a hálózati kapcsoló konfigurációs OEM eszközökkel. Tekintse meg az Azure-verem minden OEM gyártója által biztosított dokumentációt. |
| Hardver életciklus állomás                                          | Nincs a hatókörben | Rendszergazda készítsen biztonsági másolatot hardver életciklus állomás OEM eszközökkel. Tekintse meg az Azure-verem minden OEM gyártója által biztosított dokumentációt.      |
| Fájlmegosztások maximális száma                                    | 1            | Csak egy fájlmegosztást a biztonsági mentési adatok tárolására használható                                                                                        |
| App Service szolgáltatások, a függvény, a SQL, a mysql erőforrás-szolgáltató adatok biztonsági mentése | Nincs a hatókörben | Tekintse meg a közzétett központi telepítésére és felügyeletére érték-fel Microsoft által készített RPs útmutatást.                                                  |
| Biztonsági mentési külső erőforrás-szolgáltató                              | Nincs a hatókörben | Tekintse meg a központi telepítésére és felügyeletére érték-fel RPs külső szállítók által létrehozott közzétett útmutatást.                                          |

## <a name="next-steps"></a>További lépések

 - Az infrastruktúra biztonsági másolat szolgáltatással kapcsolatos további tudnivalókért lásd: [Azure verem és az infrastruktúra biztonsági mentési szolgáltatás biztonsági mentési és az adatok helyreállítását](azure-stack-backup-infrastructure-backup.md).