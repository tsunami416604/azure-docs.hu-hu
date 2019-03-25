---
title: 'Oktatóanyag: Az Azure Portal előkészítése a Data Box Edge üzembe helyezésére | Microsoft Docs'
description: Az első oktatóanyagban helyezhető üzembe az Azure Data Box Edge magában foglalja a előkészítése az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 19c4fc96653f966ea5642149d944886e4b7f4483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401680"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Oktatóanyag: Az üzembe helyezés az Azure Data Box Edge előkészítése  


Ez az az, hogy teljes mértékben az Azure Data Box Edge telepítéséhez szükséges az üzembehelyezési oktatóanyagok a sorozat első oktatóanyaga. Ez az oktatóanyag leírja, hogyan készíti elő az Azure portal Data Box Edge erőforrás üzembe helyezéséhez.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


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

- A Microsoft Azure-előfizetés engedélyezve van a Data Box Edge erőforrás. Utólagos elszámolású előfizetések nem támogatottak.
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge-eszköz esetén

Fizikai eszköz üzembe helyezése előtt győződjön meg az alábbiakról:

- Áttekintette a biztonsági adatokat, a szállítás csomag található.
- Van egy elérhető standard 19" rack 1 U tárhely rack az eszköz csatlakoztatása az Adatközpont. 
- Rendelkezik hozzáféréssel egy egybesimított, stabil és szintű munkahelyi felületére, ahol az eszköz nyugodt lehet biztonságosan.
- A hely, ahol szeretne beállítani az eszközt a szokásos hálózati áramellátás Visszaállt egy független forrás vagy egy állvány power terjesztési egység (PDU) szünetmentes tápegység (UPS) rendelkezik.
- Hozzáférése kell rendelkeznie egy fizikai eszközhöz.


### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- A hálózat, az adatközpontban a Data Box Edge-eszköz hálózati követelményei szerint van konfigurálva. További információkért lásd: [Data Box Edge rendszerkövetelmények](data-box-edge-system-requirements.md).

- A Data Box Edge normál működési feltételek mellett van:

    - Legalább 10 MB/s sávszélesség és a frissített marad, az eszköz letöltéséhez.
    - Legalább 20 MB/s dedikált sávszélesség és a fájlok átvitelét le- és feltöltése.

## <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Ha rendelkezik meglévő Data Box Edge-erőforrással a fizikai eszközök kezeléséhez, hagyja ki ezt a lépést, és folytassa [az aktiválási kulcs lekérésével](#get-the-activation-key).

Hozzon létre egy Data Box Edge erőforrást, az alábbi lépéseket az Azure Portalon.

1. Jelentkezzen be a hitelesítő adatait a Microsoft Azure használatával 
    
    - Az Azure Portalon a következő URL-címen: [ https://portal.azure.com ](http://portal.azure.com).
    - Vagy az Azure Government portálra az URL-címen: [https://portal.azure.us](https://portal.azure.us)

2. A bal oldali ablaktáblán válassza **+ erőforrás létrehozása**. Keresse meg **Data Box-Edge / Data Box-átjáró**. Válassza ki **Data Box-Edge / Data Box-átjáró**. Kattintson a **Létrehozás** gombra.
3. Válassza ki az előfizetést, amelyet a Data Box peremhálózati eszköz használni szeretne. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Edge-erőforrást. Ebben a kiadásban az USA keleti RÉGIÓJA, Délkelet-Ázsia és Nyugat-Európa érhetők el. Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon. Az a **Data Box Edge** beállításnál válassza **létrehozás**.

    ![A Data Box Edge szolgáltatás megkeresése](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Az a **alapjai** lapon adja meg vagy válassza ki az alábbi **projektadatok**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ez a rendszer automatikusan kitölti a korábban kiválasztott alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.<br>Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/resource-group-overview.md) kapcsolatban.     |

4. Adja meg vagy válassza ki az alábbi **példány részletei**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Régió     |Ebben a kiadásban az USA keleti RÉGIÓJA, Délkelet-Ázsia és Nyugat-Európa érhetők el az erőforrás üzembe helyezéséhez. Ha használja az Azure Government, government-régiók érhetők el, ahogyan a [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/).<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|

    ![Projekt és a példány részletei](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Válassza ki **tovább: Szállítási cím**.

    - Ha már rendelkezik egy eszközt, jelölje be a kombinált **Data Box peremhálózati eszköz**.
    - Ha ez az új eszköz, amely a rendszer rendezése, adja meg a kapcsolattartónak a neve, vállalati, cím az eszköz szállításra, és a kapcsolattartási adatokat.

    ![Új eszköz szállítási cím](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Válassza ki **tovább: Felülvizsgálat + létrehozás**.

7. Az a **tekintse át + létrehozása** lapján, a **díjszabás**, **használati feltételeket tartalmazó fájl**, és az erőforrás részleteit. Válassza ki a kombinált **áttekintettem az adatvédelmi szabályzat feltételeit**.

    ![Tekintse át a Data Box Edge erőforrás részleteit és adatvédelmi szabályzata](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Kattintson a **Létrehozás** gombra.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeresen létrehozott és telepített, miután, értesítést kap. Válassza ki **erőforrás megnyitása**.

![A Data Box Edge erőforrás megnyitása](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Után a rendelés kerül, a Microsoft áttekinti a rendelést, és véglegesítéskor (e-mailen keresztül), a szállítási adatok.

![Tekintse át a Data Box Edge rendelés értesítést](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Az aktiválási kulcs lekérése

Miután a Data Box Edge erőforrás helyezheti üzembe, be kell szereznie az aktiválási kulcs. Ezzel a kulccsal aktiválhatja, majd csatlakoztathatja a Data Box Edge-eszközt az erőforráshoz. Ezt a kulcsot lekérheti most, amíg az Azure Portalon van.

1. Válassza ki a létrehozott erőforrást. Válassza ki **áttekintése** majd **eszközbeállítások**.

    ![Válassza ki az eszköz beállítása](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Az a **aktiválás** csempéről válassza **kulcs generálása** hozhat létre egy aktiválási kulcsot. Kattintson a Másolás ikonra, másolja a kulcsot, és mentheti későbbi használatra.

    ![Aktiválási kulcs lekérése](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Az aktiválási kulcs jön létre, azt követően három nappal lejár.
> - Ha a kulcs érvényessége lejárt, hozzon létre egy új kulcsot. A régebbi kulcs nem lesz érvényes.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Új erőforrás létrehozása
> * Az aktiválási kulcs lekérése

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan telepítheti a Data Box Edge.

> [!div class="nextstepaction"]
> [Data Box Edge telepítése](./data-box-edge-deploy-install.md)



