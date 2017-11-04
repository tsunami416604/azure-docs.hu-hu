---
title: "Cserélje le a StorSimple eszköz vezérlő |} Microsoft Docs"
description: "Távolítsa el, és cserélje le a StorSimple eszköze egyik vagy mindkét vezérlő modulok ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e25b52b7-60f5-47f3-bffc-6c157d57ab5d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: b8c6ebddef89d48d8121da5777e62e311c234666
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Cserélje le a StorSimple eszköz vezérlő modul
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [cserélje le a StorSimple eszköz vezérlő modul](storsimple-8000-controller-replacement.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan cserélje ki a StorSimple eszköz egyik vagy mindkét vezérlő modulokat. A cikk ismerteti az egyszeres és kettős vezérlő cseréjekor az alapul szolgáló logikai is.

> [!NOTE]
> A vezérlő helyettesítő elvégzése előtt ajánlott mindig frissíteni a vezérlő belső vezérlőprogramjának legújabb verziójára.
> 
> A StorSimple eszköz kárt érdekében ne vegye ki a vezérlő mindaddig, amíg a LED jelennek meg a a következők egyikét:
> 
> * Az összes fény OFF.
> * LED 3 ![zöld pipa ikon](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), és ![piros kereszt ikon](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) villogó vannak, és LED 0 és LED 7 **ON**.
> 
> 

Az alábbi táblázat a támogatott tartományvezérlő esetén.

| Eset | Csere forgatókönyv | Az eljárás alkalmazható |
|:--- |:--- |:--- |
| 1 |Egy tartományvezérlő sikertelen állapotban van, a többi tartományvezérlő kifogástalan és aktív. |[Egyetlen tartományvezérlő helyettesítő](#replace-a-single-controller), ismerteti, amelyek a [egyetlen tartományvezérlő helyettesíti mögötti logika](#single-controller-replacement-logic), valamint a [helyettesítő lépéseket](#single-controller-replacement-steps). |
| 2 |Mindkét vezérlőhöz sikertelen volt, és helyettesítő igényel. A váz, a lemezek és a lemez ház nincs kifogástalan. |[Kettős vezérlő helyettesítő](#replace-both-controllers), ismerteti, amelyek a [kettős vezérlő helyettesíti mögötti logika](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps). |
| 3 |Tartományvezérlők ugyanarra az eszközre, illetve különböző eszközök van cserélve. A váz, a lemezek és a lemez ház nincs kifogástalan. |Egy tárolóhely eltérés figyelmeztető üzenet jelenik meg. |
| 4 |Hiányzik egy tartományvezérlőre, és a többi tartományvezérlő sikertelen lesz. |[Kettős vezérlő helyettesítő](#replace-both-controllers), ismerteti, amelyek a [kettős vezérlő helyettesíti mögötti logika](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps). |
| 5 |Legalább az egyik tartományvezérlők nem járt sikerrel. Az eszköz nem érhető el a soros konzol vagy a Windows PowerShell távoli eljáráshívás keresztül. |[Forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) manuális vezérlő helyettesítő eljárás. |
| 6 |A tartományvezérlők verziónál eltérő, amelynek oka a következő lehet:<ul><li>Tartományvezérlők különböző szoftver verziója szükséges.</li><li>Tartományvezérlők különböző belső vezérlőprogram verziója szükséges.</li></ul> |Ha a tartományvezérlő szoftver verziója nem egyezik, a csere logika észleli, hogy, és frissíti a helyettesítő vezérlőn a szoftververzió.<br><br>Ha a vezérlő belső vezérlőprogramjának következő verziójával különböző, és a régi belső vezérlőprogram-verziója **nem** automatikusan frissíthető, egy figyelmeztető üzenet jelenik meg a klasszikus Azure portálon. Frissítések keresése kell, és telepítse a belső vezérlőprogram-frissítésekre.</br></br>Ha a vezérlő belső vezérlőprogramjának következő verziójával eltérőek, és automatikusan frissíthető a régi belső vezérlőprogram-verziója, a tartományvezérlő helyettesítő logika észleli ezt, és a tartományvezérlő elindulása után a belső vezérlőprogram automatikusan frissül. |

El kell távolítania a vezérlő modulok, ha sikertelen volt. Legalább az egyik a vezérlő modulok sikertelen lehet, ami eredményezhet egyetlen tartományvezérlő helyett vagy kettős vezérlő cserélni. Csere eljárások és azok mögötti logika tekintse át a következőket:

* [Egyetlen vezérlő cseréje](#replace-a-single-controller)
* [Cserélje le mindkét tartományvezérlők](#replace-both-controllers)
* [Távolítsa el a tartományvezérlő](#remove-a-controller)
* [A vezérlő beszúrása](#insert-a-controller)
* [Az eszköz aktív vezérlőjén azonosítása](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy tartományvezérlő, tekintse át a biztonsági információk [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Cserélje le a csak egy vezérlő
A Microsoft Azure StorSimple eszközön két vezérlőket sikertelen volt, hibás vagy hiányzik, szükség csak egy vezérlő. 

### <a name="single-controller-replacement-logic"></a>Egyetlen tartományvezérlő helyettesítő logika
Egyetlen tartományvezérlő helyettesíti el kell távolítani a sikertelen vezérlő. (A többi tartományvezérlőre, az eszköz aktív vezérlőjén.) A helyettesítő tartományvezérlő beszúrásakor a következő műveletek történnek meg:

1. A helyettesítő tartományvezérlő azonnal elindítja a StorSimple eszköz kommunikál.
2. A virtuális merevlemez (VHD) az aktív vezérlőhöz pillanatképe másolódik a helyettesítő vezérlőn.
3. A pillanatkép módosítását, hogy a helyettesítő tartományvezérlő indításakor a virtuális merevlemezről felismerhető lesz készenléti tartományvezérlőként.
4. A módosítások nem fejeződik, a helyettesítő tartományvezérlő megkezdi a készenléti tartományvezérlőjeként.
5. Ha mindkét vezérlőhöz futnak, akkor a fürt online állapotba kerül.

### <a name="single-controller-replacement-steps"></a>Egyetlen tartományvezérlő helyettesítő lépései
Ha a Microsoft Azure StorSimple eszközt a tartományvezérlőinek egyike meghibásodik, kövesse az alábbi lépéseket. (A többi tartományvezérlő active és futtatni kell. Ha mindkét tartományvezérlők sikertelen, vagy hibás működését, folytassa a [kettős vezérlő helyettesítő lépéseket](#dual-controller-replacement-steps).)

> [!NOTE]
> A vezérlő indítsa újra, és az egyetlen tartományvezérlő helyettesítő eljárás teljesen helyreállítása 30 – 45 percig is tarthat. A teljes a teljes eljárást, például a kábelek csatolása ideje körülbelül 2 óra.
> 
> 

#### <a name="to-remove-a-single-failed-controller-module"></a>Egyetlen eltávolítása nem sikerült a vezérlő modul
1. Az Azure klasszikus portál, nyissa meg a StorSimple Manager szolgáltatást, kattintson a **eszközök** fülre, majd az eszközt, hogy a figyelni kívánt nevét.
2. Ugrás a **karbantartási > hardverállapot**. Vezérlő 0 vagy a vezérlő 1 állapotát kell piros, ami azt jelenti, hogy hiba.
   
   > [!NOTE]
   > Egyetlen tartományvezérlő helyettesíti a meghibásodott tartományvezérlőre, mindig egy készenléti tartományvezérlőre.
   > 
   > 
3. 1. ábra és a következő táblázat segítségével keresse meg a sikertelen vezérlő modul.  
   
    ![Az eszköz elsődleges ház modulok Csatlakozópanel](./media/storsimple-controller-replacement/IC740994.png)
   
    **1. ábra** vissza a StorSimple eszköz
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |A vezérlő 0 |
   | 4 |1. vezérlő |
4. Sikertelen a tartományvezérlő távolítsa el a csatlakoztatott hálózati kábeleket adatok portot. Ha olyan 8600 modellt használ, is távolítsa el a SAS-kábel, a tartományvezérlő a EBOD tartományvezérlő-hez.
5. Kövesse a [távolítsa el a tartományvezérlő](#remove-a-controller) eltávolítása nem sikerült a tartományvezérlő. 
6. Telepítse a gyári helyettesítő ugyanaz a tárolóhely, amelyből a hibás vezérlő el lett távolítva. Ezzel elindítja az egyetlen tartományvezérlő helyettesítő logikai. További információkért lásd: [egyetlen tartományvezérlő helyettesítő logika](#single-controller-replacement-logic).
7. Amíg az egyetlen tartományvezérlő helyettesítő logikai megfelelően a háttérben, csatlakozzon újra a kábelek. Ügyeljen arra, csatlakozzon a kábelek ugyanúgy, hogy a csere előtt csatlakoztatva lett.
8. A tartományvezérlő újraindítása után ellenőrizze a **tartományvezérlő állapotát** és a **állapot fürt** a a klasszikus Azure portálon győződjön meg arról, hogy a vezérlő vissza a kifogástalan állapotban van, és a melegtartalék módban van.

> [!NOTE]
> Az eszköz soros konzolon keresztül figyelt, amíg a tartományvezérlő helyreállítja a csere eljárást lásd: Előfordulhat, hogy több újraindítást. Ha a soros konzol menü számára jelenik meg, majd azt jelzi, hogy a csere befejeződött. A menü nem jelenik meg a vezérlő helyettesítő kiindulási két órán belül, ha [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).
>
> Update 4-től kezdődően is használhatja a parancsmag `Get-HCSControllerReplacementStatus` a Windows PowerShell felületen, az eszköz a vezérlő cseréjét állapotának figyelésére.
> 

## <a name="replace-both-controllers"></a>Cserélje le mindkét tartományvezérlők
Ha mindkét a Microsoft Azure StorSimple eszközön nem sikerült, vannak hibásan működik, vagy hiányzik, szükség mindkét tartományvezérlők. 

### <a name="dual-controller-replacement-logic"></a>Kettős vezérlő helyettesítő logika
Kettős vezérlő helyettesíti először távolítsa el a mindkét sikertelen tartományvezérlőket, és helyezze be cserékhez. Ha a két helyettesítő vezérlők szúrja be, a következő műveletek történnek:

1. A helyettesítő tartományvezérlő adatszalagot a 0 a következőket ellenőrzi:
   
   1. Akkor használja a belső vezérlőprogram és a szoftver aktuális verziója?
   2. Ez a fürt része?
   3. A partner tartományvezérlő fut, és ez a fürtözött?
      
      Ha ezek a feltételek egyike teljesül, a tartományvezérlő megkeresi a legújabb napi biztonsági mentés (található, a **nonDOMstorage** S meghajtón). A tartományvezérlő a legújabb pillanatfelvétel a virtuális merevlemez másolja át a biztonsági mentés.
2. A vezérlő adatszalagot a 0 a pillanatkép maga lemezképet használja.
3. Eközben a vezérlő 1 tárolóhelye megvárja-e a vezérlő 0 a lemezképpel végrehajtott telepítéshez befejeződését, majd indítsa el.
4. Vezérlő 0 elindulása után a vezérlő 1 észleli a 0, a tartományvezérlő által létrehozott fürt, amely elindítja az egyetlen tartományvezérlő helyettesítő logikai. További információkért lásd: [egyetlen tartományvezérlő helyettesítő logika](#single-controller-replacement-logic).
5. Ezután mindkét tartományvezérlők fog futni, és a fürt online állapotba kerül.

> [!IMPORTANT]
> Következő kettős vezérlő helyettesíti a StorSimple eszköz konfigurálása után alapvető fontosságú, hogy szánjon egy manuális biztonsági mentés az eszköz. Napi eszköz konfigurációs biztonsági másolatok addig nem által kiváltott, 24 óra eltelte után. Együttműködve [Microsoft Support](storsimple-contact-microsoft-support.md) kézi biztonsági mentési eszköz létrehozásához.
> 
> 

### <a name="dual-controller-replacement-steps"></a>Kettős vezérlő helyettesítő lépései
Ez a munkafolyamat, ha mind a Microsoft Azure StorSimple eszközt a tartományvezérlőinek sikertelen volt. Ez akkor fordulhat esetén egy adatközpontban, amelyben a hűtési rendszer leáll, és emiatt nem sikerül mindkét vezérlőhöz rövid időn belül. Attól függően, hogy a StorSimple eszköz ki van kapcsolva vagy a, és használja-e egy 8600 vagy egy 8100 modell lépéseket különböző szabálykészleteket megadása kötelező.

> [!IMPORTANT]
> A vezérlő indítsa újra, és teljesen helyreállítása kettős vezérlő helyettesítő eljárás 1 óra 45 percig is tarthat. A teljes a teljes eljárást, például a kábelek csatolása ideje körülbelül 2,5 óra.
> 
> 

#### <a name="to-replace-both-controller-modules"></a>Lecseréli a mindkét vezérlő modulok
1. Ha az eszköz ki van kapcsolva, kihagyhatja ezt a lépést, és folytassa a következő lépéssel. Ha az eszköz be van kapcsolva, kapcsolja ki az eszközt.
   
   1. Ha olyan 8600 modellt használ, kapcsolja ki az elsődleges ház első, és ezután kapcsolja ki a EBOD ház.
   2. Várjon, amíg az eszköz teljesen leállt. Az eszköz hátulján összes LED ki lesz.
2. Távolítsa el az adatok portokhoz kapcsolódó összes hálózati kábel. Ha olyan 8600 modellt használ, is távolítsa el a SAS-kábel, amelyhez az elsődleges ház csatlakozni a EBOD ház.
3. A StorSimple eszköz mindkét tartományvezérlők eltávolítása. További információkért lásd: [távolítsa el a tartományvezérlő](#remove-a-controller).
4. Először beszúrása vezérlő 0 gyári váltja fel, és helyezze a vezérlő 1. További információkért lásd: [vezérlő beszúrási](#insert-a-controller). Ezzel elindítja a kettős vezérlő helyettesítő logikát. További információkért lásd: [kettős vezérlő helyettesítő logika](#dual-controller-replacement-logic).
5. Míg a tartományvezérlő helyettesítő logika megfelelően a háttérben, csatlakozzon újra a kábelek. Ügyeljen arra, csatlakozzon a kábelek ugyanúgy, hogy a csere előtt csatlakoztatva lett. Című rész a modell a kábel részletes utasításokat az eszköz [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).
6. A StorSimple eszköz bekapcsolása. Ha olyan 8600 modellt használ:
   
   1. Győződjön meg arról, hogy a EBOD ház első van-e kapcsolva.
   2. Várjon, amíg a EBOD ház fut-e.
   3. Kapcsolja be az elsődleges ház.
   4. Miután az első tartományvezérlő újraindul, és megfelelő állapotban van, a rendszer fog futni.
      
      > [!NOTE]
      > Az eszköz soros konzolon keresztül figyelt, amíg a tartományvezérlő helyreállítja a csere eljárást lásd: Előfordulhat, hogy több újraindítást. A soros konzol menüjében, majd, hogy a csere befejeződött. A menü nem jelenik meg a vezérlő helyettesítő kiindulási 2,5 órán belül, ha [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).
      > 
      > 

## <a name="remove-a-controller"></a>Távolítsa el a tartományvezérlő
A következő eljárással hibás vezérlő modul eltávolításához a StorSimple eszközt.

> [!NOTE]
> Az alábbi ábrák a vezérlő 0 vannak. A vezérlő 1 Ezek akkor állítható vissza korábbi állapotba.
> 
> 

#### <a name="to-remove-a-controller-module"></a>A vezérlő modul eltávolítása
1. A modul zárolás a mutatóujj között görgetőgomb megfogható.
2. Óvatosan nyomja össze a görgetőgomb és mutatóujj együtt, hogy a vezérlő zárolás kiadási.
   
    ![A tartományvezérlő zárolás feloldása](./media/storsimple-controller-replacement/IC741047.png)
   
    **2. ábra** felszabadításával vezérlő zárolás
3. Csúsztassa be a vezérlő kívül a váz leírót a zárolás használja.
   
    ![A késleltetett vezérlő váz kívül](./media/storsimple-controller-replacement/IC741048.png)
   
    **3. ábra** késleltetett kívül a váz vezérlő

## <a name="insert-a-controller"></a>A vezérlő beszúrása
A következő eljárás segítségével a gyári által megadott tartományvezérlő modul telepítése után hibás modul eltávolításához a StorSimple eszköz.

#### <a name="to-install-a-controller-module"></a>A vezérlő modul telepítése
1. Ellenőrizze, hogy van-e a felület összekötőkre károkat. A modul nem telepítése, ha bármelyik az összekötő PIN-kód sérült vagy hajlított.
2. A vezérlő modul csúsztassa be azokat a váz teljesen megjelenik a zárolás közben. 
   
    ![A váz mozgó vezérlő](./media/storsimple-controller-replacement/IC741053.png)
   
    **4. ábra** azokat a váz csúszó vezérlő
3. A vezérlő modulra beszúrni miközben továbbra is a váz azokat a vezérlő modul leküldéses a zárolás bezárása kezdődik. A zárolás vesz részt a vezérlő kívánt helyére.
   
    ![A tartományvezérlő zárolás bezárása](./media/storsimple-controller-replacement/IC741054.png)
   
    **5. ábra** a vezérlő zárolás bezárása
4. Ha a zárolás dokkolása helyen végzett. A **OK** LED most kell lennie.  
   
   > [!NOTE]
   > A tartományvezérlő és aktiválása LED akár 5 percet is igénybe vehet.
   > 
   > 
5. Annak ellenőrzéséhez, hogy a csere sikeres, a klasszikus Azure portálon, Ugrás **eszközök** > **karbantartási** > **hardverállapot**, és Győződjön meg arról, hogy a vezérlő 0 és 1. vezérlő is meg Kifogástalan (állapota zöld színnel).

## <a name="identify-the-active-controller-on-your-device"></a>Az eszköz aktív vezérlőjén azonosítása
Vannak olyan helyzetek, például az első eszköz regisztrációs vagy vezérlő cserélni, igénylő keresse meg az aktív vezérlőhöz egy StorSimple eszközön. Az aktív vezérlő összes lemez belső vezérlőprogram és hálózatkezelési műveletet dolgozza fel. Azonosítsa a aktív az alábbi módszerek bármelyikét használhatja:

* [A klasszikus Azure portál segítségével azonosíthatja az aktív vezérlőhöz](#use-the-azure-classic-portal-to-identify-the-active-controller)
* [A Windows PowerShell-lel segítségével azonosíthatja az aktív vezérlőhöz](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Ellenőrizze a fizikai eszköz azonosíthatja az aktív vezérlőhöz](#check-the-physical-device-to-identify-the-active-controller)

Ezek az eljárások leírását mellett.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>A klasszikus Azure portál segítségével azonosíthatja az aktív vezérlőhöz
A klasszikus Azure portálon lépjen a **eszközök** > **karbantartási**, és görgessen a **tartományvezérlők** szakasz. Itt ellenőrizheti, melyik tartományvezérlő aktív.

![Aktív azonosítsa a klasszikus Azure portálon](./media/storsimple-controller-replacement/IC752072.png)

**6. ábra** az aktív vezérlő megjelenítése a klasszikus Azure portálon

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>A Windows PowerShell-lel segítségével azonosíthatja az aktív vezérlőhöz
Amikor az eszköz soros konzolon keresztül fér hozzá, transzparens üzenet akkor jelenik meg. A szalagcím üzenet például a modell neve, telepített szoftverek verzió vagy fér hozzá a tartományvezérlő állapotának alapvető eszköz-információkat tartalmaz. Az alábbi ábrán egy példa egy szalagcím üzenet:

![Soros szalagcím üzenet](./media/storsimple-controller-replacement/IC741098.png)

**7. ábra** szalagcím üzenet megjelenítő vezérlő 0 aktív

A szalagcím üzenet annak megállapításához, hogy a vezérlő kapcsolódik aktív vagy passzív használhatja.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Ellenőrizze a fizikai eszköz azonosíthatja az aktív vezérlőhöz
Azonosíthatja az aktív vezérlővel, az eszközön, keresse meg a kék LED fent a DATA 5 számozásig port az elsődleges ház hátulján olvasható.

Ha a LED villogó van, a vezérlő aktív és a többi tartományvezérlő készenléti üzemmódban. Használja a következő ábra és táblázat elősegítésére.

![Eszköz elsődleges ház csatlakozópanel rendelkező adatportok](./media/storsimple-controller-replacement/IC741055.png)

**8. ábra** hátsó elsődleges szolgáltatással együtt adatok portok és figyelési LED

| Címke | Leírás |
|:--- |:--- |
| 1-6 |DATA 0 – 5 hálózati portok |
| 7 |Kék LED |

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).

