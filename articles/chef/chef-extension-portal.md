---
title: A Chef-ügyfél telepítése az Azure Portalról
description: Ismerje meg, hogyan telepítheti és konfigurálhatja chef-kliensét az Azure Portalon
keywords: azúr, szakács, devops, kliens, telepítés, portál
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586359"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>A Chef-ügyfél telepítése az Azure Portalról
A Chef-ügyfélbővítményt közvetlenül hozzáadhatja egy Linux- vagy Windows-géphez az Azure Portalról. Ez a cikk végigvezeti a folyamaton egy új Linux virtuális gép használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Chef:** Ha nem rendelkezik aktív Chef-fiókkal, regisztráljon a [Hosted Chef ingyenes próbaidőszakára.](https://manage.chef.io/signup) A cikkben található utasítások követéséhez a következő értékekre van szüksége a Chef-fiókjából:
  - organization_validation kulcs
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>A Chef bővítmény telepítése egy új Linux virtuális gépre
Ebben a szakaszban először az Azure Portal on egy Linux-gép létrehozásához. A folyamat során azt is láthatja, hogyan telepítheti a Chef bővítményt az új virtuális gépen.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. A bal oldali menüben válassza a **Virtuális gépek** lehetőséget. Ha a **Virtuális gépek** beállítás nincs jelen, válassza a **Minden szolgáltatás** lehetőséget, majd válassza a Virtuális **gépek**lehetőséget.

1. A **Virtuális gépek** lapon válassza a **Hozzáadás gombot.**

    ![Új virtuális gép hozzáadása az Azure Portalon](./media/chef-extension-portal/add-vm.png)

1. A **Számítás** lapon válassza ki a kívánt operációs rendszert. Ehhez a demóhoz az **Ubuntu Server** van kiválasztva.

1. Az **Ubuntu Server** lapon válassza az **Ubuntu Server 16.04 LTS lehetőséget.**

    ![Ubuntu virtuális gép létrehozásakor adja meg a szükséges verziót](./media/chef-extension-portal/ubuntu-server-version.png)

1. Az **Ubuntu Server 16.04 LTS** lapon válassza a **Létrehozás gombot.**

    ![Az Ubuntu további információkat nyújt a termékükről](./media/chef-extension-portal/create-vm.png)

1. A **Virtuális gép létrehozása** lapon válassza **az Alapok lehetőséget.**

1. Az **Alapok** lapon adja meg a következő értékeket, majd kattintson az **OK gombra.**

   - **Név** – Adja meg az új virtuális gép nevét.
   - **VM-lemez típusa** – Adja meg **az SSD-t** vagy a **HDD-t** a tárolólemez típusához. Az Azure-beli virtuálisgép-lemeztípusokról a [Lemeztípus kiválasztása](../virtual-machines/windows/disks-types.md)című témakörben olvashat bővebben.
   - **Felhasználónév** – Adjon meg egy olyan felhasználónevet, amely rendszergazdai jogosultságokkal rendelkezik a virtuális gépen.
   - **Hitelesítés típusa** - Válassza a **Jelszó**lehetőséget. Kiválaszthatja az **SSH nyilvános kulcsot**is, és megadhat egy SSH nyilvános kulcs értéket. Ennek a demónak a céljaira (és a képernyőképeken) a **Jelszó** van kiválasztva.
   - **Jelszó** és **jelszó megerősítése** – Adja meg a felhasználó jelszavát.
   - **Bejelentkezés az Azure Active Directoryval** – Válassza a **Letiltva**lehetőséget.
   - **Előfizetés** – Válassza ki a kívánt Azure-előfizetést, ha egynél több azure-előfizetéssel rendelkezik.
   - **Erőforráscsoport** – adja meg az erőforráscsoport nevét.
   - **Hely** - Válassza ki **az USA keleti részét.**

     ![A virtuális gépek létrehozásának alapjai lapja](./media/chef-extension-portal/add-vm-basics.png)

1. A **Méret kiválasztása** lapon válassza ki a virtuális gép méretét, majd válassza a **Kijelölés lehetőséget.**

1. A **Beállítások** lapon a legtöbb érték az előző lapokon kiválasztott értékek alapján lesz feltöltve. Válassza **a Bővítmények lehetőséget**.

     ![A bővítmények a Beállítások lapon keresztül kerülnek a virtuális gépekbe](./media/chef-extension-portal/add-vm-select-extensions.png)

1. A **Bővítmények** lapon válassza a **Bővítmény hozzáadása**lehetőséget.

     ![Bővítmény hozzáadása a virtuális géphez való hozzáadásához válassza a Bővítmény hozzáadása lehetőséget.](./media/chef-extension-portal/add-vm-add-extension.png)

1. Az **Új erőforrás** lapon válassza a **Linux Chef Extension (1.2.3)** lehetőséget.

     ![Chef kiterjesztések Linux és Windows virtuális gépek](./media/chef-extension-portal/select-linux-chef-extension.png)

1. A **Linux Chef-bővítmény** lapon válassza a **Létrehozás gombot.**

1. A **Kiterjesztés telepítése** lapon adja meg a következő értékeket, majd kattintson az **OK gombra.**

    - **Chef-kiszolgáló URL-címe** – Adja meg a Chef-kiszolgáló URL-címét, amely tartalmazza a szervezet nevét, *https://api.chef.io/organization/mycompany*például .
    - **Chef-csomópont neve** – adja meg a chef-csomópont nevét.
    - **Futtatási lista** – Adja meg a számítógéphez hozzáadott chef-futtatási listát. Ez az érték üresen hagyható.
    - **Érvényesítési ügyfél neve** – Adja meg a Chef-érvényesítési ügyfél nevét. például `tarcher-validator`.
    - **Érvényesítési kulcs** – Válasszon ki egy fájlt, amely a gépek indításakor használt érvényesítési kulcsot tartalmazza.
    - **Ügyfélkonfigurációs fájl** – A séf-ügyfél konfigurációs fájljának kiválasztása. Ez az érték üresen hagyható.
    - **Chef-ügyfél verziója** – Adja meg a telepítendő chef-ügyfél verzióját. Ez az érték üresen hagyható, amely a legújabb verziót telepíti.
    - **SSL-ellenőrzési mód** – Válassza a **Nincs** vagy **a Társ**lehetőséget. *Egyik sem* lett kiválasztva a demóhoz.
    - **Chef-környezet** – Adja meg a Chef-környezetet, amelynek a csomópontnak tagja kell lennie. Ez az érték üresen hagyható.
    - **Titkosított adatcsomag titkos** - Válasszon ki egy fájlt, amely tartalmazza a titkos kulcsot a titkosított adatcsomag a gép nek szüksége van a hozzáférést. Ez az érték üresen hagyható.
    - **Chef Server SSL tanúsítvány** – Válassza ki a Chef-kiszolgálóhoz rendelt SSL-tanúsítványt. Ez az érték üresen hagyható.

      ![A Chef Server telepítése Linux rendszerű virtuális gépen](./media/chef-extension-portal/install-extension.png)

1. Amikor megjelenik a **Bővítmények** lap, kattintson **az OK gombra.**

1. Amikor megjelenik a **Beállítások** lap, kattintson **az OK gombra.**

1. Amikor megjelenik a **Létrehozás** lap, megjelenik a kijelölt és beírt beállítások összegzése. Ellenőrizze az adatokat, valamint a **használati feltételeket,** és válassza a **Létrehozás lehetőséget.**

Amikor a folyamat létrehozása és üzembe helyezése a kiszolgáló bővítmény befejeződött, egy értesítés jelzi a művelet sikeres vagy sikertelen. Emellett az új virtuális gép erőforráslapja automatikusan megnyílik az Azure Portalon, miután létrejött.

![A Chef Server telepítése Linux rendszerű virtuális gépen](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Windows os virtuális gép létrehozása az Azure-ban a Chef használatával](chef-automation.md)