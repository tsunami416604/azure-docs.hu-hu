---
title: Cserélje ki a StorSimple 8000 sorozatú eszközvezérlőt | Microsoft dokumentumok
description: A storsimple 8000 sorozatú eszközön az egyik vagy mindkét vezérlőmodul eltávolítása és cseréje.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267923"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Vezérlőmodul cseréje a StorSimple eszközön
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan távolíthatja el és cserélheti le az egyik vagy mindkét vezérlőmodult egy StorSimple-eszközben. Azt is ismerteti a mögöttes logikája az egy- és kétvezérlős helyettesítő forgatókönyvek.

> [!NOTE]
> A vezérlő cseréje előtt javasoljuk, hogy mindig frissítse a vezérlő belső vezérlőprogramját a legújabb verzióra.
> 
> A StorSimple eszköz károsodásának elkerülése érdekében ne adja ki a vezérlőt, amíg a LED-ek nem jelennek meg az alábbiak egyikeként:
> 
> * Minden lámpa ki van kapcsolva.
> * A LED ![3,](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)a ![Zöld](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) pipa ikon és a Piros kereszt ikon villog, a LED 0 és a LED 7 pedig be van **kapcsolva.**


Az alábbi táblázat a támogatott vezérlőcsere-forgatókönyveket mutatja be.

| Case (Eset) | Csereforgatókönyv | Alkalmazandó eljárás |
|:--- |:--- |:--- |
| 1 |Az egyik vezérlő hibás állapotban van, a másik kifogástalan állapotban van. |[Az egyvezérlős csere](#replace-a-single-controller), amely leírja az [egyetlen vezérlőcsere mögötti logikát](#single-controller-replacement-logic), valamint a [cserelépéseket](#single-controller-replacement-steps). |
| 2 |Mindkét vezérlő meghibásodott, és cserére szorul. A ház, a lemezek és a lemezház kifogástalan. |[A kettős kontroller csere](#replace-both-controllers), amely leírja a [kettős kontroller csere logikáját](#dual-controller-replacement-logic), valamint a [cserelépéseket](#dual-controller-replacement-steps). |
| 3 |Az ugyanazon eszközről vagy különböző eszközökről származó vezérlők et cserélik ki. A ház, a lemezek és a lemezházak kifogástalanállapotúak. |Megjelenik egy bővítőhely-eltérési figyelmeztető üzenet. |
| 4 |Az egyik vezérlő hiányzik, a másik nem sikerül. |[A kettős kontroller csere](#replace-both-controllers), amely leírja a [kettős kontroller csere logikáját](#dual-controller-replacement-logic), valamint a [cserelépéseket](#dual-controller-replacement-steps). |
| 5 |Az egyik vagy mindkét vezérlő meghibásodott. Az eszköz nem érhető el a soros konzolon vagy a Windows PowerShell-távüzeneten keresztül. |A manuális vezérlőcsere-eljárásért forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |
| 6 |A vezérlők más buildverzióval rendelkeznek, amely a következőknek köszönhető:<ul><li>A vezérlők más szoftververzióval rendelkeznek.</li><li>A vezérlők belső vezérlőprogram-verziója eltérő.</li></ul> |Ha a vezérlő szoftververziói eltérőek, a cserelogika észleli ezt, és frissíti a szoftververziót a cserevezérlőn.<br><br>Ha a vezérlő belső vezérlőbelső-vezérlő verziói eltérőek, és a régi belső vezérlőprogram-verzió **nem** frissíthető automatikusan, egy figyelmeztető üzenet jelenik meg az Azure Portalon. Keresse meg a frissítéseket, és telepítse a belső vezérlőprogram frissítéseit.</br></br>Ha a vezérlő belső vezérlőprogramjának verziói eltérőek, és a régi belső vezérlőprogram-verzió automatikusan frissíthető, a vezérlő cserelogikája észleli ezt, és a vezérlő indítása után a belső vezérlőprogram automatikusan frissül. |

El kell távolítania egy vezérlőmodult, ha az meghibásodott. Az egyik vagy mindkét vezérlőmodul meghibásodhat, ami egyetlen vezérlőcserét vagy kettős vezérlőcserét eredményezhet. A helyettesítési eljárásokat és a mögöttük lévő logikát lásd a következőkben:

* [Egyetlen vezérlő cseréje](#replace-a-single-controller)
* [Mindkét vezérlő cseréje](#replace-both-controllers)
* [Vezérlő eltávolítása](#remove-a-controller)
* [Vezérlő beszúrása](#insert-a-controller)
* [Az eszköz aktív vezérlőjének azonosítása](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> A vezérlő eltávolítása és cseréje előtt tekintse át a [StorSimple hardverösszetevő-csere](storsimple-8000-hardware-component-replacement.md)biztonsági tudnivalóit.
> 
> 

## <a name="replace-a-single-controller"></a>Egyetlen vezérlő cseréje
Ha a Microsoft Azure StorSimple-eszköz két vezérlőjének egyike meghibásodott, hibásan működik, vagy hiányzik, egyetlen vezérlőt kell cserélnie.

### <a name="single-controller-replacement-logic"></a>Egyvezérlős cserelogika
Egyetlen vezérlőcsere esetén először távolítsa el a meghibásodott vezérlőt. (Az eszköz többi vezérlője az aktív vezérlő.) A cserevezérlő behelyezésekor a következő műveletek történnek:

1. A cserevezérlő azonnal megkezdi a kommunikációt a StorSimple eszközzel.
2. A cserevezérlőre az aktív vezérlő virtuális merevlemezének (VHD) pillanatképét másolja a rendszer.
3. A pillanatkép úgy módosul, hogy amikor a cserevezérlő elindul erről a virtuális merevlemezről, a rendszer készenléti vezérlőként ismeri fel.
4. Amikor a módosítások befejeződtek, a cserevezérlő készenléti vezérlőként indul.
5. Ha mindkét vezérlő fut, a fürt online állapotba kerül.

### <a name="single-controller-replacement-steps"></a>Egyetlen vezérlőcsere lépései
Hajtsa végre a következő lépéseket, ha a Microsoft Azure StorSimple eszköz egyik vezérlője meghibásodik. (A másik vezérlőnek aktívnak és futónak kell lennie. Ha mindkét vezérlő meghibásodik vagy hibásan működik, lépjen a [kettős vezérlő csere lépéséhez](#dual-controller-replacement-steps).)

> [!NOTE]
> 30–45 percet is igénybe vehet, amíg a vezérlő újraindul, és teljesen helyreáll az egyetlen vezérlő csereeljárásából. A teljes eljárás teljes ideje, beleértve a kábelek csatlakoztatását is, körülbelül 2 óra.


#### <a name="to-remove-a-single-failed-controller-module"></a>Egyetlen hibás vezérlőmodul eltávolítása
1. Az Azure Portalon nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson az **Eszközök**elemre, majd kattintson a figyelni kívánt eszköz nevére.
2. Nyissa meg **a > hardver állapotának figyelése**lehetőséget. A 0 vagy az 1-es vezérlő állapotának pirosnak kell lennie, ami hibát jelez.
   
   > [!NOTE]
   > Az egyetlen vezérlőcsere sikertelen vezérlője mindig készenléti vezérlő.
   
3. Az 1.
   
    ![Az eszköz elsődleges házmoduljainak hátlapja](./media/storsimple-controller-replacement/IC740994.png)
   
    **1. ábra** A StorSimple eszköz háttekora
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
4. A sikertelen vezérlőn távolítsa el az összes csatlakoztatott hálózati kábelt az adatportokból. Ha 8600-as modellt használ, távolítsa el azokat a SAS-kábeleket is, amelyek a vezérlőt az EBOD-vezérlőhöz csatlakoztatják.
5. A vezérlő [eltávolításához](#remove-a-controller) kövesse a vezérlő eltávolításának lépéseit.
6. Telepítse a gyári csere ugyanabban a nyílásba, ahonnan a hibás vezérlőt eltávolították. Ez elindítja az egyvezérlőcsere logikát. További információt az [egyvezérlős cserelogikában talál.](#single-controller-replacement-logic)
7. Amíg az egyvezérlős cserelogika a háttérben halad, csatlakoztassa újra a kábeleket. Ügyeljen arra, hogy az összes kábelt pontosan ugyanúgy csatlakoztassa, mint a csere előtt.
8. A vezérlő újraindítása után ellenőrizze a **vezérlő állapotát** és a **fürt állapotát** az Azure Portalon, hogy ellenőrizze, hogy a vezérlő kifogástalan állapotban van-e, és készenléti állapotban van-e.

> [!NOTE]
> Ha a soros konzolon keresztül figyeli az eszközt, többszörös újraindítást láthat, miközben a vezérlő a csereeljárásból lábadozik. Amikor a soros konzol menü jelenik meg, akkor tudja, hogy a csere befejeződött. Ha a menü nem jelenik meg a vezérlő cseréjének megkezdésétől számított két órán belül, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)
>
> A 4-es frissítés indításával az `Get-HCSControllerReplacementStatus` eszköz Windows PowerShell felületén lévő parancsmaggal is figyelheti a vezérlő cserefolyamatának állapotát.
> 

## <a name="replace-both-controllers"></a>Mindkét vezérlő cseréje
Ha a Microsoft Azure StorSimple-eszköz mindkét vezérlője meghibásodott, hibásan működik, vagy hiányzik, mindkét vezérlőt le kell cserélnie. 

### <a name="dual-controller-replacement-logic"></a>Kettős vezérlő cserelogikája
A kettős vezérlő cseréje, először távolítsa el mindkét sikertelen vezérlők, majd helyezze csere. A két cserevezérlő behelyezésekor a következő műveletek történnek:

1. A 0 bővítőhely cserevezérlője ellenőrzi a következőket:
   
   1. Ez használ a jelenlegi verzióa a firmware és a szoftver?
   2. Ez a fürt része?
   3. Fut a társvezérlő, és fürtözött?
      
      Ha a feltételek egyike sem teljesül, a vezérlő a legújabb napi biztonsági mentést keresi (az S meghajtó **nonDOMstorage tárolójában** található). A vezérlő a virtuális merevlemez legújabb pillanatképét másolja a biztonsági másolatból.
2. A 0 tárolóhely vezérlője a pillanatkép segítségével saját maga is kép.
3. Eközben az 1-es foglalatvezérlő megvárja, amíg a 0 vezérlő befejezi a képalkotást és elindul.
4. A 0 vezérlő indítása után az 1-es vezérlő észleli a 0 vezérlő által létrehozott fürtöt, amely elindítja az egyvezérlő cserelogikáját. További információt az [egyvezérlős cserelogikában talál.](#single-controller-replacement-logic)
5. Ezt követően mindkét vezérlő futni fog, és a fürt online állapotba kerül.

> [!IMPORTANT]
> A kettős vezérlő cseréjét követően a StorSimple eszköz konfigurálása után elengedhetetlen, hogy manuálisbiztonsági másolatot készítsen az eszközről. A napi eszközkonfigurációs biztonsági mentések csak 24 óra elteltével aktiválódnak. A [Microsoft támogatási szolgálatával](storsimple-8000-contact-microsoft-support.md) manuális biztonsági másolatot készíthet az eszközről.


### <a name="dual-controller-replacement-steps"></a>Kettős vezérlő csere lépései
Erre a munkafolyamatra akkor van szükség, ha a Microsoft Azure StorSimple-eszköz mindkét vezérlője meghibásodott. Ez egy olyan adatközpontban fordulhat elő, ahol a hűtőrendszer leáll, és ennek eredményeképpen mindkét vezérlő rövid időn belül meghibásodik. Attól függően, hogy a StorSimple eszköz ki van-e kapcsolva vagy be van kapcsolva, és hogy 8600-as vagy 8100-as modellt használ, különböző lépésekre van szükség.

> [!IMPORTANT]
> A vezérlő újraindítása és a kettős vezérlő csereeljárásának teljes helyreállítása 45 perctől 1 óráig is eltarthat. A teljes eljárás teljes ideje, beleértve a kábelek csatlakoztatását is, körülbelül 2,5 óra.

#### <a name="to-replace-both-controller-modules"></a>Mindkét vezérlőmodul cseréje
1. Ha az eszköz ki van kapcsolva, hagyja ki ezt a lépést, és folytassa a következő lépéssel. Ha az eszköz be van kapcsolva, kapcsolja ki az eszközt.
   
   1. Ha 8600-as modellt használ, először kapcsolja ki az elsődleges ház, majd kapcsolja ki az EBOD ház.
   2. Várjon, amíg az eszköz teljesen leáll. A készülék hátulján lévő összes LED ki lesz kapcsolva.
2. Távolítsa el az adatportokhoz csatlakoztatott összes hálózati kábelt. Ha 8600-as modellt használ, távolítsa el azokat a SAS-kábeleket is, amelyek az elsődleges házat az EBOD házhoz csatlakoztatják.
3. Távolítsa el mindkét vezérlőt a StorSimple eszközről. További információt a Vezérlő eltávolítása című témakörben [talál.](#remove-a-controller)
4. Először helyezze be a Controller 0 gyári cseréjét, majd helyezze be az 1 vezérlőt. További információt a [Vezérlő beszúrása](#insert-a-controller)című témakörben talál. Ez elindítja a kettős vezérlő cserelogikáját. További információt a [kétvezérlős cserelogikában talál.](#dual-controller-replacement-logic)
5. Amíg a vezérlő cserelogikája a háttérben halad, csatlakoztassa újra a kábeleket. Ügyeljen arra, hogy az összes kábelt pontosan ugyanúgy csatlakoztassa, mint a csere előtt. Tekintse meg a modellrészletes utasításait a [StorSimple 8100 eszköz telepítésével](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600-as eszköz telepítésével](storsimple-8600-hardware-installation.md)kapcsolatos eszköz kábelezése szakaszban.
6. Kapcsolja be a StorSimple eszközt. 8600-as modell használata esetén:
   
   1. Győződjön meg arról, hogy az EBOD-ház be van kapcsolva először.
   2. Várjon, amíg az EBOD-ház fut.
   3. Kapcsolja be az elsődleges burkolatot.
   4. Miután az első vezérlő újraindul, és kifogástalan állapotban van, a rendszer futni fog.
      
      > [!NOTE]
      > Ha a soros konzolon keresztül figyeli az eszközt, többszörös újraindítást láthat, miközben a vezérlő a csereeljárásból lábadozik. Amikor megjelenik a soros konzol menüje, akkor tudja, hogy a csere befejeződött. Ha a menü nem jelenik meg a vezérlő cseréjének megkezdésétől számított 2,5 órán belül, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)
     
## <a name="remove-a-controller"></a>Vezérlő eltávolítása
Az alábbi eljárással távolítson el egy hibás vezérlőmodult a StorSimple-eszközről.

> [!NOTE]
> A következő ábrák a 0 vezérlőhöz tartoznak. Az 1-es vezérlő esetében ezek megfordulnának.


#### <a name="to-remove-a-controller-module"></a>Vezérlőmodul eltávolítása
1. Fogja meg a modul reteszt a hüvelyk- és mutatóujja között.
2. Óvatosan nyomja össze a hüvelykujját és a mutatóujját, hogy kioldja a kontroller reteszt.
   
    ![Felszabadító vezérlő retesz](./media/storsimple-controller-replacement/IC741047.png)
   
    **2. ábra** Felszabadító vezérlő retesz
3. Használja a reteszt fogantyúként, hogy kihúzza a vezérlőt a házból.
   
    ![Csúszó vezérlő ki a futómű](./media/storsimple-controller-replacement/IC741048.png)
   
    **3. ábra** A vezérlő kicsúsztatása az alvázból

## <a name="insert-a-controller"></a>Vezérlő beszúrása
Az alábbi eljárással telepítsen egy gyárilag biztosított vezérlőmodult, miután eltávolított egy hibás modult a StorSimple eszközről.

#### <a name="to-install-a-controller-module"></a>Vezérlőmodul telepítése
1. Ellenőrizze, hogy megsérült-e az interfész csatlakozója. Ne szerelje fel a modult, ha a csatlakozócsapok bármelyike sérült vagy elhajlott.
2. Csúsztassa a vezérlőmodult a házba, amíg a retesz teljesen kiold.
   
    ![Csúszó vezérlő a házba](./media/storsimple-controller-replacement/IC741053.png)
   
    **4.** Csúszó vezérlő az alvázba
3. A vezérlőmodul behelyezése után kezdje el bezárni a reteszt, miközben folytatja a vezérlőmodul benyomását a házba. A retesz bekapcsolja, hogy a vezérlőt a helyére vezesse.
   
    ![Záró vezérlő retesz](./media/storsimple-controller-replacement/IC741054.png)
   
    **5. ábra** A vezérlő reteszének bezárása
4. Akkor végzel, ha a zár a helyére pattan. Az **OK** LED-nek most be kell kapcsolva.
   
   > [!NOTE]
   > A vezérlő és a LED aktiválása akár 5 percet is igénybe vehet.
  
5. Annak ellenőrzéséhez, hogy a csere sikeres, az Azure Portalon nyissa meg az eszközt, majd keresse meg a**Hardver állapotának** **figyelése** > , és győződjön meg arról, hogy mind a vezérlő 0 és 1 vezérlő kifogástalan (állapot zöld).

## <a name="identify-the-active-controller-on-your-device"></a>Az eszköz aktív vezérlőjének azonosítása
Számos olyan helyzet van, például az első alkalommal eszköz regisztráció vagy vezérlő cseréje, amelyek megkövetelik, hogy keresse meg az aktív vezérlő egy StorSimple eszközön. Az aktív vezérlő feldolgozza az összes lemez belső vezérlőprogramját és hálózati műveletét. Az aktív vezérlő azonosítására az alábbi módszerek bármelyikét használhatja:

* [Az Aktív vezérlő azonosításához használja az Azure Portalt](#use-the-azure-portal-to-identify-the-active-controller)
* [Az aktív vezérlő azonosításához használja a StorSimple hez készült Windows PowerShellt](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Ellenőrizze a fizikai eszközt az aktív vezérlő azonosításához](#check-the-physical-device-to-identify-the-active-controller)

Ezen eljárások mindegyikét a következőként ismertetjük.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Az Aktív vezérlő azonosításához használja az Azure Portalt
Az Azure Portalon keresse meg az eszközt, majd **a Hardver állapotának figyelése** > **Hardware health**, és görgessen a **Vezérlők** szakaszba. Itt ellenőrizheti, hogy melyik vezérlő aktív.

![Aktív vezérlő azonosítása az Azure Portalon](./media/storsimple-controller-replacement/IC752072.png)

**6. ábra** Az aktív vezérlőt megjelenítő Azure portal

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Az aktív vezérlő azonosításához használja a StorSimple hez készült Windows PowerShellt
Amikor a soros konzolon keresztül éri el a készüléket, egy szalagcímüzenet jelenik meg. A szalagcímüzenet olyan alapvető eszközinformációkat tartalmaz, mint a modell, a név, a telepített szoftververzió és az elérni kívánt vezérlő állapota. Az alábbi képen egy szalagcímüzenet látható:

![Soros szalagcím üzenet](./media/storsimple-controller-replacement/IC741098.png)

**7. ábra** A 0 vezérlőt aktívként megjelenítő szalagüzenet

A szalagcímüzenet segítségével meghatározhatja, hogy a vezérlő, amelyhez csatlakozik, aktív vagy passzív.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Ellenőrizze a fizikai eszközt az aktív vezérlő azonosításához
Az eszköz aktív vezérlőjének azonosításához keresse meg a kék LED-et az elsődleges ház hátoldalán található DATA 5 port felett.

Ha ez a LED villog, a vezérlő aktív, a másik vezérlő pedig készenléti üzemmódban van. Használja az alábbi ábrát és táblázatot segédeszközként.

![Eszköz elsődleges ház hátsík adatportokkal](./media/storsimple-controller-replacement/IC741055.png)

**8. ábra** Az elsődleges ház háttoldalában adatportok és monitorozási LED-ek

| Címke | Leírás |
|:--- |:--- |
| 1-6 |DATA 0 – 5 hálózati port |
| 7 |Kék LED |

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

