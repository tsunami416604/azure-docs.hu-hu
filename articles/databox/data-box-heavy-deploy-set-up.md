---
title: Oktatóanyag a Azure Data Box Heavy beállításához | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f5473ae7bd9ac1615d59d18c996e01902e1adbd6
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241415"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Oktatóanyag: Kábel és kapcsolódás a Azure Data Box Heavyhoz

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Ismerkedés a Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Ez az oktatóanyag azt ismerteti, hogyan lehet kábeleket, csatlakoztatást és bekapcsolni a Azure Data Box Heavy.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakoztassa a Data Box Heavy
> * Kapcsolódás a Data Box Heavyhoz

::: zone-end

::: zone target = "chromeless"

Ez az útmutató útmutatást nyújt az előfeltételek, a kábelek és az eszköz csatlakoztatása, az adatok másolása, az Azure-ba való feltöltés és a feltöltött adatok ellenőrzéséhez.

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az [oktatóanyagot: Megrendelés Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Megkapta a Data Box Heavyt, és a portálon megjelenő megrendelés állapota **kézbesítve**.
3. Áttekintette a [Data Box Heavy biztonsági irányelveit](data-box-safety.md).
4. Hozzá kell férnie egy olyan, az adatközpontban található lapos webhelyhez, amely egy olyan elérhető hálózati kapcsolat közelében van, amely képes az adott helyigényű eszköz befogadására. Az eszköz nem csatlakoztatható állványhoz.
5. Négy megalapozott tápkábelt kapott a tárolóeszköz használatára.
6. Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box Heavy a számítógépről másolja az adatait. A gazdagépnek [támogatott operációs rendszert](data-box-heavy-system-requirements.md)kell futtatnia.
7. Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 
8. A helyi felhasználói felülethez való kapcsolódáshoz és az eszköz konfigurálásához egy RJ-45 kábelt tartalmazó laptop szükséges. A laptop használatával konfigurálja egyszer az eszköz egyes csomópontjait.
9. Az eszköz csomópontjain 1 40-Gbps vagy 10 GB/s-os kábelre van szükség.
    - Válassza ki azokat a kábeleket, amelyek kompatibilisek a [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adapterrel.
    - Az 40-Gbps kábel esetében a kábel QSFP +-nak kell lennie.
    - A 10 GB/s-os kábel esetében olyan SFP + kábelre van szükség, amely egy 10 GB/s-os kapcsolóra csatlakozik egy végponton, és egy QSFP + – SFP + adapterrel (vagy a QSA-adapterrel) az eszközhöz csatlakozó végponthoz.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Az eszköz csatlakoztatása a powerhez

Az eszköz csatlakoztatásához hajtsa végre az alábbi lépéseket.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. [Lépjen kapcsolatba Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md) azonnal, hogy segítsen felmérni, hogy az eszköz megfelelően működik-e, és hogy szükség van-e a cserére.
2. Helyezze át az eszközt a telepítési helyre.

    ![Data Box Heavy eszköz telepítési helye](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zárolja a hátsó görgőket az eszközön az alább látható módon.

    ![Eszközök Data Box Heavy zárolva](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Keresse meg az eszköz elülső és hátsó ajtajait feloldó gombokat. Oldja fel és helyezze át a bejárati ajtót, amíg az az eszköz oldalára nem lesz ürítve. Ismételje meg a műveletet a hátsó ajtóval is.
    Mindkét ajtónak nyitva kell maradnia, amikor az eszköz működőképes, hogy lehetővé tegye az eszközön az optimális, elölről visszaáramló levegő áramlását.

    ![Nyitott ajtók Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Az eszköz hátoldalán lévő tálcának négy tápkábelrel kell rendelkeznie. Távolítsa el az összes kábelt a tálcáról, és helyezze azokat félre.

    ![Data Box Heavy tápkábelek a tálcában](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A következő lépés az eszköz hátoldalán található különböző portok azonosítása. Kétféle csomópont, **csomópont1** és **Csomópont2**található. Mindegyik csomóponthoz négy hálózati adapter, **mgmt**, **DATA1**, **DATA2**és **DATA3**tartozik. A **mgmt** a felügyelet konfigurálására szolgál az eszköz kezdeti konfigurálása során. **A DATA1**-**DATA3** adatportok. A **mgmt** és a **DATA3** portok 1 GB/s, míg a **DATA1**, a **DATA2** 40 – Gbps vagy 10 GB/s portokkal működhet. A két eszköz csomópontjainak alján négy energiaellátási egység (PSUs) van, amelyek a két eszköz csomópontjain vannak megosztva. Az eszközön való szembenézés után a **PSUs** a **PSU1**, a **PSU2**, a **PSU3**és a **PSU4** , balról jobbra.

    ![Data Box Heavy portok](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Csatlakoztasson mind a négy tápkábelt az eszköz energiaellátási eszközeihez. A zöld LED-ek bekapcsolják és villognak.
8. Az eszköz csomópontjainak bekapcsolásához használja az első síkon lévő főkapcsoló gombokat. Tartsa lenyomva a főkapcsoló gombját néhány másodpercig, amíg a kék fény be nem következik. Az eszköz hátulján lévő tápegységek zöld LED-je most már nem lehet stabil. Az eszköz elülső operációs paneljén a hibák LED-ek is szerepelnek. Ha a hiba LED-je világít, a hibás TÁPEGYSÉGet vagy ventilátort vagy a lemezmeghajtókkal kapcsolatos problémát jelez.  

    ![Data Box Heavy elülső Ops panel](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Hálózati kábel első csomópontja

Az eszköz egyik csomópontján hajtsa végre a következő lépéseket a hálózati kábel csatlakoztatásához.

1. A gazdagép számítógépének 1 GB/s-os felügyeleti portjához való csatlakoztatásához használjon CAT 6 RJ-45 hálózati kábelt (a képen kék kábelt).
2. QSFP + kábel (Fiber vagy réz) használatával csatlakoztassa legalább 1 40 GB/s (előnyben részesített 1 GB/s) hálózati adaptert az adatkezeléshez. Ha 10 GB/s-os kapcsolót használ, használjon egy SFP + kábelt egy QSFP + és SFP + adapter (a QSA adapter) használatával az 40 GB/s hálózati adapter csatlakoztatásához az adatkapcsolathoz.

    ![Data Box Heavy-portok összekábele](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > Az 1. és a DATA2 nem egyeznek a helyi webes felhasználói felületen megjelenített ÉRTÉKekkel.
    > Az 40 GB/s-os kábelhossz a lent látható módon csatlakozik.

    ![Data Box Heavy 40 – Gbps kábel adapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Az első csomópont konfigurálása

A következő lépésekkel állíthatja be az eszközt a helyi konfiguráció és a Azure Portal használatával.

1. Töltse le az eszköz hitelesítő adatait a portálról. Lépjen az **Általános > Eszköz adatai** menüpontra. Másolja ki **az eszköz jelszavát**. Ezek a jelszavak egy adott sorrendhez vannak kötve a portálon. A Data Box Heavy két csomópontjának megfelelően megjelenik a két eszköz sorozatszáma. Az eszköz rendszergazdai jelszava mindkét csomópont esetében azonos.

    ![Data Box Heavy eszköz hitelesítő adatai](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Csatlakoztassa az ügyfél-munkaállomást az eszközhöz egy CAT6 RJ-45 hálózati kábelen keresztül.
3. Konfigurálja az Ethernet-adaptert azon a számítógépen, amelyről a statikus IP-címmel `192.168.100.5` és alhálózattal `255.255.255.0`csatlakozik az eszközhöz.

    ![Data Box Heavy a helyi webes felhasználói felülethez csatlakozik](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Kapcsolódjon az eszköz helyi webes FELÜLETéhez a következő URL-címen: `http://192.168.100.10`. Kattintson a **speciális** elemre, majd **a folytatás a 192.168.100.10 (nem biztonságos)** elemre.
5. Megjelenik a helyi webes felhasználói felület **bejelentkezési** oldala.
    
    - Ezen az oldalon az egyik csomópont-sorozatszám a portál felhasználói felületén és a helyi webes felületen is megfelel. Jegyezze fel a csomópont számát a sorozatszám leképezéséhez. A portálon két csomópont és két eszköz sorozatszáma található. Ez a leképezés segít megismerni, hogy melyik csomópont felel meg a sorozatszámnak.
    - Ekkor az eszköz még le van zárva.
    - Adja meg az eszközre való bejelentkezéshez az előző lépésben beszerzett rendszergazdai jelszót. Kattintson a **Bejelentkezés** elemre.

    ![Bejelentkezés Data Box Heavy helyi webes felhasználói felületre](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Az irányítópulton ellenőrizze, hogy a hálózati adapterek konfigurálva vannak-e. Az eszköz csomópontján négy hálózati adapter található, két 1 GB/s és 2 40 GB/s. Az 1 GB/s-os interfészek egyike egy felügyeleti felület, ezért nem lehet a felhasználó által konfigurálható. A fennmaradó három hálózati adapter az adathoz van hozzárendelve, és a felhasználó konfigurálható.

- Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik.
- Ha a DHCP nincs engedélyezve, lépjen a hálózati adapterek beállítása elemre, és szükség esetén rendeljen hozzá statikus IP-címeket.

    ![Data Box Heavy irányítópult-csomópont 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Második csomópont konfigurálása

Hajtsa végre az eszköz második csomópontjának [első csomópontjának konfigurálása](#configure-first-node) című témakör lépéseit.

![Data Box Heavy irányítópult-csomópont 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Ha az eszköz beállításával elkészült, csatlakozhat az eszközmegosztásokhoz, és a számítógépről az eszközre másolhatja az adatokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megAzure Data Box Heavy ismerte a következő témaköröket:

> [!div class="checklist"]
> * Csatlakoztassa a Data Box Heavy
> * Kapcsolódás a Data Box Heavyhoz

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan másolhat Adatmásolást a Data Box Heavy.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra](./data-box-heavy-deploy-copy-data.md)

::: zone-end
