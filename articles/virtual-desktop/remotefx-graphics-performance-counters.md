---
title: A távoli asztali – Azure grafikus teljesítményproblémák diagnosztizálása
description: Ez a cikk ismerteti a távoli asztali protokoll-munkamenetek RemoteFX grafikus számlálók segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémák a Windows virtuális asztal grafikákkal.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499264"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>A távoli asztali grafikus teljesítménybeli problémák diagnosztizálása

Amikor a rendszer nem hajt végre a várt módon, fontos a probléma okának azonosításához. Ezen cikk segítségével azonosíthatja és javíthatja a grafikus kapcsolatos teljesítmény szűk Remote Desktop Protocol (RDP) munkamenet során.

## <a name="find-your-remote-session-name"></a>Keresse meg a távoli munkamenet neve

A távoli munkamenet nevét a grafikus teljesítményszámlálók lesz szüksége. Kövesse az utasításokat, ez a rész azonosíthatja a Windows virtuális asztal előzetes távoli munkamenet nevét.

1. Nyissa meg a Windows-parancssort a távoli munkamenet.
2. Futtassa a **qwinsta** parancsot.
    - Ha a munkamenet több munkamenet virtuális gépen (VM) üzemel: Az utótag minden számláló neve pedig ugyanazon utótag a munkamenet nevében, például az "rdp-tcp 37."
    - Ha a munkamenet üzemel, amely támogatja a virtuális grafikai feldolgozási egységek (vGPU) virtuális gépen: A számlálók van a kiszolgálón tárolt helyett a virtuális gépen. A teljesítményszámláló-példányok tartalmaznak, a virtuális gép neve helyett a szám a munkamenet nevében, mint például "Win8 Enterprise VM."

>[!NOTE]
> Míg a számlálók RemoteFX nevükben, távoli asztali grafikus vGPU forgatókönyvek, valamint ezek tartalmazzák.

## <a name="access-performance-counters"></a>Access-teljesítményszámlálók

Teljesítményszámlálók a RemoteFX grafikus segítséget nyújtanak többek között a keret kódolás idő nyomon követése segít észlelni a szűk keresztmetszeteket, és kihagyja a keretek.

Miután ellenőrizte a távoli munkamenet neve, kövesse ezeket az utasításokat a távoli munkamenet a RemoteFX grafikus teljesítményszámlálót gyűjt.

1. Válassza ki **Start** > **felügyeleti eszközök** > **Teljesítményfigyelő**.
2. Az a **Teljesítményfigyelő** párbeszédpanelen bontsa ki **figyelési eszközök**, jelölje be **Teljesítményfigyelő**, majd válassza ki **Hozzáadás**.
3. Az a **Számláló hozzáadása** párbeszédpanelen, a **elérhető számlálók** list, bontsa ki a teljesítményszámláló objektum RemoteFX grafikus.
4. Válassza ki a figyelendő számlálókat.
5. Az a **példányok kijelölt objektum** listájához, válassza ki a figyelni a kiválasztott számlálókat, és válassza ki a konkrét példányok **Hozzáadás**. Válassza ki az összes rendelkezésre álló számlálópéldányt, válassza ki a **példányainak**.
6. Válassza ki a számlálók a felvett **OK**.

A kiválasztott teljesítményszámlálókat a Teljesítményfigyelő képernyőn jelenik meg.

>[!NOTE]
>Minden aktív munkamenet egy gazdagépen rendelkezik minden teljesítményszámláló a saját példányát.

## <a name="diagnosis"></a>Elemzés céljából

Grafikus kapcsolatos teljesítményproblémák általában négy kategóriába sorolhatók:

- Alacsony képkockasebessége
- Véletlenszerű leállások
- Magas bemeneti késés
- Keret gyenge minőség

Első lépésként címzés alacsony képkockasebessége, véletlenszerű leállások és magas a bemeneti késés. A következő szakaszban megtudhatja, mely teljesítményszámlálókat mérni az egyes kategóriák.

### <a name="performance-counters"></a>Teljesítményszámlálók

Ez a szakasz segítséget nyújt a szűk keresztmetszetek azonosítása.

Először ellenőrizze a kimeneti Frames/Second számlálót. Az ügyfél számára elérhetővé tett keretek számát méri. Ha ez az érték kisebb, mint a bemeneti Frames/Second számlálót, keretek vannak hagyva. A szűk keresztmetszetet azonosításához használja a keretek kihagyva másodpercenként számlálókat.

A keretek kihagyva másodpercenként számlálók három típusa van:

- A képkockák kihagyott/másodperc (nincs elegendő hálózati erőforrások)
- Keretek kihagyott/másodperc (nincs elegendő ügyfél erőforrások)
- A képkockák kihagyott/másodperc (nincs elég rendszererőforrás)

Egy magas szintű bármely kihagyva másodpercenként számlálók azt jelenti, hogy a problémát az erőforrás a keretek a számlálóval követi nyomon. Például ha az ügyfél nem dekódolható, és ugyanez a díjszabás a kiszolgáló jelen képkockákat lehetővé teszi a keretek, a keretek kihagyva/másodperc (ügyfél erőforráshiány) számláló magas lesz.

Ha a kimeneti Frames/Second számláló megegyezik a bemeneti Frames/Second számlálót, még szokatlan lag továbbra is fennáll, vagy elakad, a probléma lehet, hogy a kódolást átlagos ideje. Kódolás az szinkron, amely egyetlen munkamenet-(vGPU) forgatókönyvben a kiszolgálón, és több munkamenet forgatókönyvben a virtuális gépen történik. Kódolás átlagos ideje 33 ms alatt kell lennie. Kódolás átlagos ideje 33 ezredmásodperc alatti, de továbbra is problémákba ütközik a teljesítményt, ha az alkalmazás vagy a használt operációs rendszertől probléma lehet.

Alkalmazáshoz kapcsolódó problémák diagnosztizálásával kapcsolatos további információkért lásd: [felhasználói bevitel késleltetés teljesítményszámlálók](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

RDP egy átlagos kódolás idő, MS-os 33 támogatja, mert támogatja egy bemeneti képkockasebessége legfeljebb 30 képkocka/másodperc. Vegye figyelembe, hogy 33 ms a maximális támogatott képkockasebességet. Sok esetben a felhasználó által tapasztalt képkockasebessége függően, hogy milyen gyakran keret a forrás az RDP-hez biztosított alacsonyabb lesz. Például a feladatok például a figyelt videó szükséges a teljes bemeneti képkockasebessége 30 képkocka/másodperc, de kevesebb erőforrás műveltekből feladatokat, mint a word-dokumentumban ritkán szerkesztése nem igénylik az ilyen a képkockák másodpercenkénti bemeneti nagy mértékű jó felhasználói élményt.

A keret minőségi számláló segítségével keret minőségi problémák diagnosztizálásához. Ez a számláló a forrás-keret minőség százalékos fejezi ki a kimeneti keret minőségét. Minőségi elvesztését okozhatja a RemoteFX, vagy a grafikus forrás rejlő lehet. A RemoteFX által okozott a minőségi adatveszteség, ha a probléma magasabb színvonalú használatot élvezhet tartalom küldése hálózati vagy kiszolgálói erőforrások hiánya lehet.

## <a name="mitigation"></a>Kezelés

Ha a kiszolgáló-erőforrásokon a szűk keresztmetszetet okozó, próbálja meg egy, a teljesítmény javítása az alábbiak egyikét:

- Csökkentse a gazdagép-munkamenetek számát.
- Növelje a memória, és a számítási erőforrásokat a kiszolgálón.
- A kapcsolat a feloldás eldobni.

Ha a szűk keresztmetszetet okozó hálózati erőforrások, próbálja meg egy munkamenetenként a hálózat rendelkezésre állásának javítása érdekében az alábbiak egyikét:

- Csökkentse a gazdagép-munkamenetek számát.
- A kapcsolat a feloldás eldobni.
- Nagyobb sávszélesség hálózatot használ.

Ha a szűk keresztmetszetet okozó ügyfél erőforrások, tegye a egyikét vagy mindkettőt a teljesítmény javítása az alábbiak egyikét:

- Telepítse a legújabb távoli asztali ügyfél.
- Növelje a memória méretét, és a számítási erőforrásokat az ügyfélszámítógépen.

> [!NOTE]
> A forrás Frames/Second számláló jelenleg nem támogatottak. Most a forrás Frames/Second számláló mindig állítja be 0.

## <a name="next-steps"></a>További lépések

- Az Azure GPU-optimalizált virtuális gépek létrehozásához lásd: [grafikus processzorral (GPU) egység gyorsítását Windows virtuális asztal előzetes verziójú környezet konfigurálásához](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Hibaelhárítás és eszkalációs nyomon követi az áttekintést lásd: [hibaelhárítási áttekintése, visszajelzés és támogatás](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
