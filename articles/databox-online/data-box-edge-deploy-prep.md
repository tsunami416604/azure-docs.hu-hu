---
title: Oktatóanyag az Azure Portal, adatközpont-környezet előkészítéséhez az Azure Data Box Edge üzembe helyezéséhez | Microsoft dokumentumok
description: Az Azure Data Box Edge üzembe helyezéséről szóló első oktatóanyag az Azure Portal előkészítését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474475"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Oktatóanyag: Felkészülés az Azure Data Box Edge üzembe helyezésére  

Ez az első oktatóanyag a központi telepítési oktatóanyagok sorozatában, amelyek az Azure Data Box Edge teljes üzembe helyezéséhez szükségesek. Ez az oktatóanyag ismerteti, hogyan készülhet elő az Azure Portalon egy Data Box Edge-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

### <a name="get-started"></a>Bevezetés

A Data Box Edge üzembe helyezéséhez tekintse meg az alábbi útmutatókat az előírt sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Edge-hez](data-box-edge-deploy-prep.md)** |A Data Box Edge fizikai eszköz üzembe helyezése előtt hozza létre és konfigurálja a Data Box Edge-erőforrást. |
| 2. |**[A Data Box Edge telepítése](data-box-edge-deploy-install.md)**|Csomagolja ki, helyezze állványra, és csatlakoztassa a kábeleket a Data Box Edge fizikai eszközhöz.  |
| 3. |**[A Data Box Edge csatlakoztatása, beállítása és aktiválása](data-box-edge-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Az eszköz készen áll az SMB- vagy NFS-megosztások beállítására.  |
| 4. |**[Adatok átvitele a Data Box Edge segítségével](data-box-edge-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
| 5. |**[Adatok átalakítása a Data Box Edge segítségével](data-box-edge-deploy-configure-compute.md)** |Konfigurálja a számítási modulokat az eszközön az adatok azure-ba való áthelyezése során. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Edge erőforrás, a Data Box Edge eszköz és az adatközpont-hálózat konfigurációs előfeltételeit kell felkeresni.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve van egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint a [Microsoft Nagyvállalati Szerződés (EA),](https://azure.microsoft.com/overview/sales-number/) [a Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)vagy [a Microsoft Azure szponzorálása](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tulajdonosi vagy közreműködői hozzáféréssel rendelkezik erőforráscsoport-szinten a Data Box Edge/Data Box Gateway, az IoT Hub és az Azure Storage-erőforrásokhoz.
    - Bármely Data Box Edge/ Data Box Gateway erőforrás létrehozásához erőforráscsoport-szinten közreműködői (vagy magasabb szintű) jogosultságokkal kell rendelkeznie. Azt is meg kell `Microsoft.DataBoxEdge` győződnie arról, hogy a szolgáltató regisztrálva van. A regisztrációval kapcsolatos további tudnivalókért látogasson el az [Erőforrás-szolgáltató regisztrálása](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)című oldalra.
    - Bármilyen IoT Hub-erőforrás létrehozásához győződjön meg arról, hogy a Microsoft.Devices szolgáltató regisztrálva van. A regisztrációval kapcsolatos további tudnivalókért látogasson el az [Erőforrás-szolgáltató regisztrálása](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)című oldalra.
    - Egy tárfiók-erőforrás létrehozásához ismét szükség van közreműködői vagy magasabb hozzáférési hatókört az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
- Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik a Microsoft Graph API-hoz. További információt a [Microsoft Graph engedélyek hivatkozása című témakörben talál.](https://docs.microsoft.com/graph/permissions-reference)
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge-eszköz esetén

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette a csomagban található biztonsági információkat.
- Van egy 1U slot áll rendelkezésre egy szabványos 19 "rack az adatközpontban rack szerelés a készüléket.
- Egy sima, stabil és vízszintes munkafelülethez férhet hozzá, ahol a készülék biztonságosan pihenhet.
- Az a hely, ahol az eszközt be kívánja állítani, szabványos hálózati áramforrással rendelkezik egy független forrásból vagy egy szünetmentes áramforrással (UPS) rendelkező rack-áramelosztó egységből .the site where you intend to set up the device has standard AC power from independent source or a rack power distribution unit (PDU) with a uninterruptible power supply (UPS).
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpontban lévő hálózat a Data Box Edge-eszköz hálózati követelményei szerint van konfigurálva. További információ: [Data Box Edge System Requirements](data-box-edge-system-requirements.md).

- A Data Box Edge normál működési feltételei esetén:

    - Legalább 10 Mb/s letöltési sávszélesség az eszköz frissítésének biztosításához.
    - Legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség fájlok átviteléhez.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Edge-erőforrással a fizikai eszközök kezeléséhez, hagyja ki ezt a lépést, és folytassa [az aktiválási kulcs lekérésével](#get-the-activation-key).

Data Box Edge-erőforrás létrehozásához tegye a következő lépéseket az Azure Portalon.

1. Microsoft Azure-hitelesítő adatok használata a bejelentkezéshez 
    
    - Az Azure portal ezen [https://portal.azure.com](https://portal.azure.com)az URL-címen: .
    - Vagy az Azure Government portálja [https://portal.azure.us](https://portal.azure.us)ezen az URL-címen: . További részletekért nyissa meg a [Csatlakozás az Azure Governmenthez a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)című oldalon című.

2. A bal oldali ablaktáblában válassza a **+ Erőforrás létrehozása**lehetőséget. A **Data Box Edge / Data Box Gateway**keresése . Válassza a **Data Box Edge / Data Box Gateway lehetőséget.** Kattintson a **Létrehozás** gombra.
3. Válassza ki a Data Box Edge eszközhöz használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. Az összes olyan régió listáját, ahol az Azure Stack Edge erőforrás elérhető, tekintse meg az [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

    Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. A régió csak az eszközkezelés metaadatait tárolja. A tényleges adatok bármely tárfiókban tárolhatók.
    
    A **Data Box Edge (Mezőél)** beállításban válassza a **Létrehozás lehetőséget.**

    ![A Data Box Edge szolgáltatás megkeresése](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Az **Alapok lapon** adja meg vagy jelölje ki a Projekt következő **adatait.**
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ez automatikusan kitöltődik a korábbi kijelölés alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.     |

4. Adja meg vagy válassza ki a következő **példányadatokat**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Régió     |Az összes olyan régió listáját, ahol az Azure Stack Edge erőforrás elérhető, tekintse meg az [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Az Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![Projekt- és példányrészletek](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Válassza a **Tovább: Szállítási cím**lehetőséget.

    - Ha már van eszköze, jelölje be a Data Box Edge eszköz kombinált **jelölőnégyzetét.**
    - Ha ez az új eszköz, amelyet megrendel, adja meg az ügyfél nevét, a vállalatot, az eszköz szállításához szükséges címet és a kapcsolattartási adatokat.

    ![Szállítási cím az új eszközhöz](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Válassza a **Tovább lehetőséget: Véleményezés + létrehozás**.

7. A **Véleményezés + létrehozás** lapon tekintse át az **Árképzés részleteit**, a **használati feltételeket**és az erőforrás részleteit. Jelölje be az **adatvédelmi kifejezések hez.**

    ![A Data Box Edge erőforrás részleteinek és adatvédelmi feltételeinek áttekintése](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Kattintson a **Létrehozás** gombra.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza **az Ugrás az erőforráshoz**lehetőséget.

![Ugrás a Data Box Edge erőforrásra](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

A megrendelés után a Microsoft áttekinti a megrendelést, és (e-mailben) kapcsolatba lép Önnel a szállítási adatokkal.

![Értesítés a Data Box Edge rendelés felülvizsgálatára](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Edge erőforrás működik, be kell szereznie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Edge-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást. Válassza **az Áttekintés,** majd **az Eszköz beállítása**lehetőséget.

    ![Eszköz beállításának kiválasztása](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Az **Aktiválás** csempe **intése** gombon válassza a Kulcs létrehozása lehetőséget az aktiválási kulcs létrehozásához. Válassza a másolás ikont a kulcs másolásához és későbbi használatra történő mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs a létrehozása után három nappal lejár.
> - Ha a kulcs lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Haladjon tovább a következő oktatóanyaghoz, és ismerje meg, hogyan telepítheti a Data Box Edge-et.

> [!div class="nextstepaction"]
> [A Data Box Edge telepítése](./data-box-edge-deploy-install.md)



