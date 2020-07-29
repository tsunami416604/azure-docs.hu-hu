---
title: Oktatóanyag a Azure Portal, adatközpont-környezet előkészítéséhez Azure Stack Edge üzembe helyezéséhez | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésének első oktatóanyaga a Azure Portal előkészítését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c8f64bc81afb941e13dd310a7efd9432639ec281
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131837"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge"></a>Oktatóanyag: Felkészülés a Azure Stack Edge üzembe helyezésére  

Ez az első oktatóanyag az üzembe helyezési oktatóanyagok sorozatában, amelyek a Azure Stack Edge teljes telepítéséhez szükségesek. Ez az oktatóanyag leírja, hogyan készítheti elő a Azure Portal egy Azure Stack Edge-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="get-started"></a>Bevezetés

Azure Stack Edge telepítéséhez tekintse meg az alábbi oktatóanyagokat az előírt sorozatban.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[A Azure Portal előkészítése Azure Stack Edge-re](azure-stack-edge-deploy-prep.md)** |Hozza létre és konfigurálja a Azure Stack Edge-erőforrást, mielőtt telepítené a Azure Stack Box Edge fizikai eszközét. |
| 2. |**[Azure Stack Edge telepítése](azure-stack-edge-deploy-install.md)**|A Azure Stack Edge fizikai eszköz kicsomagolása, állványra és kábelre csatlakoztatása.  |
| 3. |**[Azure Stack Edge összekapcsolása, beállítása és aktiválása](azure-stack-edge-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Az eszköz készen áll az SMB- vagy NFS-megosztások beállítására.  |
| 4. |**[Adatok átvitele Azure Stack Edge-vel](azure-stack-edge-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
| 5. |**[Az adatátalakítás Azure Stack Edge-sel](azure-stack-edge-deploy-configure-compute.md)** |Konfigurálja a számítási modulokat az eszközön az adatok átalakításához az Azure-ba való átlépéshez. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

A következő konfigurációs előfeltételek vonatkoznak az Azure Stack Edge-erőforrásra, az Azure Stack Edge-eszközre és az adatközpont-hálózatra.

### <a name="for-the-azure-stack-edge-resource"></a>Az Azure Stack Edge-erőforráshoz

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve van egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint például a [Microsoft nagyvállalati szerződés (EA)](https://azure.microsoft.com/overview/sales-number/), a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)vagy a [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/). Az utólagos elszámolású előfizetések nem támogatottak.

* Az Azure Stack Edge/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrások esetében tulajdonosi vagy közreműködői hozzáférése van az erőforráscsoport szintjén.

  * A közreműködői hozzáférés biztosításához az előfizetés szintjén kell lennie a **tulajdonosnak** . Ahhoz, hogy a közreműködői hozzáférhessen valaki másnak, a Azure Portal lépjen a **minden szolgáltatás**  >  **előfizetések**  >  **hozzáférés-vezérlés (iam)**  >  **+**  >  **Add role assignment**adja hozzá a szerepkör-hozzárendelés hozzáadása lehetőséget. További információ: [oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal használatával](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

  * Ha Azure Stack Edge/Data Box Gateway erőforrást szeretne létrehozni, akkor az erőforrás-csoport szintjén a közreműködő (vagy magasabb szintű) jogosultsággal kell rendelkeznie. Győződjön meg arról is, hogy az `Microsoft.DataBoxEdge` erőforrás-szolgáltató regisztrálva van. Az erőforrás-szolgáltatók regisztrálásával kapcsolatos információkért lásd: az [erőforrás-szolgáltató regisztrálása](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Ha IoT Hub erőforrást szeretne létrehozni, győződjön meg arról, hogy a Microsoft. Devices szolgáltató regisztrálva van. A regisztrálásával kapcsolatos információkért lépjen az erőforrás- [szolgáltató regisztrálása](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)elemre.
  * A Storage-fiók erőforrásának létrehozásához ismét közreműködői vagy magasabb szintű hozzáférési hatókörre van szükség az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
* Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik Azure Active Directory Graph APIhoz. További információ: [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A rendszergazda által beállított bármely Azure-házirend nem blokkolja. A szabályzatokkal kapcsolatos további információkért lásd: gyors útmutató: szabályzat- [hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal).

### <a name="for-the-azure-stack-edge-device"></a>Az Azure Stack Edge-eszközhöz

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* Áttekintette a szállítási csomagban található biztonsági információkat.
* Az eszköz csatlakoztatásához az adatközpontban egy szabványos 19. állványban érhető el egy 1U-tárolóhely.
* Olyan sima, stabil és szintű munkafelülethez férhet hozzá, ahol az eszköz biztonságosan megnyugodtan működik.
* A hely, ahol be kívánja állítani az eszközt, szabványos AC-teljesítménnyel rendelkezik egy független forrásból, vagy egy szünetmentes áramforrással (UPS) rendelkező rack Power Distribution Unit (PDU).
* Hozzáférése kell rendelkeznie egy fizikai eszközhöz.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpontban található hálózat a Azure Stack Edge-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információ: [Azure stack Edge rendszerkövetelményei](azure-stack-edge-system-requirements.md).

* Az Azure Stack Edge normál működési feltételei:

  * Legalább 10 MB/s letöltési sávszélesség, hogy az eszköz naprakész maradjon.
  * A fájlok átviteléhez legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség szükséges.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Azure Stack Edge-erőforrással a fizikai eszköz kezeléséhez, hagyja ki ezt a lépést, és lépjen [az aktiválási kulcs lekérése](#get-the-activation-key)elemre.

Azure Stack peremhálózati erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be 

    - A Azure Portal ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .
    - Vagy a Azure Government portál ezen az URL-címen: [https://portal.azure.us](https://portal.azure.us) . További részletekért lépjen a [kapcsolódás Azure Government a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. A bal oldali panelen válassza az **+ erőforrás létrehozása**lehetőséget. Keresse meg és válassza ki **Azure stack Edge/Data Box Gateway**. Válassza a **Létrehozás** lehetőséget.
3. Válassza ki az Azure Stack Edge-eszközhöz használni kívánt előfizetést. Válassza ki azt a régiót, ahol az Azure Stack Edge-erőforrást telepíteni kívánja. Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. A régió csak az eszközkezelés metaadatait tárolja. A tényleges adatok bármilyen Storage-fiókban tárolhatók.
    
    Az **Azure stack Edge** lehetőségnél válassza a **Létrehozás**elemet.

    ![Keresés Azure Stack Edge szolgáltatásban](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ezt a rendszer automatikusan kitölti a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |

4. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Régió     |Az Azure Stack Edge-erőforrást tartalmazó régiók listáját itt tekintheti meg: [régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![Projekt és példány részletei](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Válassza a **Next (tovább): szállítási címet**.

    - Ha már rendelkezik egy eszközzel, válassza az **Azure stack Edge-eszközhöz**tartozó kombinált listát.
    - Ha ez az új eszköz, amelyet Ön megrendelt, adja meg a kapcsolattartó nevét, a vállalatot, az eszköz szállítását és a kapcsolattartási adatokat.

    ![Az új eszköz szállítási címe](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

7. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek**és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő**kombinált listát.

    ![Tekintse át Azure Stack Edge-erőforrás részleteit és adatvédelmi feltételeit](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Válassza a **Létrehozás** lehetőséget.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.

![Ugrás az Azure Stack Edge-erőforrásra](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

A megrendelés elhelyezése után a Microsoft áttekinti a rendelést, és elküldi Önt (e-mailben) a szállítási adatokkal.

![Értesítés az Azure Stack Edge-sorrend felülvizsgálatához](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Az Azure Stack Edge-erőforrás működésének megkezdése után le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja és összekapcsolhatja Azure Stack peremhálózati eszközét az erőforrással. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást. Válassza az **Áttekintés** lehetőséget, majd válassza az **eszköz beállítása**lehetőséget.

    ![Eszköz beállításának kiválasztása](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. A csempe **aktiválása** lapon válassza a **kulcs létrehozása** lehetőséget az aktiválási kulcs létrehozásához. Kattintson a másolás ikonra a kulcs másolásához és a későbbi használatra mentéséhez.

    ![Aktiválási kulcs lekérése](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Az aktiválási kulcs három nappal a létrehozása után lejár.
> * Ha a kulcs lejárt, állítson be egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte a Azure Stack Edge-témaköröket, például a következőket:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti Azure Stack Edge-t.

> [!div class="nextstepaction"]
> [Azure Stack Edge telepítése](./azure-stack-edge-deploy-install.md)
