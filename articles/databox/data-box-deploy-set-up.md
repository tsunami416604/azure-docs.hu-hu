---
title: Az Azure Data Box beállítása | Microsoft Docs
description: Az Azure Data Box szoftveres és hálózati követelményeinek ismertetése
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6623744a26d79ccbc8b68ba84baf3a37024e0ed7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952307"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Oktatóanyag: Az Azure Data Box bekábelezése és csatlakoztatása

Az oktatóanyag az Azure Data Box bekábelezését, csatlakoztatását és bekapcsolását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte [az Azure Data Box rendelését ismertető oktatóanyagot](data-box-deploy-ordered.md).
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú. 
3. Áttekintette [a Data Box biztonsági útmutatásait](data-box-safety.md).
4. Kapott egy földelt tápkábelt a 100 TB-os tárolóeszközhöz.
5. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE kapcsolat nem áll rendelkezésre, egy 1 GbE sebességű adatkapcsolat is használható, azonban ez rontja a másolási sebességet. 
6. Szüksége lesz egy vízszintes felületre, ahová a Data Boxot helyezheti. Ha az eszközt egy standard kiszolgálószekrény-polcra szeretné helyezni, egy legalább 7U méretű helyre lesz szükség az adatközponti szekrényben. Az eszközt a talpán vagy az oldalán is elhelyezheti a szekrényben.
7. A következő kábelekkel rendelkezik a Data Box a gazdagéphez való csatlakoztatásához.
    - Két 10 GbE SFP+ Twinax rézkábel (a DATA 1 és DATA 2 hálózati adapterekhez)
    - Egy RJ-45 CAT 6 hálózati kábel (az MGMT hálózati adapterhez)
    - Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

## <a name="cable-your-device"></a>Az eszköz bekábelezése

Kövesse az alábbi lépéseket az eszköz bekábelezéséhez.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. Azonnal vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e, vagy esetleg küldenünk kell egy csereeszközt.
2. Helyezze az eszközt arra a helyre, ahol be kívánja kapcsolni. Helyezze az eszközt egy vízszintes felületre. Az eszközt egy standard kiszolgálószekrény-polcra is helyezheti.
3. Csatlakoztassa a tápkábelt és a hálózati kábelt. Alább látható egy gyakori konfigurációban csatlakoztatott eszköz hátlapja. 
    
    ![Data Box-eszköz bekábelezett hátlapja](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Csatlakoztassa a tápkábelt a jelzett tápbemeneti helyre. A tápkábel másik végét egy áramelosztó egységhez kell csatlakoztatnia.
    2. Az RJ-45 CAT 6-kábellel csatlakoztasson egy laptopot az eszköz felügyeleti portjára (MGMT).            
    3. Az RJ-45 CAT 6A-kábel egyik végét csatlakoztassa a DATA 3-portra. A DATA 3-port 10 GbE sebességre van konfigurálva RJ-45 CAT 6A-kábellel való csatlakozáshoz, és 1 GbE sebességre RJ-45 CAT 6-kábellel való csatlakozáshoz.
    4. A 10 GbE SFP+ Twinax-rézkábeleket csatlakoztassa a DATA 1- és DATA 2-porthoz. 
    5. Az adatportokra csatlakoztatott kábelek másik vége a gazdagéphez csatlakozik egy 10 GbE sebességű kapcsolón keresztül.

4. Keresse meg a bekapcsológombot az eszköz előlapján. Kapcsolja be az eszközt.

    ![A Data Box bekapcsológombja](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Csatlakozás az eszközhöz

Az eszköz beállításához kövesse az alábbi lépéseket a helyi webes felhasználói felületen és a portál felhasználói felületén.

1. Azon a laptopon, amellyel az eszközhöz csatlakozik, állítsa be az Ethernet-adaptert a 192.168.100.5 statikus IP-cím és a 255.255.255.0 alhálózat használatára. 
2. Csatlakozzon az eszköz felügyeleti portjához (MGMT), hogy hozzáférjen a helyi webes felhasználói felülethez a következő címen: https://192.168.100.10. Ez az eszköz bekapcsolását követően 5 percet is igénybe vehet.
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

Ha az eszköz beállításával elkészült, csatlakozhat az eszközmegosztásokhoz, és a számítógépről az eszközre másolhatja az adatokat. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat a Data Boxra.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra](./data-box-deploy-copy-data.md)

