---
title: 'Oktatóanyag: Az Azure Portal előkészítése a Data Box Edge üzembe helyezésére | Microsoft Docs'
description: Az Azure Data Box Edge üzembe helyezésével foglalkozó első oktatóanyag részét képezi az Azure Portal előkészítése is.
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
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 79061caac3d598df79f383b9b13458ab9537cd81
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832799"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>Oktatóanyag: Az Azure Data Box Edge üzembe helyezésének előkészítése (előzetes verzió)


A jelen oktatóanyag az első az Azure Data Box Edge teljes üzembe helyezéséhez szükséges üzembehelyezési oktatóanyagok sorozatában. Ez az oktatóanyag az Azure Portal előkészítésének módját ismerteti a Data Box Edge-erőforrás üzembe helyezéséhez. 

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Bevezetés

A Data Box Edge üzembe helyezéséhez tekintse meg az alábbi oktatóanyagokat a megadott sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Edge-hez](data-box-edge-deploy-prep.md)** |A Data Box Edge fizikai eszköz üzembe helyezése előtt hozza létre és konfigurálja a Data Box Edge-erőforrást. |
| 2. |**[A Data Box Edge telepítése](data-box-edge-deploy-install.md)**|Csomagolja ki, helyezze állványra, és csatlakoztassa a kábeleket a Data Box Edge fizikai eszközhöz.  |
| 3. |**[A Data Box Edge csatlakoztatása, konfigurálása és aktiválása](data-box-edge-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Az eszköz készen áll az SMB- vagy NFS-megosztások beállítására.  |
| 4. |**[Adatok átvitele a Data Box Edge segítségével](data-box-edge-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
| 5. |**[Adatok átalakítása a Data Box Edge segítségével](data-box-edge-deploy-configure-compute.md)** |Konfigurálja úgy az eszközön lévő Edge-modulokat, hogy átalakítsák az adatokat az Azure-ba való átvitelkor. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban a Data Box Edge-erőforrással, a Data Box Edge-eszközzel és az adatközponti hálózattal kapcsolatos konfigurációs előfeltételeket láthatja.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetést engedélyezni kell a Data Box Edge-erőforrás használatára.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge-eszköz esetén

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Van egy elérhető 1U méretű helye az adatközpontban egy 19 hüvelykes standard kiszolgálószekrényben az eszköz állványra rögzítéséhez. 
- Győződjön meg arról, hogy van egy sima, stabil és vízszintes munkafelülete, ahol az eszköz biztonságban lehet.
- Gondoskodjon arról, hogy az eszköz beállításához kiszemelt helyen legyen standard, független forrású áramforrás vagy egy kiszolgálószekrényhez való áramelosztó egység (PDU) szünetmentes tápegységgel (UPS).
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont hálózata a Data Box Edge-eszköz hálózati követelményeinek megfelelően lett konfigurálva. További információt a [Data Box Edge rendszerkövetelményeit ismertető](data-box-gateway-system-requirements.md) témakörben talál.

* A Data Box Edge számára folyamatosan (legalább) 20 Mbps sebességű dedikált internetes sávszélesség áll rendelkezésre. Ez a sávszélesség nem osztható meg más alkalmazásokkal. A hálózati sávszélesség szabályozása, majd a munkavégzés céljából történő szabályozás esetén javasoljuk, hogy legalább 32 Mbps sebességű internetes sávszélességet használjon.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Az alábbi lépések végrehajtásával hozhat létre új Data Box Edge-erőforrást. 

Ha rendelkezik meglévő Data Box Edge-erőforrással a fizikai eszközök kezeléséhez, hagyja ki ezt a lépést, és folytassa [az aktiválási kulcs lekérésével](#get-the-activation-key).

Data Box-erőforrás létrehozásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. Microsoft Azure hitelesítő adatait használva jelentkezzen be az Azure Portalra ezen az URL-címen: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Válassza ki a Data Box Edge előzetes verziójához használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. A **Data Box Edge** lehetőségnél kattintson a **Létrehozás** elemre.

    ![A Data Box Edge szolgáltatás megkeresése](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Adja meg vagy válassza ki az alábbi adatokat az új erőforrás számára.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforrás neve   | Az erőforrást azonosító valódi név.<br>Az erőforrásnévnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjeleket tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Előfizetés    |A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |
    |Hely     |Ebben a kiadásban az USA keleti régiója, az USA 2. nyugati régiója, Délkelet-Ázsia és Nyugat-Európa érhető el. <br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|
    
    ![Data Box Edge-erőforrás létrehozása](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Kattintson az **OK** gombra.
 
Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozását követően értesítést kap.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Ha a Data Box Edge-erőforrás működik és elérhető, le kell kérnie az aktiválási kulcsot. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Edge-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Kattintson a létrehozott erőforrásra, majd az **Áttekintés** elemre.

2. Kattintson a **Kulcs létrehozása** elemre az aktiválási kulcs létrehozásához. Kattintson a másolás ikonra a kulcs másolásához, és későbbi használatra történő mentéséhez.

    ![Aktiválási kulcs lekérése](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs a létrehozása után 3 nappal lejár. 
> - Ha a kulcs lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

A következő oktatóanyagból a Data Box Edge üzembe helyezését ismerheti meg. 

> [!div class="nextstepaction"]
> [Data Box Edge üzembe helyezése](./data-box-edge-deploy-install.md)



