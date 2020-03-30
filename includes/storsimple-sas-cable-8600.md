---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179153"
---
#### <a name="to-attach-the-sas-cables"></a>A SAS-kábelek csatlakoztatása
1. Azonosítsa az elsődleges és az EBOD-házakat. A két burkolat a megfelelő hátsó síkok vizsgálatával azonosítható. Az alábbi képen útmutatást talál. 
   
    ![Az elsődleges és az EBOD-házak hátsó síkja](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Az elsődleges és az EBOD-házak háttérnézete**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges burkolat |
   | 2 |EBOD ház |
2. Keresse meg a sorozatszámokat az elsődleges és az EBOD-házak. A sorozatszám matricát minden ház hátsó fülére rögzítik. A sorozatszámnak mindkét burkolaton azonosnak kell lennie. Ha a sorozatszámok nem egyeznek, azonnal forduljon a [Microsoft támogatási szolgálatához.](../articles/storsimple/storsimple-contact-microsoft-support.md) A sorozatszámok megkereséséhez lásd az alábbi ábrát.
   
    ![A ház hátsó nézete sorozatszámmal](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **A sorozatszámmatricán való elhelyezés**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |A kamra füle |
3. A mellékelt SAS-kábelek segítségével csatlakoztassa az EBOD házat az elsődleges házhoz az alábbiak szerint:
   
   1. Azonosítsa a négy SAS-portot az elsődleges házon és az EBOD-házon. A SAS-portok EBOD-ként vannak címkézve az elsődleges házon, és megfelelnek az EBOD-ház A portjának, ahogy az az alábbi SAS-kábelezési ábrán látható.
   2. Használja a megadott SAS-kábeleket az EBOD port és az A port csatlakoztatásához.
   3. A 0-as vezérlő EBOD-portját a 0 eBOD-vezérlő A portjához kell csatlakoztatni. Az 1-es vezérlő EBOD-portját az 1-es EBOD-vezérlő A portjához kell csatlakoztatni. Az útmutatást az alábbi ábrán talál. 
      
      ![SAS-kábelezés a készülékhez](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS kábelezés**
      
      | Címke | Leírás |
      |:--- |:--- |
      | A |Elsődleges burkolat |
      | B |EBOD ház |
      | 1 |Vezérlő 0 |
      | 2 |Vezérlő 1 |
      | 3 |EBOD vezérlő 0 |
      | 4 |EBOD vezérlő 1 |
      | 5, 6 |SAS-portok az elsődleges házon (eBOD címkével) |
      | 7, 8 |SAS-portok az EBOD-házon (A port) |

