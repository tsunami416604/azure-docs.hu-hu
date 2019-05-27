---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161285"
---
Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Feltöltési korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 TB                                                 |
| Lapblob         | 1 TB <br> Minden feltöltött Lapblob formátumú fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> A VHD és VHDX igazítva 512 bájt. |
| Azure Files         | 1 TB <br> Minden feltöltött Lapblob formátumú fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> A VHD és VHDX igazítva 512 bájt. |

> [!IMPORTANT]
> Akár 5 TB-os engedélyezve van a fájlok (attól függetlenül, a tárolás típusa) létrehozása. Azonban ha létrehoz egy fájlt, amelynek a mérete meghaladja a feltöltési korlátot, az előző táblázatban definiált, a fájl nem első feltöltve. A fájl a lemezterületet felszabadítása érdekében manuálisan törölnie kell.