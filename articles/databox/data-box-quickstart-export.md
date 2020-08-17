---
title: 'Gyorsútmutató: Adatok exportálása a Microsoft Azure Data Boxhoz'
description: Megtudhatja, hogyan exportálhatja gyorsan az Azure Data Box adatait az Azure Portalon
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: v-grpr
ms.localizationpriority: high
ms.openlocfilehash: fbe14151845d19dd1d0f2a4069afb2badb125077
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122241"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Gyorsútmutató: Ismerkedés az Azure Data Boxszal az adatok Azure-ból való exportálásához

Ez a gyorsútmutató bemutatja, hogyan exportálhat adatokat az Azure-ból a saját helyére az Azure Portal használatával. A lépések bemutatják a bekábelezés és a konfigurálás menetét, valamint az adatok másolását az Azure-ból. A rövid útmutató az Azure Portalon és az eszköz helyi webes felületén hajtható végre.

Részletes üzembehelyezési és nyomkövetési utasítások: [Oktatóanyag: Exportálási rendelés létrehozása az Azure Data Boxhoz](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

* Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
  * Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure szponzorálás. További információk az [Azure szponzorálási programjáról](https://azure.microsoft.com/offers/ms-azr-0036p/).

* A Data Box-rendelések létrehozásához tulajdonosi vagy közreműködői jogosultsággal kell rendelkeznie az előfizetésen.
* Tekintse át [a Data Box biztonsági útmutatásait](data-box-safety.md).
* Rendelkezik Azure Data Box-eszközzel, amellyel adatokat helyezhet át a saját helyére. A gazdaszámítógépen:
  * egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
  * egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, egy 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet.
* Szüksége lesz egy vízszintes felületre, ahová a Data Boxot helyezheti. Ha az eszközt egy standard kiszolgálószekrény-polcra szeretné helyezni, egy legalább 7U méretű helyre lesz szükség az adatközponti szekrényben. Az eszközt a talpán vagy az oldalán is elhelyezheti a szekrényben.
* Rendelkezik a következő kábelekkel a Data Box gazdagéphez való csatlakoztatásához.
  * Két 10 GbE SFP+ Twinax-rézkábel (a DATA 1 és DATA 2 hálózati adapterhez)
  * Egy RJ-45 CAT 6 hálózati kábel (a felügyeleti (MGMT) hálózati adapterhez)
  * Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="order"></a>Rendelés

Ez a lépés nagyjából 10 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon.
2. Válasszon ki egy meglévő előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Exportálás az Azure-ba** lehetőséget. Az adatátvitelhez adja meg az **Azure-régiót**, ahol az adatok jelenleg találhatók, valamint a **célországot**.
3. Válassza a **Data Box** lehetőséget. A maximális hasznos kapacitás 80 TB, nagyobb mennyiségű adat esetén több rendelést is létrehozhat.
4. Válassza ki a **Tárfiók és exportálási típus hozzáadása**, majd az **Exportálási beállítás kiválasztása** lehetőséget.
5. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik az eszköz a szállításra való előkészítése.

## <a name="cable"></a>Kábel

Ez a lépés nagyjából 10 percet vesz igénybe.

A Data Box megérkezését követően hajtsa végre az alábbi lépéseket az eszköz kábeles csatlakoztatásához és bekapcsolásához. Ez a lépés nagyjából 10 percet vesz igénybe.

1. Amennyiben az eszközön sérülés vagy illetéktelen hozzáférés nyomai észlelhetők, ne folytassa az üzembe helyezést. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, és küldessen egy csereeszközt.
2. Az eszköz kábeles csatlakoztatása előtt ellenőrizze, hogy rendelkezik-e az alábbi kábelekkel:

   * Egy földelt 10 A vagy nagyobb névleges kapacitású tápkábel (mellékelve) az egyik végén IEC60320 C-13 csatlakozóval az eszközhöz való csatlakoztatáshoz.
   * Egy RJ-45 CAT 6 hálózati kábel (a felügyeleti (MGMT) hálózati adapterhez)
   * Két 10 GbE SFP+ Twinax rézkábel (a 10 Gb/s DATA 1 és DATA 2 hálózati adapterekhez)
   * Egy RJ-45 CAT 6A VAGY egy RJ-45 CAT 6 hálózati kábel (a DATA 3 hálózati adapterhez 10 Gb/s vagy 1 Gb/s sebességre konfigurálva)

3. Vegye ki az eszközt a dobozból, és helyezze egy vízszintes felületre.

4. Csatlakoztassa a kábeleket az eszközhöz az ábra szerint.  

    ![Data Box-eszköz bekábelezett hátlapja](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Csatlakoztassa a tápkábelt az eszközhöz.
    2. Az RJ-45 CAT 6 hálózati kábellel csatlakoztassa a gazdagépet az eszköz MGMT-portjához.
    3. Az SFP+ Twinax rézkábellel csatlakoztassa legalább az egyik 10 Gb/s (előnyben részesítve az 1 Gb/s-ossal szemben) hálózati adaptert, a DATA 1 vagy a DATA 2 adaptert az adatforgalom számára.
    4. Kapcsolja be az eszközt. A bekapcsológomb az eszköz előlapján található.

## <a name="connect"></a>Kapcsolódás

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Az eszköz jelszavát az [Azure Portal](https://portal.azure.com) **Általános** > **Eszköz adatai** lapján találja.
2. A Data Boxhoz csatlakoztatott számítógépen rendelje a 192.168.100.5 statikus IP-címet és a 255.255.255.0 alhálózatot az Ethernet-adapterhez. Nyissa meg az eszköz helyi webes felületét a következő helyen: `https://192.168.100.10`. A csatlakozás az eszköz bekapcsolását követően 5 percet is igénybe vehet.
3. Jelentkezzen be az Azure Portalról beszerzett jelszóval. Egy hibaüzenet jelenik meg, amely szerint a webhely biztonsági tanúsítványa hibás. A böngészőspecifikus utasításokat követve lépjen a weblapra.
4. Alapértelmezés szerint a 10 Gb/s (vagy az 1 Gb/s) sebességű adatillesztő DHCP-ként van konfigurálva. Szükség esetén statikusként is konfigurálhatja az illesztőt, és megadhat egy IP-címet.

## <a name="copy-data"></a>Adatok másolása

A művelet végrehajtásának időtartama az adatok mennyiségétől és a hálózat sebességétől függ.

1. Windows-ügyfél használata esetén használjon SMB-kompatibilis fájlmásoló eszközt, amilyen például a Robocopy. NFS-gazdagép esetén a `cp` parancs vagy a `rsync` használatával másolhatja az adatokat. Az adatok a Robocopyval való másolásával kapcsolatos további információkért lásd: [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
2. Csatlakozzon az eszközmegosztásokhoz, és kezdje meg az adatok másolását a gazdagépre.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

A művelet befejezése kb. 10–15 percet vesz igénybe.

1. Nyissa meg a helyi webes kezelőfelület **Prepare to ship** (Szállítás előkészítése) lapját, és kezdje meg az előkészületeket a szállításra.
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről.
3. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
4. A visszaküldési fuvarlevélcímkének láthatónak kell lennie az E-ink kijelzőn. Ha az E-ink kijelzőn nem jelenik meg a címke, lépjen az Azure Portalon az **Áttekintés** > **Fuvarlevélcímke letöltése** területre. Töltse le a fuvarlevélcímkét, és rögzítse az eszközre.
5. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával, ha visszaküldi az eszközt.
6. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

* A Data Box-rendelést a feldolgozása előtt bármikor visszavonhatja az Azure Portalon. A rendelést a feldolgozása után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot. A rendelés visszavonásához lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** parancsra a parancssávon.

* A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon. A rendelés törléséhez lépjen az **Áttekintés** területre, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>Következő lépések

Ezzel a gyorsútmutatóval létrehozott egy exportálási rendelést az Azure-ból az Azure Data Boxra. Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Data Box kezeléséről:

> [!div class="nextstepaction"]
> [A Data Box az Azure Portal használatával történő kezelése](data-box-portal-admin.md)
