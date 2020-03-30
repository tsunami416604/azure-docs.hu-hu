---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179150"
---
| **Korlátazonosító** | **Korlát** | **Megjegyzések** |
| --- | --- | --- |
| Teljes kapacitás (felhővel együtt) |Akár 64 TB virtuális eszközönként |A teljes StorSimple virtuális tömb egy másik üres tömb. Ha ugyanarra az eszközre próbál visszaállítani, győződjön meg arról, hogy elegendő hely áll rendelkezésre az eszközön a művelet végrehajtásához. A 32 TB túllépés után nem állítható vissza ugyanarra az eszközre. |
| A tárfiók hitelesítő adatainak maximális száma eszközönként |1 | |
| Kötetek/megosztások maximális száma |16 | |
| A többszintű megosztás minimális mérete |500 GB | |
| A többszintű kötet minimális mérete |500 GB | |
| Többszintű megosztás maximális mérete |20 TB | |
| Többszintű kötet maximális mérete |5 TB | |
| Helyileg rögzített megosztás minimális mérete |50 GB | |
| A helyileg rögzített kötet legkisebb mérete |50 GB | |
| Helyileg rögzített megosztás maximális mérete |2 TB | |
| Helyileg rögzített kötet maximális mérete |200 GB | |
| A kezdeményezőitől származó iSCSI-kapcsolatok maximális száma |512 | |
| A hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| A virtuális eszköz által a fájlkiszolgáló *.backups* mappájában megőrzött biztonsági mentések maximális száma |5 |Ez magában foglalja a legutóbbi ütemezett (az alapértelmezett biztonsági mentési házirend által létrehozott) és a manuális biztonsági mentések. |
| Az eszköz által visszatartott ütemezett biztonsági mentések maximális száma |55 |30 napi biztonsági mentés<br>12 havi biztonsági mentés<br>13 éves biztonsági mentés |
| Az eszköz által visszatartott kézi biztonsági mentések maximális száma |45 | |
| Fájlkiszolgáló megosztásonkénti fájljainak maximális száma |1 millió |Eszköz-visszaállítás végrehajtásakor a visszaállítási idő arányos az eszköz összes megosztásán lévő fájlok számával. |
| Az iSCSI-kiszolgáló kötetenkénti fájljainak maximális száma |1 millió | |
| A fájlok maximális száma virtuális tömbenként |4 millió | |
| Visszaállítási idő visszaállítása |Gyors visszaállítás |A visszaállítás a hőtérképen alapul, és a kötet méretétől függ.<br>Biztonsági mentési műveletek akkor is előfordulhatnak, ha a visszaállítási művelet folyamatban van. |

