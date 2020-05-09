---
title: Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa
description: Az Azure-fájlmegosztás biztonsági mentésekor a támogatási beállítások és korlátozások összegzését tartalmazza.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 4da17bb591e94a0eaf26f95210a3e841ad17973b
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890727"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági mentésének támogatási mátrixa

Az Azure-fájlmegosztás biztonsági mentését a [Azure Backup szolgáltatással](https://docs.microsoft.com/azure/backup/backup-overview) végezheti el. Ez a cikk összefoglalja a támogatási beállításokat, amikor biztonsági mentést készít az Azure-fájlmegosztás Azure Backup.

## <a name="supported-geos"></a>Támogatott térségek

Az Azure-fájlmegosztás biztonsági mentése a következő térségek érhető el:

| GA-régiók | Támogatott régiók (az előzetes verzió részeként), de még nem                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Délkelet-Ausztrália, Közép-Kanada (CNC), USA nyugati középső régiója (WCUS), USA 2. nyugati régiója (WUS 2), Dél-India (INS), USA északi középső régiója (NCUS), Kelet-Japán (JPE), Dél-Brazília (BRS), Dél-Kelet-Ázsia (tenger), Nyugat-Svájc (SZW), Egyesült Arab Emírségek középső (UAC), Kelet-Japán (nazt), India nyugati régiója (Egyesült Királyság nyugati régiója INW), Ausztrália középső régiója (ACL), Korea középső régiója (KRC), Dél-Afrika , Dél-Korea (KRS), Észak-Németország (GN), Norvégia nyugati régiója (NWW), Dél-Afrika nyugati régiója (SAW), Észak-Svájc (SZN), Középnyugat-Németország (GWC), UAE North (karbamid), Közép-Franciaország (FRC), India középső régiója (INC), Kelet-Kanada (CNE), Kelet-Ázsia (EA), Kelet-Ausztrália (AE), Közép-USA                                                  |  USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), Észak-Európa (NE), az USA déli középső régiója (SCUS), Egyesült Királyság déli régiója (UKS), Nyugat-Európa (WE), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov Virginia (UGV)           |

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

| Storage-fiók adatai | Támogatás                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Fiók típusa            | A Azure Backup támogatja az általános célú v1, általános célú v2 és file Storage típusú Storage-fiókokban lévő Azure-fájlmegosztás használatát |
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
