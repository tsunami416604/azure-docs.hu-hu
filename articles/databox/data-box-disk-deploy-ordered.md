---
title: Oktatóanyag a Microsoft Azure Data Box Disk megrendeléséhez | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan regisztrálhat az Azure Data Box Disk szolgáltatásra, és hogyan rendelheti meg azt az adatok Azure-ba történő importálásához.
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
ms.date: 07/17/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f46a3ecb3c4ef6ace31b7010ba9068ab04a43315
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126541"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Oktatóanyag: A Microsoft Azure Data Box Disk megrendelése (előzetes verzió)

Az Azure Data Box Disk egy felhőalapú megoldás, amelynek segítségével gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Ön letölti az adatokat a Microsoft által SSD-lemezekre, majd visszaküldi azokat a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba. 

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box Disk szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Regisztráció a Data Box Disk szolgáltatásra
> * A Data Box Disk megrendelése
> * A rendelés nyomon követése
> * A rendelés lemondása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> - A Data Box Disk előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Az előzetes verzió ideje alatt a Data Box Disk az USA és az Európai Unió területére szállítható ki. További információt a [Regionális elérhetőség](data-box-disk-overview.md#region-availability) témakörben talál.

## <a name="sign-up"></a>Regisztráció 

A Data Box Disk előzetes verzióban érhető el, és az igénybe vételéhez regisztrációra van szükség. A Data Box szolgáltatásra történő regisztrációhoz hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure Portalra itt: [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Válassza ki azt az előfizetést, amelyet engedélyezni szeretne az előzetes verzióhoz. Válaszoljon a kérdésekre, amelyek az adatok méretére, az adattárolási országra, az időkeretre és az adatátviteli gyakoriságra vonatkoznak. Kattintson a **Regisztrálok!** elemre.
3. Miután regisztrált és engedélyezte az előzetes verziót, megrendelheti a Data Box Disket.

## <a name="order-data-box-disk"></a>A Data Box Disk megrendelése

A Data Box Disk megrendeléséhez a következő lépéseket kell végrehajtania az [Azure Portalon](https://aka.ms/azuredataboxfromdiskdocs).

1. Az Azure Portal bal felső sarkában kattintson a **+ Erőforrás létrehozása** elemre, és keresse meg az *Azure Data Box* lehetőséget. Kattintson az **Azure Data Box** lehetőségre.
    
   ![Keresse meg az Azure Data Box lehetőséget 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kattintson a **Create** (Létrehozás) gombra.

3. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg, vagy jelölje ki a következő információkat, majd kattintson az **Alkalmaz** elemre.

    ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Beállítás|Érték|
    |---|---|
    |Előfizetés|Válassza ki azt az előfizetést, amely számára engedélyezve van a Data Box szolgáltatás.<br> Az előfizetés az Ön számlázási fiókjához lesz társítva. |
    |Átvitel típusa| Importálás az Azure-ba|
    |Forrásország | Válassza ki azt az országot, ahol az adatok jelenleg találhatók.|
    |Azure-beli célrégió|Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.|

  
5.  Kattintson a **Data Box Disk** elemre. A megoldás maximális kapacitása megrendelésenként (5 lemez) 35 TB. Nagyobb adatmennyiséghez több megrendelést is létrehozhat. 

     ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat.

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Adjon meg egy rövid nevet a megrendelés nyomon követéséhez.<br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie. |
    |Erőforráscsoport| Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Azure-beli célrégió| Válasszon ki egy régiót a tárfiókhoz.<br> Jelenleg minden Egyesült Államokbeli, valamint Észak- és Nyugat-Európai régió tárfiókjai támogatottak. |
    |Tárfiók(ok)|A megadott Azure-régió alapján válasszon ki egy meglévő tárfiókot a szűrt listából. <br>Létre is hozhat egy új Általános célú v1 vagy Általános célú v2 fiókot. |
    |Adatok becsült mérete (TB)| Adjon meg egy becsült mennyiséget TB-ban. <br>Az adatmennyiség alapján a Microsoft kiküldi Önnek a megfelelő számú 8 TB-os SSD-t (7 TB felhasználható kapacitás). <br>5 lemez maximális felhasználható kapacitása 35 TB. |

13. Kattintson a **Tovább** gombra. 

    ![A megrendelés részletei](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. A **Szállítási cím** lapon adja meg az utónevét és a vezetéknevét, a cége nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. 

    ![Szállítási cím megadása](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre. 

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

16. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

17. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.

 
## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, az Azure betekintő portálon nyomon követheti a megrendelés állapotát. Keresse meg a megrendelést, és nyissa meg az **Áttekintés** területet az állapot megtekintéséhez. A portál a **Megrendelve** állapotot mutatja. 

![Data Box Disk állapot megrendelve](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Ha a lemezek nem érhetők el, értesítést kap. Ha a lemezek elérhetők, a Microsoft azonosítja a lemezeket a szállításhoz, és előkészíti a csomagot. A lemezek előkészítése a következőket foglalja magába:

- A lemezek titkosítása a AES-128 BitLocker használatával.  
- A lemezek zárolása az illetéktelen hozzáférés megakadályozására.
- A folyamat során létrejön a lemezek zárolását feloldó hozzáférési kulcs.

A lemezek előkészítésének lezárultával a portál a **Feldolgozott** állapotot mutatja.

A Microsoft ezután előkészíti, majd lebonyolítja a szállítást egy regionális szolgáltatón keresztül. A szállítás megkezdésekor Ön megkapja a nyomkövetési azonosító számot. A portál a **Feladva** állapotot mutatja.



## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure betekintő portálon nyissa meg az **Áttekintés** területet, majd a parancssávon kattintson a **Lemondás** elemre. 

Csak akkor mondhatja le a rendelést, ha a lemezek megrendelése után a szállításra való előkészítés még folyamatban van. Ha a megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést. 

![Rendelés lemondása](media/data-box-disk-deploy-ordered/cancel-order1.png)

A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre. 


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Regisztráció a Data Box Disk szolgáltatásra
> * A Data Box Disk megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box Disk beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box Disk beállítása](./data-box-disk-deploy-set-up.md)


