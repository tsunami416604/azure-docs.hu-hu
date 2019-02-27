---
title: A StorSimple Snapshot Manager adminisztrációs |} A Microsoft Docs
description: Áttekintése és a StorSimple Snapshot Manager megoldás felügyeleti feladatok és a munkafolyamatok kapcsolatos további információkra mutató hivatkozásokat biztosít.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872183"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Használja a StorSimple Snapshot Manager felügyelete a StorSimple-megoldásokra

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatok védelme és a biztonságimásolat-felügyelet a Microsoft Azure StorSimple-környezetben. A StorSimple Snapshot Managerrel kezelheti az adatközpontban és a felhőben a Microsoft Azure StorSimple data egyetlen integrált tárolási megoldás, így leegyszerűsíti a biztonsági mentési folyamatokat és költségeit.

A StorSimple Snapshot Manager központi felügyeleti konzol lehetővé teszi, hogy következetes, időponthoz – biztonsági másolat készítése helyi és felhőbeli adatok. Ha például a konzolt is használhatja:

* Konfigurálja, biztonsági mentése és kötetek törlése.
* Konfigurálja a kötet csoportokat, győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági mentési házirendek kezelése, így az adatok biztonsági mentésének előre meghatározott ütemezés szerint.
* Az adatokat, amelyeket a felhőben tárolt, és a vészhelyreállításhoz használt független példányt hoz létre.

Ez a cikk az oktatóanyagok, amelyek ismertetik a StorSimple Snapshot Manager és rendszer-felügyeleti feladatok és a munkafolyamatok végrehajtásához használatával mutató hivatkozásokat tartalmaz.

* További információ a StorSimple Snapshot Manager összetevőkről és architektúráról: [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Töltse le a StorSimple Snapshot Managerben, lépjen a [a StorSimple Snapshot Manager letöltési oldalról](https://www.microsoft.com/download/details.aspx?id=44220).
* A StorSimple Snapshot Manager üzembe helyezési eljárásokat Ugrás [üzembe helyezése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> A StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple Virtual Arrayt (más néven a StorSimple helyszíni virtuális eszköz) nem használható.


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>A StorSimple Snapshot Manager-feladatok és a munkafolyamatok
Használhatja a StorSimple Snapshot Manager figyelése és felügyelete az aktuális, ütemezett és befejeződött a biztonsági mentési feladatok. A StorSimple Snapshot Manager emellett egy katalógus, akár 64 befejezett biztonsági mentéseket. A katalógus segítségével megkeresését és helyreállítását a kötetek vagy csak egyes fájlokat. 

| HA AZT SZERETNÉ, EHHEZ... | EZ AZ OKTATÓANYAG HASZNÁLATA... |
|:--- |:--- |
| További információ a StorSimple Snapshot Managerrel |[Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Install StorSimple Snapshot Manager<br>Reinstall StorSimple Snapshot Manager<br>Remove StorSimple Snapshot Manager |[Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Használja a StorSimple Snapshot Manager menük és szolgáltatások:<ul><li>A menüsávon</li><li>Az eszköztáron</li><li>Hatókör panel</li><li>Eredmények ablaktábla</li><li>Műveletek panel</li><li>Billentyűzetnavigációt és billentyűparancsok</li></ul> |[StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md) |
| Közös MMC-ben elérhető funkciókat a StorSimple Snapshot Manager használata:<ul><li>Nézet</li><li>Itt új ablakban</li><li>Frissítés</li><li>Lista exportálása</li><li>Súgó</li></ul> |[Az MMC-menü műveletek használata a StorSimple Snapshot Managerben](storsimple-snapshot-manager-mmc-menu.md) |
| Adja hozzá, vagy cserélje le az eszközt<br>Eszköz csatlakoztatása<br>Ellenőrizze az importált kötet csoportok<br>Csatlakoztatott eszközök frissítése<br>Egy eszköz hitelesítéséhez<br>Eszközadatok megtekintése<br>Egy eszköz konfigurációjának törlése<br>Egy eszköz jelszavának módosítása<br>Cserélje le a sikertelen eszközök<br> |[A StorSimple Snapshot Manager használatával csatlakozhat, és a StorSimple-eszközök kezelése](storsimple-snapshot-manager-manage-devices.md) |
| Kötetek csatlakoztatása<br>Kötetek adatainak megtekintése<br>Kötet törlése<br>Kötetek ismételt vizsgálata<br>Konfigurálja és biztonsági mentése egyszerű kötet<br>Konfigurálja és a dinamikus tükrözött kötetek biztonsági mentése |[A StorSimple Snapshot Manager segítségével megtekintheti, és a kötetek kezelése](storsimple-snapshot-manager-manage-volumes.md) |
| Kötet csoportok megtekintése<br>Hozzon létre egy kötetet csoportot<br>Készítsen biztonsági másolatot egy kötetcsoport<br>Kötet csoport szerkesztése<br>Kötet csoport törlése |[Létrehozása és kezelése a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-volume-groups.md) |
| A biztonsági mentési szabályzat létrehozása <br>Biztonsági mentési szabályzat szerkesztése<br>A biztonsági mentési szabályzat törlése |[A StorSimple Snapshot Manager segítségével létrehozhat, és a biztonsági mentési házirendek kezelése](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ütemezett biztonsági mentési feladatok megtekintése és kezelése<br>Legutóbbi biztonsági mentési feladatok megtekintése és kezelése<br>Megtekintheti és kezelheti a jelenleg futó biztonsági mentési feladatok |[A biztonsági mentési feladatok megtekintése és kezelése a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Egy kötet visszaállítása<br>Egy kötet vagy kötet klónozása<br>A biztonsági másolat törlése<br>Fájl helyreállítása<br>A StorSimple Snapshot Manager-adatbázis visszaállítása |[Használja a StorSimple Snapshot Manager, a biztonságimásolat-katalógus kezelése](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>További lépések
[Töltse le a StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

