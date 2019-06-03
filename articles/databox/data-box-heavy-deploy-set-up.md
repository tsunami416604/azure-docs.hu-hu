---
title: Oktatóanyag az Azure Data Box nehéz beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan bekötéséhez és csatlakozás az Azure Data Box (nagy erőforrásigényű)
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: f2f6b544b56977b3f1bfb6a4fb46a9f1e3bcc294
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427940"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Oktatóanyag: Kábelek, és csatlakozzon az Azure Data Box (nagy erőforrásigényű) (előzetes verzió)


Ebben az oktatóanyagban a kábelek, csatlakozzon, és kapcsolja be az Azure Data Box nehéz a módját ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box (nagy erőforrásigényű) kábelezése
> * Csatlakozás a Data Box (nagy erőforrásigényű)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Az Azure Data Box (nagy erőforrásigényű) Order](data-box-heavy-deploy-ordered.md).
2. A Data Box nehéz kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Áttekintette a [Data Box nehéz biztonságának irányelvek](data-box-safety.md).
4. Hozzáféréssel kell rendelkeznie a helyi adatközpontban segítségével közel kerülhet egy elérhető hálózati kapcsolat, amely lehetővé teszi az erőforrás-igényű rendelkező eszköz, a strukturálatlan helyre. Ez az eszköz nem lehet csatlakoztatni, állványra.
5. Négy földelve tápkábelek használata a tárolóeszköz kapott.
6. Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box nehéz lesz másolja az adatokat erről a számítógépről. A gazdaszámítógép futtatnia kell egy [támogatott operációs rendszer](data-box-heavy-system-requirements.md).
7. Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 
8. Szüksége lesz egy laptop RJ-45 kábellel csatlakozik a helyi felhasználói felület és az eszköz konfigurálása. A hordozható számítógép segítségével az eszköz minden egyes csomópontja egyszer konfigurálhatja.
9. Szüksége vagy egy 40-GB/s 10-GB/s kábellel eszköz csomópontonkénti.
    - Válassza ki a kábelek, amelyek kompatibilisek a [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adaptert.
    - A 40-GB/s kábel, az eszköz vége kell lennie QSFP +.
    - A 10-GB/s kábel szüksége lesz egy 10-GB/s kapcsolót az egyik, a egy QSFP + End SFP + adapter (vagy a DSS foglalt követelményeknek adapter), amely az eszköz rendkívüli rendkívüli SFP + kábellel.

## <a name="cable-your-device-for-power"></a>Az energiagazdálkodási az eszköz bekábelezése

Az alábbi lépéseket az eszköz bekábelezése.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. [Forduljon a Microsoft Support](data-box-disk-contact-microsoft-support.md) felmérheti, hogy az eszköz működőképes állapotban van-e, és szükség esetén egy helyettesítő szállításra, hogy azonnal.
2. Helyezze át a telepítési hely az eszközön.

    ![Data Box – nehéz eszköz telepítési hely](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Az eszközön a hátsó járószerkezetre zárolhatja az alább látható módon.

    ![Data Box – nehéz eszköz járószerkezetre zárolva van](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Keresse meg a belül, amely az első és az összetevőkről, az eszköz zárolásának feloldásához. Zárolásának feloldásához, és helyezze át a bejárati ajtó, egészen addig, amíg az eszköz oldalán az kiürítése. Ismételje meg ezt a biztonsági ajtó, valamint a.
    Mindkét ajtók nyissa meg kell maradnia, ha az eszköz működik, hogy az optimális elölről-hátra áramlásának keresztül az eszköz.

    ![Data Box – nehéz ajtók megnyitása](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. A tálca hátulján az eszközön található négy tápkábelek kell rendelkeznie. Távolítsa el a tálca minden kábel, és képezzen helyezze el őket.

    ![Data Box – nehéz tápkábelek a tálca](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A következő lépés, hogy a különböző portok hátulján található az eszköz azonosítására. Két eszköz csomópont, **csomópont1** és **csomópont2**. Mindegyik fürtcsomópont négy hálózati adapterek **MGMT**, **adat1**, **adat2**, **DATA3**. **MGMT** az eszköz a kezdeti konfiguráció során felügyeleti konfigurálására szolgál. **Adat1**-**DATA3** adatok port. **MGMT** és **DATA3** portok: 1 GB/s, mivel a **adat1**, **adat2** 40-GB/s-port vagy 10-GB/s portok is dolgozhat. A két eszköz csomópontok alján négy power ellátási egység (PSUs) az eszköz két csomópont között megosztott vannak. Ez az eszköz között, a **PSUs** vannak **PSU1**, **PSU2**, **PSU3**, és **PSU4** balról jobbra.

    ![Data Box – nehéz portok](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Az eszköz tápegységek összes négy tápkábelek csatlakozni. A zöld LED-ek kapcsolja be, és kurzorvillogás.
8. Az első síknak power gombok segítségével kapcsolja be az eszköz csomópontok. Tartsa a főkapcsoló válságos néhány másodpercet, amíg a kék lámpa. Minden a zöld LED-ek esetében a tápegységek hátulján az eszköz most már szilárd kell lennie. Az eszköz operációs előlapján tartalék LED-ek is tartalmaz. Amikor egy tartalék LED megvilágítva, az azt jelzi, hibás PSU vagy egy ventilátor vagy a merevlemez-meghajtók problémáját.  

    ![Data Box – nehéz első ops panel](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Hálózati kábel első csomópont

Az eszköz a csomópontok egyikén a következő lépésekkel, a hálózati kábel.

1. A CAT 6 RJ-45 hálózati kábellel (a képen látható kék színű kábel) használatával csatlakoztassa a gazdaszámítógépet az 1-GB/s sebességű felügyeleti porthoz.
2. A Twinax QSFP + rézeres kábellel (a kép fekete kábel) használatával legalább egy 40 Gbps (1 GB/s felett preferált) adatátvitelre szolgáló hálózati adaptert. Ha a 10-GB/s kapcsoló használata a Twinax SFP + rézeres kábellel egy QSFP + SFP + adapter (DSS foglalt követelményeknek adapter) való csatlakozáshoz a 40 GB/s hálózati adapter adatok.

    ![Data Box – nehéz portok bekábelezte](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > ADATOK 1 és adat2 bekapcsolt állapotban, és nem egyezik a helyi webes felhasználói felületen jelenik meg.
    > A 40 GB/s kábel adapter beszúrt módja, ahogy az alábbi indításakor automatikusan kapcsolódik.

    ![Data Box – nehéz 40 Gbps adapterről kábelezése](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Első csomópontok konfigurálása

A következő lépésekkel konfigurálja az eszközt a helyi konfigurációs és az Azure portal használatával.

1. Töltse le az eszköz hitelesítő adatait a portálról. Lépjen az **Általános > Eszköz adatai** menüpontra. Másolja ki **az eszköz jelszavát**. Ezek a jelszavak a portálon adott sorrendben vannak társítva. A Data Box nehéz a két csomópont megfelelő, látni fogja a két az eszközök sorozatszámait. A csomópontok mindkét az eszköz rendszergazdai jelszava megegyezik.

    ![Data Box – nehéz hitelesítő adatai](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Az ügyfél-munkaállomás csatlakozni az eszközhöz CAT6 RJ-45 hálózati kábel segítségével.
3. Ethernet-adapterét konfigurálja a számítógépen használja a statikus IP-címmel rendelkező eszköz csatlakozni `192.168.100.5` és alhálózati `255.255.255.0`.

    ![Data Box nehéz csatlakozik a helyi webes felhasználói felületen](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. A helyi webes felhasználói felületen, az eszköz a következő URL-címen csatlakozhat: `http://192.168.100.10`. Kattintson a **speciális** majd **lépjen tovább a (nem biztonságos) 192.168.100.10**.
5. Megjelenik a helyi webes felhasználói felület **bejelentkezési** oldala.
    
    - A csomópont sorozatszámokat ezen az oldalon egyik felel meg a portál felhasználói Felületét és a helyi webes felületén keresztül. Jegyezze fel a csomópont száma az a sorozatszám-lel. Nincsenek két csomópontot és két az eszközök sorozatszámait a portálon. Ez a leképezés segítségével megismerheti, hogy melyik csomópont felel meg, melyik sorozatszám.
    - Ekkor az eszköz még le van zárva.
    - Adja meg az eszköz rendszergazdai jelszava, jelentkezzen be az eszközt, az előző lépésben beszerzett. Kattintson a **Bejelentkezés** elemre.

    ![Jelentkezzen be a Data Box nehéz helyi webes felhasználói felületen](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Az irányítópulton győződjön meg arról, hogy vannak-e konfigurálva a hálózati adapterek. Nincsenek az eszköz csomóponton, négy hálózati adapter két 1 GB/s, és két 40 GB/s. Az 1-GB/s kapcsolat egyik kezelőfelület, és ezért nem felhasználó által konfigurálható. A fennmaradó három hálózati adapterek adatokhoz van dedikálva, és a felhasználó által konfigurálható.

- Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik.
- Ha a DHCP nem engedélyezett, nyissa meg beállítani a hálózati adapterek, és szükség esetén végezze el a statikus IP-címek.

    ![Data Box – nehéz irányítópult 1. csomópont](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurálja a második csomópont

Hajtsa végre a részletes lépéseket a [konfigurálása az első fürtcsomópont](#configure-first-node) a második csomópontot az eszköz számára.

![Data Box – nehéz irányítópult csomópont 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Ha az eszköz beállításával elkészült, csatlakozhat az eszközmegosztásokhoz, és a számítógépről az eszközre másolhatja az adatokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az Azure Data Box nehéz témakörök például:

> [!div class="checklist"]
> * A Data Box (nagy erőforrásigényű) kábelezése
> * Csatlakozás a Data Box (nagy erőforrásigényű)

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan másolhat adatokat a Data Box nehéz a.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra](./data-box-heavy-deploy-copy-data.md)
