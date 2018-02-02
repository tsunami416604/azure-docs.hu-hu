---
title: "A System Center Data Protection Manager alkalmazást az Azure Backup hibaelhárítása |} Microsoft Docs"
description: "A System Center Data Protection Manager elhárítása."
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>A System Center Data Protection Manager hibaelhárítása

A legújabb kibocsátási megjegyzések a SC DPM található [Itt](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016).
A védelem támogatási mátrixa található [Itt](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="replica-is-inconsistent"></a>A replika inkonzisztens

Ez a hiba akkor fordulhat elő különböző okokból - replika-létrehozási feladatot nem sikerült, módosítási napló problémái kötet szintű szűrő bitkép hibák, forrásgép váratlanul leállítása, a szinkronizálási naplóba, vagy a replika túlcsordulása valóban inkonzisztens. Kövesse az alábbi lépéseket a probléma megoldása érdekében:
- Távolítsa el a inkonzisztens állapot, futtasson konzisztencia-ellenőrzést manuálisan, vagy napi konzisztencia-ellenőrzés ütemezése.
- Gondoskodjon arról, hogy MAB kiszolgáló vagy a System Center DPM legújabb verziója
- Győződjön meg róla, engedélyezve van az automatikus konzisztencia-ellenőrzés
- Indítsa újra a szolgáltatást, a parancssor ("net stop dpmra" majd "net start dpmra")
- Ellenőrizze a hálózati kapcsolatot és a sávszélesség-e
- Ha a forrásgép le volt állítva váratlanul ellenőrzése
- Ellenőrizze a lemez kifogástalan állapotú, és elég lemezterület a replika
- Nem ismétlődő biztonsági mentési feladat párhuzamosan futnak.

## <a name="online-recovery-point-creation-failed"></a>Online helyreállítási pont létrehozása nem sikerült

Kövesse az alábbi lépéseket a probléma megoldása érdekében:
- Gondoskodjon arról, hogy Azure Backup szolgáltatás ügynökének legújabb verzióját
- Próbálja meg manuálisan a helyreállítási pont létrehozása a védelem feladatterületen található
- Győződjön meg arról, hogy az adatforrás konzisztencia-ellenőrzés futtatása
- Ellenőrizze a hálózati kapcsolatot és a sávszélesség-e
- A replika adatok inkonzisztens állapotban van. Az adatforrás lemez helyreállítási pont létrehozása
- Győződjön meg arról, a replika jelen, és nem hiányzik-e
- A replika nehézségekkel elég hely az USN-napló

## <a name="unable-to-configure-protection"></a>Nem sikerült beállítani a védelmet

Ez a hiba akkor jelenik meg, amikor a DPM-kiszolgáló nem tud kapcsolatba lépni a védett kiszolgálón. Kövesse az alábbi lépéseket a probléma megoldása érdekében:
- Gondoskodjon arról, hogy az Azure Backup szolgáltatás ügynökének legújabb verzióját.
- Gondoskodjon arról, hogy a DPM-kiszolgáló és a védett kiszolgáló közötti kapcsolat megfelelő (hálózati/tűzfal /-proxy)
- Ha SQL Server védelmét, győződjön meg arról, NT AUTHORITY\SYSTEM rendelkezik SysAdmin (rendszergazda) engedélyezve van a bejelentkezési tulajdonságok

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Ez a kiszolgáló nincs regisztrálva a tárolónál, amelyet a tároló hitelesítő adatait

Ez a hiba akkor jelenik meg, amikor a kijelölt tároló hitelesítő adatait tartalmazó fájlt nem tartozik a System Center DPM társított Recovery Services-tároló / Azure Backup Server a helyreállítási kísérelték meg. Kövesse az alábbi lépéseket a probléma megoldása érdekében:
- A letöltés a tárolói hitelesítő adatok fájlját az a Recovery Services-tároló, amelyhez a System Center DPM / Azure Backup-kiszolgáló regisztrálva van.
- Próbálja meg a kiszolgáló regisztrálása a tárolónál a legújabb letöltött tárolói hitelesítő adatok fájlját.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>Vagy a helyreállítható adatok nem érhető el, vagy a kiválasztott kiszolgáló nem DPM-kiszolgáló
Ez a hiba akkor jelenik meg, ha nincsenek nincs más System Center DPM / az Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, vagy a kiszolgálók még nem feltöltött a metaadatok, vagy a kiválasztott kiszolgáló nem a System Center DPM / Azure Backup Server.
- Ha nincsenek más System Center DPM / Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy a legújabb Azure Backup szolgáltatás ügynöke telepítve van.
- Ha nincsenek más System Center DPM / Azure Backup-kiszolgáló regisztrálva a Recovery Services-tároló, várja meg a helyreállítási folyamat elindításához a telepítés után naponta. Az ütemezett feladat feltölt összes védett biztonsági mentés felhőbe metaadatait. Az adatok helyreállítás érhető el.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>A megadott titkosítási jelszó nem egyezik a következő kiszolgálóhoz tartozó jelszóval

> [!NOTE]
>Ha elfelejtette/elvesztette a titkosítási jelszót, majd nincs lehetőség az adatok helyreállításához. Egyetlen választása marad: újragenerálja a jelszót és a jövőbeli biztonsági mentési adatok titkosítására használja.
>
>

Ez a hiba akkor jelenik meg, amikor folyamatban van, a System Center DPM adatok titkosítására használt titkosítási jelszó /, amelyik a helyreállítandó az Azure Backup Server adat nem felel meg a megadott titkosítási jelszó. Az ügynök nem tudja visszafejteni az adatokat. Ezért a helyreállítás sikertelen lesz. Kövesse az alábbi lépéseket a probléma megoldása érdekében:
- Adja meg a System Center DPM társított pontos azonos titkosítási jelszó / Azure Backup Server amelynek adatok helyreállítása folyamatban. 
