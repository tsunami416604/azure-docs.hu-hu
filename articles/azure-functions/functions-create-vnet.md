---
title: Azure Functions integrálása Azure-beli virtuális hálózattal
description: Lépésenkénti oktatóanyag, amely bemutatja, hogyan csatlakoztatható egy függvény egy Azure-beli virtuális hálózathoz
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: f50c923104fdfcf26f400f20f0de66a82eb3d245
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387523"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Oktatóanyag: függvények integrálása Azure-beli virtuális hálózattal

Ez az oktatóanyag bemutatja, hogyan használható a Azure Functions az Azure-beli virtuális hálózatok erőforrásaihoz való kapcsolódáshoz. létrehozhat egy olyan függvényt, amely hozzáférést biztosít az internethez és a virtuális hálózatban WordPress-t futtató virtuális gépekhez.

> [!div class="checklist"]
> * Function-alkalmazás létrehozása a prémium csomaggal
> * WordPress-webhelyek üzembe helyezése virtuális hálózaton
> * A Function alkalmazás összekötése a virtuális hálózattal
> * Function proxy létrehozása a WordPress-erőforrások eléréséhez
> * A WordPress-fájl igénylése a virtuális hálózaton belül

## <a name="topology"></a>Topológia

A következő ábra a létrehozott megoldás architektúráját mutatja be:

 ![Virtuális hálózati integráció felhasználói felülete](./media/functions-create-vnet/topology.png)

A prémium szintű csomagban futó függvények ugyanazok az üzemeltetési képességek, mint a Azure App Service webalkalmazásai, beleértve a VNet-integrációs funkciót is. További információ a VNet-integrációról, beleértve a hibaelhárítást és a speciális konfigurációt: [az alkalmazás integrálása Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos megérteni az IP-címzést és alhálózatokat. Ebből a cikkből megtudhatja, hogyan kezelheti [a címzési és alhálók alapjait](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Számos további cikk és videó elérhető online állapotban.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-function-app-in-a-premium-plan"></a>Function-alkalmazás létrehozása prémium csomaggal

Először létre kell hoznia egy Function alkalmazást a [prémium]csomagban. Ez a csomag kiszolgáló nélküli skálázást biztosít a virtuális hálózatok integrálásának támogatása mellett.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

A Function alkalmazást a jobb felső sarokban található rögzítés ikonra kattintva rögzítheti az irányítópulton. A rögzítéssel a virtuális gép létrehozása után könnyebben térhet vissza ehhez a Function alkalmazáshoz.

## <a name="create-a-vm-inside-a-virtual-network"></a>Virtuális gép létrehozása virtuális hálózaton belül

Következő lépésként hozzon létre egy előre konfigurált virtuális GÉPET, amely a WordPress-t futtatja egy virtuális hálózaton belül (a[WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). A WordPress virtuális gépeket a szolgáltatás alacsony díja és kényelme miatt használják. Ugyanez a forgatókönyv a virtuális hálózatok bármely erőforrásával, például a REST API-kkal, App Service környezetekkel és egyéb Azure-szolgáltatásokkal is működik. 

1. A portálon válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, a keresőmezőbe írja be a kifejezést `WordPress LEMP7 Max Performance` , majd nyomja le az ENTER billentyűt.

1. Válassza a **WordPress LEMP max teljesítmény** lehetőséget a keresési eredmények között. Válassza ki a **WordPress LEMP Max teljesítményének** a **szoftver csomagját** , és válassza a **Létrehozás**lehetőséget.

1. Az **alapvető** beállítások lapon használja a virtuális gép beállításait az alábbi táblázatban megadott módon:

    ![Alapismeretek lap virtuális gép létrehozásához](./media/functions-create-vnet/create-vm-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza ki `myResourceGroup` vagy a Function alkalmazással létrehozott erőforráscsoportot. A Function app, a WordPress VM és a üzemeltetési csomag azonos erőforráscsoport használatával könnyebben törölheti az erőforrásokat, amikor elkészült ezzel az Oktatóanyaggal. |
    | **Virtuális gép neve** | VNET – WordPress | A virtuális gép nevének egyedinek kell lennie az erőforráscsoporthoz |
    | **[Régió](https://azure.microsoft.com/regions/)** | Európa Nyugat-Európa | Válasszon egy Önhöz közeli régiót vagy a virtuális gépet elérő függvények közelében. |
    | **Méret** | B1s | Válassza a **méret módosítása** lehetőséget, majd válassza ki a B1s standard rendszerképet, amely 1 vCPU és 1 GB memóriát tartalmaz. |
    | **Hitelesítés típusa** | Jelszó | A jelszó-hitelesítés használatához meg kell adnia egy **felhasználónevet**és egy biztonságos **jelszót**is, majd **meg kell erősítenie a jelszót**. Ebben az oktatóanyagban nem kell bejelentkeznie a virtuális gépre, hacsak nem kell a hibakeresést végeznie. |

1. Válassza a **hálózatkezelés** fület, majd a virtuális hálózatok konfigurálása területen válassza az **új létrehozása**lehetőséget.

1. A **virtuális hálózat létrehozása**területen használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    ![Hálózatkezelés lap a virtuális gép létrehozásakor](./media/functions-create-vnet/create-vm-2.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | myResourceGroup – vnet | A virtuális hálózathoz generált alapértelmezett nevet használhatja. |
    | **Címtartomány** | 10.10.0.0/16 | Használjon egyetlen címtartományt a virtuális hálózathoz. |
    | **Alhálózat neve** | Oktatóanyag – net | Az alhálózat neve. |
    | **Címtartomány** (alhálózat) | 10.10.1.0/24   | Az alhálózat mérete határozza meg, hogy hány csatolót lehet hozzáadni az alhálózathoz. Ezt az alhálózatot a WordPress webhely használja.  Az `/24` alhálózat 254 gazdagép-címet biztosít. |

1. A virtuális hálózat létrehozásához kattintson **az OK gombra** .

1. A **hálózatkezelés** lapon válassza a **nincs lehetőséget** a **nyilvános IP-címeknél**.

1. Válassza a **felügyelet** fület, majd a **diagnosztika Storage-fiók**területen válassza ki a Function alkalmazással létrehozott Storage-fiókot.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás**lehetőséget. A virtuális gép létrehozási folyamata néhány percet vesz igénybe. A létrehozott virtuális gép csak a virtuális hálózat elérésére használható.

1. A virtuális gép létrehozása után válassza az **erőforrás keresése** lehetőséget az új virtuális gép oldalának megtekintéséhez, majd válassza a **hálózatkezelés** lehetőséget a **Beállítások**területen.

1. Ellenőrizze, hogy nincs **-e nyilvános IP-cím**. Jegyezze fel a **magánhálózati IP-címet**, amelyet a Function alkalmazásból a virtuális géphez való kapcsolódáshoz használ.

    ![Hálózati beállítások a virtuális gépen](./media/functions-create-vnet/vm-networking.png)

Most már rendelkezik egy, a virtuális hálózaton belül üzembe helyezett WordPress-webhellyel. Ez a hely nem érhető el a nyilvános internetről.

## <a name="connect-your-function-app-to-the-virtual-network"></a>A Function alkalmazás összekötése a virtuális hálózattal

A virtuális hálózatban lévő virtuális gépeken futó WordPress-webhelyekhez mostantól csatlakozhat a Function alkalmazáshoz a virtuális hálózathoz.

1. Az új függvény alkalmazásban válassza a bal oldali menü **hálózatkezelés** elemét.

1. A **VNet-integráció**területen válassza **a kattintson ide a konfiguráláshoz**lehetőséget.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="A Function alkalmazásban válassza a hálózatkezelés lehetőséget.":::

1. A **VNET-integráció** lapon válassza a **VNET hozzáadása**elemet.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="A VNet-integráció előzetes verziójának hozzáadása":::

1. A **hálózati szolgáltatások állapota**területen használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    ![A Function app virtuális hálózat megadása](./media/functions-create-vnet/networking-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup – vnet | Ez a virtuális hálózat a korábban létrehozott. |
    | **Alhálózat** | Új alhálózat létrehozása | Hozzon létre egy alhálózatot a virtuális hálózatban a Function alkalmazás használatára. A VNet-integrációt üres alhálózat használatára kell konfigurálni. Nem számít, hogy a függvények más alhálózatot használnak, mint a virtuális gép. A virtuális hálózat automatikusan átirányítja a forgalmat a két alhálózat között. |
    | **Alhálózat neve** | Függvény – net | Az új alhálózat neve. |
    | **Virtuális hálózati címterület** | 10.10.0.0/16 | Válassza ki ugyanazt a Címterület-blokkot, amelyet a WordPress-webhely használ. Csak egy címterület van definiálva. |
    | **Címtartomány** | 10.10.2.0/24   | Az alhálózat mérete korlátozza azon példányok számát, amelyeket a prémium szintű csomag funkciójának alkalmazásával fel lehet méretezni. Ez a példa egy `/24` alhálózatot használ, amely 254 elérhető gazdagép-címmel rendelkezik. Ez az alhálózat túl van kiépítve, de könnyen kiszámítható. |

1. Az alhálózat hozzáadásához kattintson **az OK gombra** . Az **VNet-integráció** és a **hálózati szolgáltatás állapota** lapok bezárásával térjen vissza a Function app-oldalára.

A Function alkalmazás mostantól elérheti azt a virtuális hálózatot, ahol a WordPress-webhely fut. Ezután a [Azure functions-proxyk](functions-proxies.md) használatával egy fájlt ad vissza a WordPress webhelyről.

## <a name="create-a-proxy-to-access-vm-resources"></a>Proxy létrehozása a virtuális gépek erőforrásainak eléréséhez

Ha a VNet-integráció engedélyezve van, létrehozhat egy proxyt a Function alkalmazásban, hogy továbbítsa a kéréseket a virtuális hálózaton futó virtuális GÉPHEZ.

1. A Function alkalmazásban válassza a bal oldali menü **proxyk** elemét, majd válassza a **Hozzáadás**lehetőséget. Használja az alábbi táblázatban található proxybeállítások közül a rendszerképet:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Proxybeállítások megadása":::

    | Beállítás  | Ajánlott érték  | Leírás      |
    | -------- | ---------------- | ---------------- |
    | **Név** | Üzem | A név tetszőleges érték lehet. A proxy azonosítására szolgál. |
    | **Útvonal sablonja** | /plant | Egy VM-erőforráshoz hozzárendelt útvonal. |
    | **Háttér-URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Cserélje le a helyére `<YOUR_VM_IP>` a korábban létrehozott WordPress-virtuális gép IP-címét. Ez a leképezés egyetlen fájlt ad vissza a helyről. |

1. Válassza a **Létrehozás** lehetőséget, hogy hozzáadja a proxyt a Function alkalmazáshoz.

## <a name="try-it-out"></a>Próba

1. A böngészőben próbálja meg elérni a **háttérbeli URL-címként**használt URL-címet. A várt módon a kérelem időtúllépést mutat. Időtúllépés történik, mert a WordPress-webhely csak a virtuális hálózathoz csatlakozik, és nem az internethez.

1. Másolja a **proxy URL-címét** az új proxyból, és illessze be a böngésző címsorába. A visszaadott rendszerkép a virtuális hálózaton belül futó WordPress-webhelyről származik.

    ![A WordPress webhelyről visszaadott növényi képfájl](./media/functions-create-vnet/plant.png)

A Function alkalmazás csatlakozik az internethez és a virtuális hálózathoz is. A proxy kérést kap a nyilvános interneten keresztül, majd egyszerű HTTP-proxyként viselkedik a kérésnek a csatlakoztatott virtuális hálózatra való továbbításához. A proxy ezután az interneten keresztül továbbítja a választ az Ön számára nyilvánosan.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a WordPress-webhely olyan API-ként szolgál, amely a Function alkalmazásban proxy használatával lett meghívva. Ez a forgatókönyv jó oktatóanyagot tesz lehetővé, hiszen egyszerűen beállítható és megjeleníthető. Bármely más, a virtuális hálózaton belül üzembe helyezett API-t használhat. Létrehozhat egy olyan kóddal rendelkező függvényt is, amely meghívja a virtuális hálózaton belül üzembe helyezett API-kat. A reálisabb forgatókönyv egy olyan függvény, amely adatügyfél-API-kat használ a virtuális hálózatban üzembe helyezett SQL Server példány meghívásához.

A prémium csomagokban futó függvények ugyanazokat a mögöttes App Service infrastruktúrát használják, mint a PremiumV2-csomagokban lévő webalkalmazások. A [Azure app Service webalkalmazásaihoz](../app-service/overview.md) tartozó összes dokumentáció a Prémium csomag funkcióival kapcsolatos.

> [!div class="nextstepaction"]
> [További tudnivalók a függvények hálózati beállításairól](./functions-networking-options.md)

[Prémium szintű csomag]: functions-scale.md#premium-plan
