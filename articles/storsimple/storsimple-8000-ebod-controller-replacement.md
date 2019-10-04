---
title: A StorSimple 8600-as EBOD-vezérlő cseréje |} A Microsoft Docs
description: Távolítsa el, és cserélje le a StorSimple 8600-as eszközön egyik vagy mindkét EBOD-vezérlő módját ismerteti.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578663"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>A StorSimple eszköz az EBOD-vezérlő cseréje

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan cserélje le a hibás EBOD vezérlő modul a Microsoft Azure StorSimple eszközön. Az EBOD-vezérlő modul cseréjéhez kell tennie:

* Távolítsa el a hibás az EBOD-vezérlő
* Egy új EBOD-vezérlő telepítése

Mielőtt elkezdené, vegye figyelembe a következő információkat:

* Üres EBOD modulok összes nem használt tárolóhelyek kell beszúrni. A ház nem elérésű megfelelően, ha a tárhely marad nyitva.
* Az EBOD-vezérlő ritkáról gyakori elérésű-cserélhető és eltávolított vagy lecserélt. Ne távolítsa el a sikertelen modul, amíg nincs egy helyettesítő. A helyettesítő folyamatot kezdeményez, ha 10 percen belül kell befejezéséhez.

> [!IMPORTANT]
> Távolítsa el vagy cserélje le valamelyik StorSimple összetevő megkísérlése előtt győződjön meg arról, hogy tekintse át a [biztonsági ikon konvenciók](storsimple-safety.md#safety-icon-conventions) és egyéb [biztonsági óvintézkedések](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Az EBOD-vezérlő eltávolítása
A StorSimple eszköz sikertelen EBOD vezérlő moduljának cseréje, előtt ellenőrizze, hogy a többi EBOD vezérlő modul aktív, és fut. Az alábbi eljárást és táblázatot azt ismertetik, hogyan távolítsa el az EBOD-vezérlő modul.

#### <a name="to-remove-an-ebod-module"></a>Az EBOD-moduljának eltávolítása
1. Nyissa meg az Azure Portalt.
2. Nyissa meg az eszközt, és navigáljon a **beállítások** > **hardverállapot**, és ellenőrizze, hogy a LED-jét az aktív EBOD-vezérlő modult állapota zöld, és a LED-jét a sikertelen az EBOD-vezérlő modul az piros.
3. Keresse meg a sikertelen EBOD vezérlő modul hátulján az eszköz található.
4. Távolítsa el a kábelek, amely az EBOD-vezérlő modul csatlakozni a vezérlő előtt az EBOD-modul a rendszerből.
5. Jegyezze fel a pontos SAS-port a tartományvezérlőre, amely a csatlakoztatott EBOD-vezérlő modul. Az EBOD modul cseréje után állíthatja vissza a rendszer ezt a konfigurációt kell adnia.
   
   > [!NOTE]
   > Általában ez a Port, amely van jelölve lesz **üzemeltethetnek** a következő diagram mutatja.
   
    ![Csatlakozópanel meghibásodása az EBOD-vezérlő](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **1. ábra** vissza az EBOD-modul
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Tartalék LED |
   | 2 |Energiagazdálkodási LED |
   | 3 |SAS-összekötők |
   | 4 |SAS LEDs |
   | 5 |Csak a gyári használatra soros portok |
   | 6 |Port egy (a gazdagép) |
   | 7 |B port (gazda ki) |
   | 8 |Port C (csak gyári használata) |

## <a name="install-a-new-ebod-controller"></a>Egy új EBOD-vezérlő telepítése
Az alábbi eljárást és táblázatot ismertetik a StorSimple-eszköz az EBOD-vezérlő modul telepítése.

#### <a name="to-install-an-ebod-controller"></a>Az EBOD-vezérlő telepítése
1. Ellenőrizze az EBOD eszköz kárt, különösen az adapter-összekötő számára. Ne telepítse az új EBOD-vezérlő, ha bármely elgörbülve.
2. A zárolás van életben nyitva húzza az ujját a modul a ház, mindaddig, amíg a zárolás van életben léphet.
   
    ![Az EBOD-vezérlő telepítése](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **2. ábra** az EBOD-vezérlő modul telepítése
3. Zárja be a zárolás. Egy-két kattintással kell hall, a zárolás bekövetkezése várható.
   
    ![EBOD zárolás feloldása](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **3. ábra** a EBOD modul zárolás bezárása
4. Újracsatlakozás a kábelek. Használjon, amely a cseréje előtt szerepelt pontos konfigurációját. Tekintse meg a következő ábra és csatlakoztassa a kábeleket részleteit a táblában.
   
    ![Az energiagazdálkodási 4U eszköz kábelezése](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **4. ábra**. Újracsatlakozás a kábelek
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Vezérlő 0 |
   | 5 |Vezérlő 1 |
   | 6 |Az EBOD-vezérlő 0 |
   | 7 |Az EBOD-vezérlő 1 |
   | 8 |EBOD ház |
   | 9 |Áramelosztó egységek |

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

