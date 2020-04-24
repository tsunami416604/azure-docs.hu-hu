---
title: Oktatóanyag a Azure Portal, adatközpont-környezet előkészítéséhez Azure Data Box Edge üzembe helyezéséhez | Microsoft Docs
description: A Azure Data Box Edge telepítésének első oktatóanyaga a Azure Portal előkészítését foglalja magában.
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
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Oktatóanyag: Felkészülés a Azure Data Box Edge üzembe helyezésére  

Ez az első oktatóanyag a Azure Data Box Edge teljes telepítéséhez szükséges telepítési oktatóanyagok sorozatában. Ez az oktatóanyag leírja, hogyan készítheti elő a Azure Portal egy Data Box Edge-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

### <a name="get-started"></a>Bevezetés

Data Box Edge telepítéséhez tekintse meg az alábbi oktatóanyagokat az előírt sorozatban.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Edge-hez](data-box-edge-deploy-prep.md)** |A Data Box Edge fizikai eszköz üzembe helyezése előtt hozza létre és konfigurálja a Data Box Edge-erőforrást. |
| 2. |**[A Data Box Edge telepítése](data-box-edge-deploy-install.md)**|Csomagolja ki, helyezze állványra, és csatlakoztassa a kábeleket a Data Box Edge fizikai eszközhöz.  |
| 3. |**[Data Box Edgeek összekapcsolása, beállítása és aktiválása](data-box-edge-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Az eszköz készen áll az SMB- vagy NFS-megosztások beállítására.  |
| 4. |**[Adatok átvitele a Data Box Edge segítségével](data-box-edge-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
| 5. |**[Adatok átalakítása a Data Box Edge segítségével](data-box-edge-deploy-configure-compute.md)** |Konfigurálja a számítási modulokat az eszközön az adatok átalakításához az Azure-ba való átlépéshez. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

A következő konfigurációs előfeltételek vonatkoznak a Data Box Edge erőforrásra, a Data Box Edge eszközre és az adatközpont-hálózatra.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve van egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint például a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/overview/sales-number/), a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tulajdonosi vagy közreműködői hozzáférése van az erőforráscsoport szintjén a Data Box Edge/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrások számára.
    - Bármilyen Data Box Edge/Data Box Gateway erőforrás létrehozásához jogosultságot kell biztosítania a közreműködői (vagy magasabb) hatókörnek az erőforráscsoport szintjén. Győződjön meg arról is, hogy a `Microsoft.DataBoxEdge` szolgáltató regisztrálva van. A regisztrálásával kapcsolatos információkért lépjen az erőforrás- [szolgáltató regisztrálása](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)elemre.
    - Ha IoT Hub erőforrást szeretne létrehozni, győződjön meg arról, hogy a Microsoft. Devices szolgáltató regisztrálva van. A regisztrálásával kapcsolatos információkért lépjen az erőforrás- [szolgáltató regisztrálása](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)elemre.
    - A Storage-fiók erőforrásának létrehozásához ismét közreműködői vagy magasabb szintű hozzáférési hatókörre van szükség az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
- Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik Microsoft Graph API-hoz. További információ: [Microsoft Graph engedélyek referenciája](https://docs.microsoft.com/graph/permissions-reference).
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge-eszköz esetén

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette a szállítási csomagban található biztonsági információkat.
- Az eszköz csatlakoztatásához az adatközpontban egy szabványos 19. állványban érhető el egy 1U-tárolóhely.
- Olyan sima, stabil és szintű munkafelülethez férhet hozzá, ahol az eszköz biztonságosan megnyugodtan működik.
- A hely, ahol be kívánja állítani az eszközt, szabványos AC-teljesítménnyel rendelkezik egy független forrásból, vagy egy szünetmentes áramforrással (UPS) rendelkező rack Power Distribution Unit (PDU).
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpont hálózata a Data Box Edge eszköz hálózati követelményeinek megfelelően van konfigurálva. További információ: [Data Box Edge rendszerkövetelmények](data-box-edge-system-requirements.md).

- A Data Box Edge normál működési feltételei:

    - Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
    - A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Edge-erőforrással a fizikai eszközök kezeléséhez, hagyja ki ezt a lépést, és folytassa [az aktiválási kulcs lekérésével](#get-the-activation-key).

Data Box Edge-erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be 
    
    - A Azure Portal ezen az URL- [https://portal.azure.com](https://portal.azure.com)címen:.
    - Vagy a Azure Government portál ezen az URL-címen [https://portal.azure.us](https://portal.azure.us):. További részletekért lépjen a [kapcsolódás Azure Government a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. A bal oldali panelen válassza az **+ erőforrás létrehozása**lehetőséget. **Data Box Edge/Data Box Gateway**keresése. Válassza a **Data Box Edge/Data Box Gateway**lehetőséget. Kattintson a **Létrehozás** gombra.
3. Válassza ki a Data Box Edge eszközhöz használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. A régió csak az eszközkezelés metaadatait tárolja. A tényleges adatok bármilyen Storage-fiókban tárolhatók.
    
    A **Data Box Edge** lehetőségnél válassza a **Létrehozás**lehetőséget.

    ![A Data Box Edge szolgáltatás megkeresése](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ezt a rendszer automatikusan kitölti a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.     |

4. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Régió     |Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![Projekt és példány részletei](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Válassza a **Next (tovább): szállítási címet**.

    - Ha már rendelkezik egy eszközzel, válassza a kombinált listát, amelyhez **Data Box Edge eszközem van**.
    - Ha ez az új eszköz, amelyet Ön megrendelt, adja meg a kapcsolattartó nevét, a vállalatot, az eszköz szállítását és a kapcsolattartási adatokat.

    ![Az új eszköz szállítási címe](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Válassza a **Next (tovább): felülvizsgálat + létrehozás**elemet.

7. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek**és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő**kombinált listát.

    ![A Data Box Edge erőforrás részleteinek és adatvédelmi feltételeinek áttekintése](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Kattintson a **Létrehozás** gombra.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza **az Ugrás erőforráshoz**lehetőséget.

![Ugrás a Data Box Edge erőforrásra](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

A megrendelés elhelyezése után a Microsoft áttekinti a rendelést, és elküldi Önt (e-mailben) a szállítási adatokkal.

![Értesítés a Data Box Edge sorrend felülvizsgálatához](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Edge erőforrás működik, le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Edge-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást. Válassza az **Áttekintés** lehetőséget, majd válassza az **eszköz beállítása**lehetőséget.

    ![Eszköz beállításának kiválasztása](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. A csempe **aktiválása** lapon válassza a **kulcs létrehozása** lehetőséget az aktiválási kulcs létrehozásához. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs három nappal a létrehozása után lejár.
> - Ha a kulcs lejárt, állítson be egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti Data Box Edge.

> [!div class="nextstepaction"]
> [A Data Box Edge telepítése](./data-box-edge-deploy-install.md)



