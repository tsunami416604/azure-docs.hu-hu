---
title: A System Center Data Protection Manager hibaelhárítása
description: Ebben a cikkben a System Center Adatvédelmi kezelő használata során felmerülő problémák megoldásait találja.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664748"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>A System Center Data Protection Manager hibaelhárítása

Ez a cikk az Adatvédelmi kezelő használata során felmerülő problémák megoldásait ismerteti.

A System Center Data Protection Manager legújabb kiadási feljegyzéseit a [System Center dokumentációjában](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)találja. Ebben a [mátrixban](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)többet is megtudhat az Adatvédelmi menedzser támogatásáról.

## <a name="error-replica-is-inconsistent"></a>Hiba: A replika inkonzisztens

A replika a következő okok miatt lehet inkonzisztens:

- A replika létrehozási feladata sikertelen.
- Problémák vannak a módosítási naplóval.
- A kötetszintű szűrő bittérképhibákat tartalmaz.
- A forrásgép váratlanul leáll.
- A szinkronizálási napló túlcsordul.
- A replika valóban inkonzisztens.

A probléma megoldásához hajtsa végre a következő műveleteket:

- Az inkonzisztens állapot eltávolításához manuálisan futtassa a konzisztencia-ellenőrzést, vagy ütemezzen napi konzisztencia-ellenőrzést.
- Győződjön meg arról, hogy a Microsoft Azure Backup Server and Data Protection legújabb verzióját használja.
- Győződjön meg arról, hogy az **Automatikus konzisztencia** beállítás engedélyezve van.
- Próbálja meg újraindítani a szolgáltatásokat a parancssorból. Használja `net stop dpmra` a parancsot, `net start dpmra`majd .
- Győződjön meg arról, hogy megfelel a hálózati kapcsolatés a sávszélesség követelményeinek.
- Ellenőrizze, hogy a forrásgép váratlanul leállt-e.
- Győződjön meg arról, hogy a lemez kifogástalan, és hogy van elég hely a replika számára.
- Győződjön meg arról, hogy nincsenek ismétlődő biztonsági mentési feladatok, amelyek egyidejűleg futnak.

## <a name="error-online-recovery-point-creation-failed"></a>Hiba: Az online helyreállítási pont létrehozása nem sikerült

A probléma megoldásához hajtsa végre a következő műveleteket:

- Győződjön meg arról, hogy az Azure Backup ügynök legújabb verzióját használja.
- Próbáljon manuálisan létrehozni egy helyreállítási pontot a védelmi feladatterületen.
- Győződjön meg arról, hogy konzisztencia-ellenőrzést futtat az adatforráson.
- Győződjön meg arról, hogy megfelel a hálózati kapcsolatés a sávszélesség követelményeinek.
- Ha a replikaadatok inkonzisztens állapotban vannak, hozzon létre egy lemez-helyreállítási pontot az adatforrásból.
- Győződjön meg arról, hogy a replika jelen van, és nem hiányzik.
- Győződjön meg arról, hogy a kópia elegendő helyet biztosít a frissítési sorszám (USN) napló létrehozásához.

## <a name="error-unable-to-configure-protection"></a>Hiba: Nem lehet beállítani a védelmet

Ez a hiba akkor fordul elő, ha az Adatvédelmi kezelő kiszolgáló nem tud kapcsolatba lépni a védett kiszolgálóval.

A probléma megoldásához hajtsa végre a következő műveleteket:

- Győződjön meg arról, hogy az Azure Backup ügynök legújabb verzióját használja.
- Győződjön meg arról, hogy van kapcsolat (hálózati/tűzfal/proxy) az Adatvédelmi kezelő kiszolgáló és a védett kiszolgáló között.
- Ha sql-kiszolgálót véd, győződjön meg **arról,** > hogy az**NT AUTHORITY\SYSTEM** tulajdonság ban a **rendszergazdai** beállítás engedélyezve van.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Hiba: A kiszolgáló nincs regisztrálva a tároló hitelesítő fájljában megadottmódon

Ez a hiba az adatvédelmi kezelő/Azure Backup kiszolgálóadatainak helyreállítási folyamata során fordul elő. A helyreállítási folyamat során használt tároló hitelesítő fájl nem tartozik az adatvédelmi kezelő/Azure backup kiszolgáló helyreállítási szolgáltatások tárolójához.

A probléma megoldásához hajtsa végre az alábbi lépéseket:

1. Töltse le a tároló hitelesítő fájlját a Recovery Services-tárolóból, amelyre az Adatvédelmi kezelő/Az Azure Backup kiszolgáló regisztrálva van.
2. Próbálja meg regisztrálni a kiszolgálót a tárolóban a legutóbb letöltött tároló hitelesítő fájl használatával.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Hiba: Nincs helyreállítható adat vagy a kijelölt kiszolgáló nem adatvédelmi kezelőkiszolgáló

A hiba a következő okok miatt fordul elő:

- Nincs más Adatvédelmi manager/Azure backup kiszolgálók regisztrálva van a Recovery Services-tárolóban.
- A kiszolgálók még nem töltötték fel a metaadatokat.
- A kijelölt kiszolgáló nem adatvédelmi kezelő/Azure Backup kiszolgáló.

Ha más Adatvédelmi menedzser-/Azure backup-kiszolgálók regisztrálva vannak a Recovery Services-tárolóban, hajtsa végre az alábbi lépéseket a probléma megoldásához:

1. Győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van.
2. Miután biztosította, hogy a legújabb ügynök telepítve van, várjon egy napot, mielőtt elindítja a helyreállítási folyamatot. Az éjszakai biztonsági mentési feladat feltölti az összes védett biztonsági mentés metaadatait a felhőbe. A biztonsági mentési adatok ezután helyreállítási célokra rendelkezésre állnak.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Hiba: Feltéve, hogy a titkosítási jelszó nem egyezik a kiszolgáló jelmondataival

Ez a hiba a titkosítási folyamat során fordul elő az Adatvédelmi kezelő/Azure Backup kiszolgáló adatainak helyreállításakor. A helyreállítási folyamat során használt titkosítási jelszó nem egyezik meg a kiszolgáló titkosítási jelmondatának. Ennek eredményeképpen az ügynök nem tudja visszafejteni az adatokat, és a helyreállítás sikertelen lesz.

> [!IMPORTANT]
> Ha elfelejti vagy elveszíti a titkosítási jelszót, nincs más módszer az adatok helyreállítására. Az egyetlen lehetőség a jelszó újragenerálása. Az új jelszóval titkosíthatja a jövőbeli biztonsági mentési adatokat.
>
> Az adatok helyreállításakor mindig adja meg ugyanazt a titkosítási jelszót, amely az adatvédelmi kezelő/Azure Backup kiszolgálóhoz van társítva.
>
