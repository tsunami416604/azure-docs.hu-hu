---
title: Akkumulátor cseréje a Microsoft Azure StorSimple 8000 sorozatú eszközön
description: Ez a témakör a StorSimple-eszközön lévő tartalék akkumulátormodul eltávolítását, cseréjét és karbantartását ismerteti.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255014"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>A tartalék akkumulátor modul cseréje a StorSimple-eszközön

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple-eszköz elsődleges házteljesítmény- és hűtési modulja (PCM) egy további akkumulátorral rendelkezik. Ez a csomag biztosítja a tápellátást, így a StorSimple eszköz adatokat menthet, ha az elsődleges ház hálózati áramvesztése csökken. Ezt az akkumulátort *tartalék akkumulátormodulnak nevezzük.* A tartalék akkumulátor modul csak a StorSimple eszköz elsődleges házához létezik (az EBOD ház nem tartalmaz tartalék akkumulátormodult).

Ez az oktatóanyag a következőket ismerteti:

* A tartalék akkumulátormodul eltávolítása
* Új tartalék akkumulátormodul telepítése
* A tartalék akkumulátormodul karbantartása

> [!IMPORTANT]
> A tartalék akkumulátormodul eltávolítása és cseréje előtt tekintse át a Biztonsági információkat a Bevezetés a [StorSimple hardverösszetevő-csere című](storsimple-8000-hardware-component-replacement.md)cikkből.


## <a name="remove-the-backup-battery-module"></a>A tartalék akkumulátormodul eltávolítása
A StorSimple eszköz tartalék akkumulátormodulja egy mezőre cserélhető egység. A PCM-be való beszerelés előtt az akkumulátormodult az eredeti csomagolásban kell tárolni. A tartalék akkumulátor eltávolításához hajtsa végre az alábbi lépéseket.

#### <a name="to-remove-the-backup-battery-module"></a>A tartalék akkumulátormodul eltávolítása
1. Az Azure Portalon nyissa meg a StorSimple Eszközkezelő szolgáltatás panel. Nyissa meg az **Eszközök lapot,** és válassza ki az eszközt az eszközök listájából. Nyissa meg a**Hardver állapotának** **figyelése** > lehetőséget. A **Megosztott összetevők csoportban**tekintse meg az akkumulátor állapotát.
2. Azonosítsa azt a PCM-et, amelyben az akkumulátor meghibásodott. Az 1.
   
    ![Az eszköz elsődleges házmoduljainak hátlapja](./media/storsimple-battery-replacement/IC740994.png)
   
    **1. ábra** Az elsődleges eszköz háttekusa PCM- és vezérlőmodulokkal
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
   
    Amint azt a 2. **Battery Fault**
   
    ![Backplane Eszköz PCM monitoring jelző LED-ek](./media/storsimple-battery-replacement/IC740992.png)
   
    **2. ábra** A PCM hátulján a monitorozási jelző LED-ek láthatók
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Hálózati áramkimaradás |
   | 2 |Ventilátor hiba |
   | 3 |Akkumulátor hiba |
   | 4 |PCM – rendben |
   | 5 |DC áramkimaradás |
   | 6 |Az akkumulátor kifogástalan |
3. Ha a PCM-et meghibásodott akkumulátorral szeretné eltávolítani, kövesse a [PCM eltávolítása](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)című részben leírt lépéseket.
4. A PCM eltávolítása után emelje fel és forgassa felfelé az akkumulátormodul fogantyúját az alábbi ábrán látható módon, és húzza fel felé az akkumulátor eltávolításához.
   
    ![Akkumulátor eltávolítása pcm-ből](./media/storsimple-battery-replacement/IC741019.png)
   
    **3. ábra** Az akkumulátor eltávolítása a PCM-ből
5. Helyezze a modult a mezőre cserélhető egység csomagolásába.
6. A hibás egységet a megfelelő szervizelés és kezelés érdekében juttassa vissza a Microsoftnak.

## <a name="install-a-new-backup-battery-module"></a>Új tartalék akkumulátormodul telepítése
Hajtsa végre az alábbi lépéseket a csereakkumulátor-modul telepítéséhez a PCM-ben a StorSimple-eszköz elsődleges burkolatában.

#### <a name="to-install-the-battery-module"></a>Az akkumulátormodul telepítése
1. Helyezze a tartalék akkumulátormodult a megfelelő tájolásba a PCM-ben.
2. Nyomja le az akkumulátormodul fogantyúját egészen a csatlakozó üléséhez.
3. Cserélje ki a PCM-et az elsődleges házban a [StorSimple eszköz energia- és hűtőmodulcseréje](storsimple-8000-power-cooling-module-replacement.md)című irányelv ben található irányelvek betartásával.
4. A csere befejezése után nyissa meg az eszközt, majd nyissa meg a**Hardver állapotának** **figyelése** > az Azure Portalon. Ellenőrizze az akkumulátor állapotát, és ellenőrizze, hogy a telepítés sikeres volt-e. A zöld állapot azt jelzi, hogy az akkumulátor kifogástalan állapotban van.

## <a name="maintain-the-backup-battery-module"></a>A tartalék akkumulátormodul karbantartása
A StorSimple-eszközben a tartalék akkumulátormodul áramellátással biztosítja a vezérlőt áramkimaradás esetén. Lehetővé teszi, hogy a StorSimple eszköz mentse a kritikus adatokat, mielőtt ellenőrzött módon leállna. A PCM-ek két teljesen feltöltött akkumulátorával a rendszer két egymást követő veszteséges eseményt képes kezelni.

Az Azure Portalon a **Hardver állapota** a **Figyelő** panel alatt azt jelzi, hogy az akkumulátor hibásan működik, vagy az élettartam vége közeledik. Az akkumulátor állapotát a **PCM 0 akkumulátora** vagy **a PCM 1 akkumulátora** a **Megosztott összetevők**csoportban jelzi. Ez a panel egy **degradált állapotot** jelenít meg az élettartam vége közeledtével, és **sikertelen** az elért élettartam végén.

> [!NOTE]
> Az akkumulátor **jelentheti, hogy nem sikerült,** ha egyszerűen csak fel kell tölteni.


Ha a **DEGRADED** állapot jelenik meg, a következő lépéseket javasoljuk:

* Előfordulhat, hogy a rendszer áramkimaradást szenvedett, vagy az elemek időszakos karbantartáson estek át. A folytatás előtt 12 órán át figyelje meg a rendszert.
  
  * Ha az állapot 12 órás folyamatos csatlakozás után is **romlik** a hálózati áramhoz, és a vezérlők és a PCM-ek működnek, akkor az akkumulátort ki kell cserélni. A tartalék akkumulátormodul cseréjével [kapcsolatban forduljon](storsimple-8000-contact-microsoft-support.md) a Microsoft támogatási szolgálatához.
  * Ha az állapot 12 óra elteltével rendben lesz, az akkumulátor működőképes, és csak karbantartási díjra volt szüksége.
* Ha nem történt hálózati áramveszteség, és a PCM be van kapcsolva, és hálózati tápellátáshoz csatlakozik, az akkumulátort ki kell cserélni. A tartalék akkumulátormodul megrendeléséhez forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)

> [!IMPORTANT]
> A meghibásodott akkumulátort a nemzeti és regionális előírásoknak megfelelően dobja ki.

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

