---
title: 'Oktatóanyag: az Azure Portal előkészítése a Data Box Gateway üzembe helyezésére | Microsoft Docs'
description: Az Azure Data Box Gateway üzembe helyezésével foglalkozó első oktatóanyag részét képezi az Azure Portal előkészítése is.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: f4c3cf5329c10cda3691370e946b8397662a8d66
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953412"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Oktatóanyag: Az Azure Data Box Gateway üzembe helyezésének előkészítése (előzetes verzió)


A jelen oktatóanyag az első az Azure Data Box Gateway teljes üzembe helyezéséhez szükséges üzembehelyezési oktatóanyagok sorozatában. Ez az oktatóanyag az Azure Portal előkészítésének módját ismerteti a Data Box Gateway-erőforrás üzembe helyezéséhez. 

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * A virtuáliseszköz-rendszerkép letöltése
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


> [!IMPORTANT]
> - A Data Box Gateway előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Bevezetés

A Data Box Gateway üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat a megadott sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Gatewayhez](data-box-gateway-deploy-prep.md)** |Virtuális Data Box Gateway-eszköz üzembe helyezése előtt létre kell hoznia és konfigurálnia kell a Data Box Gateway-erőforrást. |
| 2. |**[A Data Box Gateway üzembe helyezése a Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[A Data Box Gateway üzembe helyezése a VMware-ben](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V esetén a virtuális Data Box Gateway-eszköz üzembe helyezéséhez, majd az eszközhöz való csatlakozáshoz a Hyper-V-t Windows Server 2016 vagy Windows Server 2012 R2 rendszeren futtató gazdarendszert használjon. <br><br><br> Hyper-V esetén a virtuális Data Box Gateway-eszköz üzembe helyezéséhez, majd az eszközhöz való csatlakozáshoz a VMware ESXi 6.0-t vagy 6.5-öt futtató gazdarendszert használjon.<br></br> |
| 3. |**[A Data Box Gateway csatlakoztatása, konfigurálása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Ezután üzembe helyezheti az SMB-megosztásokat.  |
| 4. |**[Adatok átvitele a Data Box Gatewayjel](data-box-gateway-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Gateway-erőforrással, a Data Box Gateway-eszközzel és az adatközponti hálózattal kapcsolatos konfigurációs előfeltételeket láthatja.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetést engedélyezni kell a Data Box Gateway-erőforrás használatára.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway-eszköz

A virtuális eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

* Rendelkezik hozzáféréssel a Hyper-V-t Windows Server 2012 R2 vagy újabb rendszeren futtató gazdarendszerhez, vagy pedig a VMware-t (ESXi 6.0 vagy 6.5) futtató olyan gazdarendszerhez, amely használható eszközök üzembe helyezésére.
* A gazdarendszernek képesnek kell lennie az alábbi erőforrásokat a virtuális Data Box-eszköz üzembe helyezésére elkülöníteni:
  
  * Legalább 4 mag.
  * Legalább 8 GB RAM. 
  * Egy hálózati adapter.
  * 250 GB-os operációsrendszer-lemez.
  * 2 TB-os virtuális lemez a rendszeradatok számára.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont hálózata a Data Box Gateway-eszköz hálózati követelményeinek megfelelően lett konfigurálva. További információt a [Data Box Gateway rendszerkövetelményeit ismertető](data-box-gateway-system-requirements.md) témakörben talál.

* A Data Box Gateway számára folyamatosan (legalább) 20 Mbps sebességű dedikált internetes sávszélesség áll rendelkezésre. Ez a sávszélesség nem osztható meg más alkalmazásokkal. A hálózati sávszélesség szabályozása, majd a munkavégzés céljából történő szabályozás esetén javasoljuk, hogy legalább 32 Mbps sebességű internetes sávszélességet használjon.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

A Data Box Gateway-erőforrás egyetlen példánya több virtuális Data Box Gateway-eszközt kezelhet. Az alábbi lépések végrehajtásával hozhat létre új Data Box Gateway-erőforrást. 

Ha rendelkezik meglévő Data Box Gateway-erőforrással a virtuális eszközök kezeléséhez, hagyja ki ezt a lépést, és ugorjon [Az aktiválási kulcs lekérése](#get-the-activation-key) lépésre.

Data Box-erőforrás létrehozásához hajtsa végre az alábbi lépéseket az Azure Portalon.
1. Microsoft Azure hitelesítő adatait használva jelentkezzen be az Azure Portalra ezen az URL-címen: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Válassza ki a Data Box Edge előzetes verziójához használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. A **Data Box Gateway** lehetőségnél kattintson a **Létrehozás** elemre.

    ![A Data Box Gateway szolgáltatás keresése](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Adja meg vagy válassza ki az alábbi adatokat az új erőforrás számára.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforrás neve   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Előfizetés    |A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |
    |Hely     |Ebben a kiadásban az USA keleti régiója, az USA 2. nyugati régiója, Délkelet-Ázsia és Nyugat-Európa érhető el. <br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|
    
    ![Data Box Gateway-erőforrás létrehozása](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Kattintson az **OK** gombra.
 
Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozását követően értesítést kap.


## <a name="download-the-virtual-device-image"></a>A virtuáliseszköz-rendszerkép letöltése

A Data Box Gateway-erőforrás létrehozása után töltse le a megfelelő virtuáliseszköz-rendszerképet a virtuális eszköz a gazdarendszeren való üzembe helyezéséhez. Minden operációs rendszerhez külön virtuáliseszköz-rendszerkép tartozik, amely az erőforrásnak az Azure Portalon található **Gyorsindítás** paneljén keresztül tölthető le.

> [!IMPORTANT]
> A Data Box Gatewayen futó szoftver csak a Data Box Gateway-erőforrással használható.


Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

1. Kattintson a létrehozott erőforrásra, majd az **Áttekintés** elemre. Ha rendelkezik meglévő Azure Data Box Gateway-erőforrással, kattintson az erőforrásra, majd nyissa meg az **Áttekintés** lapot.

    ![Új Data Box Gateway-erőforrás](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. A jobb oldali panelen található gyorsindítási részben kattintson a letölteni kívánt rendszerképnek megfelelő hivatkozásra. A rendszerképfájlok mérete körülbelül 4,8 GB.
   
   * [Windows Server 2012 R2 vagy újabb rendszer futó Hyper-V VHDX-fájlja](https://aka.ms/dbe-vhdx-2012).
   * [VMware ESXi 6.0 vagy 6.5 VMDK-fájlja](https://aka.ms/dbe-vmdk).

5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Ha a Data Box Gateway-erőforrás működik és elérhető, le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Gateway-eszközt az erőforráshoz.

Az aktiválási kulccsal regisztrálható az összes olyan Data Box Gateway-eszköz, amelyet a Data Box Gateway-erőforrással aktiválni kell. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Kattintson a létrehozott erőforrásra, majd az **Áttekintés** elemre.

    ![Új Data Box Gateway-erőforrás](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Kattintson a **Kulcs létrehozása** elemre az aktiválási kulcs létrehozásához. Kattintson a másolás ikonra a kulcs másolásához, és későbbi használatra történő mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs a létrehozása után 3 nappal lejár. 
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


