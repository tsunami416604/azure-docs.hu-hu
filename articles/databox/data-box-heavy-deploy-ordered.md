---
title: Oktatóanyag az Azure Data Box nehéz sorrend |} A Microsoft Docs
description: Ismerje meg, a telepítés előfeltételeit, és hogyan lehet egy Azure Data Box nehéz order
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592407"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Oktatóanyag: Rendelés az Azure Data Box (nagy erőforrásigényű)


Az Azure Data Box (nagy erőforrásigényű), amely lehetővé teszi, hogy a helyszíni adatok importálása az Azure-gyors, egyszerű, és megbízhatóan hibrid megoldás is. Az adatok átvitele a Microsoft által biztosított 770 TB (hozzávetőleges felhasználható kapacitás) tárolóeszköz, és ezután küldje az vissza az eszközt. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendezheti az Azure Data Box (nagy erőforrásigényű). Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Data Box (nagy erőforrásigényű) előfeltételei
> * Sorrend a Data Box (nagy erőforrásigényű)
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

Az eszköz üzembe helyezése előtt teljesítse a Data Box szolgáltatás és az eszköz alábbi konfigurációs követelményeit.

### <a name="for-installation-site"></a>Telepítési hely

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz megfelel a standard szintű átjáróknak és entryways keresztül. Azonban győződjön meg arról, hogy az eszköz összes a entryways keresztül illeszkednek. Eszköz méretek: szélesség: 26" hossza: 48" height: 28”.
- Ha telepített egy kivételével az alapoktól emelet emelet, hozzáférésre van szüksége az eszköz az itt vagy egy ramp keresztül. Az eszköz körülbelül 500 KB lbs tömege.
- Győződjön meg arról, hogy a helyi adatközpontban segítségével közel kerülhet egy elérhető hálózati kapcsolat, amely lehetővé teszi az erőforrás-igényű rendelkező eszköz, a strukturálatlan hellyel rendelkezik.


### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure szponzorálás. További információk az [Azure szponzorálási programjáról](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Győződjön meg arról, hogy rendelkezik-e az előfizetésre, és hozzon létre egy Data Box nehéz rendelést tulajdonosi vagy közreműködői hozzáférés.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Az eszköz nem kicsomagolása.
- Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. Data Box nehéz lesz másolja az adatokat erről a számítógépről. A gazdaszámítógép egy támogatott operációs rendszert kell futtatnia, leírtak szerint [Azure Data Box nehéz rendszerkövetelmények](data-box-system-requirements.md).
- Szüksége lesz egy laptop RJ-45 kábellel csatlakozik a helyi felhasználói felület és az eszköz konfigurálása. A hordozható számítógép segítségével az eszköz minden egyes csomópontja egyszer konfigurálhatja.
- Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen.
- Szükség van egy 40 Gbps vagy 10 GB/s kábelezése eszköz csomópontonkénti. Válassza ki a kábelek, amelyek kompatibilisek a [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adapter:

    - A 40 GB/s kábel, az eszköz vége kell lennie QSFP +.
    - A 10 GB/s kábel szüksége lesz egy 10 G kapcsoló az egyik, a egy QSFP + SFP + adapter (vagy a DSS foglalt követelményeknek adapter), amely az eszköz rendkívüli End rendkívüli SFP + kábellel.
    - A tápkábelek is tartalmaz az eszköz.

## <a name="order-data-box-heavy"></a>Rendelés Data Box (nagy erőforrásigényű)

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza ki **+ erőforrás létrehozása** és keressen rá a *Azure Data Box*. Válassza ki **az Azure Data Box**.
    
   [![Az Azure Data Box 1 keresése](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kattintson a **Létrehozás** gombra.

4. Ellenőrizze, hogy ha a Data Box szolgáltatás az Ön régiójában érhető el. Adja meg vagy válassza ki a következő adatokat, és válassza ki **alkalmaz**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés     | Válassza ki az EA, CSP vagy az Azure sponsorship-előfizetést a Data Box szolgáltatás. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Forrásország     | Válassza ki az országot vagy régiót, ahol az adatokat jelenleg található.         |
    |Azure-beli célrégió     | Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.        |

    [![Válassza ki a Data Box-családba tartozó rendelkezésre állási](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Válassza ki **Data Box-(nagy erőforrásigényű)** . A megrendelésenkénti maximális felhasználható kapacitás esetében 770 TB.

    [![Válassza ki a Data Box (nagy erőforrásigényű)](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő adatokat, és válassza ki **tovább**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)     | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |
    |Erőforráscsoport     | Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Azure-beli célrégió     | Válasszon ki egy régiót a tárfiókhoz. <br> További információt a [regionális elérhetőséget](https://azure.microsoft.com/global-infrastructure/services/?products=databox) tárgyaló témakörben talál.        |
    |A rendeltetési tárolási hely     | Válassza ki a tárfiókot, a felügyelt lemezek vagy mindkettőhöz. <br> A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. <br>A storage-fiókok legfeljebb 10 Data Box nehéz is társítható. <br> Ezenkívül létrehozhat egy új **általános célú v1**, **általános célú v2**, vagy **Blob storage-fiók**. <br> Az Azure Data Lake Storage 2. generációs gyűjtések fiókok nem támogatottak. Tekintse meg a [az eszköz a támogatott tárfiókok](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Storage-fiókok a virtuális hálózatok támogatottak. Ahhoz, hogy a Data Box szolgáltatás-biztonságos tárfiókokkal működik, a megbízható szolgáltatások belül a tárfiók hálózati tűzfal beállításainak engedélyezése. További információkért lásd: hogyan [hozzáadása az Azure Data Box szolgáltatás megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha a storage-fiókot használ a célhelyet, az alábbi képernyőképen láthatja:

    ![Data Box – nehéz ahhoz, hogy storage-fiók](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Ha a tárfiók a storage célhelyeként, mellett is használ Data Box nehéz felügyelt lemez gyors létrehozásához a helyi VHD-ből, kell adja meg a következő információkat:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforrás-csoportokat, ha szeretne felügyelt lemez gyors létrehozásához a helyi VHD-ből. Csak akkor, ha az erőforráscsoport korábban lett létrehozva a Data Box nehéz sorrendet felügyelt lemez létrehozásakor Data Box szolgáltatás által használható egy meglévő erőforráscsoportot. <br> Adja meg pontosvesszővel elválasztva több erőforráscsoporthoz. Legfeljebb 10 az erőforrás-csoportok támogatottak.|

    ![Data Box – nehéz ahhoz, hogy felügyelt lemez](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Felügyelt lemezek megadott tárfiók egy átmeneti tárfiókban lesz. A Data Box szolgáltatás feltöltések a VHD-k lapként blobok, az előkészítési tárfiókból felügyelt lemezekké alakítja, és áthelyezi az erőforráscsoportok előtt. További információkért lásd: [adatok ellenőrzése a feltöltése az Azure-bA](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza ki **ellenőrizze a címet**. 

    A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. Kattintson a **Tovább** gombra.

8. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

9. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

10. Válassza ki **rendelés**. A megrendelés létrehozása néhány percet vesz igénybe.


## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Nyissa meg a Data Box nehéz megrendelését, és folytassa a **áttekintése** állapotának megtekintéséhez. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

- SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
- Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
- Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
- A Data Box nehéz zárolva van az eszközön, bármikor jogosulatlan hozzáférés elkerülése érdekében.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![Data Box – nehéz sorrendben dolgozza fel a](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Adatok elküldése Box – nehéz sorrendje](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.
 
A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az Azure Data Box nehéz témakörök például:

> [!div class="checklist"]
> * Előfeltételek
> * Rendelés Data Box (nagy erőforrásigényű)
> * A megrendelés nyomon követése
> * A rendelés lemondása

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan állítható be a Data Box (nagy erőforrásigényű).

> [!div class="nextstepaction"]
> [Állítsa be az Azure Data Box (nagy erőforrásigényű)](./data-box-heavy-deploy-set-up.md)
