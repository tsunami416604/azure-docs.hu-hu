---
title: Privát webhely-hozzáférés engedélyezése az Azure Functionshez
description: Ismerje meg az Azure virtuális hálózati magánwebhely-hozzáférésének beállítását az Azure Functions számára.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851284"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Oktatóanyag: Az Azure Functions privát webhely-hozzáférésének létrehozása

Ez az oktatóanyag bemutatja, hogyan engedélyezheti a [privát webhely-hozzáférést](./functions-networking-options.md#private-site-access) az Azure Functions használatával. A privát webhely-hozzáférés használatával megkövetelheti, hogy a függvénykód csak egy adott virtuális hálózatról aktiválódik.

A privát webhely-hozzáférés akkor hasznos, ha a függvényalkalmazáshoz való hozzáférést egy adott virtuális hálózatra kell korlátozni. Például a függvényalkalmazás csak egy adott szervezet alkalmazottaira vagy a megadott virtuális hálózaton belüli szolgáltatásokra (például egy másik Azure-függvényre, az Azure virtuális gépre vagy egy AKS-fürtre) alkalmazható.

Ha egy Functions alkalmazásnak hozzá kell férnie az Azure-erőforrásokhoz a virtuális hálózaton belül, vagy [szolgáltatásvégpontokon](../virtual-network/virtual-network-service-endpoints-overview.md)keresztül kell csatlakoznia, akkor [virtuális hálózati integrációra](./functions-create-vnet.md) van szükség.

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja a privát webhely-hozzáférést a függvényalkalmazáshoz:

> [!div class="checklist"]
> * Virtuális gép létrehozása
> * Azure-bástyaszolgáltatás létrehozása
> * Azure Functions-alkalmazás létrehozása
> * Virtuális hálózati szolgáltatás végpontjának konfigurálása
> * Azure-függvény létrehozása és üzembe helyezése
> * A függvény meghívása kívülről és a virtuális hálózaton belül

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="topology"></a>Topológia

Az alábbi ábra a létrehozandó megoldás architektúráját mutatja be:

![Magas szintű architektúradiagram a privát webhely-hozzáférési megoldáshoz](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos, hogy tisztában legyen az IP-címzésésés és -alhálózatolás. Kezdheti ezzel a [cikkel, amely a címzés és a részkapcsolat alapjait ismerteti.](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics) Sokkal több cikk és videó érhető el az interneten.

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az oktatóanyag első lépése egy új virtuális gép létrehozása a virtuális hálózaton belül.  A virtuális gép lesz használva a funkció eléréséhez, ha már korlátozta, hogy a hozzáférést csak a virtuális hálózaton belül.

1. Válassza az **Erőforrás létrehozása gombot.**

2. A keresőmezőbe írja `Windows Server`be a kívánt parancsot, és a keresési eredmények között válassza a **Windows Server** elemet.

3. Válassza a **Windows Server 2019 Adatközpont** elemet a Windows Server beállításai között, és nyomja meg a **Létrehozás** gombot.

4. Az **Alapok lapon** használja a virtuális gép beállításait a kép alatti táblázatban megadottak szerint:

    >[!div class="mx-imgBorder"]
    >![Az új Windows virtuális gépek alapjai lapja](./media/functions-create-private-site-access/create-vm-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amely alatt az erőforrások létrejönnek. |
    | [**Erőforráscsoport**](../azure-resource-manager/management/overview.md) | myResourceGroup | Válassza ki az erőforráscsoportot, amely tartalmazza az oktatóanyag összes erőforrását.  Ugyanannak az erőforráscsoportnak a használatával könnyebben megtisztíthatja az erőforrásokat, ha végzett ezzel az oktatóanyaggal. |
    | **Virtuális gép neve** | myVM | A virtuális gép nevének egyedinek kell lennie az erőforráscsoportban |
    | [**Régió**](https://azure.microsoft.com/regions/) | (US) USA északi középső régiója | Válasszon ki egy régiót az Ön közelében vagy az elérhető funkciók közelében. |
    | **Nyilvános bejövő portok** | None | Válassza **a Nincs** lehetőséget, ha azt szeretné, hogy nincs bejövő kapcsolat a virtuális géphez az internetről. A virtuális gép távoli elérése az Azure Bastion szolgáltatáson keresztül lesz konfigurálva. |

5. Válassza a **Hálózat** lapot, és válassza **az Új létrehozása lehetőséget** új virtuális hálózat konfigurálásához.

    >[!div class="mx-imgBorder"]
    >![Új virtuális hálózat létrehozása az új virtuális géphez](./media/functions-create-private-site-access/create-vm-networking.png)

6. A **Virtuális hálózat létrehozása csoportban**használja a kép alatti táblázat beállításait:

    >[!div class="mx-imgBorder"]
    >![Új virtuális hálózat létrehozása az új virtuális géphez](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | myResourceGroup-vnet | A virtuális hálózathoz létrehozott alapértelmezett nevet használhatja. |
    | **Címtartomány** | 10.10.0.0/16 | Használjon egyetlen címtartományt a virtuális hálózathoz. |
    | **Alhálózat neve** | Oktatóanyag | Az alhálózat neve. |
    | **Címtartomány** (alhálózat) | 10.10.1.0/24 | Az alhálózat mérete határozza meg, hogy hány összeköttetés adható hozzá az alhálózathoz. Ezt az alhálózatot a virtuális gép használja. Az `/24` alhálózat 254 állomáscímet biztosít. |

7. A virtuális hálózat létrehozásához válassza az **OK gombot.**
8. A **Hálózat** lapon **ellenőrizze,** hogy nincs-e kiválasztva a Nyilvános **IP .**
9. Válassza a **Kezelés** lapot, majd a **Diagnosztikai tárfiókban**válassza **az Új létrehozása új** tárfiók létrehozásához lehetőséget.
10. Hagyja meg az **Identitás**, **Az Automatikus leállítás**és a **Biztonsági másolat** szakasz alapértelmezett értékeit.
11. Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás gombot.** A virtuális gép létrehozása folyamat néhány percet vesz igénybe.

## <a name="configure-azure-bastion"></a>Az Azure-bastion konfigurálása

[Az Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) egy teljes körűen felügyelt Azure-szolgáltatás, amely biztonságos RDP- és SSH-hozzáférést biztosít a virtuális gépekhez közvetlenül az Azure Portalról. Az Azure Bastion szolgáltatás használatával az RDP-hozzáféréshez kapcsolódó hálózati beállítások konfigurálása szükségtelen.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
2. A keresőmezőbe írja be a "Bástya" kifejezést.  Válassza a "Bástya" lehetőséget.
3. Válassza **a Létrehozás** lehetőséget az új Azure-bástya-erőforrás létrehozásának megkezdéséhez. Hibaüzenet jelenik meg a **Virtuális hálózat** szakaszban, `AzureBastionSubnet` mivel még nincs alhálózat. Az alhálózat a következő lépésekben jön létre. Használja a beállításokat a táblázatban a kép alatt:

    >[!div class="mx-imgBorder"]
    >![Az Azure Bastion létrehozásának kezdete](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Név** | mibaszció | Az új megerősített erőforrás neve |
    | **Régió** | USA északi középső régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |
    | **Virtuális hálózat** | myResourceGroup-vnet | Az a virtuális hálózat, amelyben a megerősített erőforrás |
    | **Alhálózat** | AzureBastionSubnet | Az az alhálózat a virtuális hálózatban, amelyre az új megerősített gazdaállomás-erőforrás lesz telepítve. Alhálózatot kell létrehoznia a `AzureBastionSubnet`névérték használatával. Ez az érték lehetővé teszi az Azure számára, hogy melyik alhálózatra telepítse a Bástya-erőforrásokat. Legalább legalább `/27` nagyobb (`/27`, `/26`és így tovább) alhálózatot kell használnia. |

    > [!NOTE]
    > Az Azure Bastion-erőforrás létrehozásához részletes, lépésenkénti útmutatót az [Azure-bastion gazdagép](../bastion/bastion-create-host-portal.md) oktatóanyag ának létrehozása című részletes útmutatóban talál.

4. Hozzon létre egy alhálózatot, amelyben az Azure kiépítheti az Azure Bastion gazdat. Az **Alhálózat-konfiguráció kezelése beállítás** ával megnyit egy új ablaktáblát, ahol új alhálózatot definiálhat.  Új alhálózat létrehozásához válassza **a + Alhálózat** lehetőséget.
5. Az alhálózatnak névvel `AzureBastionSubnet` kell rendelkeznie, és az `/27`alhálózati előtagnak legalább .  Az alhálózat létrehozásához válassza az **OK gombot.**

    >[!div class="mx-imgBorder"]
    >![Alhálózat létrehozása az Azure Bastion gazdagéphez](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. A **Bástya létrehozása** lapon válassza `AzureBastionSubnet` ki az újonnan létrehozott alhálózatok listájából.

    >[!div class="mx-imgBorder"]
    >![Azure-bástya-állomás létrehozása adott alhálózattal](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Válassza **a Véleményezés & létrehozás lehetőséget.** Az ellenőrzés befejezése után válassza a **Létrehozás gombot.** Az Azure Bastion erőforrás létrehozása néhány percet vesz igénybe.

## <a name="create-an-azure-functions-app"></a>Azure Functions-alkalmazás létrehozása

A következő lépés egy függvényalkalmazás létrehozása az Azure-ban a [felhasználási csomag](functions-scale.md#consumption-plan)használatával. A függvénykódot az oktatóanyag későbbi részében telepíti erre az erőforrásra.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
2. **Válassza a Számítási > függvényalkalmazás kiválasztása**
3. Az **Alapok szakaszban** használja az alábbi táblázatban megadott függvényalkalmazás-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Erőforráscsoport** | myResourceGroup | Válassza ki az erőforráscsoportot, amely tartalmazza az oktatóanyag összes erőforrását.  Ugyanazt az erőforráscsoportot használja a függvényalkalmazáshoz és a virtuális géphez, így könnyebben megtisztíthatja az erőforrásokat, ha végzett ezzel az oktatóanyaggal. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek a-z (kis- és nagybetűk et nem figyelembe vevő), 0-9 és -. |
    | **Közzététele** | Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. |
    | **Régió** | USA északi középső régiója | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    Válassza a **Tovább: >** gombot.
4. A **Tárhely** szakaszban válassza ki a megfelelő **tárfiókot**, **az operációs rendszert**és az alábbi táblázatban ismertetett **tervet.**

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | **Tárfiók** | Globálisan egyedi név | Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [tárfiók követelményeinek.](./functions-scale.md#storage-account-requirements) |
    | **Operációs rendszer** | Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **Felkészülés** | Használat | Az [üzemeltetési terv](./functions-scale.md) határozza meg, hogyan méretezhető a függvényalkalmazás, és az egyes példányok számára elérhető erőforrások. |
5. Válassza **a Véleményezés + Létrehozás** lehetőséget az alkalmazáskonfigurációs beállítások áttekintéséhez.
6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

## <a name="configure-access-restrictions"></a>Hozzáférési korlátozások konfigurálása

A következő lépés a [hozzáférési korlátozások konfigurálása](../app-service/app-service-ip-restrictions.md) annak biztosítására, hogy csak a virtuális hálózat erőforrásai hívhatják meg a függvényt.

[A privát webhely-hozzáférés](functions-networking-options.md#private-site-access) engedélyezve van egy Azure Virtual Network [szolgáltatás végpont](../virtual-network/virtual-network-service-endpoints-overview.md) jának létrehozásával a függvényalkalmazás és a megadott virtuális hálózat között. A hozzáférési korlátozások a szolgáltatás végpontjain keresztül valósulnak meg. A szolgáltatásvégpontok biztosítják, hogy csak a megadott virtuális hálózatról származó forgalom férhessen hozzá a kijelölt erőforráshoz. Ebben az esetben a kijelölt erőforrás az Azure-függvény.

1. A függvényalkalmazáson belül lépjen a Platform **Networking** **szolgáltatások** lapra. *Networking*
2. A **hálózati szolgáltatás állapota** lap a kiindulási pont az Azure Bejárati ajtajának, az Azure CDN és a hozzáférési korlátozások konfigurálásához. Válassza **a Hozzáférési korlátozások konfigurálása** lehetőséget a magánhálózati hozzáférés konfigurálásához.
3. A **Hozzáférési korlátozások** lapon csak az alapértelmezett korlátozás jelenik meg. Az alapértelmezett beállítás nem korlátozza a függvényalkalmazáshoz való hozzáférést.  Válassza **a Szabály hozzáadása** lehetőséget a saját webhely-hozzáférés korlátozási konfigurációjának létrehozásához.
4. A **Hozzáférés korlátozásának hozzáadása** ablaktáblában válassza a **Virtuális hálózat** elemet a **Típus** legördülő lista mezőben, majd jelölje ki a korábban létrehozott virtuális hálózatot és alhálózatot.
5. A **Hozzáférési korlátozások** lap most azt mutatja, hogy új korlátozás van érvényben. Eltarthat néhány másodpercig, amíg a Végpont `Disabled` `Provisioning` `Enabled` **állapota** átváltozik a állásról a-ra.

    >[!IMPORTANT]
    > Minden függvényalkalmazás rendelkezik egy [Speciális eszköz (Kudu) hellyel,](../app-service/app-service-ip-restrictions.md#scm-site) amely a függvényalkalmazás-telepítések kezelésére szolgál. Ez az oldal egy URL-címről érhető el, mint például: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Mivel a hozzáférési korlátozások nincsenek engedélyezve ezen a központi telepítési helyen, továbbra is telepítheti a projektkódot egy helyi fejlesztői munkaállomásról vagy buildszolgáltatásból anélkül, hogy ügynöket kellene kiépítenie a virtuális hálózaton belül.

## <a name="access-the-functions-app"></a>A függvények alkalmazás elérése

1. Visszatérés a korábban létrehozott függvényalkalmazáshoz.  Az **Áttekintés szakaszban** másolja az URL-címet.

    >[!div class="mx-imgBorder"]
    >![A Függvényalkalmazás URL-címének beszereznie](./media/functions-create-private-site-access/access-function-overview.png)

2. Ha most próbálja meg elérni a függvényalkalmazást a virtuális hálózaton kívüli számítógépről, egy HTTP 403-as lapot fog kapni, amely jelzi, hogy az alkalmazás leállt.  Az alkalmazás nem áll le. A válasz valójában egy HTTP 403 IP Tiltott állapot.
3. Most a funkció a korábban létrehozott virtuális gép, amely csatlakozik a virtuális hálózathoz. Annak érdekében, hogy a virtuális gépről érje el a webhelyet, csatlakoznia kell a virtuális géphez az Azure Bastion szolgáltatáson keresztül.  Először válassza a **Csatlakozás,** majd **a Bástya**lehetőséget.
4. Adja meg a szükséges felhasználónevet és jelszót a virtuális gépbe való bejelentkezéshez.  Kattintson a **Csatlakozás** gombra. Egy új böngészőablak jelenik meg, amely lehetővé teszi a virtuális gépkel való interakciót.
5. Mivel ez a virtuális gép a virtuális hálózaton keresztül éri el a funkciót, a virtuális gép webböngészőjéből is elérheti a webhelyet.  Fontos megjegyezni, hogy bár a függvényalkalmazás csak a kijelölt virtuális hálózaton belül érhető el, egy nyilvános DNS-bejegyzés marad. Amint az a fentiekben látható, a webhely elérésének megkísérlése HTTP 403-as választ eredményez.

## <a name="create-a-function"></a>Függvény létrehozása

Az oktatóanyag következő lépése egy HTTP-aktivált Azure-függvény létrehozása. A függvény HTTP GET vagy POST protokollon keresztültörténő meghívása a "Hello, {name}" válaszát eredményezi.  

1. Kövesse az alábbi rövid útmutatók egyikét az Azure Functions alkalmazás létrehozásához és üzembe helyezéséhez.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Parancssor](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Az Azure Functions projekt közzétételekor válassza ki az oktatóanyag korábbi, korábban létrehozott függvényalkalmazás-erőforrást.
3. Ellenőrizze, hogy a függvény telepítve van-e.

    >[!div class="mx-imgBorder"]
    >![Telepített függvény a függvénylistában](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>A függvény közvetlen meghívása

1. A funkcióhoz való hozzáférés teszteléséhez másolnia kell a függvény URL-címét. Jelölje ki az üzembe helyezett függvényt, majd válassza **a</> függvény URL-címének bekerülése**lehetőséget. Ezután kattintson a **Másolás** gombra az URL-cím vágólapra másolásához.

    >[!div class="mx-imgBorder"]
    >![A függvény URL-címének másolása](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Amikor a függvény fut, megjelenik egy futásidejű hiba a portálon, amely szerint a függvény futásidejű nem tud elindítani. Annak ellenére, hogy az üzenet szövegét, a függvény alkalmazás valójában fut. A hiba az új hozzáférési korlátozások eredménye, amelyek megakadályozzák, hogy a portál lekérdezze a futásidejű ellenőrzést.

2. Illessze be az URL-címet egy webböngészőbe. Amikor most megpróbálja elérni a függvényalkalmazást a virtuális hálózaton kívüli számítógépről, http 403-as választ kap, amely jelzi, hogy az alkalmazás le van állítva.

## <a name="invoke-the-function-from-the-virtual-network"></a>A függvény meghívása a virtuális hálózatból

A funkció elérése egy webböngészőn keresztül (az Azure Bastion szolgáltatás használatával) a virtuális hálózaton konfigurált virtuális gépen sikeres!

>[!div class="mx-imgBorder"]
>![Az Azure-funkció elérése az Azure Bastion on keresztül](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések


> [!div class="nextstepaction"]
> [További információ a Funkciók hálózati lehetőségeiről](./functions-networking-options.md)
