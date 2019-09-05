---
title: Oktatóanyag a Azure Data Box Heavy rendeléséhez | Microsoft Docs
description: Ismerje meg az üzembe helyezés előfeltételeit, és hogyan rendeljen hozzá egy Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241392"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Oktatóanyag: Megrendelés Azure Data Box Heavy


A Azure Data Box Heavy egy hibrid megoldás, amely lehetővé teszi, hogy gyorsan, egyszerűen és megbízható módon importálja a helyszíni adatait az Azure-ba. Az adatok átvitele egy Microsoft által biztosított 770 TB-os (kihasználható kapacitású) tárolóeszközre, majd az eszköz újbóli szállítása. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag azt ismerteti, hogyan rendelhet egy Azure Data Box Heavy. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Data Box Heavy előfeltételei
> * Data Box Heavy megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

Az eszköz üzembe helyezése előtt teljesítse a Data Box szolgáltatás és az eszköz alábbi konfigurációs követelményeit.

### <a name="for-installation-site"></a>A telepítési helyhez

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz a standard ajtókon és a bejáratokon keresztül illeszkedik. Azonban győződjön meg arról, hogy az eszköz elfér az összes bebejáratán. Az eszköz méretei a következők: width: 26 "hossz: 48 "magasság: 28 ".
- Ha a földszinten kívülre van telepítve, akkor egy lift vagy egy rámpa használatával kell elérnie az eszközt. Az eszköz körülbelül ~ 500 lbs-t mérlegel.
- Győződjön meg arról, hogy az adatközpontban van egy olyan, a rendelkezésre álló hálózati kapcsolat közelsége, amely képes az adott helyigényű eszköz elhelyezésére.


### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure szponzorálás. További információk az [Azure szponzorálási programjáról](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Győződjön meg arról, hogy az előfizetéshez tulajdonos vagy közreműködő fér hozzá Data Box Heavy rendelés létrehozásához.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Az eszköz kicsomagolása megtörténik.
- Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. Data Box Heavy másolja az adatait a számítógépről. A gazdaszámítógépnek egy támogatott operációs rendszert kell futtatnia [Azure Data Box Heavy rendszerkövetelmények](data-box-system-requirements.md)című témakörben leírtak szerint.
- A helyi felhasználói felülethez való kapcsolódáshoz és az eszköz konfigurálásához egy RJ-45 kábelt tartalmazó laptop szükséges. A laptop használatával konfigurálja egyszer az eszköz egyes csomópontjait.
- Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen.
- Az eszköz csomópontjain 1 40 GB/s vagy 10 GB/s kábelre van szükség. Válassza ki a [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adapterrel kompatibilis kábeleket:

    - Az 40 GB/s-os kábel esetén a kábel QSFP +-nak kell lennie.
    - A 10 GB/s-os kábel esetében egy olyan SFP + kábelre van szükség, amely egy 10 G-os kapcsolóhoz csatlakozik egy végponton, egy QSFP + – SFP + adapterrel (vagy a QSA-adapterrel), amely az eszközhöz csatlakoztatja a végpontot.
    - Az energiaellátási kábelek az eszköz részét képezik.

## <a name="order-data-box-heavy"></a>Megrendelés Data Box Heavy

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza az **+ erőforrás létrehozása** és a *Azure Data Box*keresése lehetőséget. Válassza a **Azure Data Box**lehetőséget.
    
   [![Az Azure Data Box 1 keresése](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kattintson a **Létrehozás** gombra.

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régiójában. Adja meg vagy válassza ki a következő adatokat, majd válassza az **alkalmaz**lehetőséget.

    |Beállítás  |Value  |
    |---------|---------|
    |Subscription     | Válasszon ki egy EA, CSP vagy Azure szponzorálási előfizetést Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Forrásország     | Válassza ki azt az országot/régiót, ahol az adatai jelenleg találhatók.         |
    |Azure-beli célrégió     | Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.        |

    [![Data Box család rendelkezésre állásának kiválasztása](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Válassza a **Data Box Heavy**lehetőséget. Egy megrendelés maximális felhasználható kapacitása 770 TB.

    [![Data Box Heavy kiválasztása](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő adatokat, majd kattintson a **tovább**gombra.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)     | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |
    |Resource group     | Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Azure-beli célrégió     | Válasszon ki egy régiót a tárfiókhoz. <br> További információt a [regionális elérhetőséget](https://azure.microsoft.com/global-infrastructure/services/?products=databox) tárgyaló témakörben talál.        |
    |Tárolási cél     | Válasszon a Storage-fiók vagy a felügyelt lemezek közül, vagy mindkettőt. <br> A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. <br>Data Box Heavy legfeljebb 10 Storage-fiókkal lehet összekapcsolni. <br> Létrehozhat egy új **általános célú v1**-, **általános célú v2-** vagy **blob Storage-fiókot**is. <br> Azure Data Lake Storage 2. generációs fiókok nem támogatottak. Tekintse [meg az eszköz által támogatott Storage-fiókokat](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>A virtuális hálózatokkal rendelkező Storage-fiókok támogatottak. Annak engedélyezéséhez, hogy Data Box szolgáltatás biztonságos Storage-fiókokkal működjön, engedélyezze a megbízható szolgáltatásokat a Storage-fiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box szolgáltatás hozzáadása megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha a Storage-fiókot tároló célhelyként használja, a következő képernyőkép jelenik meg:

    ![A Storage-fiók Data Box Heavy sorrendje](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Ha a Storage-fiókon kívül tárolási célként is használja a Data Box Heavy a felügyelt lemezek létrehozásához a helyszíni virtuális merevlemezekről, meg kell adnia a következő információkat:

    |Beállítás  |Value  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni a helyszíni virtuális merevlemezekről. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoportot korábban hozták létre a felügyelt lemez Data Box szolgáltatás általi Data Box Heavy rendelésének létrehozásakor. <br> Több erőforráscsoportot is megadhat pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport támogatott.|

    ![Felügyelt lemez Data Box Heavy sorrendje](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A felügyelt lemezekhez megadott Storage-fiók átmeneti tárolási fiókként van használatban. A Data Box szolgáltatás a virtuális merevlemezeket blobként tölti fel az átmeneti tárolási fiókba, mielőtt a felügyelt lemezekre konvertálja, és áthelyezi azokat az erőforráscsoportokba. További információ: az [adatok feltöltésének ellenőrzése az Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure)-ban.

7. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza a **címek ellenőrzése**lehetőséget. 

    A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. Kattintson a **Tovább** gombra.

8. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

9. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

10. Válassza ki a **sorrendet**. A megrendelés létrehozása néhány percet vesz igénybe.


## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Lépjen a Data Box Heavy sorrendbe, majd az állapot megtekintéséhez lépjen az **Áttekintés** gombra. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

- SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
- Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
- Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
- A Data Box Heavy zárolva van, hogy megakadályozza az eszköz jogosulatlan elérését.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![Data Box Heavy feldolgozott rendelés](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Data Box Heavy rendelés elküldve](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.
 
A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megAzure Data Box Heavy ismerte a következő témaköröket:

> [!div class="checklist"]
> * Előfeltételek
> * Megrendelés Data Box Heavy
> * A megrendelés nyomon követése
> * A rendelés lemondása

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan állíthatja be a Data Box Heavy.

> [!div class="nextstepaction"]
> [A Azure Data Box Heavy beállítása](./data-box-heavy-deploy-set-up.md)
