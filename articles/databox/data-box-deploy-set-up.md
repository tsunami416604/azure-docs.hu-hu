---
title: Az Azure Data Box beállítása | Microsoft Docs
description: Bemutatja az Azure Data Box bekábelezését és csatlakoztatását
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: alkohli
ms.openlocfilehash: b601ad5936820e2c237b7b9d37d9af73aa468bbc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895753"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Oktatóanyag: Kábelek, és csatlakozzon az Azure Data Box

Az oktatóanyag az Azure Data Box bekábelezését, csatlakoztatását és bekapcsolását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejeződött a [oktatóanyag: Az Azure Data Box ORDER](data-box-deploy-ordered.md).
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú. 
    - Az aktuális fuvarlevélcímke alatt egy átlátszó zacskó van rögzítve az eszközre, benne egy másik címkével. Őrizze meg ezt a címkét, mivel ezt fogja használni a küldemény visszaküldéséhez.
    - Az eszköz egy csomagolt egyes európai régiókban jelenhet meg. Győződjön meg arról, hogy az eszköz kicsomagolása, és mentse a mezőbe a visszaszállításhoz.
3. Áttekintette [a Data Box biztonsági útmutatásait](data-box-safety.md).
4. Kapott egy földelt tápkábelt a 100 TB-os tárolóeszközhöz.
5. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, egy 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet. 
6. Szüksége lesz egy vízszintes felületre, ahová a Data Boxot helyezheti. Ha az eszközt egy standard kiszolgálószekrény-polcra szeretné helyezni, egy legalább 7U méretű helyre lesz szükség az adatközponti szekrényben. Az eszközt a talpán vagy az oldalán is elhelyezheti a szekrényben.
7. A következő kábelekkel rendelkezik a Data Box a gazdagéphez való csatlakoztatásához.
    - Egy vagy több 10-GbE SFP + rézeres Twinax vagy SFP + száloptikai kábeleit (az adatok 1, a DATA 2 hálózati adapterek használata). Data Box a a Mellanox ConnectX®-3 Pro EN kétportos 10GBASE-T adapterek használatával a PCI Express 3.0-s hálózati adaptert, rendelkezik, így az adott csatolón kompatibilis kábelek működnie kell. A CISCO SFP-H10GB-CU3M 10GBASE-CU TWINMAX SFP + 3 M például kábel inhouse tesztelési lett megadva. További információkért lásd: a [támogatott kábelek és kapcsolók a Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Egy RJ-45 CAT 6 hálózati kábel (az MGMT hálózati adapterhez)
    - Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

## <a name="cable-your-device"></a>Az eszköz bekábelezése

Kövesse az alábbi lépéseket az eszköz bekábelezéséhez.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. Azonnal vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e, vagy esetleg küldenünk kell egy csereeszközt.
2. Helyezze az eszközt arra a helyre, ahol be kívánja kapcsolni. Helyezze az eszközt egy vízszintes felületre. Az eszközt egy standard kiszolgálószekrény-polcra is helyezheti.
3. Csatlakoztassa a tápkábelt és a hálózati kábelt. Alább látható egy gyakori konfigurációban csatlakoztatott eszköz hátlapja. A környezettől függően kiválaszthatják a többi [beállítások kábelezést](data-box-cable-options.md).
    
    ![Data Box-eszköz bekábelezett hátlapja](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Csatlakoztassa a tápkábelt a jelzett tápbemeneti helyre. A tápkábel másik végét egy áramelosztó egységhez kell csatlakoztatnia.
    2. Az RJ-45 CAT 6-kábellel csatlakoztasson egy laptopot az eszköz felügyeleti portjára (MGMT).            
    3. Az RJ-45 CAT 6A-kábel egyik végét csatlakoztassa a DATA 3-portra. A DATA 3-port 10 GbE sebességre van konfigurálva RJ-45 CAT 6A-kábellel való csatlakozáshoz, és 1 GbE sebességre RJ-45 CAT 6-kábellel való csatlakozáshoz.
    4. A hálózati adaptereket, az adatátviteli szeretné csatlakoztatni, attól függően használatával legfeljebb két 10-GbE SFP + rézeres Twinax vagy SFP + száloptikai kábeleit az adatok 1 és a DATA 2 portok jelölik. 
    5. Az adatportokhoz csatlakoztatott kábelek másik vége a gazdagéphez csatlakozik egy 10 GbE sebességű kapcsolón keresztül.

4. Keresse meg a bekapcsológombot az eszköz előlapján. Kapcsolja be az eszközt.

    ![A Data Box bekapcsológombja](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Csatlakozás az eszközhöz

Az eszköz beállításához kövesse az alábbi lépéseket a helyi webes felhasználói felületen és a portál felhasználói felületén.

1. Azon a laptopon, amellyel az eszközhöz csatlakozik, állítsa be az Ethernet-adaptert a 192.168.100.5 statikus IP-cím és a 255.255.255.0 alhálózat használatára. 
2. MGMT port az eszköz csatlakozik, és a helyi webes felületén, https eléréséhez\:/ / 192.168.100.10. Ez az eszköz bekapcsolását követően 5 percet is igénybe vehet.
3. Kattintson a **Részletek**, majd a **Továbblépés a weblapra** elemre.

   ![Csatlakozás a helyi webes felhasználói felülethez](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. Megjelenik a helyi webes felhasználói felület **bejelentkezési** oldala. Győződjön meg róla, hogy az eszköz sorozatszáma megegyezik a helyi webes felhasználói felületen és a portál felhasználói felületén. Ekkor az eszköz még le van zárva.
5. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
6. Töltse le az eszköz hitelesítő adatait a portálról. Lépjen az **Általános > Eszköz adatai** menüpontra. Másolja ki **az eszköz jelszavát**. Az eszköz jelszava a portálon megadott sorrendhez kötött. 

    ![Eszköz hitelesítő adatainak letöltése](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Használja az előző lépésben az Azure Portalról beszerzett eszközjelszót az eszköz helyi webes felületére való bejelentkezéshez. Kattintson a **Bejelentkezés** elemre.
8. Az **Irányítópulton** győződjön meg róla, hogy a hálózati adapterek be vannak állítva. 
   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. 
   - Ha a DHCP nincs engedélyezve, lépjen a **Hálózati adapterek beállítása** lapra, és szükség esetén rendeljen hozzájuk statikus IP-címeket.

     ![Az eszköz irányítópultja](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Az adathálózati adapterek konfigurálása konfigurálását követően a DATA 1–DATA 3 adapterek bármelyikének IP-címét használhatja a helyi webes felhasználói felülethez való hozzáféréshez a következő címen: `https://<IP address of a data network interface>`. 

Ha az eszköz beállításával elkészült, csatlakozhat az eszközmegosztásokhoz, és a számítógépről az eszközre másolhatja az adatokat. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat a Data Boxra.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra](./data-box-deploy-copy-data.md)

