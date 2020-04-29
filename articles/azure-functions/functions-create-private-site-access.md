---
title: Privát hely elérésének engedélyezése Azure Functions
description: Megtudhatja, hogyan állíthatja be az Azure-beli virtuális hálózat saját webhelyhez való hozzáférését Azure Functionshoz.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851284"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Oktatóanyag: Azure Functions Private site-hozzáférés létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a [Private site-hozzáférést](./functions-networking-options.md#private-site-access) a Azure functions. A Private site Access használatával megkövetelheti, hogy a függvény kódja csak egy adott virtuális hálózatról induljon el.

A Private site Access olyan helyzetekben hasznos, amikor a Function alkalmazáshoz való hozzáférést egy adott virtuális hálózatra kell korlátozni. A Function alkalmazás például csak egy adott szervezet alkalmazottaira, illetve a megadott virtuális hálózaton belüli szolgáltatásokra (például egy másik Azure-függvényre, Azure-beli virtuális gépre vagy egy AK-fürtre) alkalmazható.

Ha egy functions alkalmazásnak hozzá kell férnie az Azure-erőforrásokhoz a virtuális hálózaton belül, vagy [szolgáltatási végpontokon](../virtual-network/virtual-network-service-endpoints-overview.md)keresztül kell csatlakoznia, akkor a [virtuális hálózat integrációja](./functions-create-vnet.md) szükséges.

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a saját webhelyhez való hozzáférést a Function alkalmazáshoz:

> [!div class="checklist"]
> * Virtuális gép létrehozása
> * Azure-beli megerősített szolgáltatás létrehozása
> * Azure Functions-alkalmazás létrehozása
> * Virtuális hálózati szolgáltatás végpontjának konfigurálása
> * Azure-függvény létrehozása és üzembe helyezése
> * A függvény meghívása a virtuális hálózaton kívülről és azokon belül

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="topology"></a>Topológia

Az alábbi ábrán a létrehozandó megoldás architektúrája látható:

![Magas szintű architektúra diagram a Private site Access megoldáshoz](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos megérteni az IP-címzést és alhálózatokat. Ebből a cikkből megtudhatja, hogyan kezelheti [a címzési és alhálók alapjait](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Számos további cikk és videó elérhető online állapotban.

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az oktatóanyag első lépése egy új virtuális gép létrehozása egy virtuális hálózaton belül.  A rendszer a virtuális gép használatával fér hozzá a függvényhez, miután korlátozta, hogy hozzáférése csak a virtuális hálózaton belülről legyen elérhető.

1. Válassza az **erőforrás létrehozása** gombot.

2. A Keresés mezőbe írja be `Windows Server`a kifejezést, majd a keresési eredmények között válassza a **Windows Server** elemet.

3. Válassza a **Windows server 2019 Datacenter** lehetőséget a Windows Server beállításai közül, majd kattintson a **Létrehozás** gombra.

4. Az **alapvető** beállítások lapon használja a virtuális gép beállításait az alábbi táblázatban megadott módon:

    >[!div class="mx-imgBorder"]
    >![Az új Windowsos virtuális gép alapjai lap](./media/functions-create-private-site-access/create-vm-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. |
    | [**Erőforráscsoport**](../azure-resource-manager/management/overview.md) | myResourceGroup | Válassza ki az oktatóanyaghoz tartozó összes erőforrást tartalmazó erőforráscsoportot.  Ugyanezen erőforráscsoport használatával egyszerűbbé válik az erőforrások törlése, amikor ezzel az Oktatóanyaggal végzett. |
    | **Virtuális gép neve** | myVM | A virtuális gép nevének egyedinek kell lennie az erőforráscsoporthoz |
    | [**Régió**](https://azure.microsoft.com/regions/) | USA USA északi középső régiója | Válasszon egy Önhöz közeli régiót vagy az elérni kívánt funkciók közelében. |
    | **Nyilvános bejövő portok** | None | Válassza a **nincs** lehetőséget, hogy a virtuális géphez ne legyen bejövő kapcsolat az internetről. A virtuális géphez való távoli hozzáférés az Azure Bastion szolgáltatáson keresztül lesz konfigurálva. |

5. Válassza a **hálózatkezelés** fület, és válassza az **új létrehozása** lehetőséget az új virtuális hálózat konfigurálásához.

    >[!div class="mx-imgBorder"]
    >![Új virtuális hálózat létrehozása az új virtuális GÉPHEZ](./media/functions-create-private-site-access/create-vm-networking.png)

6. A **virtuális hálózat létrehozása**területen használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    >[!div class="mx-imgBorder"]
    >![Új virtuális hálózat létrehozása az új virtuális GÉPHEZ](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | myResourceGroup – vnet | A virtuális hálózathoz generált alapértelmezett nevet használhatja. |
    | **Címtartomány** | 10.10.0.0/16 | Használjon egyetlen címtartományt a virtuális hálózathoz. |
    | **Alhálózat neve** | Oktatóanyag | Az alhálózat neve. |
    | **Címtartomány** (alhálózat) | 10.10.1.0/24 | Az alhálózat mérete határozza meg, hogy hány csatolót lehet hozzáadni az alhálózathoz. Ezt az alhálózatot a virtuális gép használja. Az `/24` alhálózat 254 gazdagép-címet biztosít. |

7. A virtuális hálózat létrehozásához kattintson **az OK gombra** .
8. A **hálózatkezelés** lapon ellenőrizze, hogy **nincs** -e kiválasztva a **nyilvános IP-cím**beállítás.
9. Válassza a **felügyelet** fület, majd a **diagnosztikai Storage-fiók**területen válassza az **új létrehozása** lehetőséget egy új Storage-fiók létrehozásához.
10. Hagyja meg az **identitás**, az **automatikus leállítás**és a **biztonsági mentési** csoportok alapértelmezett értékeit.
11. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás**lehetőséget. A virtuális gép létrehozási folyamata néhány percet vesz igénybe.

## <a name="configure-azure-bastion"></a>Az Azure Bastion konfigurálása

Az [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) egy teljes körűen felügyelt Azure-szolgáltatás, amely biztonságos RDP-és SSH-hozzáférést biztosít a virtuális gépekhez közvetlenül a Azure Portal. Az Azure Bastion szolgáltatás használatával nem kell konfigurálnia az RDP-hozzáféréshez kapcsolódó hálózati beállításokat.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
2. A keresőmezőbe írja be a "Bastion" kifejezést.  Válassza a "bástya" lehetőséget.
3. A **Létrehozás** gombra kattintva megkezdheti egy új Azure Bastion-erőforrás létrehozásának folyamatát. A **virtuális hálózat** szakaszban egy hibaüzenet jelenik meg, mivel még nincs `AzureBastionSubnet` alhálózat. Az alhálózat a következő lépésekben jön létre. Használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    >[!div class="mx-imgBorder"]
    >![Az Azure Bastion létrehozásának megkezdése](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | myBastion | Az új megerősített erőforrás neve |
    | **Régió** | USA északi középső régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |
    | **Virtuális hálózat** | myResourceGroup – vnet | Az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva |
    | **Alhálózat** | AzureBastionSubnet | Annak a virtuális hálózatnak az alhálózata, amelyhez az új megerősített gazdagép erőforrása telepítve lesz. Létre kell hoznia egy alhálózatot a `AzureBastionSubnet`Name érték használatával. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Legalább `/27` vagy nagyobb (`/27`, `/26`, stb.) alhálózatot kell használnia. |

    > [!NOTE]
    > Az Azure megerősített erőforrás létrehozásával kapcsolatos részletes útmutatóért tekintse meg az [Azure Bastion-gazdagép létrehozása](../bastion/bastion-create-host-portal.md) oktatóanyagot.

4. Hozzon létre egy alhálózatot, amelyben az Azure kiépítheti az Azure-beli megerősített gazdagépet. Az **alhálózati konfiguráció kezelése** lehetőség megnyit egy új ablaktáblát, ahol megadhat egy új alhálózatot.  Válassza a **+ alhálózat** lehetőséget egy új alhálózat létrehozásához.
5. Az alhálózatnak a névnek `AzureBastionSubnet` kell lennie, és az alhálózati előtagnak legalább `/27`a következőnek kell lennie:.  Az alhálózat létrehozásához kattintson **az OK gombra** .

    >[!div class="mx-imgBorder"]
    >![Alhálózat létrehozása az Azure Bastion-gazdagéphez](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. A **bástya létrehozása** lapon válassza ki az újonnan létrehozott `AzureBastionSubnet` elemet az elérhető alhálózatok listájából.

    >[!div class="mx-imgBorder"]
    >![Egy adott alhálózattal rendelkező Azure Bastion-gazdagép létrehozása](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Válassza a **felülvizsgálat & létrehozás**lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás**lehetőséget. Az Azure Bastion-erőforrás létrehozása eltarthat néhány percig.

## <a name="create-an-azure-functions-app"></a>Azure Functions-alkalmazás létrehozása

A következő lépés egy Function-alkalmazás létrehozása az Azure-ban a [felhasználási terv](functions-scale.md#consumption-plan)használatával. Az oktatóanyag későbbi részében üzembe helyezi a függvény kódját erre az erőforrásra.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
2. Válassza ki a **számítási > függvényalkalmazás**
3. Az **alapismeretek** szakaszban használja az alábbi táblázatban megadott Function App-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Erőforráscsoport** | myResourceGroup | Válassza ki az oktatóanyaghoz tartozó összes erőforrást tartalmazó erőforráscsoportot.  Ha a Function alkalmazáshoz ugyanazt az erőforráscsoportot használja, és a virtuális gép megkönnyíti az erőforrások törlését, amikor ezzel az Oktatóanyaggal végzett. |
    | **függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: a – z (kis-és nagybetűk megkülönböztetése), 0-9 és –. |
    | **Közzététel** | Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. |
    | **Régió** | USA északi középső régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    Válassza a **következő: >üzemeltetése** gombot.
4. Az **üzemeltetés** szakaszban válassza ki a megfelelő **Storage-fiókot**, **operációs rendszert**, és **tervezze** meg a következő táblázatban leírt lépéseket.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Tárfiók** | Globálisan egyedi név | Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](./functions-scale.md#storage-account-requirements). |
    | **Operációs rendszer** | Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **Felkészülés** | Használat | A [üzemeltetési terv](./functions-scale.md) azt diktálja, hogy a függvény alkalmazás hogyan méretezhető, és az erőforrások elérhetők legyenek az egyes példányok számára. |
5. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.
6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

## <a name="configure-access-restrictions"></a>Hozzáférési korlátozások konfigurálása

A következő lépés a [hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md) konfigurálása annak biztosítására, hogy csak a virtuális hálózaton található erőforrások hívhatják meg a függvényt.

A [privát hely](functions-networking-options.md#private-site-access) elérését az Azure Virtual Network [szolgáltatás végpontjának](../virtual-network/virtual-network-service-endpoints-overview.md) létrehozásával engedélyezheti a Function alkalmazás és a megadott virtuális hálózat között. A hozzáférési korlátozások a szolgáltatási végpontokon keresztül valósíthatók meg. A szolgáltatási végpontok biztosítják, hogy csak a megadott virtuális hálózaton belülről származó forgalom férhessenek hozzá a kijelölt erőforráshoz. Ebben az esetben a kijelölt erőforrás az Azure-függvény.

1. A Function alkalmazásban folytassa a **platform szolgáltatásai** lapot. a hálózati szolgáltatás állapota szakasz megnyitásához válassza **a hálózatkezelési hivatkozást a** *hálózat szakasz fejléc* alatt.
2. A **hálózati szolgáltatások állapota** lap a kiindulási pont, amely az Azure bejárati ajtó, a Azure CDN és a hozzáférés korlátozásait konfigurálja. Válassza a **hozzáférési korlátozások konfigurálása** lehetőséget a privát hely elérésének konfigurálásához.
3. A **hozzáférési korlátozások** lapon csak az alapértelmezett korlátozás jelenik meg. Az alapértelmezett érték nem korlátozza a Function alkalmazáshoz való hozzáférést.  Válassza a **szabály hozzáadása** lehetőséget a privát hely hozzáférés-korlátozási konfigurációjának létrehozásához.
4. A **hozzáférés-korlátozás hozzáadása** panelen válassza ki a **Virtual Network** elemet a **típus** legördülő listából, majd válassza ki a korábban létrehozott virtuális hálózatot és alhálózatot.
5. A **hozzáférési korlátozások** lap most már azt mutatja, hogy van új korlátozás. Eltarthat néhány másodpercig, amíg a **végpont állapota** a-tól `Disabled` `Provisioning` a- `Enabled`ig változik.

    >[!IMPORTANT]
    > Minden Function alkalmazáshoz egy [speciális eszköz-(kudu-) hely](../app-service/app-service-ip-restrictions.md#scm-site) tartozik, amely a Function app üzemelő példányának kezelésére szolgál. A webhely a következőhöz hasonló URL-címről érhető `<FUNCTION_APP_NAME>.scm.azurewebsites.net`el:. Mivel ezen a telepítési helyen nem engedélyezték a hozzáférési korlátozásokat, továbbra is telepítheti a projekt kódját egy helyi fejlesztői munkaállomásról vagy Build szolgáltatásból anélkül, hogy ügynököt kellene létesítenie a virtuális hálózaton.

## <a name="access-the-functions-app"></a>A functions alkalmazás elérése

1. Térjen vissza az előzőleg létrehozott Function alkalmazáshoz.  Az **Áttekintés** szakaszban másolja az URL-címet.

    >[!div class="mx-imgBorder"]
    >![A függvény alkalmazás URL-címének beolvasása](./media/functions-create-private-site-access/access-function-overview.png)

2. Ha most a virtuális hálózaton kívülről próbál hozzáférni a Function alkalmazáshoz, a rendszer egy HTTP 403-lapot fog kapni, amely jelzi, hogy az alkalmazás le van állítva.  Az alkalmazás nincs leállítva. A válasz valójában egy HTTP 403 IP tiltott állapot.
3. Most elérheti a függvényt a korábban létrehozott virtuális gépről, amely csatlakozik a virtuális hálózathoz. Ahhoz, hogy a virtuális gépről hozzáférhessen a webhelyhez, az Azure megerősített szolgáltatáson keresztül kell csatlakoznia a virtuális géphez.  Először válassza a **kapcsolat** lehetőséget, majd válassza a **Bastion**lehetőséget.
4. Adja meg a virtuális géphez való bejelentkezéshez szükséges felhasználónevet és jelszót.  Kattintson a **Csatlakozás** gombra. Egy új böngészőablak jelenik meg, amely lehetővé teszi a virtuális gép interakcióját.
5. Mivel ez a virtuális gép a virtuális hálózaton keresztül éri el a függvényt, lehetséges, hogy a virtuális GÉPEN lévő webböngészőből fér hozzá a webhelyhez.  Fontos megjegyezni, hogy amíg a Function alkalmazás csak a kijelölt virtuális hálózatról érhető el, a nyilvános DNS-bejegyzés marad. Ahogy a fentiekben is látható, a webhely elérésére tett kísérlet egy HTTP 403-választ eredményez.

## <a name="create-a-function"></a>Függvény létrehozása

Az oktatóanyag következő lépése egy HTTP-triggerű Azure-függvény létrehozása. Ha a függvényt HTTP GET vagy POST metódussal hívja meg, a "Hello, {Name}" választ kell eredményeznie.  

1. A Azure Functions-alkalmazás létrehozásához és üzembe helyezéséhez kövesse az alábbi rövid útmutatók egyikét.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Parancssor](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Azure Functions projekt közzétételekor válassza ki az oktatóanyag során korábban létrehozott Function app-erőforrást.
3. Ellenőrizze, hogy a függvény telepítve van-e.

    >[!div class="mx-imgBorder"]
    >![Üzembe helyezett függvény a függvények listájában](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>A függvény közvetlen meghívása

1. A függvényhez való hozzáférés teszteléséhez át kell másolnia a függvény URL-címét. Válassza ki az üzembe helyezett függvényt, majd válassza a **</> a függvény URL-címének beolvasása**elemet. Ezután a **Másolás** gombra kattintva másolja az URL-címet a vágólapra.

    >[!div class="mx-imgBorder"]
    >![A függvény URL-címének másolása](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > A függvény futtatásakor egy futásidejű hiba jelenik meg a portálon, amely azt jelzi, hogy a függvény futtatókörnyezete nem indul el. Az üzenet szövege ellenére a Function alkalmazás ténylegesen fut. A hiba az új hozzáférési korlátozások eredménye, ami megakadályozza, hogy a portál lekérdezze a futtatókörnyezetet.

2. Illessze be az URL-címet egy webböngészőbe. Ha most megpróbál hozzáférni a Function alkalmazáshoz egy, a virtuális hálózaton kívüli számítógépről, megjelenik egy HTTP 403-válasz, amely jelzi az alkalmazás leállítását.

## <a name="invoke-the-function-from-the-virtual-network"></a>A függvény meghívása a virtuális hálózatról

A függvény elérése egy webböngészőn keresztül (az Azure Bastion szolgáltatás használatával) a konfigurált virtuális GÉPEN, a virtuális hálózatban sikerrel jár.

>[!div class="mx-imgBorder"]
>![Az Azure-függvény elérése az Azure Bastion használatával](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések


> [!div class="nextstepaction"]
> [További tudnivalók a függvények hálózati beállításairól](./functions-networking-options.md)
