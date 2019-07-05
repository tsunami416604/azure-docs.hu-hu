---
title: Rövid útmutató a Microsoft Azure Data Box (nagy erőforrásigényű) |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe gyorsan az Azure Data Box (nagy erőforrásigényű) az Azure Portalon
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 3467b25c085fb86d4aed3918d5446d118f76ffb8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446734"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Gyors útmutató: Üzembe helyezése az Azure Data Box (nagy erőforrásigényű) az Azure portal használatával

Ez a rövid útmutató azt ismerteti, üzembe helyezése az Azure Data Box (nagy erőforrásigényű) az Azure portal használatával. A lépések bemutatják, hogyan kábelek, konfigurálása és adatok másolása az adatok Box – nehéz, úgy, hogy feltölti az Azure-bA. A rövid útmutató az Azure Portalon és az eszköz helyi webes felületén hajtható végre.

Részletes részletes üzembe helyezés és a nyomkövetési utasításokat, keresse fel [oktatóanyag: Rendelés az Azure Data Box (nagy erőforrásigényű)](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Előfeltételek

Végezze el a telepítési hely, a Data Box szolgáltatás és az eszköz a következő konfigurálási előfeltételeket, mielőtt üzembe helyezné az eszközt.

### <a name="for-installation-site"></a>Telepítési hely

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz összes a entryways keresztül illeszkednek. Eszköz méretek: szélesség: 26" hossza: 48" height: 28”.
- Ha azt tervezi, hogy egy emelet az alapoktól emelet kívül telepítse az eszköz az itt vagy egy ramp keresztül elérhető lesz.
- Az eszköz kezelésére két személy rendelkezik. Az eszköz körülbelül 500 KB lbs tömege. a kerekek, és.
- A helyi adatközpontban segítségével közel kerülhet egy elérhető hálózati kapcsolat, amely lehetővé teszi az erőforrás-igényű rendelkező eszköz, a strukturálatlan hellyel rendelkezik.

### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- A Data Box szolgáltatás használja az előfizetés [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview), vagy [a Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Az előfizetésre, és hozzon létre egy Data Box nehéz rendelést tulajdonosi vagy közreműködői hozzáféréssel rendelkezik.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Áttekintette a [a Data Box nehéz biztonságának irányelvek](data-box-safety.md).
- Rendelkezik egy fogadó számítógép csatlakozik az adatközponti hálózathoz. Data Box nehéz lesz másolja az adatokat erről a számítógépről. A gazdaszámítógép futtatnia kell egy [támogatott operációs rendszert](data-box-heavy-system-requirements.md).
- Rendelkezik egy laptop RJ-45 kábellel csatlakozik a helyi felhasználói felület és az eszköz konfigurálása. A hordozható számítógép segítségével az eszköz minden egyes csomópontja egyszer konfigurálhatja.
- Az Adatközpont nagy sebességű hálózat rendelkezik, és rendelkeznie kell legalább egy 10 GbE kapcsolatot.
- Szüksége vagy egy 40-GB/s 10-GB/s kábellel eszköz csomópontonkénti. Válassza ki a kábelek, amelyek kompatibilisek a Mellanox MCX314A-BCCT a hálózati adapter:
    - A 40-GB/s kábel, az eszköz vége kell lennie QSFP +.
    - A 10-GB/s kábel szüksége lesz egy 10 – G kapcsoló az egyik, a egy QSFP + SFP + adapter (vagy a DSS foglalt követelményeknek adapter), amely az eszköz rendkívüli End rendkívüli SFP + kábellel.
- A tápkábelek szerepelnek a tálca hátulján az eszköz található.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="order"></a>Rendelés

Ez a lépés nagyjából 5 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon.
2. Válasszon ki egy meglévő előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Importálás** lehetőséget. Adja meg a **Forrásország** mezőben azt a helyet, ahol az adatok jelenleg találhatók, az **Azure-beli célrégió** mezőben pedig az adatátvitel célját.
3. Válassza ki **Data Box-(nagy erőforrásigényű)** . A maximálisan felhasználható kapacitás 770 TB, és több rendeléseket adatok nagyobb méretek esetében hozhat létre.
4. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik az eszköz a szállításra való előkészítése.

## <a name="cable-for-power"></a>Az energiagazdálkodási kábel

Ez a lépés 5 percet vesz igénybe.

Amikor megjelenik a Data Box (nagy erőforrásigényű), a következő lépések bekapcsolási az eszköz bekábelezése, és kapcsolja be az eszközt.

1. Amennyiben az eszközön sérülés vagy illetéktelen hozzáférés nyomai észlelhetők, ne folytassa az üzembe helyezést. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, és küldessen egy csereeszközt.
2. Telepítési hely helyezheti az eszközöket, és a hátsó kerekek zárolása.
3. Csatlakozzon a tápegységek hátulján az eszközön található összes négy tápkábelek.
4. Az első síknak power gombok segítségével kapcsolja be az eszköz csomópontok.

## <a name="cable-first-node-for-network"></a>Hálózati kábel első csomópont

Ebben a lépésben a befejezéséhez körülbelül 10 – 15 percet vesz igénybe.

1. Az RJ-45 CAT 6 hálózati kábellel csatlakoztassa a gazdagépet az eszköz felügyeleti portjához (MGMT).
2. A Twinax QSFP + rézeres kábellel csatlakoztasson legalább egy 40 Gbps a (javasolt több mint 1 GB/s) hálózati adaptert, adatok 1 vagy az adatok a 2. Ha a 10-GB/s kapcsoló használata a Twinax SFP + rézeres kábellel egy QSFP + SFP + adapter (DSS foglalt követelményeknek adapter) való csatlakozáshoz a 40 Gbps hálózati adapter adatok.
3. Csatlakoztassa a kábeleket az eszközhöz az ábra szerint.  

    ![Data Box nehéz bekábelezte](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Első csomópontok konfigurálása

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Az eszköz jelszavát az [Azure Portal](https://portal.azure.com) **Általános > Eszköz adatai** lapján találja. Ugyanazt a jelszót mindkét csomópontját az eszköz használható.
2. És a 255.255.255.0 alhálózati maszkkal 192.168.100.5 statikus IP-cím hozzárendelése az Ethernet-adaptert a számítógépen a Data Box (nagy erőforrásigényű) való kapcsolódáshoz használ. Nyissa meg az eszköz helyi webes felületét a következő helyen: `https://192.168.100.10`. A csatlakozás az eszköz bekapcsolását követően 5 percet is igénybe vehet.
3. Jelentkezzen be az Azure Portalról beszerzett jelszóval. Egy hibaüzenet jelenik meg, miszerint a webhely biztonsági tanúsítványa hibás. A böngészőspecifikus utasításokat követve lépjen a weblapra.
4. Alapértelmezés szerint a hálózati adapterek (kivéve a MGMT) beállításai DHCP. Ha szükséges, konfigurálja ezeket az adaptereket statikus, és adjon meg egy IP-címet.

## <a name="cable-and-configure-the-second-node"></a>Bekötéséhez és konfigurálásához a második csomópont

Ebben a lépésben a befejezéséhez körülbelül 15 – 20 percet vesz igénybe.

Kövesse a lépéseket az első fürtcsomópont bekötéséhez és konfigurálásához a második csomópontot az eszközön használt.  

## <a name="copy-data"></a>Adatok másolása

Ez a művelet elvégzéséhez szükséges idő attól függ, az adatok mérete és a sebesség, a hálózat, amelyen a rendszer az adatokat másolja.
 
1. Adatok másolása az mindkét eszköz a csomópontok mindkét a 40-GB/s sebességű adatok felületek segítségével párhuzamosan.

    - Ha Windows-állomás, például használja az SMB kompatibilis fájlmásoló eszközt [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - NFS-gazdagép esetén a `cp` parancs vagy a `rsync` használatával másolhatja az adatokat.
2. Csatlakozás a megosztásokat, az eszközön, az elérési út alapján:`\\<IP address of your device>\ShareName`. A megosztás hozzáférés szükséges hitelesítő adatokat, keresse fel a **Connect & copy** a helyi webes felületén a Data Box (nagy erőforrásigényű) lapján.
3. Győződjön meg arról, hogy a megosztás és mappanevek, és az adatok kövesse az ismertetett irányelvek, a [Azure Storage és a Data Box nehéz szolgáltatási korlátozásaival](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>A szállítás előkészítése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. Lépjen az adatmásolás befejezése után hibák nélkül, **szállításra való** helyi webes felületén oldalra, majd indítsa el az előkészületeket a szállításra.
2. Miután a **szállításra való** rendelkezik sikeresen befejeződött, mind a csomópontokon, kapcsolja ki az eszköz helyi webes felületén.

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

Ez a művelet a végrehajtásához körülbelül 15 – 20 percet vesz igénybe.

1. Távolítsa el a kábelek, és vissza kell a tálca hátulján az eszköz található.
2. Ütemezhet begyűjtést a regionális szolgáltató.
3. Kapcsolatfelvétel [Box Adatműveletek](mailto:DataBoxOps@microsoft.com) tájékoztatja a begyűjtés kapcsolatban, valamint hogy a visszaszállítási címkét.
4. Lehet, hogy a visszaszállítási címke az eszköz az első egyértelmű panelen látható.

## <a name="verify-data"></a>Az adatok ellenőrzése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A Data Box (nagy erőforrásigényű) eszközt az Azure-adatközponti hálózathoz való csatlakozáskor az adatok automatikusan feltölti az Azure-bA.
2. A Data Box szolgáltatás értesíti, hogy befejeződött-e az adatok másolása az Azure Portalon keresztül.

    1. Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
    2. Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

- Az Azure Portalon a Data Box nehéz rendelés megszakíthatja a rendelés feldolgozása előtt. A rendelést a teljesítése után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot. A rendelés visszavonásához lépjen az **Áttekintés** oldalra, és kattintson a **Megszakítás** parancsra a parancssávon.

- A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon. A rendelés törléséhez lépjen az **Áttekintés** oldalra, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy Data Box (nagy erőforrásigényű) segítségével az adatok importálása az Azure-bA helyezte. Az Azure Data Box nehéz felügyeleti kapcsolatos további információkért folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az Azure portal segítségével felügyelheti a Data Box (nagy erőforrásigényű)](data-box-portal-admin.md)
