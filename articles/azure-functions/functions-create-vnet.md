---
title: Az Azure Functions integrálása azure-beli virtuális hálózattal
description: Lépésről lépésre bemutatja, hogyan csatlakoztathat egy függvényt egy Azure virtuális hálózathoz
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433215"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Oktatóanyag: függvények integrálása Azure-beli virtuális hálózattal

Ez az oktatóanyag bemutatja, hogyan csatlakozhat az Azure Functions az Azure virtuális hálózat erőforrásaihoz való csatlakozáshoz. létrehoz egy olyan funkciót, amely mind az internethez, mind a Virtuális hálózatban wordpress-t futtató virtuális géphez hozzáfér.

> [!div class="checklist"]
> * Függvényalkalmazás létrehozása a Prémium csomagban
> * WordPress-webhely telepítése virtuális hálózatban a virtuális gépre
> * A függvényalkalmazás csatlakoztatása a virtuális hálózathoz
> * Függvényproxy létrehozása a WordPress-erőforrások eléréséhez
> * WordPress fájl kérése a virtuális hálózaton belülről

## <a name="topology"></a>Topológia

Az alábbi ábra a létrehozott megoldás architektúráját mutatja be:

 ![Felhasználói felület a virtuális hálózatok integrációjához](./media/functions-create-vnet/topology.png)

A prémium csomagban futó funkciók ugyanazokkal az üzemeltetési képességekkel rendelkeznek, mint az Azure App Service webalkalmazásai, amely tartalmazza a Virtuálishálózat-integráció funkciót. Ha többet szeretne megtudni a virtuális hálózatok integrációjáról, beleértve a hibaelhárítást és a speciális konfigurációt, olvassa el [az Alkalmazás integrálása Azure virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos, hogy tisztában legyen az IP-címzésésés és -alhálózatolás. Kezdheti ezzel a [cikkel, amely a címzés és a részkapcsolat alapjait ismerteti.](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics) Sokkal több cikk és videó érhető el az interneten.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-function-app-in-a-premium-plan"></a>Függvényalkalmazás létrehozása prémium csomagban

Először hozzon létre egy függvényalkalmazást a [Prémium csomagban.] Ez a terv kiszolgáló nélküli méretezést biztosít, miközben támogatja a virtuális hálózati integrációt.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

A funkcióalkalmazást rögzítheti az irányítópulton, ha a jobb felső sarokban kiválasztja a pin ikont. A rögzítés megkönnyíti a függvényalkalmazáshoz való visszatérést a virtuális gép létrehozása után.

## <a name="create-a-vm-inside-a-virtual-network"></a>Virtuális gép létrehozása virtuális hálózaton belül

Ezután hozzon létre egy előre konfigurált virtuális gép, amely futtatja a WordPress belsejében egy virtuális hálózat[(WordPress LEMP7 Max Teljesítmény](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) Jetware). A WordPress virtuális gép az alacsony költségek és a kényelem miatt használatos. Ugyanez a forgatókönyv működik a virtuális hálózat bármely erőforrásával, például a REST API-kkal, az App Service-környezetekkel és más Azure-szolgáltatásokkal. 

1. A portálon válassza a **+ Erőforrás létrehozása** lehetőséget a bal `WordPress LEMP7 Max Performance`oldali navigációs ablakban, a keresőmező típusában , és nyomja le az Enter billentyűt.

1. Válassza **a Wordpress LEMP Max Performance lehetőséget** a keresési eredmények között. Válassza ki a **Wordpress LEMP Max Performance for CentOS** szoftvercsomag **szoftvertervét,** és válassza a **Létrehozás lehetőséget.**

1. Az **Alapok lapon** használja a virtuális gép beállításait a kép alatti táblázatban megadottak szerint:

    ![Alapjai lap a virtuális gép létrehozásához](./media/functions-create-vnet/create-vm-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amely alatt az erőforrások létrejönnek. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Válassza `myResourceGroup`a lehetőséget, vagy a függvényalkalmazással létrehozott erőforráscsoportot. Ugyanazt az erőforráscsoportot használja a függvényalkalmazáshoz, a WordPress virtuális géphez és az üzemeltetési tervhez, így könnyebben megtisztíthatja az erőforrásokat, ha végzett ezzel az oktatóanyaggal. |
    | **Virtuális gép neve** | VNET-Wordpress | A virtuális gép nevének egyedinek kell lennie az erőforráscsoportban |
    | **[Régió](https://azure.microsoft.com/regions/)** | (Európa) Nyugat-Európa | Válasszon ki egy régiót az Ön közelében vagy a virtuális géphez hozzáférő funkciók közelében. |
    | **Méret** | B1-ek | Válassza **a Méret módosítása lehetőséget,** majd válassza ki a B1s szabványos lemezképet, amely 1 vCPU-val és 1 GB memóriával rendelkezik. |
    | **Hitelesítés típusa** | Jelszó | A jelszó-hitelesítés használatához meg kell adnia egy **felhasználónevet**, egy biztonságos **jelszót,** majd **a Jelszó megerősítése lehetőséget**is. Ebben az oktatóanyagban nem kell bejelentkeznie a virtuális gépbe, kivéve, ha hibaelhárítási. |

1. Válassza a **Hálózat** lapot, és a Virtuális hálózatok konfigurálása csoportban válassza **az Új létrehozása lehetőséget.**

1. A **Virtuális hálózat létrehozása csoportban**használja a kép alatti táblázat beállításait:

    ![Virtuális gép létrehozása hálózattal kapcsolatban](./media/functions-create-vnet/create-vm-2.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | myResourceGroup-vnet | A virtuális hálózathoz létrehozott alapértelmezett nevet használhatja. |
    | **Címtartomány** | 10.10.0.0/16 | Használjon egyetlen címtartományt a virtuális hálózathoz. |
    | **Alhálózat neve** | Oktatóanyag-Net | Az alhálózat neve. |
    | **Címtartomány** (alhálózat) | 10.10.1.0/24   | Az alhálózat mérete határozza meg, hogy hány összeköttetés adható hozzá az alhálózathoz. Ezt az alhálózatot a WordPress webhely használja.  Az `/24` alhálózat 254 állomáscímet biztosít. |

1. A virtuális hálózat létrehozásához válassza az **OK gombot.**

1. A **Hálózat** lapon válassza a **Nincs** **nyilvános IP-címhez**lehetőséget.

1. Válassza a **Kezelés** lapot, majd a **Diagnosztikai tárfiók**ban válassza ki a függvényalkalmazással létrehozott Tárfiókot.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás gombot.** A virtuális gép létrehozása folyamat néhány percet vesz igénybe. A létrehozott virtuális gép csak a virtuális hálózat eléréséhez.

1. A virtuális gép létrehozása után válassza az **Ugrás az erőforráshoz** lehetőséget az új virtuális gép lapjának megtekintéséhez, majd válassza a **Hálózat lehetőséget** a **Beállítások csoportban.**

1. Ellenőrizze, hogy **nincs-e nyilvános IP.** Jegyezze fel a **privát IP-címet,** amellyel a funkcióalkalmazásból csatlakozhat a virtuális géphez.

    ![Hálózati beállítások a virtuális gépben](./media/functions-create-vnet/vm-networking.png)

Most már van egy WordPress webhely telepített teljes egészében a virtuális hálózat. Ez az oldal nem érhető el a nyilvános interneten.

## <a name="connect-your-function-app-to-the-virtual-network"></a>A függvényalkalmazás csatlakoztatása a virtuális hálózathoz

A virtuális hálózatban futó WordPress-webhelysegítségével most már csatlakoztathatja a függvényalkalmazást a virtuális hálózathoz.

1. Az új függvényalkalmazásban válassza **a Platform funkciói nak** > **a Hálózatlehetőséget.**

    ![Hálózat kiválasztása a függvényalkalmazásban](./media/functions-create-vnet/networking-0.png)

1. A **Virtuálishálózat-integráció**csoportban válassza **a Kattintson ide lehetőséget a beállításhoz.**

    ![Hálózati szolgáltatás konfigurálásának állapota](./media/functions-create-vnet/Networking-1.png)

1. A virtuális hálózati integráció lapon válassza **a Virtuális hálózat hozzáadása (előzetes verzió)** lehetőséget.

    ![A virtuális hálózat integrációs előnézetének hozzáadása](./media/functions-create-vnet/networking-2.png)

1. A **Hálózati szolgáltatás állapota**párbeszédpanelen használja a kép alatti táblázat beállításait:

    ![A függvényalkalmazás virtuális hálózatának meghatározása](./media/functions-create-vnet/networking-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Virtuális hálózat** | MyResourceGroup-vnet | Ez a virtuális hálózat az, amelyet korábban létrehozott. |
    | **Alhálózat** | Új alhálózat létrehozása | Hozzon létre egy alhálózatot a virtuális hálózatban a függvényalkalmazás számára. A virtuális hálózat integrációját úgy kell konfigurálni, hogy üres alhálózatot használjon. Nem számít, hogy a függvények használata egy másik alhálózat, mint a virtuális gép. A virtuális hálózat automatikusan irányítja a két alhálózat közötti forgalmat. |
    | **Alhálózat neve** | Függvény-Net | Az új alhálózat neve. |
    | **Virtuális hálózati címblokk** | 10.10.0.0/16 | Válassza ki ugyanazt a címblokkot, amelyet a WordPress webhely használ. Csak egy címblokkot kell definiálni. |
    | **Címtartomány** | 10.10.2.0/24   | Az alhálózat mérete korlátozza a teljes példányok száma, hogy a Prémium csomag függvény alkalmazás horizontális felskálázása. Ez a `/24` példa 254 elérhető állomáscímmel rendelkező alhálózatot használ. Ez az alhálózat túlvan kiépítve, de könnyen kiszámítható. |

1. Az alhálózat hozzáadásához válassza az **OK gombot.** Zárja be a Virtuálishálózat-integráció és a Hálózati szolgáltatás állapota lapot a függvényalkalmazás lapjára való visszatéréshez.

A funkció alkalmazás most már elérheti a virtuális hálózatot, ahol a WordPress webhely fut. Ezután az [Azure Functions Proxyk](functions-proxies.md) segítségével visszaküldhet egy fájlt a WordPress webhelyről.

## <a name="create-a-proxy-to-access-vm-resources"></a>Proxy létrehozása a virtuális gép erőforrásainak eléréséhez

Ha a Virtuálishálózat-integráció engedélyezve van, létrehozhat egy proxyt a függvényalkalmazásban, amely továbbítja a kérelmeket a virtuális hálózatban futó virtuális gépnek.

1. A függvényalkalmazásban válassza a **Proxyk** > **+** lehetőséget, majd használja a proxybeállításokat a kép alatti táblázatban:

    ![A proxybeállítások megadása](./media/functions-create-vnet/create-proxy.png)

    | Beállítás  | Ajánlott érték  | Leírás      |
    | -------- | ---------------- | ---------------- |
    | **Név** | Üzem | A név bármilyen érték lehet. Arra használják, hogy azonosítsák a proxyt. |
    | **Útvonalsablon** | /növény | Útvonal, amely leképezi a virtuális gép erőforrás. |
    | **Háttér-URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Cserélje `<YOUR_VM_IP>` le a korábban létrehozott WordPress virtuális gép IP-címét. Ez a leképezés egyetlen fájlt ad vissza a helyről. |

1. A **Létrehozás gombra** választva adja hozzá a proxyt a függvényalkalmazáshoz.

## <a name="try-it-out"></a>Próba

1. A böngészőben próbáljon meg hozzáférni a **háttér-URL-címként használt URL-címhez.** Ahogy az várható volt, a kérelem idővel elévül. Az időtúloldal azért következik be, mert a WordPress webhely csak a virtuális hálózathoz csatlakozik, az internethez nem.

1. Másolja a **proxy URL-címét** az új proxyból, és illessze be a böngésző címsorába. A visszaadott kép a WordPress webhelyről származik, amely a virtuális hálózaton belül fut.

    ![Plant képfájl tért vissza a WordPress oldalon](./media/functions-create-vnet/plant.png)

A függvényalkalmazás mind az internethez, mind a virtuális hálózathoz csatlakozik. A proxy a nyilvános interneten keresztül kap egy kérést, majd egyszerű HTTP-proxyként működik, hogy továbbítsa a kérelmet a csatlakoztatott virtuális hálózatra. A proxy ezután továbbítja a választ vissza az Interneten keresztül nyilvánosan.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a WordPress webhely api-ként szolgál, amelyet proxy használatával hívnak meg a függvényalkalmazásban. Ez a forgatókönyv teszi a jó bemutató, mert könnyen beállítható és vizualizálása. A virtuális hálózaton belül telepített bármely más API-t használhatja. Létrehozhatott egy olyan függvényt is, amely olyan kódot hív meg, amely a virtuális hálózaton belül üzembe helyezett API-kat hívja meg. A reálisabb forgatókönyv egy olyan függvény, amely adatügyfél API-kat használ a virtuális hálózatban telepített SQL Server-példány hívásához.

A Prémium csomagban futó funkciók ugyanazt az alapjául szolgáló App Service-infrastruktúrát osztják meg, mint a PremiumV2-csomagokon futó webalkalmazások. Az Azure [App Service-ben a webalkalmazások](../app-service/overview.md) dokumentációja a Prémium csomag függvényeire vonatkozik.

> [!div class="nextstepaction"]
> [További információ a Funkciók hálózati lehetőségeiről](./functions-networking-options.md)

[Prémium szintű csomag]: functions-scale.md#premium-plan
