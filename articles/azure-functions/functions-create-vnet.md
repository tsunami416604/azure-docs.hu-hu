---
title: Az Azure Functions integrálható az Azure-beli virtuális hálózathoz
description: Részletes útmutató, amely bemutatja, hogyan csatlakozhat Azure-beli virtuális hálózathoz függvény
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612889"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Oktatóanyag: függvények integrálása az Azure-beli virtuális hálózathoz

Az oktatóanyag bemutatja, hogyan lehet egy Azure virtuális hálózatban lévő erőforrások eléréséhez az Azure Functions használatával. létrehozhat egy függvényt, amely hozzáfér a mindkét az internethez, és a WordPress fut a virtuális hálózatban lévő virtuális géphez.

> [!div class="checklist"]
> * A prémium csomag egy függvényalkalmazás létrehozása
> * Egy WordPress-webhely üzembe helyezése virtuális géphez a virtuális hálózaton
> * A függvényalkalmazás csatlakozni a virtuális hálózathoz
> * Hozzon létre egy függvény proxyt WordPress-erőforrások eléréséhez
> * A virtuális hálózaton belül egy WordPress-fájlba

> [!NOTE]  
> Ebben az oktatóanyagban létrehoz egy függvényalkalmazást a prémium szintű csomag. A szolgáltatási csomag jelenleg előzetes verzióban érhető el. További információkért lásd: [prémium szintű csomag].

## <a name="topology"></a>Topológia

Az alábbi ábrán az Ön által létrehozott megoldás architektúráját mutatja be:

 ![Virtuális hálózati integráció felhasználói felülete](./media/functions-create-vnet/topology.png)

-Függvények futtatása a prémium szintű csomagban a webalkalmazások megegyező üzemeltetési funkciókat, az Azure App Service, amely tartalmazza a VNet-integráció funkciót. További információ a VNet-integráció, beleértve a hibaelhárítás és speciális konfiguráció, lásd: [az alkalmazás integrálása az Azure-beli virtuális hálózathoz](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Előfeltételek

A jelen oktatóanyag esetében fontos, hogy ismeri az IP-címzést és alhálózati használata. Kezdésként használhatja az [Ez a cikk, amely lefedi a címzési és alhálózatok alapjait](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Számos további cikkek és videók érhetők el online.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-function-app-in-a-premium-plan"></a>Egy függvényalkalmazás létrehozása egy prémium szintű csomag

Először hozzon létre egy függvényalkalmazást a [prémium szintű csomag]. Ebben a csomagban kiszolgáló nélküli méretezési biztosít, miközben támogatja a virtuális hálózat integrációja.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

A PIN-kód ikon kiválasztásával a jobb felső sarokban a függvényalkalmazás az irányítópulton is rögzíthet. Rögzítés megkönnyíti a virtuális gép létrehozása után visszatérhet a függvényalkalmazást.

## <a name="create-a-vm-inside-a-virtual-network"></a>Egy virtuális hálózatban lévő virtuális gép létrehozása

Ezután hozzon létre egy előre konfigurált virtuális Gépet, amely a WordPress egy virtuális hálózaton belül ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) Jetware által). Virtuális gép WordPress miatt az alacsony költségű és kényelmes használatra vonatkozó szolgál. Ebben a forgatókönyvben egy virtuális hálózatban, például REST API-k, App Service Environment-környezetek és más Azure-szolgáltatások bármely erőforrás működik. 

1. A portálon **+ erőforrás létrehozása** a bal oldali navigációs ablaktáblán, a Keresés mezőbe írja be a `WordPress LEMP7 Max Performance`, és nyomja le az Enter billentyűt.

1. Válasszon **Wordpress LEMP Max Performance** a keresési eredmények között. A szoftvercsomag kiválasztása **Wordpress LEMP Max Performance a CentOS** , a **szoftvercsomag** válassza **létrehozás**.

1. Az a **alapjai** lapon, a virtuális gép beállításait használja az ábra alatti táblázatban megadott módon:

    ![Alapvető beállítások lapon a virtuális gép létrehozása](./media/functions-create-vnet/create-vm-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben az erőforrások jönnek létre. | 
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Válasszon `myResourceGroup`, vagy a függvényalkalmazás a létrehozott erőforráscsoportot. Használja ugyanazt az erőforráscsoportot a függvényalkalmazás, WordPress VM, és a szolgáltatási csomag könnyebb erőforrások törlése, amikor elkészült, ebben az oktatóanyagban. |
    | **A virtuális gép neve** | VNET-Wordpress | A virtuális gép nevének egyedinek kell lennie az erőforráscsoportban |
    | **[Régió](https://azure.microsoft.com/regions/)** | (Európa) Nyugat-Európa | Válasszon egy Önhöz közeli régiót, vagy az funkciók mellett, amely eléri a virtuális Gépet. |
    | **Méret** | B1s | Válasszon **méretének módosítása** majd válassza ki a B1s standard lemezképet, 1 vCPU- és 1 GB memóriával rendelkező. |
    | **Hitelesítés típusa** | Windows 10 | Jelszó-hitelesítés használatához meg kell adnia egy **felhasználónév**, biztonságos **jelszó**, majd **jelszó megerősítése**. A jelen oktatóanyag esetében nem kell bejelentkezni a virtuális Gépet, ha hibaelhárításkor. |

1. Válassza ki a **hálózatkezelés** lapra, és válassza a virtuális hálózatok konfigurálása **új létrehozása**.

1. A **virtuális hálózat létrehozása**, az ábra alatti táblázatban található beállításokkal:

    ![Hálózat lap a virtuális gép létrehozása](./media/functions-create-vnet/create-vm-2.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Name** | myResourceGroup-vnet | Használhatja az alapértelmezett nevet, a virtuális hálózat jön létre. |
    | **Címtartomány** | 10.10.0.0/16 | A virtuális hálózatához használni egy egyetlen címtartomány. |
    | **Alhálózat neve** | Az oktatóanyag-Net | Az alhálózat neve. |
    | **Címtartomány** (alhálózat) | 10.10.1.0/24   | Az alhálózat méretét határozza meg, hány felületek vehetők az alhálózathoz. A WordPress-webhelyet használja ezt az alhálózatot.  A `/24` alhálózati 254 gazdagép-címet biztosít. |

1. Válassza ki **OK** a virtuális hálózat létrehozásához.

1. Térjen vissza a **hálózatkezelés** lapra, majd **nincs** a **nyilvános IP-cím**.

1. Válassza ki a **felügyeleti** lapon ezt a **diagnosztikai tárfiók**, válassza ki a függvényalkalmazást a létrehozott tárfiókot.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Érvényesítés befejezése után jelölje ki a **létrehozás**. A virtuális gép létrehozása a folyamat veszi fel néhány percig. A létrehozott virtuális gép csak tudja elérni a virtuális hálózat.

1. A virtuális gép létrehozása után válassza ki a **erőforrás megnyitása** megtekintése a lap az új virtuális gép, majd válassza ki **hálózatkezelés** alatt **beállítások**.

1. Győződjön meg arról, hogy nincs **nyilvános IP-cím**. Jegyezze fel a **magánhálózati IP**, amellyel csatlakozhat a virtuális gép a függvényalkalmazást.

    ![A virtuális gép hálózati beállításai](./media/functions-create-vnet/vm-networking.png)

Most már teljes mértékben a virtuális hálózaton belül üzembe helyezett egy WordPress-webhelyet. Ez a hely nem a nyilvános interneten.

## <a name="connect-your-function-app-to-the-virtual-network"></a>A függvényalkalmazás csatlakozni a virtuális hálózathoz

A virtuális hálózatban lévő virtuális gépen futó WordPress-webhely most már kapcsolódhat a függvényalkalmazás virtuális hálózathoz.

1. Válassza ki az új függvényalkalmazást **platformfunkciók** > **hálózatkezelés**.

    ![Válassza ki a hálózatkezelés a függvényalkalmazáshoz](./media/functions-create-vnet/networking-0.png)

1. A **VNet-integráció**válassza **kattintson ide a konfiguráláshoz**.

    ![A hálózati szolgáltatás konfigurálásához állapota](./media/functions-create-vnet/Networking-1.png)

1. Válassza ki a virtuális hálózati integráció lapon **hozzáadása virtuális hálózathoz (előzetes verzió)** .

    ![Adja hozzá a virtuális hálózati integráció előzetes verzió](./media/functions-create-vnet/networking-2.png)

1. A **hálózati funkció állapota**, az ábra alatti táblázatban található beállításokkal:

    ![A függvény alkalmazás virtuális hálózat meghatározása](./media/functions-create-vnet/networking-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Ez a virtuális hálózat lesz a korábban létrehozott. |
    | **Alhálózat** | Új alhálózat létrehozása | Hozzon létre egy alhálózatot a virtuális hálózat használatához a függvényalkalmazás számára. VNet-integráció egy üres alhálózatot használatára kell konfigurálni. Nem számít, hogy az funkciók használata, mint a virtuális gép egy másik alhálózatot. A virtuális hálózat automatikusan irányítja a forgalmat a két alhálózat között. |
    | **Alhálózat neve** | Function-Net | Az új alhálózat neve. |
    | **Virtuális hálózat címterülete** | 10.10.0.0/16 | Válassza ki a WordPress-webhely által használt azonos címterület. Egy meghatározott címterületek kell rendelkeznie. |
    | **Címtartomány** | 10.10.2.0/24   | Az alhálózat méretét a Premium csomag függvényalkalmazás lehet horizontálisan-példányok száma korlátozza. Ez a példa egy `/24` 254 rendelkezésre álló gazdagép-címmel rendelkező alhálózatot. Ez az alhálózat túlterhelt, de könnyen kiszámítása. |

1. Válassza ki **OK** az alhálózat hozzáadása. Zárja be a VNet-integráció és a hálózati funkció állapota lapok, térjen vissza a függvényalkalmazás oldaláról.

A függvényalkalmazás most már elérheti a virtuális hálózat a WordPress-webhely futtató kiszolgáló. Ezután [Azure Functions-proxyk](functions-proxies.md) visszaadása egy fájlt a WordPress-webhelyet.

## <a name="create-a-proxy-to-access-vm-resources"></a>Hozzon létre egy Virtuálisgép-erőforrások eléréséhez proxykiszolgáló

A VNet-integráció engedélyezve van hozzon létre egy proxyt a függvényalkalmazásban, úgy, hogy a virtuális hálózatban futó virtuális gép kérelmeket továbbítsa.

1. A függvényalkalmazásban, jelölje be a **proxyk** >  **+** , majd használja az ábra alatti táblázatban a proxykiszolgáló beállításait:

    ![A proxy-beállítások megadása](./media/functions-create-vnet/create-proxy.png)

    | Beállítás  | Ajánlott érték  | Leírás      |
    | -------- | ---------------- | ---------------- |
    | **Name** | Üzem | A név bármilyen érték lehet. A proxy azonosítására szolgál. |
    | **Útvonalsablon** | /Plant | Olyan útvonalat, amely egy VM-erőforrás van leképezve. |
    | **Háttérkiszolgáló URL-címe** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Cserélje le `<YOUR_VM_IP>` a korábban létrehozott WordPress virtuális gép IP-címmel. Ez a leképezés egyetlen fájl a helyet adja vissza. |

1. Válassza ki **létrehozás** a proxy hozzáadása a függvényalkalmazáshoz.

## <a name="try-it-out"></a>Próbálja ki!

1. A böngészőben, próbálja meg elérni az URL-címet, akkor használja a **háttérkiszolgáló URL-cím**. Megfelelően működik, a kérés túllépi az időkorlátot. Időtúllépés az oka, hogy a WordPress-webhely csak a virtuális hálózathoz, és nem az interneten kapcsolódik.

1. Másolás a **Proxy URL-címe** érkezett az új értéket, és illessze be a böngésző címsorába. A visszaadott kép származik, a virtuális hálózatokon belüli WordPress-webhelyet.

    ![A WordPress-webhely által visszaadott üzem képfájl](./media/functions-create-vnet/plant.png)

A függvényalkalmazás az internetről és a virtuális hálózat is kapcsolódik. A proxy-kérelem fogadása a nyilvános interneten keresztül, és ezután továbbítja a kérést a csatlakoztatott virtuális hálózathoz történő egyszerű HTTP-proxyt átjáróként. A proxy ezután továbbítja a választ, nyilvánosan az interneten keresztül.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a WordPress-webhely is nevezett proxyn keresztül a függvényalkalmazáshoz egy API-t funkcionál. Ebben a forgatókönyvben egy jó oktatóanyag lehetővé teszi, mert beállítása és megjelenítése könnyen. Bármely más virtuális hálózaton belüli üzembe helyezett API használatával. Sikerült is létrehozott egy függvényt, amely meghívja ezt a virtuális hálózaton belül üzembe helyezett API-k kóddal. Ha valószerűbb forgatókönyvet szeretne egy függvényt, amely az adatok ügyfél API-k használatával hívja meg a virtuális hálózaton üzembe helyezett egy SQL Server-példány.

Prémium szintű csomagot futó függvények, web apps-csomagok PremiumV2 ugyanazon az alapul szolgáló App Service-ben infrastruktúrán osztozzon. A teljes dokumentáció [webalkalmazások Azure App Service-ben](../app-service/overview.md) a prémium szintű csomag függvények vonatkozik.

> [!div class="nextstepaction"]
> [További információ a hálózatkezelési funkciók beállításait](./functions-networking-options.md)

[Prémium szintű csomag]: functions-scale.md#premium-plan
