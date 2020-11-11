---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: a71762010984928b93c19c7256c2ba4f0fe0f64b
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503875"
---
| Erőforrás | Szabványos fájlmegosztás\* | Prémium fájlmegosztás |
|----------|---------------|------------------------------------------|
| Fájlmegosztás minimális mérete | Nincs minimum; Utólagos fizetés | 100 GiB; kiépítve |
| Fájlmegosztás maximális mérete | 100 TiB \* \* , 5 TIB | 100 TiB |
| Fájlmegosztás maximális mérete | 1 TiB | 4 TiB |
| Fájlmegosztás fájljainak maximális száma | Korlátlan | Korlátlan |
| IOPS maximális száma | 10 000 IOPS \* \* , 1 000 IOPS vagy 100 kérelem a 100ms-ben | 100 000 IOPS |
| Tárolt hozzáférési szabályzatok maximális száma fájlmegosztás esetén | 5 | 5 |
| Cél átviteli sebesség egyetlen fájlmegosztás esetén | akár 300 MiB/s \* \* , akár 60 MIB/s,  | Tekintse meg a prémium szintű fájlmegosztás bejövő és kimenő értékeit|
| Maximális kimenő forgalom egyetlen fájlmegosztás esetén | Tekintse meg a szabványos fájlmegosztás céljának átviteli sebességét | Akár 6 204 MiB/s |
| Egyetlen fájlmegosztás maximális bejövő száma | Tekintse meg a szabványos fájlmegosztás céljának átviteli sebességét | Akár 4 136 MiB/s |
| Fájlok vagy könyvtárak maximálisan megnyitott leírói | 2 000 nyitott fogantyúk | 2 000 nyitott fogantyúk |
| Megosztási Pillanatképek maximális száma | 200 megosztási Pillanatképek | 200 megosztási Pillanatképek |
| A maximális objektum (könyvtárak és fájlok) nevének hossza | 2 048 karakter | 2 048 karakter |
| A maximális elérésiút-összetevő (az elérési út \A\B\C\D, minden betű egy összetevő) | 255 karakter | 255 karakter |
| Rögzített hivatkozás korlátja (csak NFS esetén) | N/A | 178 |

\* A standard fájlmegosztás korlátai a standard fájlmegosztás számára elérhető mindhárom rétegre vonatkoznak: a tranzakció optimalizált, a gyors és a lassú.

\*\* A standard fájlmegosztás alapértelmezett értéke 5 TiB, lásd a [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md) című témakört, amely bemutatja, hogyan növelhető a standard fájlmegosztás mérete akár 100 TiB-ra.
