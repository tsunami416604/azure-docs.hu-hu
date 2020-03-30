---
title: Cserélje le a StorSimple 8600 EBOD vezérlő | Microsoft dokumentumok
description: A storSimple 8600-as eszközön az egyik vagy mindkét EBOD-vezérlő eltávolítását és cseréjét ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254884"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>EBOD-vezérlő cseréje a StorSimple eszközön

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan cserélhet le egy hibás EBOD-vezérlőmodult a Microsoft Azure StorSimple-eszközön. Az EBOD vezérlőmodul cseréjéhez a következőket kell tenni:

* A hibás EBOD-vezérlő eltávolítása
* Új EBOD-vezérlő telepítése

A kezdés előtt vegye figyelembe a következő információkat:

* Az üres EBOD modulokat minden nem használt bővítőhelybe be kell illeszteni. A burkolat nem hűl le megfelelően, ha egy nyílás nyitva marad.
* Az EBOD-vezérlő üzem közben cserélhető, és eltávolítható vagy cserélhető. Ne távolítson el egy meghibásodott modult, amíg nincs csere. A cserefolyamat indításakor 10 percen belül be kell fejeznie.

> [!IMPORTANT]
> Mielőtt megpróbálna eltávolítani vagy kicserélni a StorSimple bármely összetevőjét, ellenőrizze, hogy áttekinti-e a [biztonsági ikonra vonatkozó konvenciókat](storsimple-safety.md#safety-icon-conventions) és egyéb [biztonsági óvintézkedéseket.](storsimple-safety.md)

## <a name="remove-an-ebod-controller"></a>EBOD-vezérlő eltávolítása
A storSimple-eszköz ben meghibásodott EBOD vezérlőmodul cseréje előtt győződjön meg arról, hogy a másik EBOD vezérlőmodul aktív és fut. Az alábbi eljárás és a táblázat bemutatja, hogyan távolíthatja el az EBOD vezérlőmodult.

#### <a name="to-remove-an-ebod-module"></a>EBOD modul eltávolítása
1. Nyissa meg az Azure Portalt.
2. Nyissa meg az eszközt, és keresse meg a **Beállítások** > **hardver állapota**, és ellenőrizze, hogy az aktív EBOD vezérlőmodul LED-jének állapota zöld, és a sikertelen EBOD vezérlőmodul LED-je piros.
3. Keresse meg a sikertelen EBOD vezérlőmodult az eszköz hátulján.
4. Távolítsa el az EBOD vezérlőmodult a vezérlőhöz csatlakoztató kábeleket, mielőtt kivenné az EBOD modult a rendszerből.
5. Jegyezze fel a vezérlőhöz csatlakoztatott EBOD vezérlőmodul pontos SAS-portját. Az EBOD modul cseréje után vissza kell állítania a rendszert erre a konfigurációra.
   
   > [!NOTE]
   > Ez általában az A port lesz, amely az alábbi ábrán **állomásként** van megjelölve.
   
    ![Az EBOD vezérlő hátlapja](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **1. ábra** Az EBOD modul háttámnyi része
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Hiba LED |
   | 2 |Teljesítmény LED |
   | 3 |SAS-csatlakozók |
   | 4 |SAS LED-ek |
   | 5 |Soros portok csak gyári használatra |
   | 6 |A port (állomás) |
   | 7 |B port (állomás kimenő) |
   | 8 |C port (csak gyárilag használható) |

## <a name="install-a-new-ebod-controller"></a>Új EBOD-vezérlő telepítése
Az alábbi eljárás és a táblázat bemutatja, hogyan telepíthet egy EBOD vezérlőmodult a StorSimple-eszközbe.

#### <a name="to-install-an-ebod-controller"></a>EBOD-vezérlő telepítése
1. Ellenőrizze az EBOD-eszköz sérülését, különösen az interfész csatlakozóján. Ne telepítse az új EBOD vezérlőt, ha a csapok elhajlottak.
2. Nyitott helyzetben a reteszekkel csúsztassa a modult a burkolatba, amíg a zárak be nem kapcsolódnak.
   
    ![Az EBOD-vezérlő telepítése](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **2. ábra**  Az EBOD vezérlőmodul telepítése
3. Csukja be a zárat. Egy kattanást kell hallania, amint a retesz bekapcsolódik.
   
    ![Felszabadító EBOD retesz](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **3. ábra**  Az EBOD modul reteszének bezárása
4. Csatlakoztasd újra a kábeleket. Pontosan azt a konfigurációt használja, amely a csere előtt volt jelen. A kábelek csatlakoztatásával kapcsolatos részleteket az alábbi ábrán és táblázatban találja.
   
    ![Kábelezze a 4U-s készüléket a tápellátáshoz](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **4. ábra**. Kábelek újracsatlakoztatása
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges burkolat |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Vezérlő 0 |
   | 5 |Vezérlő 1 |
   | 6 |EBOD vezérlő 0 |
   | 7 |EBOD-vezérlő 1 |
   | 8 |EBOD ház |
   | 9 |Energiaelosztó egységek |

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

