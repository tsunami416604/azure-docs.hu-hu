---
title: A hálózatra vonatkozó követelmények
description: Hálózati követelmények és ajánlott hálózati eljárások az optimális élmény érdekében
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617879"
---
# <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure-adatközpontok stabil, kis késleltetésű hálózati kapcsolatai elengedhetetlenek az Azure-alapú távoli renderelés jó felhasználói élményéhez. A gyenge hálózati feltételek miatt eldobott kapcsolatok, instabil, ideges vagy "jumping" Hologramok és észrevehető késés tapasztalható a kiszolgálóoldali jelenet-gráf frissítésekor.

## <a name="guidelines-for-network-connectivity"></a>A hálózati kapcsolatra vonatkozó irányelvek

A hálózati követelmények pontos feltételei a konkrét használati esettől függenek, például a távoli jelenet gráfon végzett módosítások számával és gyakoriságával, valamint a megjelenített nézet bonyolultságával, de számos iránymutatással gondoskodhat arról, hogy a lehető legjobb élmény legyen:

* Az internetkapcsolatnak legalább **50 MB/** s sebességű és **10 MB/s sebességű** , az Azure-beli távoli renderelés egyetlen felhasználói munkamenetére vonatkozó támogatásra van szüksége, feltéve, hogy nincs versengő forgalom a hálózaton. A jobb élmény érdekében ajánlott magasabb díjszabást javasolni. Ha több felhasználóval is rendelkezik ugyanazon a hálózaton, ezek a követelmények a megfelelő skálázással rendelkeznek.
* Az **5 GHz-es Wi-Fi együttes** használata általában jobb eredményeket hoz létre, mint az 2,4 GHz-es Wi-Fi sávban, bár mindkettőnek működnie kell.
* Ha a közelben más Wi-Fi-hálózatok vannak, ne használja ezeket a hálózatokat a Wi-Fi csatornákon. A hálózati ellenőrzési eszközök, például a [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) segítségével ellenőrizheti, hogy a Wi-Fi-hálózat által használt csatornák szabad-e a versengő forgalomtól.
* Szigorúan **Kerülje a Wi-Fi repeaterek vagy a** LAN-over-vezetékes továbbítás használatát.
* **Kerülje a versengő sávszélességet** – többek között a video-vagy játék-adatfolyamot – ugyanazon a Wi-Fi-hálózaton.
* A **jó Wi-Fi jel erőssége** elengedhetetlen. Ha lehetséges, maradjon a Wi-Fi hozzáférési ponthoz, és elkerülje az ügyfél-eszköz és a hozzáférési pontok közötti akadályokat.
* Győződjön meg arról, hogy mindig csatlakozik a **legközelebbi Azure-adatközponthoz** a [régiójában](regions.md). Minél közelebb van az adatközponthoz, annál kisebb a hálózati késés, ami óriási hatással van a hologramos stabilitásra.

> [!NOTE]
> Az alsóbb rétegbeli sávszélességet többnyire a video stream használja, amely viszont a szín-és a mélységi információk (60 Hz, sztereó) között oszlik meg.

## <a name="network-performance-tests"></a>Hálózati teljesítménnyel kapcsolatos tesztek

Ha szeretné megismerni, hogy a hálózati kapcsolat minősége elegendő-e az Azure távoli renderelés futtatásához, meglévő online eszközöket is használhat. Javasoljuk, hogy az ilyen online eszközöket olyan ésszerűen nagy teljesítményű laptopon futtassa, amely ugyanahhoz a Wi-Fi-hálózathoz csatlakozik, mint az az eszköz, amelyen az Azure távoli renderelési ügyfélalkalmazás futtatását tervezi. A tesztek mobiltelefonon vagy HoloLens2 való futtatásának eredményei általában kevésbé hasznosak, mert bebizonyosodott, hogy az alacsony teljesítményű végponti eszközökön jelentős eltéréseket mutatnak. Az a hely, ahol a laptopot elhelyezi, nagyjából ugyanazon a helyen kell lennie, ahol az Azure távoli renderelési ügyfélalkalmazás futtatására várt eszközt fogja használni.

Íme néhány egyszerű lépés a hálózati kapcsolat gyors teszteléséhez:

1. **Futtasson egy hálózati tesztelési eszközt, például a www.speedtest.net-t, hogy lekérje a hálózati kapcsolat teljes késését és felsőbb rétegbeli sávszélességét.**
Válasszon egy Önhöz legközelebb eső kiszolgálót, és futtassa a tesztet. Amíg a kiszolgáló nem lesz az Azure-beli távoli rendereléshez kapcsolódó Azure-adatközpont, az eredményül kapott adat továbbra is hasznos lehet az internetkapcsolat és a Wi-Fi teljesítményének megismeréséhez.
   * Az Azure Remote rendering **minimális követelménye** : kb. 40 Mbps és 5 Mbps upstream.
   * Az Azure Remote rendering esetében **ajánlott** : kb. 100 Mbps és 10 Mbps.
Javasoljuk, hogy a tesztet többször is futtassa, és a legrosszabb eredményeket használja.
1. **Használjon olyan eszközt, mint a www.azurespeed.com, amely az Azure-adatközpontok késését méri**. Válassza ki az Azure-alapú távoli renderelés által támogatott Azure-adatközpontot (lásd a [támogatott régiókat](regions.md)), és futtasson **késési tesztet**. Ha a megjelenő számok variációban szerepelnek, akkor az eredményeket egy kis idő elteltével kell megadnia.
   * Az Azure távoli renderelés **minimális követelménye** : a késésnek konzisztensnek kell lennie, mint 100 MS.
   * Az Azure Remote rendering esetében **ajánlott** : a késésnek a 70 MS-nál kisebbnek kell lennie.

Habár az alacsony késés nem garantálható, hogy az Azure távoli renderelés jól működik a hálózaton, általában úgy láttuk, hogy olyan helyzetekben, amikor a tesztek sikeresen sikeresek voltak.
Ha olyan összetevőket észlel, mint például az instabil, a vibrálás vagy az Ugrás Hologramok az Azure távoli renderelés futtatásakor, tekintse meg a [hibaelhárítási útmutatót](../resources/troubleshoot.md).

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)
