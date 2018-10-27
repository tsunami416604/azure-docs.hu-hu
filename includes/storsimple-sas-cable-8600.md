---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 792589f4aa2a80c05378224ffe4e4d1dad2f935c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164353"
---
<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>A SAS-kábel csatolása
1. Azonosítsa az elsődleges és a EBOD mindegyikéből. A két házak azonosíthatók azok megfelelő vissza síkok megtekintésével. Lásd az alábbi képen útmutatást. 
   
    ![Biztonsági elsődleges adatsík és EBOD ház](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Biztonsági másolatot az elsődleges és a EBOD ház nézete**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |EBOD ház |
2. Keresse meg a sorozatszámokat, az elsődleges és a EBOD mindegyikéből. A sorozatszám matricát a háttérrendszer fülön az összes rendszerházon rögzítettek. A sorozatszámokat mindkét ház azonosnak kell lennie. [Forduljon a Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) azonnal, ha a sorozatszám nem egyezik. Tekintse meg az alábbi ábrán a sorozatszám megkereséséhez.
   
    ![Rendszerház sorozatszáma megjelenítő hátsó nézete](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Sorozatszám matricát helye**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |A ház, sszes törlése |
3. A megadott SAS-kábel használatával a EBOD ház csatlakozhat az elsődleges ház módon:
   
   1. Azonosítsa az elsődleges ház és a EBOD ház négy SAS-porttal. A SAS-porttal EBOD a elsődleges tárolóeszközön van jelölve, és a port a EBOD tárolóeszközön felel meg a SAS-kábelezés ábrán is látható, alább látható módon.
   2. A megadott SAS-kábel segítségével csatlakozzon az EBOD-port a port A.
   3. Az EBOD 0. vezérlő portja csatlakozniuk kell az EBOD-vezérlő 0 port A. 1. vezérlő EBOD portját a port a az EBOD-vezérlő 1 csatlakozniuk. Tekintse meg a következő ábra útmutatást. 
      
      ![Az eszköz kábelezése SAS](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kábelezés**
      
      | Címke | Leírás |
      |:--- |:--- |
      | A |Elsődleges ház |
      | B |EBOD ház |
      | 1 |0. vezérlő |
      | 2 |1. vezérlő |
      | 3 |Az EBOD-vezérlő 0 |
      | 4 |Az EBOD-vezérlő 1 |
      | 5, 6 |SAS-portot az elsődleges lemezház (EBOD címkézett) |
      | 7, 8 |EBOD lemezház (Port A) a SAS-porttal |

