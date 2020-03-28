---
title: 'Oktatóanyag: az Azure Portal előkészítése a Data Box Gateway üzembe helyezésére | Microsoft Docs'
description: Az Azure Data Box Gateway üzembe helyezésével foglalkozó első oktatóanyag részét képezi az Azure Portal előkészítése is.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474458"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Oktatóanyag: Felkészülés az Azure Data Box Gateway üzembe helyezésére

A jelen oktatóanyag az első az Azure Data Box Gateway teljes üzembe helyezéséhez szükséges üzembehelyezési oktatóanyagok sorozatában. Ez az oktatóanyag az Azure Portal előkészítésének módját ismerteti a Data Box Gateway-erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

## <a name="get-started"></a>Bevezetés

A Data Box Gateway üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat a megadott sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Gatewayhez](data-box-gateway-deploy-prep.md)** |Virtuális Data Box Gateway-eszköz üzembe helyezése előtt létre kell hoznia és konfigurálnia kell a Data Box Gateway-erőforrást. |
| 2. |**[A Data Box Gateway üzembe helyezése a Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[A Data Box Gateway üzembe helyezése a VMware-ben](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V esetén a virtuális Data Box Gateway-eszköz üzembe helyezéséhez, majd az eszközhöz való csatlakozáshoz a Hyper-V-t Windows Server 2016 vagy Windows Server 2012 R2 rendszeren futtató gazdarendszert használjon. <br><br><br> VMware esetén a VMware ESXi 6.0, 6.5 vagy 6.7 rendszert futtató gazdarendszeren a Data Box Gateway virtuális eszköz kiépítése és csatlakoztatása.<br></br> |
| 3. |**[A Data Box Gateway csatlakoztatása, konfigurálása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Ezután üzembe helyezheti az SMB-megosztásokat.  |
| 4. |**[Adatok átvitele a Data Box Gatewayjel](data-box-gateway-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Gateway-erőforrással, a Data Box Gateway-eszközzel és az adatközponti hálózattal kapcsolatos konfigurációs előfeltételeket láthatja.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve van egy Azure Stack Edge-erőforráshoz. Győződjön meg arról, hogy olyan támogatott előfizetést használt, mint a [Microsoft Nagyvállalati Szerződés (EA),](https://azure.microsoft.com/overview/sales-number/) [a Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)vagy [a Microsoft Azure szponzorálása](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tulajdonosi vagy közreműködői hozzáféréssel rendelkezik erőforráscsoport-szinten a Data Box Edge/Data Box Gateway, az IoT Hub és az Azure Storage-erőforrásokhoz.
    - Bármely Data Box Edge/ Data Box Gateway erőforrás létrehozásához erőforráscsoport-szinten közreműködői (vagy magasabb szintű) jogosultságokkal kell rendelkeznie. Azt is meg kell `Microsoft.DataBoxEdge` győződnie arról, hogy a szolgáltató regisztrálva van. A regisztrációval kapcsolatos további tudnivalókért látogasson el az [Erőforrás-szolgáltató regisztrálása](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)című oldalra.
    - Egy tárfiók-erőforrás létrehozásához ismét szükség van közreműködői vagy magasabb hozzáférési hatókört az erőforráscsoport szintjén. Az Azure Storage alapértelmezés szerint regisztrált erőforrás-szolgáltató.
- Rendszergazdai vagy felhasználói hozzáféréssel rendelkezik a Microsoft Graph API-hoz. További információt a [Microsoft Graph engedélyek hivatkozása című témakörben talál.](https://docs.microsoft.com/graph/permissions-reference)
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway-eszköz

A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Hozzáférése van egy Hyper-V rendszert futtató gazdarendszerhez Windows Server 2012 R2 vagy újabb rendszeren, illetve vmware-hez (ESXi 6.0, 6.5 vagy 6.7), amelyek egy eszköz létesítéséhez használhatók.
- A gazdarendszernek képesnek kell lennie az alábbi erőforrásokat a virtuális Data Box-eszköz üzembe helyezésére elkülöníteni:
  
  - Legalább 4 virtuális processzor.
  - Legalább 8 GB RAM.
  - Egy hálózati adapter.
  - 250 GB-os operációsrendszer-lemez.
  - 2 TB-os virtuális lemez a rendszeradatok számára.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpont hálózata a Data Box Gateway-eszköz hálózati követelményeinek megfelelően lett konfigurálva. További információt a [Data Box Gateway rendszerkövetelményei](data-box-gateway-system-requirements.md)című témakörben talál.

- A Data Box Gateway normál működési feltételei esetén rendelkeznie kell a következőkkel:

    - Legalább 10 Mb/s letöltési sávszélesség az eszköz frissítésének biztosításához.
    - Legalább 20 Mbps dedikált feltöltési és letöltési sávszélesség fájlok átviteléhez.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Gateway-erőforrással a virtuális eszközök kezeléséhez, hagyja ki ezt a lépést, és ugorjon [Az aktiválási kulcs lekérése](#get-the-activation-key) lépésre.

Data Box Gateway erőforrás létrehozásához tegye a következő lépéseket az Azure Portalon.

1. Microsoft Azure-hitelesítő adataival jelentkezzen be:

    - Az Azure portal ezen [https://portal.azure.com](https://portal.azure.com)az URL-címen: .
    - Vagy az Azure Government portálja [https://portal.azure.us](https://portal.azure.us)ezen az URL-címen: . További részletekért nyissa meg a [Csatlakozás az Azure Governmenthez a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)című oldalon című.

2. A bal oldali ablaktáblában válassza a **+ Erőforrás létrehozása**lehetőséget. A **Data Box Edge / Data Box Gateway**keresése . Válassza a Data Box Edge / Data Box Gateway lehetőséget. Kattintson a **Létrehozás** gombra.
3. Válassza ki a Data Box Gateway eszközhöz használni kívánt előfizetést. Válassza ki azt a régiót, ahol telepíteni szeretné a Data Box Gateway erőforrást. Az összes olyan régió listáját, ahol az Azure Stack Edge erőforrás elérhető, tekintse meg az [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. A **Data Box Gateway (Adatdoboz-átjáró)** beállításban válassza a **Létrehozás gombot.**

    ![A Data Box Gateway szolgáltatás keresése](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Az **Alapok lapon** adja meg vagy jelölje ki a Projekt következő **adatait.**
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ez automatikusan kitöltődik a korábbi kijelölés alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.     |

5. Adja meg vagy válassza ki a következő **példányadatokat**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |   
    |Régió     |Az összes olyan régió listáját, ahol az Azure Stack Edge erőforrás elérhető, tekintse meg az [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Az Azure Government esetében az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/) felsorolt összes kormányzati régió elérhető. <br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|
    
    ![Data Box Gateway-erőforrás létrehozása](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Válassza az **Áttekintés + létrehozás** lehetőséget.
 
7. A **Véleményezés + létrehozás** lapon tekintse át az **Árképzés részleteit**, a **használati feltételeket**és az erőforrás részleteit. Kattintson a **Létrehozás** gombra.

    ![Adatdoboz átjáróerőforrás-részleteinek áttekintése](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza **az Ugrás az erőforráshoz**lehetőséget.

![Adatdoboz átjáróerőforrás-részleteinek áttekintése](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>A virtuáliseszköz-rendszerkép letöltése

A Data Box Gateway-erőforrás létrehozása után töltse le a megfelelő virtuáliseszköz-rendszerképet a virtuális eszköz a gazdarendszeren való üzembe helyezéséhez. A virtuális eszközlemezképek egy operációs rendszerre vonatkoznak.

> [!IMPORTANT]
> A Data Box Gatewayen futó szoftver csak a Data Box Gateway-erőforrással használható.

Kövesse az alábbi lépéseket az [Azure Portalon](https://portal.azure.com/) egy virtuális eszközkép letöltéséhez.

1. A létrehozott erőforrásban válassza az **Áttekintés**lehetőséget. Ha meglévő Azure Data Box Gateway-erőforrással rendelkezik, válassza ki az **erőforrást,** és nyissa meg az Áttekintés lehetőséget. Válassza **az Eszköz beállítása**lehetőséget.

    ![Új Data Box Gateway-erőforrás](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. A **Kép letöltése** csempén válassza ki a virtuális gép kiépítéséhez használt gazdakiszolgáló operációs rendszerének megfelelő virtuális eszközlemezképet. A képfájlok körülbelül 5,6 GB-osak.
   
   * [Windows Server 2012 R2 vagy újabb rendszer futó Hyper-V VHDX-fájlja](https://aka.ms/dbe-vhdx-2012).
   * [VMDK a VMWare ESXi 6.0, 6.5 vagy 6.7](https://aka.ms/dbe-vmdk).

    ![A Data Box Gateway virtuális eszköz képének letöltése](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Gateway erőforrás működik, be kell szereznie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Gateway-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Jelölje ki a létrehozott erőforrást, majd válassza **az Áttekintés**lehetőséget. Az **Eszköz beállítása párbeszédpanelen**nyissa meg a **Konfigurálás és aktiválás** csempét.

    ![Csempe konfigurálása és aktiválása](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Az **aktiválási** kulcs létrehozásához válassza a Kulcs létrehozása lehetőséget. Válassza a másolás ikont a kulcs másolásához és későbbi használatra történő mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs a létrehozása után három nappal lejár.
> - Ha a kulcs lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

A következő oktatóanyag azt mutatja be, hogyan helyezhet üzembe egy virtuális gépet a Data Box Gateway számára. A gazdagép operációs rendszerétől függően az alábbi témakörökben tekintheti meg a részletes utasításokat:

> [!div class="nextstepaction"]
> [A Data Box Gateway üzembe helyezése a Hyper-V-ben](./data-box-gateway-deploy-provision-hyperv.md)

VAGY

> [!div class="nextstepaction"]
> [A Data Box Gateway üzembe helyezése a VMware-ben](./data-box-gateway-deploy-provision-vmware.md)


