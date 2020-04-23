---
title: Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa
description: Az Azure-fájlmegosztás biztonsági mentésekor a támogatási beállítások és korlátozások összegzését tartalmazza.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: e74d04cf8ae9010a860b8467d0de771524bd3f3a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103203"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa

Az Azure-fájlmegosztás biztonsági mentését a [Azure Backup szolgáltatással](https://docs.microsoft.com/azure/backup/backup-overview) végezheti el. Ez a cikk összefoglalja a támogatási beállításokat, amikor biztonsági mentést készít az Azure-fájlmegosztás Azure Backup.

## <a name="supported-geos"></a>Támogatott térségek

Az Azure-fájlmegosztás biztonsági mentése a következő térségek érhető el:

| GA-régiók | Támogatott régiók, de nem GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Kelet-Ausztrália (AE), Közép-Kanada (CNC), USA nyugati középső régiója (WCUS), Dél-India (INS), USA északi középső régiója (NCUS), Kelet-Japán (JPE), Dél-Brazília (BRS)                                                     |Kelet-Ausztrália, Délkelet-Kanada (CE), Közép-USA (ke), Kelet-Ázsia (EA), USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), Nyugat-Japán (JPW), Közép-India (INC), Dél-Korea (KRC), Dél-Korea (KRS), Észak-Európa (nem), az USA déli középső régiója (SCUS), South Kelet-Ázsia (Sea), Egyesült Királyság déli régiója (UKs), Egyesült Királyság nyugati régiója (Ukw), Nyugat-Európa (mi), USA nyugati régiója (WUS), US gov Arizona (UGA US gov Texas , US Gov Virginia (UGV), Ausztrália középső régiója (ACL), Nyugat-India (INW), Dél-Afrika északi régiója (SAN), Egyesült Arab Emírségek, Észak (karbamid), Közép-Franciaország (FRC), Észak-Németország (GN), Középnyugat-Németország (GWC), Dél-Afrika nyugati régiója (SAW), Egyesült Arab Emírségek középső régiója (UAC), Norvégia keleti régiója (NWW 2) Észak-Svájc             |

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

| Storage-fiók adatai | Támogatás                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Fiók típusa            | Azure Backup támogatja az Azure-fájlmegosztást az általános célú v1-ben és az általános célú v2 Storage-fiókokban |
| Teljesítmény              | A Azure Backup a standard és a Premium Storage fiókban egyaránt támogatja a fájlmegosztást |
| Replikáció              | A Storage-fiókokban bármely replikációs típussal rendelkező Azure Files-megosztás támogatott |

## <a name="supported-file-shares"></a>Támogatott fájlmegosztás

| Fájlmegosztás típusa                                   | Támogatás   |
| -------------------------------------------------- | --------- |
| Standard                                           | Támogatott |
| Nagy                                              | Támogatott |
| Prémium                                            | Támogatott |
| Az Azure file Sync szolgáltatással összekapcsolt fájlmegosztás | Támogatott |

## <a name="protection-limits"></a>Védelmi korlátok

| Beállítás                                                      | Korlát |
| ------------------------------------------------------------ | ----- |
| A felhasználónként naponta védhető fájlmegosztás maximális száma | 200   |
| A tár naponta regisztrálható tárolási fiókok maximális száma | 50    |

## <a name="backup-limits"></a>Biztonsági mentési korlátok

| Beállítás                                      | Korlát |
| -------------------------------------------- | ----- |
| Igény szerinti biztonsági másolatok maximális száma naponta | 4     |
| Ütemezett biztonsági mentések maximális száma naponta | 1     |

## <a name="restore-limits"></a>Visszaállítási korlátok

| Beállítás                                                      | Korlát   |
| ------------------------------------------------------------ | ------- |
| Napi visszaállítások maximális száma                           | 10      |
| Fájlok maximális száma visszaállításkor                         | 10      |
| A nagyméretű fájlmegosztás esetében a maximálisan ajánlott visszaállítási méret | 15 TiB |

## <a name="retention-limits"></a>Megőrzési korlátok

| Beállítás                                                      | Korlát    |
| ------------------------------------------------------------ | -------- |
| A fájlmegosztás összes helyreállítási pontjának maximális száma az adott időpontban | 200      |
| Igény szerinti biztonsági mentés által létrehozott helyreállítási pont maximális megőrzése | 10 év |
| A napi helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén| 200 nap |
| A heti helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 200 hét |
| A havi helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 120 hónap |
| Az éves helyreállítási pontok (Pillanatképek) maximális megőrzése fájlmegosztás esetén | 10 év |

## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszerek

| Restore metódus     | Részletek                                                      |
| ------------------ | ------------------------------------------------------------ |
| Teljes megosztás visszaállítása | A teljes fájlmegosztást visszaállíthatja az eredeti vagy egy másik helyre. |
| Elemszintű visszaállítás | Az egyes fájlokat és mappákat az eredetire vagy egy másik helyre állíthatja vissza |

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést az Azure file shares](backup-afs.md) -ről
* Ismerje meg, hogyan [állíthatja vissza az Azure-fájlmegosztást](restore-afs.md)
* Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait](manage-afs-backup.md)
