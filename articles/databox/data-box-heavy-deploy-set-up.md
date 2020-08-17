---
title: Az Azure Data Box Heavy beállítása – oktatóanyag | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan kábelezheti be, csatlakoztathatja és kapcsolhatja be az Azure Data Box Heavyt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920942"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Oktatóanyag: Az Azure Data Box Heavy bekábelezése és csatlakoztatása

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Ismerkedés az Azure Data Box Heavyvel

::: zone-end

::: zone target = "docs"

Az oktatóanyag az Azure Data Box Heavy bekábelezését, csatlakoztatását és bekapcsolását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Heavy bekábelezése
> * A Data Box Heavy csatlakoztatása

::: zone-end

::: zone target = "chromeless"

Ebből az útmutatóból megtudhatja, hogyan történik az előfeltételek áttekintése, az eszköz bekábelezése és csatlakoztatása, az adatok másolása és Azure-ba való feltöltése, illetve a feltöltött adatok jóváhagyása.

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Azure Data Box Heavy megrendelését ismertető oktatóanyag](data-box-heavy-deploy-ordered.md) végére ért.
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Áttekintette [a Data Box Heavy biztonsági útmutatásait](data-box-safety.md).
4. Szüksége lesz az adatközpontban egy olyan vízszintes felületű helyre, amelynek közelében van elérhető hálózati csatlakozási lehetőség, és ahol elfér egy ilyen helyigényű eszköz. Az eszköz nem rögzíthető állványra.
5. Kapott négy földelt tápkábelt a tárolóeszközhöz.
6. Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box Heavy erről a számítógépről másolja majd az adatokat. A gazdagépen [támogatott operációs rendszernek](data-box-heavy-system-requirements.md) kell futnia.
7. Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 
8. RJ-45 kábellel ellátott laptop szükséges a helyi felhasználó felülethez való csatlakozáshoz és az eszköz konfigurálásához. A laptop használatával konfigurálhatja az eszköz csomópontjait.
9. Eszközcsomópontonként egy-egy 40 Gb/s-os vagy 10 Gb/s-os kábelre van szükség.
    - A [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adapterrel kompatibilis kábeleket válasszon.
    - A 40 Gb/s-os kábel esetében a kábel eszközoldali végének QSFP+ rendszerűnek kell lennie.
    - A 10 Gb/s-os kábel esetén szükség van egy olyan SFP+ kábelre, amely az egyik végén egy 10 Gb/s-os kapcsolóhoz, az eszközoldali végén pedig egy QSFP+–SFP+ adapterhez (vagy a QSA-adapterhez) csatlakozik.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Az eszköz tápkábeleinek bekötése

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. Azonnal [vegye fel a kapcsolatot a Microsoft támogatási szolgálatával](data-box-disk-contact-microsoft-support.md), ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e, vagy esetleg küldenünk kell egy csereeszközt.
2. Vigye az eszközt az üzembehelyezési helyre.

    ![A Data Box Heavy üzembehelyezési helye](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Rögzítse az eszköz hátsó görgőit az alább látható módon.

    ![A Data Box Heavy görgői rögzített állapotban](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Keresse meg az eszköz elülső és hátsó ajtaját nyitó gombokat. Nyissa ki az elülső ajtót az eszköz oldalával párhuzamos állásba. Ezt ismételje meg a hátsó ajtóval is.
    Mindkét ajtónak nyitva kell lennie az eszköz működése közben, hogy lehetővé váljon az elölről hátrafelé irányú optimális légmozgás az eszközön keresztül.

    ![A Data Box Heavy nyitott ajtókkal](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Az eszköz hátoldali tálcáján négy tápkábelnek kell lennie. Távolítsa el a kábeleket a tálcáról, és tegye félre őket.

    ![A Data Box Heavy tápkábelei a tálcán](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A következő lépés az eszköz hátulján található különböző portok azonosítása. Itt két eszközcsomópont található, a **NODE1** és a **NODE2**. Mindegyik csomópont a következő négy hálózati adapterrel rendelkezik: **MGMT**, **DATA1**, **DATA2**, **DATA3**. Az **MGMT** a felügyelet konfigurálására használható az eszköz kezdeti beállítása során. **DATA1**-**DATA3**: adatportok. Az **MGMT** és a **DATA3** 1 Gb/s sebességű, míg a **DATA1** és a **DATA2** 40 Gb/s vagy 10 Gb/s sebességű portként is üzemeltethető. A két eszközcsomópont alján négy tápegység (PSU) található, amelyek a két eszközcsomópont között vannak megosztva. Ha szemben áll az eszközzel, a **PSU-k** nevei balról jobbra haladva a következők: **PSU1**, **PSU2**, **PSU3** és **PSU4**.

    ![A Data Box Heavy portjai](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Csatlakoztassa mind a négy tápkábelt az eszköz tápegységeihez. A zöld LED-ek bekapcsolnak és villogni kezdenek.
8. Az eszközcsomópontok bekapcsolásához nyomja meg a bekapcsológombokat az eszköz előlapján. Tartsa nyomva a bekapcsológombot néhány másodpercig, amíg világítani nem kezdenek a kék fények. Az eszköz hátoldalán a tápegységek zöld LED-jeinek ekkor folyamatosan világítaniuk kell. Az eszköz elülső kezelőpaneljén is találhatók hibajelző LED-ek. Ha a hibajelző LED világít, az a tápegység vagy a ventilátor meghibásodására, illetve a lemezmeghajtóval kapcsolatos problémára utal.  

    ![A Data Box Heavy elülső kezelőpanelje](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Az első csomópont bekötése a hálózatba

Végezze el az alábbi lépéseket az eszköz egyik csomópontján a hálózatba történő bekötéshez.

1. CAT 6 RJ-45 hálózati kábellel (az MGMT csatlakozóhoz csatlakoztatott, jobb felső kábel a képen) csatlakoztassa a gazdagépet az 1 Gb/s-os felügyeleti porthoz.
2. QSFP+ (üvegszálas vagy réz-) kábellel csatlakoztassa legalább az egyik 40 Gb/s-os (előnyben részesítve az 1 Gb/s-ossal szemben) hálózati adaptert az adatforgalom számára. Ha 10 Gb/s-os kapcsolót használ, használjon SFP+ kábelt QSFP+–SFP+ adapterrel (QSA-adapterrel) a 40 Gb/s-os hálózati adapter csatlakoztatásához az adatforgalom számára.

    ![A Data Box Heavy portjai bekábelezve](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > A DATA 1 és a DATA2 fel vannak cserélve, és nem egyeznek meg a helyi webes felhasználói felületen található képen látható portokkal.
    > A 40 Gb/s-os kábeladapter csatlakoztatása az alább látható módon történik.

    ![A Data Box Heavy 40 Gb/s-os kábeladaptere](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Az első csomópont konfigurálása

Kövesse az alábbi lépéseket az eszköz beállításához a helyi konfiguráció és az Azure Portal használatával.

1. Töltse le az eszköz hitelesítő adatait a portálról. Lépjen az **Általános > Eszköz adatai** menüpontra. Másolja ki **az eszköz jelszavát**. Ezek a jelszavak megadott sorrendhez kötöttek a portálon. A Data Box Heavy két csomópontjának megfelelően láthatja a két eszköz sorozatszámát. A két csomópont szolgáltatásadminisztrátori jelszava megegyezik.

    ![A Data Box Heavy eszközhitelesítő adatai](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Csatlakoztassa az ügyfél-munkaállomást az eszközhöz egy CAT6 RJ-45 hálózati kábellel.
3. Azon a számítógépen, amellyel az eszközhöz csatlakozik, állítsa be az Ethernet-adaptert a `192.168.100.5` statikus IP-cím és a `255.255.255.0` alhálózat használatára.

    ![A Data Box Heavy csatlakozik a helyi webes felhasználói felülethez](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Csatlakozzon az eszköz helyi webes felhasználói felületéhez a következő URL-címen: `http://192.168.100.10`. Kattintson a **Speciális**, majd a **Továbblépés a 192.168.100.10 címre (nem biztonságos)** elemre.
5. Megjelenik a helyi webes felhasználói felület **bejelentkezési** oldala.
    
    - Az oldalon látható egyik csomópont-sorozatszám megegyezik a helyi webes felhasználói felületen és a portál felhasználói felületén. Jegyezze fel a csomópont számát a sorozatszám társításához. A portálon két csomópont és két eszközsorozatszám látható. Ezzel a megoldással megtudhatja, hogy melyik csomópont melyik sorozatszámnak felel meg.
    - Ekkor az eszköz még le van zárva.
    - Használja az előző lépésben beszerzett szolgáltatásadminisztrátori jelszót az eszközre való bejelentkezéshez. Kattintson a **Bejelentkezés** elemre.

    ![Bejelentkezés a Data Box Heavy helyi webes felhasználói felületére](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Az Irányítópulton győződjön meg róla, hogy a hálózati adapterek be vannak állítva. Az eszközcsomóponton négy hálózati adapter található: két 1 Gb/s-os és két 40 Gb/s-os. Az egyik 1 Gb/s-os adapter felügyeleti adapter, ezért felhasználó által nem konfigurálható. A többi három hálózati adapter az adatforgalom számára van dedikálva, és felhasználó által konfigurálható.

- Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik.
- Ha a DHCP nincs engedélyezve, lépjen a Hálózati adapterek beállítása lapra, és szükség esetén rendeljen hozzájuk statikus IP-címeket.

    ![Data Box Heavy-irányítópult – 1. csomópont](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Második csomópont konfigurálása

Végezze el [Az első csomópont konfigurálása](#configure-first-node) részben ismertetett lépéseket az eszköz második csomópontja esetében is.

![Data Box Heavy-irányítópult – 2. csomópont](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Ha az eszköz beállításával elkészült, csatlakozhat az eszközmegosztásokhoz, és a számítógépről az eszközre másolhatja az adatokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * A Data Box Heavy bekábelezése
> * A Data Box Heavy csatlakoztatása

A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat a Data Box Heavyre.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra](./data-box-heavy-deploy-copy-data.md)

::: zone-end
