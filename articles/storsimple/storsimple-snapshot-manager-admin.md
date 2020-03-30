---
title: StorSimple Snapshot Manager adminisztráció | Microsoft dokumentumok
description: Áttekintést és hivatkozásokat tartalmaz a StorSimple Snapshot Manager megoldásfelügyeleti feladatokkal és munkafolyamatokkal kapcsolatos további tudnivalókra.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: twooley
ms.openlocfilehash: b8fe87a2df633af310bc661308813a60987e77d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933282"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>A StorSimple Snapshot Manager használata a StorSimple-megoldás felügyeletéhez

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentés kezelését egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével egyetlen integrált tárolási megoldásként kezelheti a Microsoft Azure StorSimple-adatokat az adatközpontban és a felhőben, így egyszerűsítve a biztonsági mentési folyamatokat és csökkentve a költségeket.

A StorSimple Snapshot Manager központi felügyeleti konzol lehetővé teszi, hogy a helyi és a felhőbeli adatok konzisztens, időponthoz fért el biztonsági másolatokat. A konzol segítségével például a következőkre használhatja:

* Kötetek konfigurálása, biztonsági és törlése.
* A kötetcsoportok konfigurálásával győződjön meg arról, hogy a biztonsági másolatok adatai alkalmazáskonzisztensek.
* A biztonsági mentési házirendek kezelése, hogy az adatokról egy előre meghatározott ütemezés szerint készüljön biztonsági másolat.
* Hozzon létre független adatmásolatokat, amelyek a felhőben tárolhatók, és vész-helyreállítási célokra használhatók.

Ez a cikk a StorSimple Snapshot Managert leíró oktatóanyagokra mutató hivatkozásokat tartalmaz, és azt, hogyan használható a rendszerfelügyeleti feladatok és munkafolyamatok végrehajtásához.

* A StorSimple Snapshot Manager összetevőiről és architektúrájáról a [Mi a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* A StorSimple Snapshot Manager letöltéséhez nyissa meg [a StorSimple Snapshot Manager letöltési oldalát.](https://www.microsoft.com/download/details.aspx?id=44220)
* A StorSimple Snapshot Manager telepítési eljárásaihoz látogasson el [a StorSimple Snapshot Manager telepítése](storsimple-snapshot-manager-deployment.md)elemre.

> [!NOTE]
> A StorSimple Snapshot Manager nem használható a Microsoft Azure StorSimple virtuális tömbök (más néven helyszíni StorSimple virtuális eszközök) kezelésére.


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager feladatok és munkafolyamatok
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti az aktuális, ütemezett és befejezett biztonsági mentési feladatokat. Emellett a StorSimple Snapshot Manager legfeljebb 64 befejezett biztonsági mentésből álló katalógust biztosít. A katalógus segítségével köteteket vagy egyes fájlokat kereshet és állíthat vissza. 

| HA EZT AKAROD TENNI... | HASZNÁLJA EZT AZ OKTATÓANYAGOT... |
|:--- |:--- |
| További információ a StorSimple Snapshot Managerről |[Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| StorSimple Snapshot Manager telepítése<br>StorSimple snapshot manager újratelepítése<br>StorSimple snapshot manager eltávolítása |[StorSimple snapshot manager telepítése](storsimple-snapshot-manager-deployment.md) |
| A StorSimple Snapshot Manager menüinek és szolgáltatásainak használata:<ul><li>Menüsor</li><li>Eszköztár</li><li>Hatókör ablaktábla</li><li>Eredmények ablaktábla</li><li>Műveletek ablaktábla</li><li>Billentyűzetnavigáció és billentyűparancsok</li></ul> |[StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md) |
| Használja a StorSimple Snapshot Manager ben található általános MMC-funkciókat:<ul><li>Nézet</li><li>Új ablak innen</li><li>Frissítés</li><li>Lista exportálása</li><li>Súgó</li></ul> |[Az MMC menüműveletek használata a StorSimple Snapshot Managerben](storsimple-snapshot-manager-mmc-menu.md) |
| Eszköz hozzáadása vagy cseréje<br>Eszköz csatlakoztatása<br>Importált kötetcsoportok ellenőrzése<br>Csatlakoztatott eszközök frissítése<br>Eszköz hitelesítése<br>Eszközadatok megtekintése<br>Eszközkonfiguráció törlése<br>Eszközjelszó módosítása<br>Meghibásodott eszköz cseréje<br> |[StorSimple-eszközök csatlakoztatása és kezelése a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-devices.md) |
| Kötetek csatlakoztatása<br>Kötetek adatainak megtekintése<br>Kötet törlése<br>Kötetek újrabeszekveretése<br>Alapkötet konfigurálása és biztonsági másolatot ad<br>Dinamikus tükrözött kötet konfigurálása és biztonsági mentése |[Kötetek megtekintése és kezelése a StorSimple Snapshot Manager segítségével](storsimple-snapshot-manager-manage-volumes.md) |
| Kötetcsoportok megtekintése<br>Kötetcsoport létrehozása<br>Kötetcsoport biztonsági és biztonsági másolatot készül<br>Kötetcsoport szerkesztése<br>Kötetcsoport törlése |[Kötetcsoportok létrehozása és kezelése a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-volume-groups.md) |
| Biztonsági mentési házirend létrehozása <br>Biztonságimentési házirend szerkesztése<br>Biztonságimentési házirend törlése |[Biztonságimentési házirendek létrehozása és kezelése a StorSimple Snapshot Manager segítségével](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ütemezett biztonsági mentési feladatok megtekintése és kezelése<br>Legutóbbi biztonsági mentési feladatok megtekintése és kezelése<br>A jelenleg futó biztonsági mentési feladatok megtekintése és kezelése |[A StorSimple Snapshot Manager segítségével megtekintheti és kezelheti a biztonsági mentési feladatokat](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Kötet visszaállítása<br>Kötet vagy kötetcsoport klónozása<br>Biztonsági másolat törlése<br>Fájl helyreállítása<br>A StorSimple Snapshot Manager adatbázis visszaállítása |[A biztonságimásolat-katalógus kezelése a StorSimple Snapshot Manager segítségével](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>További lépések
[Letöltés StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

