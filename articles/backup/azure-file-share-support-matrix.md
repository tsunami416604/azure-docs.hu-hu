---
title: Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa
description: Az Azure-fájlmegosztás biztonsági mentésekor a támogatási beállítások és korlátozások összegzését tartalmazza.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195689"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa

Az Azure-fájlmegosztás biztonsági mentését a [Azure Backup szolgáltatással](https://docs.microsoft.com/azure/backup/backup-overview) végezheti el. Ez a cikk összefoglalja a támogatási beállításokat, amikor biztonsági mentést készít az Azure-fájlmegosztás Azure Backup.

## <a name="supported-geos"></a>Támogatott térségek

Az Azure-fájlmegosztás biztonsági mentése a következő térségek érhető el:

| GA-régiók | Támogatott régiók, de nem GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Délkelet-Ausztrália, Közép-Kanada (CNC), USA nyugati középső régiója (WCUS), USA 2. nyugati régiója (WUS 2), Dél-India (INS), USA északi középső régiója (NCUS), Kelet-Japán (JPE), Dél-Brazília (BRS), Dél-Kelet-Ázsia (tenger)                                                     |Kelet-Ausztrália (AE), Kelet-Kanada (CE), Kelet-Ázsia (EA), USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), Nyugat-Japán (JPW), India Central (INC), Korea középső régiója (KRC), Dél-Korea (KRS), Észak-Európa (NE), az USA déli középső régiója (SCUS), Egyesült Királyság déli régiója (UKS), Egyesült Királyság nyugati régiója (UKW), Nyugat-Európa (mi), USA nyugati régiója (WUS), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov Virginia (UGV , India nyugati régiója (INW), Dél-Afrika északi régiója, Egyesült Arab Emírségek, Észak (karbamid), Közép-Franciaország (FRC), Észak-Németország (GN), Középnyugat-Németország (GWC), Dél-Afrika nyugati régiója (SAW), Egyesült Arab Emirátusok (UAC), Kelet-Norvégia (NAZT), Norvégia nyugati régiója (NWW), Észak-Svájc (SZN)           |

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

| Storage-fiók adatai | Támogatás                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Fiók típusa            | A Azure Backup támogatja az általános célú v1, általános célú v2 és fájlmegosztás típusú Storage-fiókokban lévő Azure-fájlmegosztás használatát |
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
