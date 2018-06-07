---
title: A System Center Data Protection Manager alkalmazást az Azure Backup hibaelhárítása
description: A System Center Data Protection Manager elhárítása.
services: backup
author: adigan
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/24/2017
ms.author: adigan
ms.openlocfilehash: d3776df8184523999433059e95bc72e1d3abb1c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606443"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>A System Center Data Protection Manager hibaelhárítása

Ez a cikk a Data Protection Manager használata során előforduló problémák megoldásait ismerteti.

A legújabb kibocsátási megjegyzések a System Center Data Protection Manager, tekintse meg a [dokumentáció a System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). A Data Protection Manager a támogatással kapcsolatos részletesebb [Ez a mátrix](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Hiba: A replika inkonzisztens

A replika inkonzisztens lehet a következők lehetnek az okai:
- A replika-létrehozási feladat sikertelen lesz.
- A módosítási naplóhoz problémák vannak.
- A kötet szintű szűrő bitkép hibákat tartalmaz.
- A forrásoldali számítógép váratlanul leáll.
- A szinkronizálási naplóba során.
- A replika valóban inkonzisztens.

A probléma megoldásához hajtsa végre a következő műveleteket:
- Az inkonzisztens állapot eltávolításához manuálisan futtassa a konzisztencia-ellenőrzést, vagy napi konzisztencia-ellenőrzés ütemezése.
- Győződjön meg arról, hogy használata a Microsoft Azure Backup Server és a Data Protection Manager legújabb verzióját.
- Győződjön meg arról, hogy a **automatikus konzisztencia** beállítás engedélyezve van.
- Próbálja meg újraindítani a szolgáltatást, a parancssorba. Használja a `net stop dpmra` parancsot, majd `net start dpmra`.
- Győződjön meg arról, hogy teljesíti-e még a hálózati kapcsolatot és a sávszélesség követelményeket.
- Ellenőrizze, hogy ha a forrásgép le volt állítva váratlanul.
- Győződjön meg arról, hogy a lemez állapota megfelelő, és hogy van-e elég hely a replika.
- Győződjön meg arról, hogy nincsenek-e nem ismétlődő egyidejűleg futó biztonsági mentési feladatok.

## <a name="error-online-recovery-point-creation-failed"></a>Hiba: Az Online helyreállítási pont létrehozása nem sikerült

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy szeretne megtudni az Azure Backup szolgáltatás ügynökének legújabb verzióját.
- Próbálja meg manuálisan az a védelem feladatterületen található helyreállítási pont létrehozása.
- Győződjön meg arról, hogy az adatforrás konzisztencia-ellenőrzés futtatása.
- Győződjön meg arról, hogy teljesíti-e még a hálózati kapcsolatot és a sávszélesség követelményeket.
- Ha a replika adatait inkonzisztens állapotban van, az adatforrás lemez helyreállítási pont létrehozása.
- Győződjön meg arról, hogy a replika jelen, és nem hiányzik-e.
- Győződjön meg arról, hogy rendelkezik-e elegendő hely a frissítés feladatütemezési száma (USN) naplót hoz létre a replikát.

## <a name="error-unable-to-configure-protection"></a>Hiba: Nem sikerült beállítani a védelmet

Ez a hiba akkor fordul elő, ha a Data Protection Manager-kiszolgáló nem tud csatlakozni a védett kiszolgálón. 

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy szeretne megtudni az Azure Backup szolgáltatás ügynökének legújabb verzióját.
- Győződjön meg arról, hogy van kapcsolat (hálózati/tűzfal /-proxy) a Data Protection Manager-kiszolgáló és a védett kiszolgáló között.
- Ha egy SQL server számára kíván védelmet biztosítani, győződjön meg arról, hogy a **bejelentkezési tulajdonságok** > **NT AUTHORITY\SYSTEM** tulajdonság mutat be a **sysadmin** beállítás engedélyezve van.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Hiba: Kiszolgáló nincs regisztrálva megadott tárolói hitelesítő adatok fájlját

Ez a hiba akkor fordul elő, a Data Protection Manager vagy az Azure biztonsági mentés server-adatok helyreállítási folyamat során. A tároló hitelesítő adatait tartalmazó fájlt, a helyreállítási folyamat során használt a Recovery Services-tárolónak a Data Protection Manager vagy az Azure biztonsági mentés kiszolgáló nem tartozik.

A probléma megoldásához hajtsa végre az alábbi lépéseket:
1. Töltse le a tárolói hitelesítő adatok fájlját a Recovery Services tárolóból, amelyben a Data Protection Manager vagy az Azure biztonsági mentés kiszolgáló regisztrálva van.
2. Próbálja meg regisztrálni a kiszolgálót a tárolóban a nemrég letöltött tárolói hitelesítő adatok fájlját használatával.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Hiba: Nem helyreállítható adatok vagy a kiválasztott kiszolgáló nem a Data Protection Manager-kiszolgáló

Ez a hiba akkor fordul elő, a következő okok miatt:
- Nincs más Data Protection Manager vagy az Azure biztonsági mentés kiszolgáló regisztrálva van a Recovery Services-tároló.
- A kiszolgálók még még nem töltötte fel a metaadatok.
- A kiválasztott kiszolgáló nem található a Data Protection Manager vagy az Azure biztonsági mentés kiszolgáló.

Ha más Data Protection Manager vagy az Azure biztonsági mentés kiszolgáló regisztrálva van a Recovery Services-tároló, hajtsa végre ezeket a lépéseket, a probléma megoldásához:
1. Győződjön meg arról, hogy a legújabb Azure Backup szolgáltatás ügynöke telepítve van-e.
2. Miután meggyőződött arról, hogy telepítve van-e a legújabb ügynököt, várja meg a helyreállítási folyamat megkezdése előtt egy nap. Az ütemezett biztonsági mentési feladat az összes védett biztonsági mentés metaadatainak feltölti a felhőbe. A biztonsági mentési adatok helyreállítási majd érhető el.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Hiba: A megadott titkosítási jelszó nem egyezik kiszolgálóhoz jelszó

Ez a hiba akkor fordul elő, a titkosítási folyamat Data Protection Manager vagy az Azure biztonsági mentés server-adatok helyreállításakor. A helyreállítási folyamat során használt titkosítási jelszó nem felel meg a kiszolgáló titkosítási jelszó. Ennek eredményeképpen az ügynök nem tudja visszafejteni az adatokat, és a helyreállítás sikertelen lesz.

> [!IMPORTANT]
> Ha elfelejti a titkosítás jelszava, nincsenek más módszer az adatok helyreállításához. Az egyetlen lehetőség újragenerálja a jelszót. Az új jelszó segítségével titkosíthatja a jövőbeli biztonsági mentési adatokat.
>
> Amikor az adatok helyreállítását mindig adja meg a Data Protection Manager vagy az Azure biztonsági mentés kiszolgálóhoz tartozó titkosítási jelszó azonos. 
>
