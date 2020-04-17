---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536435"
---
| Erőforrás | Szabványos fájlmegosztások | Prémium fájlmegosztások |
|----------|---------------|------------------------------------------|
| A fájlmegosztás minimális mérete | Nem minimum; fizetni, ahogy megy | 100 GiB; Kiépítve |
| A fájlmegosztás maximális mérete | 100 TiB*, 5 TiB | 100 TiB |
| Fájlmegosztásban lévő fájl maximális mérete | 1 TiB | 1 TiB |
| A fájlmegosztásban lévő fájlok maximális száma | Nincs korlátozás | Nincs korlátozás |
| Maximális IOPS részvényenként | 10 000 IOPS*, 1000 IOPS | 100 000 IOPS |
| A tárolt hozzáférési házirendek maximális száma fájlmegosztásonként | 5 | 5 |
| Egyetlen fájlmegosztás átviteli átviteli célja | legfeljebb 300 MiB/sec*, legfeljebb 60 MiB/mp,  | Tekintse meg a prémium fájlmegosztási be- és kilépési értékeket|
| Egyetlen fájlmegosztás maximális kimenő árfolyama | Lásd: normál fájlmegosztási célátviteli-alap | Akár 6204 MiB/s |
| Egyetlen fájlmegosztás maximális beutazása | Lásd: normál fájlmegosztási célátviteli-alap | Akár 4136 MiB/s |
| Fájlonkénti maximális megnyitott leírók | 2000 nyitott fogantyú | 2000 nyitott fogantyú |
| Megosztási pillanatképek maximális száma | 200 megosztási pillanatkép | 200 megosztási pillanatkép |
| Az objektum (könyvtárak és fájlok) maximális nevének hossza | 2048 karakter | 2048 karakter |
| A elérési út maximális összetevője (az \A\B\C\D elérési úton minden betű egy összetevő) | 255 karakter | 255 karakter |

\*A szabványos fájlmegosztások alapértelmezett száma 5 TiB, [lásd: Nagy fájlmegosztások engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md) a szabványos fájlmegosztások 100 TiB-ig történő növelésének részleteiről.
