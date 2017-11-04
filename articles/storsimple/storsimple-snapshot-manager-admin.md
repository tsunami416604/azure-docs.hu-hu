---
title: "StorSimple Snapshot Manager adminisztrációs |} Microsoft Docs"
description: "Áttekintés és a StorSimple Snapshot Manager megoldás felügyeleti feladatokkal és munkafolyamatok kapcsolatos további információkra mutató hivatkozásokat biztosít."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Használja a StorSimple Snapshot Manager felügyelete a StorSimple megoldásban

## <a name="overview"></a>Áttekintés
StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatvédelem és biztonsági mentési kezelése a Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével kezelheti az adatközpontban és a felhőben található Microsoft Azure StorSimple adatok egyetlen integrált tárolási megoldásként, így biztonsági mentési folyamat egyszerűsítése, illetve csökkenti a költségeket.

A StorSimple Snapshot Manager központi felügyeleti konzol lehetővé teszi, hogy egységes, időpontban – biztonsági másolat készítése a helyi és a felhőalapú adatokat. Például a konzolt is használhatja:

* Konfigurálhatja, készítsen biztonsági másolatot, és törölje köteteket.
* Kötet csoportokat győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági mentési házirendek kezelése, így az adatok biztonsági másolatát egy előre meghatározott ütemezés szerint.
* Az adatokat, amelyeket a felhőben tárolt és a katasztrófa utáni helyreállítás független példányt hoz létre.

Ez a cikk oktatóanyagok a StorSimple Snapshot Manager és rendszer-felügyeleti feladatok elvégzésére és a munkafolyamatok végrehajtásához használandó mutató hivatkozásokat tartalmaz.

* További információ a StorSimple Snapshot Manager összetevőit és architektúra: [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* StorSimple Snapshot Manager letöltéséhez keresse fel [a StorSimple Snapshot Manager letöltési oldal](https://www.microsoft.com/download/details.aspx?id=44220).
* A StorSimple Snapshot Manager központi telepítési eljárásokat Ugrás [StorSimple Snapshot Manager telepítése](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple a helyszíni virtuális eszköz) nem használható.


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager feladatok és a munkafolyamatok
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti a biztonsági mentési feladatok aktuális, ütemezett és befejeződött. StorSimple Snapshot Manager emellett legfeljebb 64 befejezett biztonsági mentés katalógusa. A katalógus segítségével keresse meg és kötetek vagy fájlok visszaállítása. 

| HA AZT SZERETNÉ, HOGY EHHEZ... | EZ AZ OKTATÓANYAG HASZNÁLNI... |
|:--- |:--- |
| További információ a StorSimple Snapshot Manager |[Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| StorSimple Snapshot Manager telepítése<br>Telepítse újra a StorSimple Snapshot Manager<br>Távolítsa el a StorSimple Snapshot Manager |[A StorSimple Snapshot Manager központi telepítése](storsimple-snapshot-manager-deployment.md) |
| Használja a StorSimple Snapshot Manager menük és szolgáltatások:<ul><li>Menüsáv</li><li>Eszköztár</li><li>Hatókör ablaktábla</li><li>Eredmények ablaktábla</li><li>Műveletek panel</li><li>Billentyűparancsokkal és parancsikonok</li></ul> |[StorSimple Snapshot Manager felhasználói felülete](storsimple-use-snapshot-manager.md) |
| A StorSimple Snapshot Manager szolgáltatásai közös MMC használja:<ul><li>Nézet</li><li>Itt új ablak</li><li>Frissítés</li><li>Lista exportálása</li><li>Súgó</li></ul> |[Az MMC menü műveletek a StorSimple Snapshot Manager használatára](storsimple-snapshot-manager-mmc-menu.md) |
| Adja hozzá, vagy cserélje le az eszköz<br>Egy eszköz csatlakoztatása<br>Ellenőrizze az importált kötet csoportok<br>Frissítse a csatlakoztatott eszközök<br>Egy eszköz hitelesítéséhez<br>Eszköz részleteinek megtekintése<br>Egy eszköz konfigurálásának törlése<br>A jelszó módosítása<br>Cserélje le a hibás eszköz<br> |[StorSimple Snapshot Manager segítségével csatlakozhat, és kezelheti a StorSimple eszközökhöz](storsimple-snapshot-manager-manage-devices.md) |
| Kötet csatlakoztatása<br>Kötetek adatainak megtekintése<br>Kötet törlése<br>Kötetek újraellenőrzése<br>Konfigurálja, és készítsen biztonsági másolatot alaplemezek<br>Konfigurálja és a dinamikus tükrözött kötetek biztonsági mentése |[StorSimple Snapshot Manager segítségével megtekintheti, és a kötetek kezelése](storsimple-snapshot-manager-manage-volumes.md) |
| Kötet csoportok megtekintése<br>Kötet csoport létrehozása<br>Készítsen biztonsági másolatot egy kötet csoport<br>Kötet csoport szerkesztése<br>Kötet csoport törlése |[StorSimple Snapshot Manager segítségével kötet csoportok létrehozása és kezelése](storsimple-snapshot-manager-manage-volume-groups.md) |
| A biztonsági mentési házirend létrehozása <br>A biztonsági mentési házirend szerkesztése<br>A biztonsági mentési házirend törlése |[StorSimple Snapshot Manager segítségével biztonsági szabályzatok létrehozása és kezelése](storsimple-snapshot-manager-manage-backup-policies.md) |
| Megtekinthető és kezelhető az ütemezett biztonsági mentési feladatok<br>Megtekintheti és kezelheti a legutóbbi biztonsági mentési feladatok<br>Megtekintheti és kezelheti a jelenleg futó biztonsági mentési feladatok |[StorSimple Snapshot Manager segítségével megtekintheti és kezelheti a biztonsági mentési feladatok](storsimple-snapshot-manager-manage-backup-jobs.md) |
| A kötet visszaállítása<br>Egy kötet vagy kötet klónozása<br>Biztonsági<br>A fájl helyreállításához<br>A StorSimple Snapshot Manager-adatbázis visszaállítása |[Használja a StorSimple Snapshot Manager a biztonságimásolat-katalógus kezelése](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Következő lépések
[Töltse le a StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

