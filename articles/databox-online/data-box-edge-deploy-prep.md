---
title: 'Oktatóanyag: Az Azure Portal előkészítése a Data Box Edge üzembe helyezésére | Microsoft Docs'
description: Az első oktatóanyagban helyezhető üzembe az Azure Data Box Edge magában foglalja a előkészítése az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7764b0ceee1b540e9650d232b7087811d7376f28
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452088"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Oktatóanyag: Az üzembe helyezés az Azure Data Box Edge előkészítése  


Ez az az, hogy teljes mértékben az Azure Data Box Edge telepítéséhez szükséges az üzembehelyezési oktatóanyagok a sorozat első oktatóanyaga. Ez az oktatóanyag leírja, hogyan készíti elő az Azure portal Data Box Edge erőforrás üzembe helyezéséhez. 

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. Order, és a megoldás üzembe helyezése előtt tekintse át a [Azure villámnézethez szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).  

### <a name="get-started"></a>Bevezetés

Data Box Edge telepíteni, tekintse meg az alábbi oktatóanyagok az előírt sorrendben.

| **#** | **Ebben a lépésben** | **Ezeket a dokumentumokat használja** |
| --- | --- | --- | 
| 1. |**[Az Azure Portal előkészítése a Data Box Edge-hez](data-box-edge-deploy-prep.md)** |A Data Box Edge fizikai eszköz üzembe helyezése előtt hozza létre és konfigurálja a Data Box Edge-erőforrást. |
| 2. |**[A Data Box Edge telepítése](data-box-edge-deploy-install.md)**|Csomagolja ki, helyezze állványra, és csatlakoztassa a kábeleket a Data Box Edge fizikai eszközhöz.  |
| 3. |**[Csatlakozás, állítsa be, és aktiválja a Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Csatlakozzon a helyi webes felhasználói felülethez, végezze el az eszköz konfigurálását, majd aktiválja az eszközt. Az eszköz készen áll az SMB- vagy NFS-megosztások beállítására.  |
| 4. |**[Adatok átvitele a Data Box Edge segítségével](data-box-edge-deploy-add-shares.md)** |Vegyen fel megosztásokat, és csatlakozzon a megosztásokhoz SMB vagy NFS használatával. |
| 5. |**[Adatok átalakítása a Data Box Edge segítségével](data-box-edge-deploy-configure-compute.md)** |Konfigurálja úgy az eszközön lévő Edge-modulokat, hogy átalakítsák az adatokat az Azure-ba való átvitelkor. |

Most megkezdheti az Azure Portal beállítását.

## <a name="prerequisites"></a>Előfeltételek

Az alábbiakban az adatközponti hálózathoz, a Data Box Edge erőforrás és a Data Box Edge-eszköz konfigurációs előfeltételeit.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A Microsoft Azure-előfizetés engedélyezve van a Data Box Edge erőforrás.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge-eszköz esetén

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:
- Van egy elérhető standard 19" rack 1 U tárhely rack az eszköz csatlakoztatása az Adatközpont. 
- Rendelkezik hozzáféréssel egy egybesimított, stabil és szintű munkahelyi felületére, ahol az eszköz nyugodt lehet biztonságosan.
- A hely, ahol szeretne beállítani az eszközt a szokásos hálózati áramellátás Visszaállt egy független forrás vagy egy állvány power terjesztési egység (PDU) szünetmentes tápegység (UPS) rendelkezik.
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A hálózat, az adatközpontban a Data Box Edge-eszköz hálózati követelményei szerint van konfigurálva. További információkért lásd: [Data Box Edge rendszerkövetelmények](data-box-gateway-system-requirements.md).

* Data Box Edge van az internetes sávszélesség 20 MB/s (vagy még több) dedikált mindenkor. A sávszélesség nem oszthatók meg más alkalmazásokkal. Ha használ a hálózati sávszélesség-szabályozás, majd szeretne dolgozni, szabályozási javasoljuk 32 MB/s internetes sávszélességet vagy több használt.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Az alábbi lépések végrehajtásával hozhat létre új Data Box Edge-erőforrást. 

Ha rendelkezik meglévő Data Box Edge-erőforrással a fizikai eszközök kezeléséhez, hagyja ki ezt a lépést, és folytassa [az aktiválási kulcs lekérésével](#get-the-activation-key).

Hozzon létre egy Data Box Edge erőforrást, az alábbi lépéseket az Azure Portalon.

1. A Microsoft Azure hitelesítő adatok használatával jelentkezzen be az Azure betekintő portál ezen URL-címen: [ https://aka.ms/databox-edge ](https://aka.ms/databox-edge). 

2. Válassza ki az előfizetést, amelyhez szeretné használni a Data Box Edge előzetes verziójára. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. Az a **Data Box Edge** beállításnál válassza **létrehozás**.

    ![A Data Box Edge szolgáltatás megkeresése](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Adja meg vagy válassza ki az alábbi adatokat az új erőforrás számára.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforrás neve   | Egy rövid nevet, amellyel azonosíthatja az erőforrás.<br>Az erőforrás neve betűket, számokat és kötőjelet tartalmazó 2 és 50 karakter közé tartozik.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Előfizetés    |A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Tudjon meg többet [Azure-erőforráscsoportok](../azure-resource-manager/resource-group-overview.md).     |
    |Hely     |Ebben a kiadásban az USA keleti régiója, az USA 2. nyugati régiója, Délkelet-Ázsia és Nyugat-Európa érhető el. <br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|
    
    ![Data Box Edge erőforrás létrehozása](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Kattintson az **OK** gombra.
 
Az erőforrás létrehozása néhány percet vesz igénybe. Ha az erőforrás sikeresen létrejött, értesítést kap arról megfelelően.


## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Edge erőforrás helyezheti üzembe, be kell szereznie az aktiválási kulcs. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Edge-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki az erőforrást, amelyet létrehozott, és válassza ki **áttekintése**.

2. Válassza ki **kulcs generálása** hozhat létre egy aktiválási kulcsot. Kattintson a Másolás ikonra, másolja a kulcsot, és mentheti későbbi használatra.

    ![Aktiválási kulcs lekérése](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs jön létre, azt követően három nappal lejár. 
> - Ha a kulcs érvényessége lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs beolvasása

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan telepítheti a Data Box Edge. 

> [!div class="nextstepaction"]
> [Data Box Edge telepítése](./data-box-edge-deploy-install.md)



