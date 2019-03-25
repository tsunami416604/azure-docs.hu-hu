---
title: 'Oktatóanyag: az Azure Portal előkészítése a Data Box Gateway üzembe helyezésére | Microsoft Docs'
description: Az Azure Data Box Gateway üzembe helyezésével foglalkozó első oktatóanyag részét képezi az Azure Portal előkészítése is.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 78008357e3ea8fbfe707a7dbead19e3fce83b578
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403697"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Oktatóanyag: Az Azure Data Box átjáró telepítésének előkészítése


A jelen oktatóanyag az első az Azure Data Box Gateway teljes üzembe helyezéséhez szükséges üzembehelyezési oktatóanyagok sorozatában. Ez az oktatóanyag az Azure Portal előkészítésének módját ismerteti a Data Box Gateway-erőforrás üzembe helyezéséhez. 

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

## <a name="get-started"></a>Bevezetés

A Data Box Gateway üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat a megadott sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Gatewayhez](data-box-gateway-deploy-prep.md)** |Virtuális Data Box Gateway-eszköz üzembe helyezése előtt létre kell hoznia és konfigurálnia kell a Data Box Gateway-erőforrást. |
| 2. |**[A Data Box Gateway üzembe helyezése a Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[A Data Box Gateway üzembe helyezése a VMware-ben](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V esetén a virtuális Data Box Gateway-eszköz üzembe helyezéséhez, majd az eszközhöz való csatlakozáshoz a Hyper-V-t Windows Server 2016 vagy Windows Server 2012 R2 rendszeren futtató gazdarendszert használjon. <br><br><br> VMware-ről üzembe helyezése, és a gazdagép operációs rendszert futtató VMware ESXi 6.0, 6.5-ös vagy 6.7 Data Box-átjáró virtuális eszköz csatlakozni.<br></br> |
| 3. |**[A Data Box Gateway csatlakoztatása, konfigurálása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Ezután üzembe helyezheti az SMB-megosztásokat.  |
| 4. |**[Adatok átvitele a Data Box Gatewayjel](data-box-gateway-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Gateway-erőforrással, a Data Box Gateway-eszközzel és az adatközponti hálózattal kapcsolatos konfigurációs előfeltételeket láthatja.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- A Microsoft Azure-előfizetés támogatnia kell a Data Box-Gateway-erőforráshoz. Utólagos elszámolású előfizetések nem támogatottak.
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway-eszköz

A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- A Hyper-V a Windows Server 2012 R2 vagy újabb rendszerű gazdagép rendszere vagy VMware-alapú (ESXi 6.0, 6.5-ös vagy 6.7), amely hozzáféréssel rendelkezik egy kiépítéséhez használt eszköz.
- A gazdarendszernek képesnek kell lennie az alábbi erőforrásokat a virtuális Data Box-eszköz üzembe helyezésére elkülöníteni:
  
  - Legalább 4 virtuális processzor.
  - Legalább 8 GB RAM.
  - Egy hálózati adapter.
  - 250 GB-os operációsrendszer-lemez.
  - 2 TB-os virtuális lemez a rendszeradatok számára.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az adatközpont hálózata a Data Box Gateway-eszköz hálózati követelményeinek megfelelően lett konfigurálva. További információkért lásd: a [Data Box Gateway rendszerkövetelményei](data-box-gateway-system-requirements.md).

- A normál működési feltételek a Data Box-átjáró v: kell rendelkeznie

    - Legalább 10 MB/s sávszélesség és a frissített marad, az eszköz letöltéséhez.
    - Legalább 20 MB/s dedikált sávszélesség és a fájlok átvitelét le- és feltöltése.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Gateway-erőforrással a virtuális eszközök kezeléséhez, hagyja ki ezt a lépést, és ugorjon [Az aktiválási kulcs lekérése](#get-the-activation-key) lépésre.

Hozzon létre egy Data Box-átjáró-erőforrást, az alábbi lépéseket az Azure Portalon.

1. A Microsoft Azure hitelesítő adatok használatával jelentkezzen be:

    - Az Azure Portalon a következő URL-címen: [ https://portal.azure.com ](http://portal.azure.com).
    - Vagy az Azure Government portálra az URL-címen: [ https://portal.azure.us ](https://portal.azure.us). További részletekért látogasson el [csatlakozhat az Azure Government a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. A bal oldali ablaktáblán válassza **+ erőforrás létrehozása**. Keresse meg **Data Box-Edge / Data Box-átjáró**. Válassza ki a Data Box Edge / Data Box-átjárót. Kattintson a **Létrehozás** gombra.
3. Válassza ki a Data Box átjáróeszköz használni kívánt előfizetést. Válassza ki a régiót, ahol szeretné telepíteni a Data Box-Gateway-erőforrás. Ebben a kiadásban az USA keleti RÉGIÓJA, Délkelet-Ázsia és Nyugat-Európa érhetők el. Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. Az a **Box adatátjáró** beállításnál válassza **létrehozás**.

    ![A Data Box Gateway szolgáltatás keresése](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Az a **alapjai** lapon adja meg vagy válassza ki az alábbi **projektadatok**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ez a rendszer automatikusan kitölti a korábban kiválasztott alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |

5. Adja meg vagy válassza ki az alábbi **példány részletei**.
   | Név |} Az erőforrás azonosító rövid nevet.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |   
    | Régió |} Ebben a kiadásban az USA keleti RÉGIÓJA, Délkelet-Ázsia és Nyugat-Európa érhetők el az erőforrás üzembe helyezéséhez. Az Azure Government, government-régiók szerepel a [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) érhetők el. <br> Válassza a földrajzi régióban, ahol szeretné az eszköz üzembe helyezése legközelebb eső helyet. |}
    
    ![Data Box Gateway-erőforrás létrehozása](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Válassza az **Áttekintés + létrehozás** lehetőséget.
 
7. Az a **tekintse át + létrehozása** lapján, a **díjszabás**, **használati feltételeket tartalmazó fájl**, és az erőforrás részleteit. Kattintson a **Létrehozás** gombra.

    ![Tekintse át a Data Box-Gateway-erőforrás részletei](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeresen létrehozott és telepített, miután, értesítést kap. Válassza ki **erőforrás megnyitása**.

![Tekintse át a Data Box-Gateway-erőforrás részletei](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>A virtuáliseszköz-rendszerkép letöltése

A Data Box Gateway-erőforrás létrehozása után töltse le a megfelelő virtuáliseszköz-rendszerképet a virtuális eszköz a gazdarendszeren való üzembe helyezéséhez. A virtuális eszköz lemezképek az operációs rendszer jellemzőek.

> [!IMPORTANT]
> A Data Box Gatewayen futó szoftver csak a Data Box Gateway-erőforrással használható.

Hajtsa végre a lépéseket a [az Azure portal](https://portal.azure.com/) letölti a virtuális eszköz lemezképet.

1. Az erőforrás, amelyet létrehozott, és válassza ki a **áttekintése**. Ha rendelkezik egy meglévő Azure Data Box átjáró-erőforrást, válassza ki az erőforrást, és folytassa **áttekintése**. Válassza ki **eszközbeállítások**.

    ![Új Data Box Gateway-erőforrás](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Az a **letöltési lemezkép** csempére, válassza ki a virtuális eszköz lemezképet, a gazdakiszolgáló épít ki a virtuális gép operációs rendszerének megfelelő. A kép mérete körülbelül 5.6-os GB.
   
   * [Windows Server 2012 R2 vagy újabb rendszer futó Hyper-V VHDX-fájlja](https://aka.ms/dbe-vhdx-2012).
   * [VMDK a VMWare ESXi 6.0, 6.5-ös vagy 6.7](https://aka.ms/dbe-vmdk).

    ![Töltse le a Data Box átjáró virtuális eszköz kép](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box-Gateway-erőforrás áll, be kell szereznie az aktiválási kulcs. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Gateway-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki az erőforrást, amelyet létrehozott, és válassza ki **áttekintése**. Az a **eszközbeállítások**, lépjen a a **konfigurálása és aktiválása** csempére.

    ![Konfigurálja és aktiválja a csempe](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Válassza ki **kulcs generálása** hozhat létre egy aktiválási kulcsot. Kattintson a Másolás ikonra, másolja a kulcsot, és mentheti későbbi használatra.

    ![Aktiválási kulcs lekérése](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs jön létre, azt követően három nappal lejár.
> - Ha a kulcs érvényessége lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

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


