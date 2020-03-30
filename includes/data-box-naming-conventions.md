---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244630"
---
| Entitás                                       | Konvenciók   |
|----------------------------------------------|-----------------------------------------------------|
| Tárolónevek a blokkblobhoz és a lapblobhoz | Érvényes, 3–63 karakter hosszú DNS-névnek kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A nevekben nem engedélyezettek egymást követő kötőjelek. |
| Azure-fájlok nevének megosztása                  | Lásd fent                                          |
| Azure-fájlok könyvtár- és fájlnevei     |<li> A kis- és nagybetűk megőrzése, a kis- és nagybetűk megkülönböztetése, és nem haladhatja meg a 255 karakter hosszúságot. </li><li> Nem végződhet a perjellel (/). </li><li>Ha rendelkezésre áll, akkor automatikusan törlődik. </li><li> A következő karakterek nem engedélyezettek:<code>" \\ / : \| < > * ?</code></li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> Az URL-elérési út érvénytelen karakterei nem engedélyezettek. Az uE000-hez hasonló \\kódpontok nem érvényes Unicode karakterek. Egyes ASCII- vagy Unicode karakterek, például vezérlőkarakterek (0x00–0x1F, \\u0081 stb.) szintén nem engedélyezettek. A HTTP/1.1-ben található Unicode karakterláncokra vonatkozó szabályokat lásd az RFC 2616 2.2 szakasz: Alapszabályok és RFC 3987. </li><li> A következő fájlnevek nem engedélyezettek: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.) és két pontkarakter (..).</li>|
| Blobnevek blokkblob és lapblob esetén      | </li><li>A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak </li><li>A blob nevének 1–1024 karakter hosszúságúnak kell lennie. </li><li>A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti sztringek, amelyek megegyeznek egy virtuális könyvtár nevével.</li> |
