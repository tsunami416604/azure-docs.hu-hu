---
title: Az Azure Stack infrastruktúrájának biztonsági mentési szolgáltatás leírása |} A Microsoft Docs
description: Ez a cikk az Azure Stack infrastruktúra Backup szolgáltatás – referenciaanyag tartalmazza.
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
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242903"
---
# <a name="infrastructure-backup-service-reference"></a>Infrastruktúra biztonsági mentési szolgáltatás leírása

## <a name="azure-backup-infrastructure"></a>Az Azure biztonsági mentési infrastruktúrára

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Infrastruktúra-felügyeleti környezetet és az Azure Stack számos olyan szolgáltatás, a portál, az Azure Resource Manager alkotó áll. A megoldás az üzemeltető számára elérhetővé tett egyszerűsíteni a berendezés-szerű felügyelet teljes mértékben az Azure Stack összpontosít.

Infrastruktúra biztonsági mentését az célja, hogy a biztonsági másolat készítése összetettsége internalize, és az infrastruktúra-szolgáltatások adatainak visszaállítása, biztosítva az operátorok összpontosíthat a megoldás kezelésével és karbantartásával SLA-t, a felhasználók számára.

Ne tárolja a biztonsági mentések ugyanazon a rendszeren a biztonsági mentési adatok exportálása egy külső megosztáshoz szükséges. Egy külső megosztás igénylő rugalmasságot biztosít a rendszergazda a meglévő vállalati BC/DR szabályzatok alapján az adatok tárolási helyének meghatározásához. 

### <a name="infrastructure-backup-components"></a>Biztonsági mentési infrastruktúra-összetevőket

Infrastruktúra biztonsági mentését a következő összetevőket tartalmazza:

 - **Biztonsági mentési infrastruktúra-vezérlő**  
 Az infrastruktúra biztonsági mentést vezérlő van példányosítva együtt, és minden egyes Azure Stack-felhőben található.
 - **Biztonsági mentési erőforrás-szolgáltató**  
 A biztonsági mentési erőforrás-szolgáltató (biztonsági mentési helyreállítási pont Védettként) a felhasználói felület és az alkalmazás program felületek (API) s is közzéteheti az Azure Stack-infrastruktúra alapszintű biztonsági mentési funkció tevődik össze.

#### <a name="infrastructure-backup-controller"></a>Biztonsági mentési infrastruktúra-vezérlő

Az infrastruktúra biztonsági mentést vezérlő egy Service Fabric service lekérdezi példányosítása számára az Azure Stack-felhőben. Biztonsági mentési erőforrások hozhatók létre, egy regionális szintű és a rögzítés régióspecifikus szolgáltatás adatai az Active Directory, a hitelesítésszolgáltató, Azure Resource Manager, CRP, az SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Biztonsági mentési erőforrás-szolgáltató

A biztonsági mentési erőforrás-szolgáltató megadja a felhasználói felület az alapszintű konfigurációs és biztonsági mentési erőforrások listázása az Azure Stack portálon. Operátor a következő műveleteket végezheti el a felhasználói felületen:

 - Az első alkalommal azáltal, hogy külső tárhelyen találhatók, a hitelesítő adatok és a titkosítási kulcs biztonsági mentés engedélyezése
 - Befejezett nézetet létrehozni készítés alatt állapot erőforrásokat és biztonsági mentés
 - A tárolási hely, ahol helyezi el a biztonsági mentési tartományvezérlő biztonsági mentési adatok módosítása
 - A biztonsági mentést vezérlő használó külső tárolási helyen eléréséhez hitelesítő adatok módosítása
 - A titkosítási kulcs biztonsági mentések titkosításához használó biztonsági mentést vezérlő módosítása 


## <a name="backup-controller-requirements"></a>Biztonsági mentés vezérlő követelményeinek

Ez a szakasz ismerteti a fontos infrastruktúra biztonsági mentését. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt biztonsági mentés engedélyezése az Azure Stack-példányhoz, majd tekintse át biztonsági, szükség szerint üzembe helyezési és az ezt követő művelet során.

A követelmények a következők:

  - **Szoftverkövetelmények** – támogatott tárolási helyek és olvasható méretezési útmutató írja le. 
  - **Hálózati követelmények** – ismerteti a különböző tárolási helyek hálózati követelményei.  

### <a name="software-requirements"></a>Szoftverkövetelmények

#### <a name="supported-storage-locations"></a>Támogatott tárolási helyek

| Tárolási hely                                                                 | Részletek                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben tárolóeszközön üzemeltetett SMB-fájlmegosztás | SMB-megosztás ugyanabban az adatközpontban Azure Stack telepítési helyét, vagy egy másik adatközpontban lévő. Több Azure Stack-példányok használhatja ugyanazt a fájlmegosztást. |
| SMB-fájlmegosztás az Azure-ban                                                          | Jelenleg nem támogatott.                                                                                                                                 |
| Az Azure BLOB storage                                                            | Jelenleg nem támogatott.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Támogatott SMB-verziók

| SMB | Verzió |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Tárolási hely méretezése 

Igény szerinti biztonsági mentést vezérlő infrastruktúra biztonsági másolatot adatokat. A javaslat, hogy készítsen biztonsági másolatot legalább két alkalommal napját és tarthatja a biztonsági mentések legfeljebb hét nap. 

| Környezeti-skála | A biztonsági másolat méretének becsült teljesítményigényeken | Szükséges lemezterület mennyisége összesen |
|-------------------|--------------------------|--------------------------------|
| 4-12-csomópontok        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
| Tárolási hely                                                                 | Részletek                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben tárolóeszközön üzemeltetett SMB-fájlmegosztás | 445-ös port megadása kötelező, ha az Azure Stack-példány a tűzfallal ellátott környezetben található. Infrastruktúra biztonsági mentést vezérlő az SMB-fájlkiszolgáló kapcsolatot kezdeményez a 445-ös porton keresztül. |
| A fájl kiszolgáló teljes Tartománynevét használja, a névnek kell lennie az EGP feloldható             |                                                                                                                                                                                         |

> [!Note]  
> Nincs bejövő portokat kell megnyitni.


## <a name="infrastructure-backup-limits"></a>Biztonsági mentési infrastruktúra-korlátok

Ezek a korlátok megfontolnia tervezése, telepítése és üzemeltetése a Microsoft Azure Stack-példányok. A következő táblázat ismerteti ezeket a korlátokat.

### <a name="infrastructure-backup-limits"></a>Korlátozza az infrastruktúra biztonsági mentése
| Korlátazonosító                                                 | Korlát        | Megjegyzések                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentés típusa                                                      | Csak teljes    | Infrastruktúra biztonsági mentést vezérlő csak a teljes biztonsági mentést támogatja. Nem támogatja a növekményes biztonsági mentéseket.                                          |
| Ütemezett biztonsági mentések                                                | Csak kézzel  | Biztonsági mentés vezérlő jelenleg csak támogatja az igény szerinti biztonsági mentéseket                                                                                 |
| Maximális párhuzamos biztonsági mentési feladatok                                   | 1            | Biztonsági mentés vezérlő példányonként csak egy aktív biztonsági mentési feladat támogatott.                                                                  |
| Hálózati kapcsoló konfigurálására                                     | Nincs a hatókörben | Rendszergazda készítsen biztonsági másolatot az OEM-eszközök használata a hálózati kapcsoló konfigurálására. Tekintse meg az Azure Stack minden OEM gyártója által biztosított dokumentációt. |
| Hardver életciklus gazdagép                                          | Nincs a hatókörben | Rendszergazda hardver életciklus gazdagépre OEM eszközök használatával kell készíteni. Tekintse meg az Azure Stack minden OEM gyártója által biztosított dokumentációt.      |
| Fájlmegosztások maximális száma                                    | 1            | Csak egy fájlmegosztás biztonsági mentési adatok tárolására használható                                                                                        |
| App Services, a függvény, az SQL, a mysql erőforrás-szolgáltató adatok biztonsági mentése | Nincs a hatókörben | Tekintse meg a központi telepítésére és felügyeletére értéknövelt RPs Microsoft által létrehozott közzétett útmutatást.                                                  |
| Biztonsági mentési külső erőforrás-szolgáltatók                              | Nincs a hatókörben | Tekintse meg a központi telepítésére és felügyeletére értéknövelt RPs külső gyártók által létrehozott közzétett útmutatást.                                          |

## <a name="next-steps"></a>További lépések

 - Az infrastruktúra biztonsági mentési szolgáltatással kapcsolatos további tudnivalókért lásd: [Azure stack-infrastruktúra biztonsági másolat szolgáltatással biztonsági mentés és adat helyreállítási](azure-stack-backup-infrastructure-backup.md).