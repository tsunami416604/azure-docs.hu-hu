---
title: Az ArrInspector ellenőrző eszköz
description: Az ArrInspector eszköz felhasználói kézikönyve
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680075"
---
# <a name="the-arrinspector-inspection-tool"></a>Az ArrInspector ellenőrző eszköz

Az ArrInspector egy webalapú eszköz, amellyel egy futó Azure Remote Rendering munkamenet et vizsgálhat. Azt a célt szolgál, hogy hibakeresési célokra használható, hogy vizsgálja meg a jelenet renderelése, a naplóüzenetek megjelenítése, és figyelemmel kíséri az élő teljesítményt a kiszolgálón.

![ArrInspector (arrfelügyelő)](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Csatlakozás az ArrInspector-hez

Miután megszerezte az ARR-kiszolgáló állomásnevét `mixedreality.azure.com`(végződése), csatlakozzon a [ConnectToArrInspectorAsync használatával.](../../how-tos/frontend-apis.md#connect-to-arr-inspector) Ez a `StartArrInspector.html` függvény létrehoz egy olyan eszközt, amelyen az alkalmazás fut. Az ArrInspector elindításához nyissa meg a fájlt egy böngészővel (Edge, Firefox vagy Chrome) egy számítógépen. A fájl csak 24 óráig érvényes.

Ha a hívásokat kezdeményező `ConnectToArrInspectorAsync` alkalmazás már fut a számítógépen:

* Ha az Egység integrációt használja, előfordulhat, hogy automatikusan elindul az Ön számára.
* Ellenkező esetben a fájl a *User\\Folders\\LocalAppData\\\\[your_app] AC Temp*mappában található.

Ha az alkalmazás HoloLensen fut:

1. A HoloLens elérése a [Windows eszközportálon](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)keresztül.
1. Nyissa meg a *System > Fájlkezelőt*.
1. Keresse meg *a\\User\\Folders\\LocalAppData [your_app]\\AC Temp*.
1. Mentse *a StartArrInspector.html fájlt* a számítógépre.
1. Nyissa meg *a StartArrInspector.html fájlt* a munkamenet ArrInspector fájljának betöltéséhez.

## <a name="the-performance-panel"></a>A Teljesítmény panel

![A Teljesítmény panel](./media/performance-panel.png)

Ez a panel a kiszolgáló által elérhetőnek kitett összes képkockánkénti teljesítményértékek grafikonjait jeleníti meg. Az értékek jelenleg tartalmazzák a frame time, FPS, CPU és memória használat, memória statisztika, mint a teljes RAM-használat, objektum számít, stb.

Ezen paraméterek egyikének megjelenítéséhez kattintson az **Új hozzáadása** gombra, és válassza ki a párbeszédpanelen látható értékek egyikét. Ez a művelet új görgetési diagramot ad a panelhez, amely valós időben követi nyomon az értékeket. A jobb oldalon látható a *minimális*, *maximális* és *aktuális* érték.

A grafikont az egérrel húzva pásztázhatja, azonban a vízszintes pásztázás csak akkor lehetséges, ha az ArrInspector szüneteltetve van.

A CTRL billentyű húzás közbeni lenyomva tartása lehetővé teszi a nagyítást. A vízszintes zoom az alján lévő csúszkával is szabályozható.

A függőleges tartomány számítása alapértelmezés szerint az aktuálisan megjelenített értékek alapján történik, a min és a max értékek pedig a jobb oldali szövegmezőkben jelennek meg. Ha az értékek manuálisan vannak beállítva, vagy közvetlenül a szövegdobozba írja be őket, vagy pásztázással/nagyítással, a grafikon ezeket az értékeket fogja használni. Az automatikus függőleges keretezés visszaállításához kattintson a jobb felső sarokban lévő ikonra.

![függőleges tartomány](./media/vertical-range.png)

## <a name="the-log-panel"></a>A Napló panel

![Napló panel](./media/log-panel.png)

A naplópanelen a kiszolgálóoldalon létrehozott naplóüzenetek listája látható. A kapcsolaton legfeljebb 200 korábbi naplóüzenet jelenik meg, és újakat nyomtat ki, ahogy történnek.

A lista a napló típusa `[Error/Warning/Info/Debug]` alapján szűrhető a felső gombok segítségével.
![Naplószűrő gombok](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Az Időzítési adatok rögzítése panel

![Időzítési adatok rögzítése](./media/timing-data-capture.png)

Ez a panel az időzítési adatok rögzítésére és letöltésére szolgál a kiszolgálóról. A fájl chrome [tracing JSON formátumot](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)használ. Az adatok vizsgálatához nyissa meg `Chrome://tracing` a Chrome-ot az URL-címen, és húzza a letöltött fájlt az oldalra. Az időzítési adatokat folyamatosan gyűjtik egy rögzített méretű gyűrűs pufferben. Amikor kivan írva, a rögzítés csak a közvetlen múltra vonatkozó információkat tartalmaz, ami néhány másodpercet vagy néhány percet jelent.

## <a name="the-scene-inspection-panel"></a>A Jelenet vizsgálópanel

![Jelenet vizsgálópanel](./media/scene-inspection-panel.png)

Ez a panel a renderelt jelenet szerkezetét mutatja. Az objektumhierarchia a bal oldalon, a kijelölt objektum tartalma a jobb oldalon. A panel írásvédett, és valós időben frissül.

## <a name="the-vm-debug-information-panel"></a>A Virtuálisgép hibakeresési adatai panel

![A virtuális gép hibakeresési információs panelje](./media/state-debugger-panel.png)

Ez a panel kínál néhány hibakeresési funkciót.

### <a name="restart-service"></a>Szolgáltatás újraindítása

A **Szolgáltatás újraindítása** gomb újraindítja a futásidejű t a virtuális gépen, amelyhez az arrInspector csatlakozik. Minden csatlakoztatott ügyfél megszakad, és az arrInspector lapot újra kell tölteni az újraindított szolgáltatáshoz való csatlakozáshoz.

### <a name="collect-debug-information"></a>Hibakeresési adatok gyűjtése

A **Hibakeresési adatok gyűjtése a virtuális géphez** gomb megnyit egy párbeszédpanelt, amely lehetővé teszi, hogy az ARR-példány tágítsa a hibakeresési adatok gyűjtését a virtuális gépről:

![Virtuálisgép hibakeresési információs párbeszédpanelje](./media/state-debugger-dialog.png)

A hibakeresési információk segítségével az Azure remote rendering csapata elemezheti a futó ARR-példányban előforduló problémákat. A párbeszéd ablaka további részleteket tartalmaz, például a probléma reprodukálásának lépéseit.

Miután a **Kezdő gyűjtés gombra** kattintott, a párbeszédpanel bezárul, és megkezdődik a gyűjtési folyamat. A virtuális gépre vonatkozó információk gyűjtése eltarthat néhány percig.

![Folyamatban lévő virtuális gép hibakeresési információgyűjtése](./media/state-debugger-panel-in-progress.png)

Miután a gyűjtemény befejeződött, értesítést kap az ArrInspector ablakban. Ez az értesítés egy azonosítót tartalmaz, amely azonosítja ezt a gyűjteményt. Mindenképpen mentse ezt az azonosítót, hogy továbbítsa azt az Azure távoli renderelési csapatnak.

![A Virtuálisgép-hibakeresés –adatgyűjtés sikeres](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Nem lehet letölteni, vagy más módon elérni a virtuális gép hibakeresési adatait. Csak az Azure Remote-renderelési csapat rendelkezik hozzáféréssel az összegyűjtött adatokhoz. Kapcsolatba kell lépnie velünk, és el kell küldenie a gyűjtemény azonosítóját, hogy kivizsgáljuk a látott problémát.

## <a name="pause-mode"></a>Szünet mód

A jobb felső sarokban a kapcsoló lehetővé teszi a panelek élő frissítésének szüneteltetését. Ez a mód hasznos lehet egy adott állapot alapos vizsgálatához.

![Szünet mód](./media/pause-mode.png)

Az élő frissítés újbóli engedélyezésekor az összes panel alaphelyzetbe áll.

## <a name="host-configuration"></a>Állomás konfigurációja

Alapértelmezés szerint az eszköz csatlakozik az Arr-kiszolgálóhoz, amely ugyanazon az a állomáson fut, amely az ArrInspector-t szolgálja ki. Azonban beállíthatja, hogy vizsgálja meg egy másik kiszolgálón, feltételezve, hogy fut egy ARR példány az eszköztároló port nyitva.

Ehhez fért hozzá a fejlécsáv bal oldalán található főmenühöz, és válassza a *Host configuration*lehetőséget. Kattintson **az Új állomás hozzáadása**gombra, és írja be a nevet és az állomásnevet. *Állomásnév* esetén csak a programba `.mixedreality.azure.com`végződő `http://` állomásnevet használja, ne tartalmazza vagy portját.

![Állomás konfigurációja](./media/host-configuration.png)

Ha gyorsan szeretne egyik állomásról a másikra váltani, használja a jobb felső sarokban lévő legördülő menüt.

![Host kombinált](./media/host-switch-combo.png)

Az állomáslista a böngésző helyi tárolójában tárolódik, így az ugyanannak a böngészőnek az újbóli megnyitásakor megmarad.
