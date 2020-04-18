---
title: A hálózatra vonatkozó követelmények
description: Hálózati követelmények és bevált hálózati eljárások az optimális élmény érdekében
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617879"
---
# <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure távoli renderelése során a stabil, alacsony késésű hálózati kapcsolat elengedhetetlen az Azure távoli leolvasása során a megfelelő felhasználói élményhez. A rossz hálózati feltételek eltorlaszolhatják a kapcsolatokat, instabil, ideges vagy "ugró" hologramokat eredményezhetnek, és észrevehető késést okozhatnak a kiszolgálóoldali jelenetgrafikon frissítésekor.

## <a name="guidelines-for-network-connectivity"></a>A hálózati kapcsolat irányelvei

A pontos hálózati követelmények az adott használati esettől függenek, például a távoli jelenetgrafikon módosításainak számától és gyakoriságától, valamint a renderelt nézet összetettségétől, de számos irányelv biztosítja, hogy a felhasználói élmény a lehető legjobb legyen:

* Az internetkapcsolatnak legalább **50 Mb/s alsó és** **10 Mb/s-os upstream** szintet kell támogatnia az Azure távoli renderelés egyetlen felhasználói munkamenetéhez, feltéve, hogy nincs versengő forgalom a hálózaton. A jobb élmény érdekében magasabb árakat ajánlunk. Ha több felhasználó van ugyanazon a hálózaton, ezek a követelmények ennek megfelelően felskáláznak.
* Az **5 GHz-es Wi-Fi sáv** használata általában jobb eredményeket hoz, mint a 2,4 GHz-es Wi-Fi sáv, bár mindkettőnek működnie kell.
* Ha más Wi-Fi hálózatok is vannak a közelben, ne használjon más hálózatok által használt Wi-Fi csatornákat. Használhatja a hálózati szkennelési eszközök, mint [a WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) annak ellenőrzésére, hogy a csatornák a Wi-Fi hálózat használ, mentesek a versengő forgalmat.
* Szigorúan **kerülje a Wi-Fi jelismétlők** vagy a LAN-over-powerline továbbítást.
* **Kerülje a versengő sávszélesség-intenzív forgalmat** – például a video- vagy játékstreamelést – ugyanazon a Wi-Fi hálózaton.
* A **jó Wi-Fi jelerősség** elengedhetetlen. Ha lehetséges, maradjon közel a Wi-Fi hozzáférési ponthoz, és kerülje az ügyféleszköz és a hozzáférési pontok közötti akadályokat.
* Győződjön meg arról, hogy mindig a [régióhoz](regions.md) **kapcsolódó legközelebbi Azure-adatközponthoz** csatlakozik. Minél közelebb van az adatközpont, annál alacsonyabb a hálózati késés, ami óriási hatással van a hologram stabilitására.

> [!NOTE]
> A downstream sávszélességet többnyire a videofolyam fogyasztja, ami viszont szín- és mélységinformáció (mindkettő 60 Hz, sztereó) között oszlik meg.

## <a name="network-performance-tests"></a>Hálózati teljesítménytesztek

Ha azt szeretné, hogy a kezdeti megértéséhez, hogy a hálózati kapcsolat minősége elegendő-e az Azure távoli renderelés futtatásához, vannak meglévő online eszközök, amelyek segítségével. Javasoljuk, hogy ezeket az online eszközöket egy ésszerűen hatékony laptopról futtassa, amely ugyanahhoz a Wi-Fi-hálózathoz csatlakozik, mint az az eszköz, amelyen az Azure Remote Rendering ügyfélalkalmazás futtatását tervezi. A tesztek mobiltelefonon vagy HoloLens2-en történő futtatásából származó eredmények általában kevésbé hasznosak, mivel bizonyítottan jelentős eltéréseket mutatnak az alacsony teljesítményű végpontokon. A hely, ahol a laptop elhelyezése nagyjából ugyanazon a helyen, ahol várhatóan használni az eszközt, amely az Azure Remote-leképezési ügyfélalkalmazás.

Íme néhány egyszerű lépés a hálózati kapcsolat gyors teszteléséhez:

1. **Futtasson egy hálózati tesztelési eszközt, például www.speedtest.net, hogy adatokat kapjon a hálózati kapcsolat általános késéséről és a termelési-értékesítési láncban feljebb/alsó bb rétegbeli sávszélességéről.**
Válasszon egy önhöz legközelebb eső kiszolgálót, és futtassa a tesztet. Bár a kiszolgáló nem lesz az Azure adatközpont, amelyhez az Azure távoli renderelés csatlakozik, az eredményül kapott adatok továbbra is hasznosaz internetkapcsolat és a Wi-Fi teljesítményének megértéséhez.
   * **Az** Azure távoli renderelés minimális követelménye: Kb. 40 Mbps alsó és 5 Mbps-os felsőbb réteg.
   * **Ajánlott** az Azure távoli rendereléshez: Kb. 100 Mbps alsó és 10 Mbps-os felsőbb rétegbeli.
Javasoljuk, hogy futassa le többször a tesztet, és vegye be a legrosszabb eredményt.
1. **Használjon olyan eszközt, mint www.azurespeed.com, amely méri az Azure-adatközpontok késését.** Válassza ki az Azure remote rendering által támogatott, az Önhöz legközelebb eső Azure-adatközpontot (lásd a [támogatott régiókat),](regions.md)és futtasson **késési tesztet.** Ha van változás a számok látod, hogy az eredmények egy kis időt, hogy stabilizálja.
   * **Az** Azure távoli renderelés minimális követelménye: A késésnek következetesen 100 ms-nál kisebbnek kell lennie.
   * **Ajánlott** Az Azure távoli renderelés: Késés következetesen kevesebb, mint 70 ms.

Bár az alacsony késés nem garantálja, hogy az Azure távoli renderelés jól fog működni a hálózaton, általában láttuk, hogy jól teljesítenek olyan helyzetekben, amikor ezek a tesztek sikeresen teljesítettek.
Ha az Azure távoli renderelés futtatásakor instabil, ideges vagy ugró hologramokat tapasztal, olvassa el a [hibaelhárítási útmutatót.](../resources/troubleshoot.md)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Modell renderelése unityvel](../quickstarts/render-model.md)
