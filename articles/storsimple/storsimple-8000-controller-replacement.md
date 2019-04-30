---
title: A StorSimple 8000 sorozat az eszközvezérlő cseréje |} A Microsoft Docs
description: Távolítsa el, és cserélje le a StorSimple 8000 sorozatú eszköz az egyik vagy mindkét vezérlő modulok módját ismerteti.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482870"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Cserélje le a StorSimple eszköz vezérlő modul
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan távolítsa el, és cserélje le a StorSimple eszköz egyik vagy mindkét vezérlő modulokat. A cikk ismerteti az alapul szolgáló logikai az egyszeres és kettős vezérlő cseréje forgatókönyvek esetén is.

> [!NOTE]
> A vezérlő cseréje előtt, azt javasoljuk, hogy Ön mindig a vezérlő belső vezérlőprogramjának frissítéséhez a legújabb verzióra.
> 
> A StorSimple-eszköz sérülés elkerülése ne vegye ki a vezérlő mindaddig, amíg a LED-ek láthatók a következők egyike lehet:
> 
> * Minden lámpa KAPCSOLVA.
> * LED 3, ![zöld pipa ikon](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), és ![Red közötti ikon](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) villogó vannak, és LED 0 és LED 7 **ON**.


Az alábbi táblázat a támogatott vezérlő cseréje eseteit mutatja be.

| Eset | Csere forgatókönyv | Az eljárás alkalmazható |
|:--- |:--- |:--- |
| 1 |Egy tartományvezérlő sikertelen állapotban van, a többi vezérlő kifogástalan állapotú és aktív. |[Egyetlen tartományvezérlő helyettesítő](#replace-a-single-controller), melyik leírás fedi le a [egyetlen tartományvezérlő a csere mögötti logika](#single-controller-replacement-logic), valamint a [helyettesítő lépéseket](#single-controller-replacement-steps). |
| 2 |Mindkét vezérlő sikertelen volt, és helyettesítő igényelnek. A váz, az adatlemezeket és a lemezes olyan kifogástalan állapotú. |[Kettős vezérlő cseréje](#replace-both-controllers), melyik leírás fedi le a [kettős vezérlő helyett mögötti logika](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps). |
| 3 |A tartományvezérlők vagy a különböző eszközök ugyanarra az eszközre vannak cserélni. A váz, az adatlemezeket és a lemez ház olyan kifogástalan állapotú. |A tárolóhely verzióeltérési riasztás üzenet fog megjelenni. |
| 4 |Hiányzik egy tartományvezérlőre, és a többi tartományvezérlő sikertelen lesz. |[Kettős vezérlő cseréje](#replace-both-controllers), melyik leírás fedi le a [kettős vezérlő helyett mögötti logika](#dual-controller-replacement-logic), valamint a [helyettesítő lépéseket](#dual-controller-replacement-steps). |
| 5 |Legalább az egyik vezérlő sikertelen volt. Az eszköz nem érhető el a soros konzol vagy a Windows PowerShell távoli eljáráshívás keresztül. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) manuális vezérlő cseréje eljárás. |
| 6 |A tartományvezérlők van egy másik build verziót, amely előfordulhat, hogy a következő lehet:<ul><li>Vezérlők különböző szoftver verziója szükséges.</li><li>Vezérlők különböző belső vezérlőprogram verziója szükséges.</li></ul> |Ha a tartományvezérlő szoftver verziója nem egyezik, a csere logika észleli, hogy, és frissíti a szoftververzió a helyettesítő tartományvezérlő.<br><br>Ha a tartományvezérlő belsővezérlőprogram-verziói különböző, és a belső vezérlőprogram régi verzió **nem** automatikusan frissíthető, egy figyelmeztető üzenet jelenik meg az Azure Portalon. Frissítések keresése kell, és a belső vezérlőprogram frissítéseinek telepítése.</br></br>A vezérlő belső vezérlőprogramjának verziója különböző, és a régi belső vezérlőprogram verziója automatikusan frissíthető, ha a vezérlő cseréje logika észleli ezt, és a vezérlő indítása után a belső vezérlőprogram automatikusan frissül. |

El kell távolítania egy vezérlő modult, ha sikertelen volt. Legalább az egyik a vezérlő modulok sikertelen lehet, ami eredményezhet egyetlen tartományvezérlő helyett vagy kettős vezérlő cseréje. A csere eljárások és azok mögötti logika tekintse meg a következőket:

* [Egyetlen vezérlő cseréje](#replace-a-single-controller)
* [Mindkét vezérlő cseréje](#replace-both-controllers)
* [A vezérlő eltávolítása](#remove-a-controller)
* [Egy tartományvezérlő beszúrása](#insert-a-controller)
* [Az eszközön az aktív vezérlő megkeresése](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Mielőtt eltávolítása és a egy vezérlőt, és tekintse át a biztonsági információkat a [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Egyetlen vezérlő cseréje
Ha a Microsoft Azure StorSimple eszközön a két vezérlőn egyike sikertelen volt, hibásan működik, vagy hiányzik, kell egyetlen vezérlő cseréje.

### <a name="single-controller-replacement-logic"></a>Egyetlen tartományvezérlő helyettesítő logika
Az egyetlen tartományvezérlő helyett el kell távolítania a hibás vezérlők. (A többi vezérlő az eszközön az aktív vezérlőt a.) Ha a helyettesítő tartományvezérlő, a következő műveletek történnek:

1. A helyettesítő tartományvezérlő azonnal elindítja a StorSimple-eszközhöz való kommunikáció során.
2. Pillanatkép a virtuális merevlemez (VHD) az aktív vezérlőt a helyettesítő tartományvezérlő másolódik.
3. A pillanatkép módosítását, hogy a csere vezérlő elindításakor a VHD-ből felismerhető lesz, a készenléti állapotban lévő vezérlőnek.
4. Amikor a módosításokat végzett, a helyettesítő tartományvezérlő kezdetben a készenléti állapotban lévő vezérlőnek.
5. Ha mindkét vezérlő futtatja, a fürt online állapotba kerül.

### <a name="single-controller-replacement-steps"></a>Egyetlen tartományvezérlő helyettesítő lépések
Ha az egyik vezérlő a Microsoft Azure StorSimple-eszköz nem sikerül, kövesse az alábbi lépéseket. (A többi vezérlő kell aktív és futtatni. Ha mindkét vezérlő sikertelen vagy hibás működésével, nyissa meg [kettős vezérlő cseréje lépéseket](#dual-controller-replacement-steps).)

> [!NOTE]
> A vezérlő újraindítása, és az egyetlen tartományvezérlő helyettesítő eljárás teljesen helyreállítása 30 – 45 percig is eltarthat. A teljes folyamat fordított időt a kábelek csatolása való körülbelül 2 óra.


#### <a name="to-remove-a-single-failed-controller-module"></a>Egyetlen eltávolítása sikertelen volt a vezérlő modul
1. Az Azure Portalon keresse meg a StorSimple-Eszközkezelő szolgáltatást, kattintson a **eszközök**, és kattintson a figyelni kívánt eszköz nevét.
2. Lépjen a **figyelő > hardverállapot**. 0. vezérlő vagy a vezérlő 1 állapotát kell piros, ami egy hibát jelez.
   
   > [!NOTE]
   > Az egyetlen tartományvezérlő helyettesíti a hibás vezérlők, mindig a készenléti állapotban lévő vezérlőnek.
   
3. 1. ábra és a következő táblázat segítségével keresse meg a hibás vezérlők modul.
   
    ![Az eszköz elsődleges ház modulok csatlakozópanel meghibásodása](./media/storsimple-controller-replacement/IC740994.png)
   
    **1. ábra** vissza a StorSimple-eszköz
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |0. vezérlő |
   | 4 |1. vezérlő |
4. A hibás tartományvezérlőre távolítsa el az összes csatlakoztatott hálózati kábel adatok portot. Ha egy 8600-as modellt használja, is távolítsa el a SAS-kábel, amely a vezérlő csatlakozni az EBOD-vezérlő.
5. Kövesse a [egy vezérlő eltávolítása](#remove-a-controller) a hibás vezérlők eltávolítása.
6. Telepítse a gyári helyettesítő ugyanaz a tárolóhely, amelyről a hibás vezérlők el lett távolítva. Ez elindítja az egyetlen tartományvezérlő helyettesítő logikát. További információkért lásd: [egyetlen vezérlő cseréje logikai](#single-controller-replacement-logic).
7. Az egyetlen tartományvezérlő helyettesítő logika futtatása során a háttérben, amíg újra a kábelek. Ügyeljen arra, csatlakoztassa az összes a kábeleket, ugyanúgy, hogy a csere előtt csatlakoztatva lett.
8. A vezérlő újraindítása után ellenőrizze a **vezérlő állapota** és a **a fürt állapota** arról, hogy a vezérlő vissza a kifogástalan állapotban van, és a készenléti üzemmódban van, az Azure Portalon.

> [!NOTE]
> Az eszköz soros konzolon keresztül figyelt jelenhet meg több újraindítást, amíg a vezérlő cseréje eljárás helyre. Ha a soros konzol menüjének egyike jelenik meg, majd azt jelzi, hogy a csere befejeződött. Ha a menüben nem jelenik meg a vezérlő cseréje a kiindulási két órán belül, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Update 4-től kezdődően is használhatja a parancsmagot `Get-HCSControllerReplacementStatus` az eszköz a vezérlő cseréjét állapotának figyelése a Windows PowerShell felületén.
> 

## <a name="replace-both-controllers"></a>Mindkét vezérlő cseréje
Ha mindkét vezérlő a Microsoft Azure StorSimple eszközön nem sikerült, vannak jól működik, vagy hiányoznak, kell mindkét vezérlő cseréje. 

### <a name="dual-controller-replacement-logic"></a>Kettős vezérlő cseréje logika
Kettős vezérlő helyett először távolítsa el a mindkét sikertelen vezérlő, és helyezze be cseréjére. Ha a két helyettesítő vezérlők egészül ki, a következő műveletek történnek:

1. A helyettesítő tartományvezérlő adatszalagot a 0 a következőket ellenőrzi:
   
   1. Azt a belső vezérlőprogram és a szoftver jelenlegi verzióját használja?
   2. Ez egy része a fürtnek?
   3. A társ futtató tartományvezérlőnek futnia, és ez fürtözött?
      
      Ha egyik feltétel sem teljesül, a vezérlő megjelenését a legújabb napi biztonsági mentés (található a **nonDOMstorage** S meghajtón található). A vezérlő a legutóbbi pillanatképe, a virtuális Merevlemezt másolja át a biztonsági mentés.
2. A vezérlő 0-s bővítőhelyen a kép maga a pillanatfelvételt használja.
3. A vezérlő 1 tárolóhely, végezze el a lemezképpel végrehajtott telepítéshez, és indítsa el a 0. vezérlő várakozik.
4. Miután elindult a vezérlő 0, 1. vezérlő észleli hozott létre vezérlő 0, a fürt, amely elindítja a egyetlen vezérlő cseréje logikai. További információkért lásd: [egyetlen vezérlő cseréje logikai](#single-controller-replacement-logic).
5. Ezt követően mindkét vezérlő fog futni, és a fürt online állapotba kerül.

> [!IMPORTANT]
> Kettős vezérlő helyett, a következő a StorSimple-eszköz konfigurálása után, elengedhetetlen fontosságú, hogy Ön manuális biztonsági mentést az eszköz. Napi eszköz konfigurációs biztonsági nem aktivált amíg 24 óra eltelte után. Együttműködve [Support](storsimple-8000-contact-microsoft-support.md) , készítsen biztonsági másolatot, az eszköz manuális.


### <a name="dual-controller-replacement-steps"></a>Kettős vezérlő cseréje lépések
Ez a munkafolyamat kötelező, amikor mindkét vezérlő a Microsoft Azure StorSimple-eszköz a meghibásodott. Ez akkor történhet egy adatközpontban, amelyben a hűtési rendszer leáll, és ennek eredményeképpen mindkét vezérlő nem sikerül, egy rövid időn belül. Attól függően, hogy a StorSimple-eszköz ki van kapcsolva vagy a, és használ-e egy 8600-as vagy egy 8100-as modell, a lépések egy másik készletét szükség.

> [!IMPORTANT]
> Indítsa újra, és a egy kettős vezérlő cseréje eljárás teljesen helyreállítása a vezérlő 1 óra 45 percet is igénybe vehet. A teljes folyamat fordított időt a kábelek csatolása való körülbelül 2,5 óra.

#### <a name="to-replace-both-controller-modules"></a>Cserélje le mindkét vezérlő modulok
1. Ha az eszköz ki van kapcsolva, hagyja ki ezt a lépést, és folytassa a következő lépéssel. Ha az eszköz be van kapcsolva, kapcsolja ki az eszközt.
   
   1. Ha egy 8600-as modellt használja, kapcsolja ki az elsődleges ház első, és ezután kapcsolja ki a EBOD ház.
   2. Várjon, amíg az eszköz teljesen leállt. Az összes a LED-ek hátulján az eszköz ki lesz kapcsolva.
2. Távolítsa el az adatok portok csatlakoztatott összes hálózati kábeleket. Ha egy 8600-as modellt használja, is távolítsa el a SAS-kábel, amelyhez az elsődleges ház csatlakozni a EBOD ház.
3. Távolítsa el a StorSimple-eszköz mindkét vezérlőn. További információkért lásd: [egy vezérlő eltávolítása](#remove-a-controller).
4. Helyezze be a vezérlő 0 gyári helyettesítő először, és helyezze a vezérlő 1. További információkért lásd: [beszúrása egy vezérlő](#insert-a-controller). Ez elindítja a kettős vezérlő cseréje logikát. További információkért lásd: [kettős vezérlő cseréje logikai](#dual-controller-replacement-logic).
5. Amíg a vezérlő cseréje logika futtatása során a háttérben, csatlakoztassa újra a kábelek. Ügyeljen arra, csatlakoztassa az összes a kábeleket, ugyanúgy, hogy a csere előtt csatlakoztatva lett. További tájékoztatást a modell a kábel részletesen ismerteti az eszköz [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md) vagy [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).
6. Kapcsolja be a StorSimple-eszköz. Ha egy 8600-as modellt használ:
   
   1. Győződjön meg arról, hogy a EBOD ház első van-e kapcsolva.
   2. Várjon, amíg a EBOD ház fut-e.
   3. Kapcsolja be az elsődleges ház.
   4. Miután az első vezérlő újraindul, és kifogástalan állapotban van, a rendszer fog futni.
      
      > [!NOTE]
      > Az eszköz soros konzolon keresztül figyelt jelenhet meg több újraindítást, amíg a vezérlő cseréje eljárás helyre. Amikor megjelenik a soros konzol menüjének, majd, hogy a csere befejeződött. Ha a menüben nem jelenik meg a vezérlő cseréje a kiindulási 2,5 órán belül, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>A vezérlő eltávolítása
A következő eljárás használatával távolítsa el a hibás vezérlő modul a StorSimple-eszköz.

> [!NOTE]
> Az alábbi ábrákon 0. vezérlő vonatkoznak. 1. vezérlő ezek akkor vonható vissza.


#### <a name="to-remove-a-controller-module"></a>Egy tartományvezérlő modul eltávolítása
1. A modul zárolás az USB- és mutatóujj között bonyolultnak.
2. Óvatosan nyomja össze az USB és mutatóujj együtt a tartományvezérlő-zárolás feloldásához.
   
    ![Vezérlő zárolás feloldása](./media/storsimple-controller-replacement/IC741047.png)
   
    **2. ábra** felszabadításával vezérlő zárolás
3. A zárolás használjuk egy leírót a vezérlő kívül a váz csúsztatott.
   
    ![Váz kívül mozgó vezérlő](./media/storsimple-controller-replacement/IC741048.png)
   
    **3. ábra** késleltetett a vezérlő kívül a váz

## <a name="insert-a-controller"></a>Egy tartományvezérlő beszúrása
A következő eljárással után hibás modul eltávolításához a StorSimple-eszköz gyári által megadott tartományvezérlő modul telepítésére.

#### <a name="to-install-a-controller-module"></a>A tartományvezérlő-moduljának telepítése
1. Ellenőrizze, hogy van-e a felület összekötőkre károkat. Ha bármelyik az összekötő PIN-kód sérült vagy hajlított ne telepítse a modul.
2. Húzza az ujját a vezérlő modul a váz, amíg a Zárolás feloldva teljes mértékben.
   
    ![Váz be mozgó vezérlő](./media/storsimple-controller-replacement/IC741053.png)
   
    **4. ábra** csúszó vezérlő be a váz
3. A vezérlő modullal beszúrt miközben továbbra is a vezérlő modul leküldése a váz a zárolás bezárása kezdődik. A zárolás, a vezérlő útmutató helyen vesz részt.
   
    ![Vezérlő reteszes bezárása](./media/storsimple-controller-replacement/IC741054.png)
   
    **5. ábra** a vezérlő zárolás bezárása
4. Elkészült, amikor a zárolás illeszti be a helyen. A **OK** LED most meg kell lennie.
   
   > [!NOTE]
   > A vezérlő és a LED aktiválása akár 5 percet is igénybe vehet.
  
5. Annak ellenőrzéséhez, hogy a csere sikeres, az Azure Portalon, nyissa meg az eszközt, és navigáljon arra **figyelő** > **hardverállapot**, és ellenőrizze, hogy mindkét vezérlő 0 és 1. vezérlő kifogástalan állapotú (állapota zöld színnel).

## <a name="identify-the-active-controller-on-your-device"></a>Az eszközön az aktív vezérlő megkeresése
Vannak olyan helyzetek, például az első eszköz regisztrációs vagy -vezérlő cseréje, igénylő keresse meg az aktív vezérlőt a StorSimple eszközön. Az aktív vezérlőt dolgozza fel az összes lemez belső vezérlőprogram és hálózatkezelési műveletet. Az aktív vezérlő megkeresése az alábbi módszerek bármelyikét használhatja:

* [Az aktív vezérlő megkeresése az Azure portal használatával](#use-the-azure-portal-to-identify-the-active-controller)
* [Storsimple-höz készült Windows PowerShell használatával az aktív vezérlő megkeresése](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Ellenőrizze a fizikai eszköz az aktív vezérlő megkeresése](#check-the-physical-device-to-identify-the-active-controller)

Ezek az eljárások leírását mellett.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Az aktív vezérlő megkeresése az Azure portal használatával
Az Azure Portalon keresse meg az eszköz majd **figyelő** > **hardverállapot**, és görgessen a **tartományvezérlők** szakaszban. Itt ellenőrizheti, melyik tartományvezérlő aktív.

![Azonosíthatja az aktív vezérlőn az Azure Portalon](./media/storsimple-controller-replacement/IC752072.png)

**6. ábra** az aktív vezérlőn az Azure portál

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Storsimple-höz készült Windows PowerShell használatával az aktív vezérlő megkeresése
Amikor az eszköz soros konzolon keresztül fér hozzá, egy szalagcím üzenet jelenik meg. A szalagcím üzenet alapszintű eszközadatok, például a modellt, a nevét, a telepített szoftververzió és az Ön hozzáfér a vezérlő állapotának tartalmaz. Az alábbi képen egy címsorában látható egy példa látható:

![Soros szalagcímüzenet](./media/storsimple-controller-replacement/IC741098.png)

**7. ábra** szalagcím üzenet megjelenítő vezérlő 0 aktív

Annak megállapításához, hogy a vezérlő van aktív vagy passzív használhatja a címsorában látható.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Ellenőrizze a fizikai eszköz az aktív vezérlő megkeresése
Az eszközön az aktív vezérlő megkeresése, keresse meg a kék vezetett a feletti 5 port az elsődleges ház-jének.

Ha ez LED villogó van, a vezérlő és aktív készenléti üzemmódban van, a másik vezérlőre. Használja a következő ábra és táblázat elősegítésére.

![Eszköz elsődleges ház csatlakozópanel meghibásodása az adatportok](./media/storsimple-controller-replacement/IC741055.png)

**8. ábra** oldalán az adatok portok és figyelési LED-ek elsődleges ház

| Címke | Leírás |
|:--- |:--- |
| 1-6 |DATA 0 – 5 hálózati portok |
| 7 |Kék LED |

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

