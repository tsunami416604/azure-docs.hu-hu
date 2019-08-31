---
title: Gyors útmutató a Microsoft Azure Data Box Heavyhoz | Microsoft Docs
description: Ismerkedjen meg a Azure Data Box Heavy gyors üzembe helyezésével Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 433c3ac08d8d7d8158d4cc958979782e3b3192e1
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164421"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Gyors útmutató: Azure Data Box Heavy üzembe helyezése a Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan helyezhetők üzembe a Azure Data Box Heavy a Azure Portal használatával. A lépések közé tartozik az adatok csatlakoztatása, konfigurálása és másolása Data Box Heavyba, hogy feltöltve legyenek az Azure-ba. A rövid útmutató az Azure Portalon és az eszköz helyi webes felületén hajtható végre.

A részletes üzembe helyezési és követési utasításokért keresse fel az [oktatóanyagot: Megrendelés Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Előfeltételek

Az eszköz telepítése előtt végezze el a következő konfigurálási előfeltételeket a telepítési helyhez, Data Box szolgáltatáshoz és az eszközhöz.

### <a name="for-installation-site"></a>A telepítési helyhez

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz az összes bebejáratán elfér. Az eszköz méretei a következők: width: 26 "hossz: 48 "magasság: 28 ".
- Ha az eszközt a földszinten kívüli emeleten szeretné telepíteni, egy lift vagy egy rámpa használatával férhet hozzá az eszközhöz.
- Az eszközt két személy kezelheti. Az eszköz körülbelül ~ 500 lbs-t mérlegel. és a kerekekre kerül.
- Az adatközpontban van egy olyan, a rendelkezésre álló hálózati kapcsolat közelsége, amely képes az adott helyigényű eszköz elhelyezésére.

### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- A Data Box szolgáltatáshoz használt előfizetés a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Data Box Heavy rendelés létrehozásához tulajdonos vagy közreműködő fér hozzá az előfizetéshez.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Áttekintette a [Data Box Heavy biztonsági irányelveit](data-box-safety.md).
- Az adatközpont-hálózathoz csatlakozik egy gazdaszámítógép. Data Box Heavy másolja az adatait a számítógépről. A gazdagépnek [támogatott operációs rendszert](data-box-heavy-system-requirements.md)kell futtatnia.
- A helyi felhasználói felülethez való kapcsolódáshoz és az eszköz konfigurálásához egy RJ-45 kábelt tartalmazó laptop tartozik. A laptop használatával konfigurálja egyszer az eszköz egyes csomópontjait.
- Az adatközpont nagy sebességű hálózattal rendelkezik, és legalább 1 10 GbE-kapcsolatban van.
- Az eszköz csomópontjain 1 40-Gbps vagy 10 GB/s-os kábelre van szükség. Válassza ki a Mellanox MCX314A-BCCT hálózati adapterrel kompatibilis kábeleket:
    - Az 40-Gbps kábel esetében a kábel QSFP +-nak kell lennie.
    - A 10 GB/s-os kábel esetében olyan SFP + kábelre van szükség, amely egy 10 G-os kapcsolóval csatlakozik egy végponton, egy QSFP + és SFP + adapterrel (vagy a QSA-adapterrel), amely az eszközhöz csatlakozik.
- Az energiaellátási kábelek az eszköz hátoldalán található tálcában találhatók.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="order"></a>Rendelés

Ez a lépés nagyjából 5 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon.
2. Válasszon ki egy meglévő előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Importálás** lehetőséget. Adja meg a **Forrásország** mezőben azt a helyet, ahol az adatok jelenleg találhatók, az **Azure-beli célrégió** mezőben pedig az adatátvitel célját.
3. Válassza a **Data Box Heavy**lehetőséget. A maximálisan felhasználható kapacitás 770 TB, és több megrendelést is létrehozhat nagyobb adatméretekhez.
4. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik az eszköz a szállításra való előkészítése.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kábel csatlakoztatása az eszközhöz

Miután áttekintette az előfeltételeket, csatlakoztassa az eszközt, és kapcsolódjon az eszközhöz.

::: zone-end

## <a name="cable-for-power"></a>Tápkábel

Ez a lépés körülbelül 5 percet vesz igénybe.

Amikor megkapja a Data Box Heavy, hajtsa végre az alábbi lépéseket az eszköz csatlakoztatásához, majd kapcsolja be az eszközt.

1. Amennyiben az eszközön sérülés vagy illetéktelen hozzáférés nyomai észlelhetők, ne folytassa az üzembe helyezést. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, és küldessen egy csereeszközt.
2. Helyezze át az eszközt a telepítési helyre, és zárja be a hátsó kerekeket.
3. Csatlakoztasson az összes négy tápkábelt az eszköz hátoldalán található energiaellátási eszközökhöz.
4. Az eszköz csomópontjainak bekapcsolásához használja az első síkon lévő főkapcsoló gombokat.

## <a name="cable-first-node-for-network"></a>Hálózati kábel első csomópontja

Ez a lépés körülbelül 10-15 percet vesz igénybe.

1. Az RJ-45 CAT 6 hálózati kábellel csatlakoztassa a gazdagépet az eszköz felügyeleti portjához (MGMT).
2. A Twinax QSFP + Copper kábel használatával legalább 1 40 GB/s (előnyben részesített 1 GB/s) hálózati adapter, adat 1 vagy adat 2 adatkapcsolattal csatlakoztatható. Ha 10 GB/s-os kapcsolót használ, használjon egy Twinax SFP + Copper kábelt egy QSFP + és SFP + adapter (a QSA adapter) használatával az 40-Gbps hálózati adapter csatlakoztatásához az adataihoz.
3. Csatlakoztassa a kábeleket az eszközhöz az ábra szerint.  

    ![Data Box Heavy kábellel](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Az első csomópont konfigurálása

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Az eszköz jelszavát az [Azure Portal](https://portal.azure.com) **Általános > Eszköz adatai** lapján találja. Ugyanazt a jelszót használja az eszköz mindkét csomópontja esetében.
2. Rendeljen hozzá egy statikus IP-címet a 192.168.100.5 és az alhálózat 255.255.255.0 az Ethernet-adapterhez azon a számítógépen, amelyet a Data Box Heavyhoz való csatlakozáshoz használ. Nyissa meg az eszköz helyi webes felületét a következő helyen: `https://192.168.100.10`. A csatlakozás az eszköz bekapcsolását követően 5 percet is igénybe vehet.
3. Jelentkezzen be az Azure Portalról beszerzett jelszóval. Egy hibaüzenet jelenik meg, miszerint a webhely biztonsági tanúsítványa hibás. A böngészőspecifikus utasításokat követve lépjen a weblapra.
4. Alapértelmezés szerint a csatoló hálózati beállításai (kivéve a MGMT-t) DHCP-ként vannak konfigurálva. Ha szükséges, konfigurálhatja ezeket a csatolókat statikusként, és megadhat egy IP-címet.

## <a name="cable-and-configure-the-second-node"></a>Kábel és a második csomópont konfigurálása

Ez a lépés körülbelül 15-20 percet vesz igénybe.

Kövesse az első csomóponthoz tartozó lépéseket, és konfigurálja a második csomópontot az eszközön.  


::: zone target = "docs"

## <a name="copy-data"></a>Adatok másolása

A művelet elvégzéséhez szükséges idő az adatok méretétől és annak a hálózatnak a sebességétől függ, amelyen az adatok másolása megtörtént.
 
1. Másolja az összes eszközt az eszközök csomópontjaira az 40-Gbps adatinterfészek párhuzamos használatával.

    - Ha Windows-gazdagépet használ, használjon olyan SMB-kompatibilis fájlmásolás eszközt, mint például a [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - NFS-gazdagép esetén a `cp` parancs vagy a `rsync` használatával másolhatja az adatokat.
2. Kapcsolódjon az eszközön található megosztásokhoz a következő elérési út használatával:`\\<IP address of your device>\ShareName`. A megosztás hozzáférési hitelesítő adatainak beszerzéséhez nyissa meg a Data Box Heavy helyi webes FELÜLETének **kapcsolódás & másolása** lapját.
3. Győződjön meg arról, hogy a megosztási és a mappa neve, valamint az adatkövetés az [Azure Storage-ban és a Data Box Heavy szolgáltatás korlátaiban](data-box-heavy-limits.md)ismertetett irányelvek szerint történjen.

## <a name="prepare-to-ship"></a>Szállításra való előkészítés

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. Miután az Adatmásolás hibák nélkül befejeződött, lépjen a helyi webes felhasználói felületen **szállításra való előkészítés** lapra, és indítsa el a hajó előkészítését.
2. Miután a **szállításra való előkészítés** sikeresen befejeződött a csomópontokon, kapcsolja ki az eszközt a helyi webes kezelőfelületről.

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

A művelet végrehajtása körülbelül 15-20 percet vesz igénybe.

1. Távolítsa el a kábeleket, majd küldje vissza őket az eszköz hátoldalán lévő tálcába.
2. Ütemezzen egy felvételt a regionális szolgáltatóval.
3. Lépjen kapcsolatba [Data Box műveletekkel](mailto:DataBoxOps@microsoft.com) , hogy tájékoztassa a pickupot és a visszaszállítási címkét.
4. A visszatérési szállítási címkének az eszköz elülső paneljén láthatónak kell lennie.

## <a name="verify-data"></a>Az adatok ellenőrzése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. Ha a Data Box Heavy eszköz csatlakozik az Azure Datacenter networkhez, az adatok automatikusan feltöltve lettek az Azure-ba.
2. Data Box szolgáltatás értesíti arról, hogy az Adatmásolás befejeződött az Azure Portalon keresztül.

    1. Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
    2. Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

- A sorrend feldolgozása előtt megszakíthatja a Azure Portal Data Box Heavy sorrendjét. A rendelést a teljesítése után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot. A rendelés visszavonásához lépjen az **Áttekintés** oldalra, és kattintson a **Megszakítás** parancsra a parancssávon.

- A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon. A rendelés törléséhez lépjen az **Áttekintés** oldalra, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Data Box Heavy az Azure-ba történő adatimportáláshoz. Ha többet szeretne megtudni a Azure Data Box Heavy-kezelésről, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [A Azure Portal használata a Data Box Heavy felügyeletéhez](data-box-portal-admin.md)

::: zone-end
