---
title: Oktatóanyag az Azure Data Box beállításához | Microsoft Docs
description: Bemutatja az Azure Data Box bekábelezését és csatlakoztatását
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c74b5ba5101dae9165898aeb4f265d449988ecab
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205235"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Oktatóanyag: Az Azure Data Box bekábelezése és csatlakoztatása

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Az eszköz bekábelezése és csatlakoztatása

::: zone-end

::: zone target="docs"

Az oktatóanyag az Azure Data Box bekábelezését, csatlakoztatását és bekapcsolását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Megrendelt egy Azure Data Boxot.
    - Az importálási rendelésekkel kapcsolatos információkért lásd [ Az Azure Data Box megrendelése](data-box-deploy-ordered.md) című oktatóanyagot.
    - Exportálási rendelésekkel kapcsolatos információkért lásd: [Oktatóanyag: Az Azure Data Box megrendelése](data-box-deploy-export-ordered.md)
1. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú. 
    - Az aktuális fuvarlevélcímke alatt egy átlátszó zacskó van rögzítve az eszközre, benne egy másik címkével. Őrizze meg ezt a címkét, mivel ezt fogja használni a küldemény visszaküldéséhez.
    - Egyes európai régiókban előfordulhat, hogy az eszköz egy dobozba csomagolva érkezik. Csomagolja ki az eszközt, és őrizze meg a dobozt a visszaküldéshez.
1. Áttekintette [a Data Box biztonsági útmutatásait](data-box-safety.md).
1. Kapott egy földelt tápkábelt a 100 TB-os tárolóeszközhöz.
1. Rendelkezik egy gazdagéppel, amelyet az adatok Data Boxra való (importálási rendelés) vagy onnan való (exportálási rendelés) másolására használ. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, egy 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet. 
1. Szüksége lesz egy vízszintes felületre, ahová a Data Boxot helyezheti. Ha az eszközt egy standard kiszolgálószekrény-polcra szeretné helyezni, egy legalább 7U méretű helyre lesz szükség az adatközponti szekrényben. Az eszközt a talpán vagy az oldalán is elhelyezheti a szekrényben.
1. A következő kábelekkel rendelkezik a Data Box a gazdagéphez való csatlakoztatásához.
    - Egy vagy több 10 GbE SFP+ Twinax rézkábel vagy SFP+ száloptikai kábel (a DATA 1 és DATA 2 hálózati adapterekhez). A Data Box PCI Express 3.0 hálózati adapterrel felszerelt Mellanox ConnectX®-3 Pro EN kétportos 10GBASE-T adapterekkel rendelkezik, így az ezzel az adapterrel kompatibilis kábeleknek működniük kell. A belső teszteléshez például egy CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M kábelt használtunk. További információkért tekintse meg a [Mellanox támogatott kábeleinek és kapcsolóinak listáját](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Egy RJ-45 CAT 6 hálózati kábel (az MGMT hálózati adapterhez)
    - Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

## <a name="cable-your-device"></a>Az eszköz bekábelezése

Kövesse az alábbi lépéseket az eszköz bekábelezéséhez.

1. Vizsgálja meg, hogy az eszközön nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. Amennyiben az eszközön jelentős sérülés vagy illetéktelen felnyitás nyomai észlelhetők, ne folytassa. Azonnal vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e, vagy esetleg küldenünk kell egy csereeszközt.
2. Helyezze az eszközt arra a helyre, ahol be kívánja kapcsolni. Helyezze az eszközt egy vízszintes felületre. Az eszközt egy standard kiszolgálószekrény-polcra is helyezheti.
3. Csatlakoztassa a tápkábelt és a hálózati kábelt. Alább látható egy gyakori konfigurációban csatlakoztatott eszköz hátlapja. A környezetétől függően választhat más [kábelezési lehetőségek](data-box-cable-options.md) közül is.
    
    ![Data Box-eszköz bekábelezett hátlapja](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Csatlakoztassa a tápkábelt a jelzett tápbemeneti helyre. A tápkábel másik végét egy áramelosztó egységhez kell csatlakoztatnia.
    2. Az RJ-45 CAT 6-kábellel csatlakoztasson egy laptopot az eszköz felügyeleti portjára (MGMT).            
    3. Az RJ-45 CAT 6A-kábel egyik végét csatlakoztassa a DATA 3-portra. A DATA 3-port 10 GbE sebességre van konfigurálva RJ-45 CAT 6A-kábellel való csatlakozáshoz, és 1 GbE sebességre RJ-45 CAT 6-kábellel való csatlakozáshoz.
    4. Az adatátvitel céljából csatlakoztatni kívánt hálózati adapterektől függően legfeljebb két 10 GbE sebességű SFP+ Twinax-rézkábelt vagy SFP+ száloptikai kábelt használjon a DATA 1 és a DATA 2 porthoz történő csatlakozáshoz. 
    5. Az adatportokhoz csatlakoztatott kábelek másik vége a gazdagéphez csatlakozik egy 10 GbE sebességű kapcsolón keresztül.

4. Keresse meg a bekapcsológombot az eszköz előlapján. Kapcsolja be az eszközt.

    ![A Data Box bekapcsológombja](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Az eszköz megérkezése után be kell kábeleznie és csatlakoztatnia kell azt. 

## <a name="cable-your-device"></a>Az eszköz bekábelezése

1. Amennyiben az eszközön sérülés vagy illetéktelen hozzáférés nyomai észlelhetők, ne folytassa az üzembe helyezést. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, és küldessen egy csereeszközt.
2. Az eszköz kábeles csatlakoztatása előtt ellenőrizze, hogy rendelkezik-e az alábbi kábelekkel:
    
    - (Mellékelve) egy földelt 10 A vagy nagyobb névleges kapacitású tápkábel az egyik végén IEC60320 C-13 csatlakozóval az eszközhöz való csatlakoztatáshoz.
    - Egy RJ-45 CAT 6 hálózati kábel (az MGMT hálózati adapterhez)
    - Két 10 GbE SFP+ Twinax rézkábel (a 10 Gb/s DATA 1 és DATA 2 hálózati adapterekhez)
    - Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

3. Vegye ki az eszközt a dobozból, és helyezze egy vízszintes felületre. 
    
4. Csatlakoztassa a kábeleket az eszközhöz az ábra szerint.  

    ![Data Box-eszköz bekábelezett hátlapja](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Csatlakoztassa a tápkábelt az eszközhöz.
    2. Az RJ-45 CAT 6 hálózati kábellel csatlakoztassa a gazdagépet az eszköz felügyeleti portjához (MGMT). 
    3. Az SFP+ Twinax rézkábellel csatlakoztassa legalább az egyik 10 Gb/s (előnyben részesítve az 1 Gb/s-ossal szemben) hálózati adaptert, a DATA 1 vagy a DATA 2 adaptert az adatforgalom számára. 
    4. Kapcsolja be az eszközt. A bekapcsológomb az eszköz előlapján található.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Csatlakozás az eszközhöz

Az eszköz beállításához kövesse az alábbi lépéseket a helyi webes felhasználói felületen és a portál felhasználói felületén.

1. Azon a laptopon, amellyel az eszközhöz csatlakozik, állítsa be az Ethernet-adaptert a 192.168.100.5 statikus IP-cím és a 255.255.255.0 alhálózat használatára. 
2. Csatlakozzon az eszköz felügyeleti portjához (MGMT), hogy hozzáférjen a helyi webes felhasználói felülethez a következő címen: https\://192.168.100.10. Ez az eszköz bekapcsolását követően 5 percet is igénybe vehet.
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

Ha elkészült az eszköz beállításával, csatlakozhat az eszközmegosztásokhoz, és átmásolhatja az adatokat. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Az eszköz csatlakoztatása

1. Az eszköz jelszavát az [Azure Portal](https://portal.azure.com)**Általános > Eszköz adatai** lapján találja.
2. A Data Boxhoz csatlakoztatott számítógépen rendelje a 192.168.100.5 statikus IP-címet és a 255.255.255.0 alhálózatot az Ethernet-adapterhez. Nyissa meg az eszköz helyi webes felületét a következő helyen: `https://192.168.100.10`. A csatlakozás az eszköz bekapcsolását követően 5 percet is igénybe vehet. 
3. Jelentkezzen be az Azure Portalról beszerzett jelszóval. Egy hibaüzenet jelenik meg, miszerint a webhely biztonsági tanúsítványa hibás. A böngészőspecifikus utasításokat követve lépjen a weblapra.
4. Alapértelmezés szerint a 10 Gb/s (vagy az 1 Gb/s) sebességű adatillesztő DHCP-ként van konfigurálva. Szükség esetén statikusként is konfigurálhatja az illesztőt, és megadhat egy IP-címet. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box bekábelezése
> * Csatlakozás a Data Boxhoz

A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat.

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra importálási rendeléshez](./data-box-deploy-copy-data.md)

Vagy

> [!div class="nextstepaction"]
> [Adatok másolása az Azure Data Boxra exportálási rendeléshez](./data-box-deploy-export-copy-data.md)

::: zone-end

