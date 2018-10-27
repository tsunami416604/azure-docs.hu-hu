---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166404"
---
| **Korlátazonosító** | **Korlát** | **Megjegyzések** |
| --- | --- | --- |
| Teljes kapacitás (beleértve a felhő) |Akár 64 TB-os virtuális eszközönként |Is átadja a feladatokat egy teljes StorSimple Virtual Array egy másik üres tömb. Ha megpróbál visszaállítani ugyanarra az eszközre, győződjön meg arról, hogy rendelkezik-e elegendő hely a művelet végrehajtásához az eszközön. Miután 32 TB-os túllépte, nem állítható vissza ugyanazon az eszközön. |
| Tárfiók hitelesítő adatainak eszközönként maximális száma |1 | |
| Kötetek/megosztások maximális száma |16 | |
| A rétegzett megosztás legkisebb mérete |500 GB | |
| A rétegzett kötetek minimális mérete |500 GB | |
| A rétegzett megosztás maximális mérete |20 TB-IG | |
| A rétegzett kötetek maximális mérete |5 TB | |
| Egy helyileg rögzített megosztási minimális mérete |50 GB | |
| Helyileg rögzített kötet minimális mérete |50 GB | |
| Egy helyileg rögzített fájlmegosztás maximális mérete |2 TB | |
| Egy helyileg rögzített kötet maximális mérete |200 GB | |
| Az iSCSI kezdeményezők kapcsolatok maximális száma |512 | |
| Eszközönként hozzáférés-vezérlési rekordok maximális száma |64 | |
| Megőrzi a virtuális eszköz a biztonsági mentések maximális száma *.backups* fájlkiszolgáló mappa |5 |Ez magában foglalja a legutóbbi ütemezett (az alapértelmezett biztonsági mentési szabályzat által létrehozott) és a manuális biztonsági mentéseket. |
| Az eszköz által megőrzött ütemezett biztonsági mentések maximális száma |55 |30 napi biztonsági mentésekhez<br>12 havi biztonsági mentések<br>13 éves biztonsági mentések |
| Az eszköz által megőrzött manuális biztonsági mentések maximális száma |45 | |
| Egy fájlkiszolgáló jutó fájlok maximális száma |1 millió |Egy eszköz-visszaállítás végrehajtásakor a helyreállítási időt az olyan fájlok száma arányos az eszközön a megosztásokat. |
| Az iSCSI-kiszolgálóval kötetenként fájlok maximális száma |1 millió | |
| Virtuális tömbönként fájlok maximális száma |4 millió | |
| Visszaállítási helyreállítás ideje |Gyors helyreállítás |A visszaállítás hőtérkép alapul, és a kötet méretétől függ.<br>Biztonsági mentési műveletek akkor fordulhat elő, amíg a visszaállítási művelet folyamatban van. |

