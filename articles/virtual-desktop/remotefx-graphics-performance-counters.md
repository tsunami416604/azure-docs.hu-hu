---
title: A grafikus teljesítménysel kapcsolatos problémák diagnosztizálása Távoli asztal – Azure
description: Ez a cikk azt ismerteti, hogy miként használhatók a RemoteFX grafikus számlálók távoli asztali protokollmunkamenetekben a Windows Virtual Desktop grafikus teljesítményével kapcsolatos problémák diagnosztizálására.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127559"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Grafikus teljesítményproblémák diagnosztizálása a Távoli asztal ban

A távoli munkamenetekkel kapcsolatos minőségi problémák diagnosztizálása érdekében a Teljesítményfigyelő RemoteFX graphics (RemoteFX graphics) szakaszában található számlálókat biztosítottunk. Ez a cikk segítséget nyújt a grafikával kapcsolatos teljesítménybeli szűk keresztmetszetek azonosításában és javításában a Távoli asztali protokoll (RDP) munkamenetei során ezekkel a számlálókkal.

## <a name="find-your-remote-session-name"></a>A távoli munkamenet nevének megkeresése

A grafikus teljesítményszámlálók azonosításához szüksége lesz a távoli munkamenet nevére. Az ebben a szakaszban található utasításokat követve azonosíthatja az egyes számlálók példányát.

1. Nyissa meg a Windows parancssorát a távoli munkamenetből.
2. Futtassa a **qwinsta parancsot,** és keresse meg a munkamenet nevét.
    - Ha a munkamenet egy többmunkamenetes virtuális gépen (VM) található: Az egyes számlálók példányát ugyanaz a szám helyezi el, amely a munkamenet nevét, például az "rdp-tcp 37".If your session is hosted in a multi-session virtual machine (VM): Your instance of each counter is suffixed by the same number that suffixes the session name, such as "rdp-tcp 37."
    - Ha a munkamenet egy virtuális gép, amely támogatja a virtuális grafikus feldolgozási egységek (vGPU): A példány minden számláló a kiszolgálón tárolja, nem pedig a virtuális gép. A számlálópéldányok a munkamenet nevében lévő szám helyett a virtuális gép nevét is tartalmazzák, például "Win8 Enterprise VM".

>[!NOTE]
> Bár a számlálók neve i. RemoteFX szerepel, a vGPU-forgatókönyvekben is tartalmaznak távoli asztali grafikákat.

## <a name="access-performance-counters"></a>Teljesítményszámlálók elérése

Miután meghatározta a távoli munkamenet nevét, kövesse az alábbi utasításokat a távoli munkamenet RemoteFX grafikus teljesítményszámlálóinak összegyűjtéséhez.

1. Válassza > a Felügyeleti eszközök**teljesítményfigyelőjének** **indítása** > **Administrative Tools**lehetőséget.
2. A **Teljesítményfigyelő** párbeszédpanelen bontsa ki a **Figyelési eszközök csomópontot,** válassza a **Teljesítményfigyelő**lehetőséget, majd a **Hozzáadás**lehetőséget.
3. A **Számlálók hozzáadása** párbeszédpanel Elérhető számlálók listájának kibontásával **bontsa** ki a RemoteFX-grafikák szakaszát.
4. Válassza ki a figyelni kívánt számlálókat.
5. A **kijelölt objektumpéldányok** listában jelölje ki a kijelölt számlálók figyelendő példányait, majd kattintson a **Hozzáadás gombra.** Az összes rendelkezésre álló számlálópéldány kijelöléséhez válassza a **Minden példány lehetőséget.**
6. A számlálók hozzáadása után válassza az **OK gombot.**

A kijelölt teljesítményszámlálók megjelennek a Teljesítményfigyelő képernyőn.

>[!NOTE]
>Az állomás minden aktív munkamenete saját példánysal rendelkezik az egyes teljesítményszámlálókból.

## <a name="diagnose-issues"></a>Problémák diagnosztizálása

A grafikával kapcsolatos teljesítményproblémák általában négy kategóriába sorolhatók:

- Alacsony képkockasebesség
- Véletlen standokon
- Nagy bemeneti késleltetés
- Gyenge képkockaminőség

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Az alacsony képkockasebesség, a véletlenszerű standok és a magas bemeneti késleltetés kezelése

Először ellenőrizze a Kimeneti keretek/Második számlálót. Az ügyfél rendelkezésére bocsátott keretek számát méri. Ha ez az érték kisebb, mint a bemeneti keretek/második számláló, a program kihagyja a kereteket. A szűk keresztmetszet azonosításához használja a Kimaradó/második számlálót.

A kihagyott kereteknek/másodperces számlálóknak három típusa van:

- Kihagyott keretek/másodperc (nincs elegendő kiszolgálói erőforrás)
- Kihagyott keretek/másodperc (nincs elegendő hálózati erőforrás)
- Kihagyott keretek/másodperc (nincs elegendő ügyfélerőforrás)

A kihagyott keretek/második számlálók bármelyikének nagy értéke azt jelenti, hogy a probléma a számláló által nyomon követő erőforráshoz kapcsolódik. Ha például az ügyfél nem dekódolja és ugyanolyan ütemben mutatja be a kereteket, mint a kiszolgáló, akkor a keretek kimaradnak/másodperc (elégtelen ügyfélerőforrások) számláló magas lesz.

Ha a kimeneti keretek/második számláló megegyezik a bemeneti keretek/második számláló, de még mindig azt veszi észre, szokatlan lag vagy akadozás, átlagos kódolási idő lehet a tettes. A kódolás egy szinkron folyamat, amely a kiszolgálón az egymunkamenetes (vGPU) forgatókönyvben és a virtuális gép a többmunkamenetes forgatókönyvben fordul elő. Az átlagos kódolási időnek 33 ms alatt kell lennie. Ha az átlagos kódolási idő 33 ms alatt van, de még mindig vannak teljesítményproblémái, akkor lehet, hogy a használt alkalmazással vagy operációs rendszerrel van probléma.

Az alkalmazásokkal kapcsolatos problémák diagnosztizálásáról a [Felhasználói bevitel késleltetése teljesítményszámlálók](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)című témakörben talál további információt.

Mivel az RDP 33 ms átlagos kódolási időt támogat, legfeljebb 30 képkocka/másodperc bemeneti képkockasebességet támogat. Ne feledje, hogy a 33 ms a maximálisan támogatott képkockasebesség. Sok esetben a felhasználó által tapasztalt képkockasebesség alacsonyabb lesz attól függően, hogy a forrás milyen gyakran biztosít egy keretet az RDP számára. Például az olyan feladatok, mint a videónézés, 30 képkocka/másodperc teljes bemeneti képkockasebességet igényelnek, de kevésbé gyors anamnézisben, például a dokumentumok ritkán történő szerkesztéséhez, sokkal alacsonyabb értéket eredményez a bemeneti keretek/másodperc esetében, és a felhasználói élmény minőségének romlása nélkül.

### <a name="addressing-poor-frame-quality"></a>A rossz képkockaminőség kezelése

A Keretminőség számlálóval diagnosztizálhatja a keretminőséggel kapcsolatos problémákat. Ez a számláló a kimeneti keret minőségét a forráskeret minőségének százalékában fejezi ki. A minőségromlás oka lehet a RemoteFX, vagy a grafikus forrás velejárója lehet. Ha a RemoteFX okozta a minőségromlást, akkor a probléma lehet a hálózati vagy kiszolgálói erőforrások hiánya a magasabb minőségű tartalom küldéséhez.

## <a name="mitigation"></a>Kezelés

Ha a kiszolgáló erőforrásai okozzaa a szűk keresztmetszetet, próbálkozzon az alábbi módszerek egyikével a teljesítmény javítása érdekében:

- Csökkentse a munkamenetek számát állomásonként.
- Növelje a kiszolgáló memóriáját és számítási erőforrásait.
- A kapcsolat felbontásának elhúzása.

Ha a hálózati erőforrások okozzák a szűk keresztmetszetet, próbálkozzon az alábbi módszerek egyikével a munkamenetenkénti hálózati rendelkezésre állás javítása érdekében:

- Csökkentse a munkamenetek számát állomásonként.
- Használjon nagyobb sávszélességű hálózatot.
- A kapcsolat felbontásának elhúzása.

Ha az ügyfélerőforrások okozzák a szűk keresztmetszetet, próbálkozzon az alábbi módszerek egyikével a teljesítmény javítása érdekében:

- Telepítse a legutóbbi Távoli asztali ügyfelet.
- Növelje a memóriát és a számítási erőforrásokat az ügyfélgépen.

> [!NOTE]
> Jelenleg nem támogatjuk a Forráskeretek/Második számlálót. A Forráskeretek/Második számláló egyelőre mindig 0-t jelenít meg.

## <a name="next-steps"></a>További lépések

- Gpu-ra optimalizált Azure virtuális gép létrehozásáról a [Grafikus feldolgozóegység (GPU) gyorsításának konfigurálása Windows Virtual Desktop környezetben](configure-vm-gpu.md)című témakörben található.
- A hibaelhárítási és eszkalációs pályák ról a [Hibaelhárítás áttekintése, visszajelzés és támogatás](troubleshoot-set-up-overview.md)című témakörben olvashat.
- Ha többet szeretne megtudni a szolgáltatásról, olvassa el a [Windows Asztali környezet című témakört.](environment-setup.md)
