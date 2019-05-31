---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244630"
---
| Entitás                                       | Konvenciók   |
|----------------------------------------------|-----------------------------------------------------|
| A tároló nevének a blokkblobok és lapblobok | Egy érvényes DNS-nevet, amely 3 – 63 karakter hosszúságúnak kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> Egymást követő kötőjelet a nevében nem megengedett. |
| Az Azure files szolgáltatáshoz a fájlmegosztások neve                  | Lásd fent                                          |
| Az Azure files szolgáltatáshoz a fájl és könyvtár neve     |<li> Nagybetűket, a kis-és nagybetűket, és nem haladhatja meg a 255 karakter hosszú lehet. </li><li> Nem végződhet perjellel (/). </li><li>Ha meg van adva, akkor automatikusan törlődni fog. </li><li> Nem engedélyezett a következő karaktereket: <code>" \\ / : \| < > * ?</code></li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> Érvénytelen URL-cím elérési út nem szerepelhetnek nullkarakterek. Például pontok Code \\uE000 nem érvényes Unicode-karaktereket. Bizonyos ASCII vagy Unicode-karaktereket, például a vezérlőkaraktereket (0x00 való 0x1F \\u0081, stb.), emellett nem engedélyezett. Szabályok Unicode karakterláncok HTTP/1.1 RFC 2616, szakasz 2.2 lásd: Alapszintű szabályok és RFC 3987. </li><li> Nem engedélyezett a következő: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, pont karakter (.), és két pont karakter (.).</li>|
| Blobnevek blokkblob és lapblob esetén      | </li><li>A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak </li><li>A blob nevének 1–1024 karakter hosszúságúnak kell lennie. </li><li>A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti karakterláncok, amelyek megegyeznek egy virtuális könyvtár nevével.</li> |
