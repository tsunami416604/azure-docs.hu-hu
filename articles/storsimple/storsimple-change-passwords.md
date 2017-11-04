---
title: "StorSimple Device Manager keresztül jelszavak módosítása |} Microsoft Docs"
description: "Ismerteti a StorSimple Manager szolgáltatás segítségével módosítsa a StorSimple Snapshot Manager és az eszköz rendszergazdai jelszót."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>A StorSimple Manager szolgáltatással a StorSimple-jelszavak módosítása
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple-jelszavak megváltoztatása a StorSimple Manager szolgáltatás segítségével](storsimple-8000-change-passwords.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
A klasszikus Azure portálon **konfigurálása** lap tartalmaz olyan módon konfigurálhatja újra a StorSimple eszközön a StorSimple Manager szolgáltatás által kezelt eszköz paramétereket. Ez az oktatóanyag azt ismerteti, hogyan használhatja a **konfigurálása** lapon az eszköz-rendszergazdai vagy a StorSimple Snapshot Manager jelszavának módosítása.

## <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Ha Windows PowerShell-felületet a StorSimple eszköz elérésére használja, meg kell adja meg az eszköz rendszergazdai jelszavát. Ha az első StorSimple eszköz regisztrálva van a szolgáltatásban, az alapértelmezett Ez az interfész jelszava *jelszó1*. Az adatok biztonsága érdekében szükségesek a regisztrációs folyamat végén jelszó módosításához. Ez a jelszó módosítása nélkül nem lépjen ki a regisztrációs folyamat során. További információkért lásd: [3. lépés: konfigurálja, és a Windows PowerShell segítségével az eszközt regisztrálni kell a StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A jelszó, amelyet először a Windows PowerShell felületen regisztrálás során majd a klasszikus Azure portálon keresztül lehet módosítani. A következő lépésekkel módosíthatja az eszköz rendszergazdai jelszava.

#### <a name="to-change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
1. A klasszikus portálon kattintson **eszközök** > **konfigurálása** az eszközhöz.
2. Görgessen le a **eszköz rendszergazdai jelszavát** szakasz. Adjon meg egy rendszergazdai jelszót, amely tartalmazza a 8-15 karakter. A jelszónak legalább 3 nagybetű, nagybetűk, numerikus és speciális karakterek kombinációjából kell lennie.
3. Erősítse meg a jelszót.
4. Kattintson a lap alján található **Mentés** gombra.

Az eszköz rendszergazdai jelszava most frissíteni kell. A módosított jelszó használatával érhető el a Windows PowerShell illesztőfelülete.

## <a name="change-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának módosítása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

Amikor konfigurál egy eszközt a StorSimple Snapshot Managerben, fog kérni fogja az eszköz IP-cím és a jelszót a tárolóeszköz hitelesítése kell megadniuk. Ez a jelszó van konfigurálva a Windows PowerShell felületen. További információkért lásd: [3. lépés: konfigurálja, és a Windows PowerShell segítségével az eszközt regisztrálni kell a StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A jelszó, amelyet először a Windows PowerShell felületen regisztrálás során módosíthatja a klasszikus portálon. A következő lépésekkel módosítsa a StorSimple Snapshot Manager jelszavát.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának módosítása
1. A klasszikus portálon kattintson **eszközök** > **konfigurálása** az eszközhöz.
2. Görgessen le a **StorSimple Snapshot Manager** szakasz. Adjon meg egy 14 vagy 15 karakter hosszú jelszót. Győződjön meg arról, hogy a jelszó tartalmazza-e a 3 vagy több nagybetű, nagybetűk, numerikus és speciális karaktert.
3. Erősítse meg a jelszót.
4. Kattintson a lap alján található **Mentés** gombra.

A StorSimple Snapshot Manager jelszava most frissíteni kell.

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple biztonsági](storsimple-security.md).
* További információ [az eszköz konfigurációjának módosítása](storsimple-modify-device-config.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

