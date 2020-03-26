---
title: Az Azure Data Box Heavy megrendelését ismertető oktatóanyag | Microsoft Docs
description: Megismerheti az Azure Data Box Heavy üzembehelyezési követelményeit és a megrendelésének folyamatát
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70241392"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Oktatóanyag: Az Azure Data Box Heavy megrendelése


Az Azure Data Box Heavy egy hibrid megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Az adatokat egy, a Microsoft által biztosított 770 TB (körülbelüli hasznos) kapacitású tárolóeszközre helyezheti át, amelyet aztán visszapostáz a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box Heavyt. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Heavy üzembehelyezési követelményei
> * A Data Box Heavy megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

Az eszköz üzembe helyezése előtt teljesítse a Data Box szolgáltatás és az eszköz alábbi konfigurációs követelményeit.

### <a name="for-installation-site"></a>Az üzembehelyezési hely tekintetében:

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz átfér a normál méretű ajtókon és bejáratokon. Győződjön meg azonban arról, hogy az eszköz ténylegesen átfér az adott esetben használt bejáratokon. Az eszköz méretei: szélesség: 66 cm hossz: 122 cm magasság: 71 cm.
- Ha az üzembe helyezés nem a földszinten történik, lifttel vagy rámpával férhet hozzá az eszközhöz. Az eszköz tömege körülbelül 226,8 kg.
- Győződjön meg arról, hogy az adatközpontban található olyan vízszintes hely, amelynek közelében van elérhető hálózati csatlakozási lehetőség, és ahol elfér az ilyen helyigényű eszköz.


### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure szponzorálás. További információk az [Azure szponzorálási programjáról](https://azure.microsoft.com/offers/ms-azr-0036p/).

- A Data Box Heavy-rendelések létrehozásához tulajdonosi vagy közreműködői jogosultsággal kell rendelkeznie az előfizetésen.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Az eszköz ki van csomagolva.
- Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box Heavy erről a számítógépről másolja majd az adatokat. A gazdagépnek egy támogatott operációs rendszert kell futtatnia az [Azure Data Box Heavy rendszerkövetelményeinél](data-box-system-requirements.md) leírtaknak megfelelően.
- RJ-45 kábellel ellátott laptop szükséges a helyi felhasználó felülethez való csatlakozáshoz és az eszköz konfigurálásához. A laptop használatával konfigurálhatja az eszköz csomópontjait.
- Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen.
- Eszközcsomópontonként egy-egy 40 Gb/s-os vagy 10 Gb/s-os kábelre van szükség. A [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) hálózati adapterrel kompatibilis kábeleket válasszon:

    - A 40 Gb/s-os kábel esetében a kábel eszközoldali végének QSFP+ rendszerűnek kell lennie.
    - A 10 Gb/s-os kábel esetén szükség van egy olyan SFP+ kábelre, amely az egyik végén egy 10 Gb/s-os kapcsolóhoz, az eszközoldali végén pedig egy QSFP+–SFP+ adapterhez (vagy a QSA-adapterhez) csatlakozik.
    - A tápkábelek mellékelve vannak az eszközhöz.

## <a name="order-data-box-heavy"></a>A Data Box Heavy megrendelése

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.
    
   [![Az Azure Data Box 1 keresése](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kattintson a **Létrehozás** gombra.

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Forrásország     | Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.         |
    |Azure-beli célrégió     | Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.        |

    [![Válassza ki a Data Box-termékcsalád elérhetőségét](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Válassza a **Data Box Heavy** lehetőséget. A maximális hasznos kapacitás rendelésenként 770 TB.

    [![Válassza a Data Box Heavy lehetőséget](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat, majd válassza a **Tovább** lehetőséget.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)     | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |
    |Erőforráscsoport     | Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Azure-beli célrégió     | Válasszon ki egy régiót a tárfiókhoz. <br> További információt a [regionális elérhetőséget](https://azure.microsoft.com/global-infrastructure/services/?products=databox) tárgyaló témakörben talál.        |
    |Tárolási célhely     | Válassz a Tárfiók vagy a Felügyelt lemezek lehetőséget, vagy mindkettőt. <br> A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. <br>A Data Box Heavy legfeljebb 10 tárfiókkal köthető össze. <br> Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is. <br> Az Azure Data Lake Storage Gen2-fiókok nem támogatottak. Tekintse meg az [eszköz által támogatott tárfiókokat](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információért tekintse meg, hogyan [adhat hozzá Azure Data Box-szolgáltatást megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha tárfiókot használ tárolási célhelyként, a következő képernyőképet fogja látni:

    ![A Data Box Heavy megrendelése tárfiókhoz](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Ha a tárfiók a tárolási célhelyként történő alkalmazása mellett felügyelt lemezeket is szeretne létrehozni a helyszíni VHD-kből a Data Box Heavy használatával, meg kell adnia a következő adatokat:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni helyszíni VHD-kből. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoport azt megelőzően lett létrehozva, hogy a Data Box szolgáltatással Data Box Heavy-rendelést hozott létre a felügyelt lemez esetében. <br> Adjon meg több erőforráscsoportot, pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport használata támogatott.|

    ![A Data Box Heavy megrendelése felügyelt lemezhez](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás lapblobként tölti fel a VHD-ket az előkészítési tárfiókba, majd felügyelt lemezekké konvertálja, és áthelyezi az erőforráscsoportba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza a **Cím ellenőrzése** lehetőséget. 

    A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. Kattintson a **Tovább** gombra.

8. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

9. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

10. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.


## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Keresse meg a Data Box Heavy-rendelést, és nyissa meg az **Áttekintés** területet az állapot megtekintéséhez. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

- SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
- Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
- Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
- A Data Box Heavy zárolásának célja, hogy megakadályozza az eszközhöz való illetéktelen hozzáférést a teljes eljárás során.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![A Data Box Heavy-rendelés feldolgozva](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![A Data Box Heavy-rendelés feladva](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.
 
A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * Előfeltételek
> * A Data Box Heavy megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box Heavy beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy beállítása](./data-box-heavy-deploy-set-up.md)
