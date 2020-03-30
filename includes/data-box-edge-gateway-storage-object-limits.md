---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179572"
---
Az alábbiakban az Azure-objektumok megírható méretei láthatók. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek ezeknek a korlátoknak.

| Azure-objektumtípusa | Feltöltési korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blob blokkolása        | ~ 4,75 TB                                                 |
| Lapblob         | 1 TB <br> A Lapblob formátumban feltöltött összes fájlnak 512 bájtnak kell lennie (szerves többszörös), különben a feltöltés sikertelen lesz. <br> A VHD és a VHDX 512 bájt tal vannak elrendezve. |
| Azure Files         | 1 TB <br> A Lapblob formátumban feltöltött összes fájlnak 512 bájtnak kell lennie (szerves többszörös), különben a feltöltés sikertelen lesz. <br> A VHD és a VHDX 512 bájt tal vannak elrendezve. |

> [!IMPORTANT]
> A fájlok létrehozása (a tárolási típustól függetlenül) legfeljebb 5 TB megengedett. Ha azonban olyan fájlt hoz létre, amelynek mérete nagyobb, mint az előző táblázatban megadott feltöltési korlát, a fájl feltöltése nem történik meg. A hely visszaszerzéséhez manuálisan kell törölnie a fájlt.