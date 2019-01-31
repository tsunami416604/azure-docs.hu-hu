---
title: A System Center Data Protection Manager az Azure Backup hibaelhárítása
description: A System Center Data Protection Manager hibák elhárítása.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 4108616e3ae41e2c88b74bb08d5f846c0035101f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293995"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>A System Center Data Protection Manager hibaelhárítása

Ez a cikk ismerteti a Data Protection Manager használata során előforduló problémák megoldásait.

A legújabb kibocsátási megjegyzések a System Center Data Protection Manager tekintse meg a [a System Center dokumentációja](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). A Data Protection Manager a támogatással kapcsolatos többet is megtudhat [Ez a mátrix](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Hiba: Inkonzisztens replika

A replika inkonzisztens lehet a következő okok miatt:
- A replika-létrehozási feladata meghiúsul.
- A módosítási napló probléma adódik.
- A kötet oldalszintű szűrő bitkép hibákat tartalmaz.
- A forrásgép váratlanul leáll.
- A szinkronizálási napló túlcsordulás.
- Valóban inkonzisztens replika esetén.

A probléma megoldásához hajtsa végre a következő műveleteket:
- Az inkonzisztens állapotát eltávolíthatja, manuálisan futtassa a konzisztencia-ellenőrzés vagy napi konzisztencia-ellenőrzés ütemezése.
- Győződjön meg arról, hogy használ-e a Microsoft Azure Backup Server és a Data Protection Manager legújabb verziójára.
- Ügyeljen arra, hogy a **automatikus konzisztencia** beállítás engedélyezve van.
- Próbálja meg újraindítani a szolgáltatások, a parancssor használatával. Használja a `net stop dpmra` parancsot, majd `net start dpmra`.
- Győződjön meg arról, hogy teljesíti-e még a hálózati kapcsolatot és a sávszélesség-követelmények.
- Ellenőrizze, hogy ha a forrásgép leállt váratlanul.
- Győződjön meg arról, hogy a lemez állapota megfelelő, és hogy van-e elegendő szabad terület a replikát.
- Győződjön meg arról, hogy nincsenek-e ismétlődő biztonsági mentési feladatok, amelyek egy időben futnak.

## <a name="error-online-recovery-point-creation-failed"></a>Hiba: Az online helyreállítási pont létrehozása sikertelen volt.

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy használ-e az Azure Backup szolgáltatás ügynökének legújabb verzióját.
- Próbálja meg manuálisan az a védelem feladatterületen található helyreállítási pont létrehozása.
- Győződjön meg arról, hogy az adatforrás konzisztencia-ellenőrzést futtatja.
- Győződjön meg arról, hogy teljesíti-e még a hálózati kapcsolatot és a sávszélesség-követelmények.
- Inkonzisztens állapotban van, a replika adatait, ha az adatforrás lemez helyreállítási pont létrehozása.
- Győződjön meg arról, hogy a replika jelen, és nem hiányzik-e.
- Győződjön meg arról, hogy rendelkezik-e elegendő hely a frissítés feladatütemezési száma (USN) naplót szeretne létrehozni a replikát.

## <a name="error-unable-to-configure-protection"></a>Hiba: Nem sikerült beállítani a védelmet.

Ez a hiba akkor fordul elő, ha a Data Protection Manager-kiszolgáló nem tud csatlakozni a védett kiszolgálón. 

A probléma megoldásához hajtsa végre a következő műveleteket:
- Győződjön meg arról, hogy használ-e az Azure Backup szolgáltatás ügynökének legújabb verzióját.
- Győződjön meg arról, hogy nincs-e kapcsolat (hálózati/tűzfal vagy proxy) a Data Protection Manager-kiszolgáló és a védett kiszolgáló között.
- Ha egy SQL-kiszolgáló védi, győződjön meg arról, hogy a **bejelentkezési tulajdonságok** > **NT AUTHORITY\SYSTEM** tulajdonság mutat be a **SysAdmin (rendszergazda)** beállítás engedélyezve van.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Hiba: A kiszolgáló nincs regisztrálva a megadott módon a tároló hitelesítőadat-fájlja

Ez a hiba akkor fordul elő, a Data Protection Manager vagy az Azure Backup server-adatok helyreállítási folyamat során. A Recovery Services-tároló a Data Protection Manager vagy az Azure Backup-kiszolgáló nem tartozik a tároló hitelesítőadat-fájlja a helyreállítási folyamat során használt.

A probléma megoldásához hajtsa végre az alábbi lépéseket:
1. A Recovery Services-tároló, amely a Data Protection Manager vagy az Azure Backup-kiszolgáló regisztrálva van, töltse le a tároló hitelesítőadat-fájlja.
2. Próbálja meg regisztrálni a kiszolgálót a tárolóban a legutóbb letöltött tárolói hitelesítő adatok fájlját használatával.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Hiba: Nincsenek helyreállítható adatok vagy a kiválasztott kiszolgáló nem a Data Protection Manager-kiszolgáló

Ez a hiba akkor fordul elő, a következő okok miatt:
- Egyéb Data Protection Manager vagy az Azure Backup kiszolgálók regisztrálva vannak a Recovery Services-tároló.
- A kiszolgálók még még nem töltött fel a metaadatokat.
- A kiválasztott kiszolgáló nem a Data Protection Manager vagy az Azure Backup server.

Ha a többi Data Protection Manager vagy az Azure Backup-kiszolgáló regisztrálva van a Recovery Services-tároló, hajtsa végre ezeket a lépéseket a probléma megoldásához:
1. Győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van-e.
2. Miután meggyőződött arról, hogy telepítve van-e a legújabb ügynököt, várjon egy nap, a helyreállítási folyamat megkezdése előtt. A biztonsági mentési feladat a metaadatokat az összes védett biztonsági mentés a felhőbe tölt fel. A biztonsági mentési adatok helyreállítási majd érhető el.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Hiba: A megadott titkosítási jelszó nem felel meg a kiszolgáló jelszava

Ez a hiba a folyamat során fordul elő, ha a Data Protection Manager vagy az Azure Backup server adatainak helyreállítása. A helyreállítási folyamat során használt titkosítási jelszó nem felel meg a kiszolgáló titkosítási jelszava. Ennek eredményeképpen az ügynök nem tudja visszafejteni az adatokat, és a helyreállítás sikertelen lesz.

> [!IMPORTANT]
> Ha elfelejti a titkosítási jelszót, nincsenek más módszer az adatok helyreállításához. Az egyetlen lehetőség, hogy újragenerálja a hozzáférési kódot. Az új jelszót használja a jövőbeli biztonsági mentési adatok titkosításához.
>
> Adatok végzi a helyreállítást, mindig adja meg a Data Protection Manager vagy az Azure Backup server társított ugyanolyan titkosítási jelszava. 
>
