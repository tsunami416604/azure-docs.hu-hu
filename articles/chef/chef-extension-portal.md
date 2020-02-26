---
title: A Chef-ügyfél telepítése a Azure Portal
description: Ismerje meg, hogyan helyezheti üzembe és konfigurálhatja a Chef-ügyfelet a Azure Portal
keywords: Azure, Chef, devops, ügyfél, telepítés, portál
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586359"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>A Chef-ügyfél telepítése a Azure Portal
A Chef-ügyfél bővítményt közvetlenül egy Linux vagy Windows rendszerű gépre is hozzáadhatja a Azure Portal. Ez a cikk végigvezeti a folyamaton egy új linuxos virtuális gép használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Chef**: Ha nincs aktív Chef-fiókja, regisztráljon az [üzemeltetett Chef ingyenes kipróbálására](https://manage.chef.io/signup). Ahhoz, hogy követni tudja a cikk utasításait, a Chef-fiók alábbi értékeire lesz szüksége:
  - organization_validation kulcs
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>A Chef bővítmény telepítése új Linux rendszerű virtuális gépre
Ebben a szakaszban először a Azure Portal fogja használni a Linux rendszerű gépek létrehozásához. A folyamat során azt is megtudhatja, hogyan telepítheti a Chef bővítményt az új virtuális gépre.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. A bal oldali menüben válassza a **virtuális gépek** lehetőséget. Ha a **virtuális gépek** beállítás nincs megadva, válassza a **minden szolgáltatás** elemet, majd válassza a **virtuális gépek**lehetőséget.

1. A **virtuális gépek** lapon válassza a **Hozzáadás**lehetőséget.

    ![Új virtuális gép hozzáadása a Azure Portal](./media/chef-extension-portal/add-vm.png)

1. A **számítás** lapon válassza ki a kívánt operációs rendszert. Ebben a bemutatóban az **Ubuntu-kiszolgáló** van kiválasztva.

1. Az **Ubuntu Server** lapon válassza az **Ubuntu Server 16,04 LTS**lehetőséget.

    ![Ubuntu rendszerű virtuális gép létrehozásakor meg kell adnia a szükséges verziót](./media/chef-extension-portal/ubuntu-server-version.png)

1. Az **Ubuntu Server 16,04 LTS** lapon válassza a **Létrehozás**lehetőséget.

    ![Az Ubuntu további információkat nyújt a termékről](./media/chef-extension-portal/create-vm.png)

1. A **virtuális gép létrehozása** lapon válassza az **alapismeretek**lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a következő értékeket, majd kattintson az **OK gombra**.

   - **Név** – adja meg az új virtuális gép nevét.
   - Virtuálisgép- **lemez típusa** – adja meg az **SSD** -t vagy a **HDD** -t a tárolási lemez típusához. További információ az Azure-beli virtuálisgép-lemezek típusairól: [lemez típusának kiválasztása](../virtual-machines/windows/disks-types.md).
   - **Felhasználónév** – adjon meg egy felhasználónevet, amely rendszergazdai jogosultságokat kapott a virtuális gépen.
   - **Hitelesítés típusa** – válassza a **jelszó**lehetőséget. Kiválaszthatja az **SSH nyilvános kulcs**lehetőséget is, és megadhat egy nyilvános SSH-kulcs értékét. Ebben a bemutatóban (és a képernyőképeken) a **jelszó** van kiválasztva.
   - **Jelszó és jelszó** **megerősítése** – adja meg a felhasználó jelszavát.
   - **Bejelentkezés Azure Active Directory** – válassza a **Letiltva**lehetőséget.
   - **Előfizetés** – válassza ki a kívánt Azure-előfizetést, ha egynél több van.
   - **Erőforráscsoport** – adja meg az erőforráscsoport nevét.
   - Hely – válassza az **USA keleti** **régióját** .

     ![Alapismeretek lap virtuális gép létrehozásához](./media/chef-extension-portal/add-vm-basics.png)

1. A **méret kiválasztása** lapon válassza ki a virtuális gép méretét, majd válassza a **kiválasztás**lehetőséget.

1. A **Beállítások** lapon az értékek többsége az előző lapokon kiválasztott értékek alapján lesz feltöltve. Kattintson az **Extensions** (Bővítmények) gombra.

     ![A bővítmények a beállítások lapon adhatók hozzá a virtuális gépekhez](./media/chef-extension-portal/add-vm-select-extensions.png)

1. A **bővítmények** lapon válassza a bővítmény **hozzáadása**elemet.

     ![Válassza a bővítmény hozzáadása lehetőséget egy bővítmény virtuális géphez való hozzáadásához](./media/chef-extension-portal/add-vm-add-extension.png)

1. Az **új erőforrás** lapon válassza a **Linux Chef Extension (1.2.3)** lehetőséget.

     ![A Chef a Linux és a Windows rendszerű virtuális gépekhez tartalmaz bővítményeket](./media/chef-extension-portal/select-linux-chef-extension.png)

1. A **Linux Chef bővítmény** lapon válassza a **Létrehozás**lehetőséget.

1. A **bővítmény telepítése** lapon adja meg a következő értékeket, majd kattintson az **OK gombra**.

    - **Chef-kiszolgáló URL-címe** – adja meg a Chef-kiszolgáló URL-címét, amely tartalmazza a szervezet nevét (például *https://api.chef.io/organization/mycompany* ).
    - **Chef-csomópont neve** – adja meg a Chef-csomópont nevét.
    - **Futtatási lista** – Itt adhatja meg a géphez hozzáadott Chef-futtatási listát. Ez az érték maradhat üresen.
    - **Érvényesítési ügyfél neve** – adja meg a Chef érvényesítési ügyfelének nevét. Például `tarcher-validator`.
    - **Érvényesítési kulcs** – válasszon ki egy fájlt, amely tartalmazza a gépek indításakor használt ellenőrző kulcsot.
    - **Ügyfél-konfigurációs fájl** – válassza ki a Chef-Client konfigurációs fájlját. Ez az érték maradhat üresen.
    - **Chef-ügyfélprogram verziója** – Itt adhatja meg a telepítendő Chef-ügyfél verzióját. Ez az érték maradhat üresen, ami telepíti a legújabb verziót.
    - **SSL-ellenőrzési mód** – válassza a **none** vagy a **társ**lehetőséget. *Nincs* kiválasztva a bemutatóhoz.
    - **Chef Environment** – adja meg a Chef-környezetet, amelynek a csomópontnak a tagja kell lennie. Ez az érték maradhat üresen.
    - **Titkosított adattáska titkos kulcsa** – válasszon ki egy fájlt, amely tartalmazza a titkosított adattároló titkos kódját, amelyhez a gépnek hozzá kell férnie. Ez az érték maradhat üresen.
    - **Chef-kiszolgáló SSL-tanúsítványa** – válassza ki a Chef-kiszolgálóhoz hozzárendelt SSL-tanúsítványt. Ez az érték maradhat üresen.

      ![A Chef-kiszolgáló telepítése Linux rendszerű virtuális gépen](./media/chef-extension-portal/install-extension.png)

1. Amikor megjelenik a **bővítmények** lap, kattintson az **OK gombra**.

1. Amikor megjelenik a **Beállítások** lap, kattintson az **OK gombra**.

1. Amikor megjelenik a **Létrehozás** lap, a kiválasztott és megadott beállítások összegzése látható. Ellenőrizze az adatokat, valamint a **használati feltételek**, majd válassza a **Létrehozás**lehetőséget.

Ha a virtuális gép a Chef bővítménnyel való létrehozásának és üzembe helyezésének folyamata befejeződött, egy értesítés jelzi a művelet sikerességét vagy hibáját. Emellett az új virtuális gép erőforrás lapja automatikusan megnyílik a Azure Portal a létrehozása után.

![A Chef-kiszolgáló telepítése Linux rendszerű virtuális gépen](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Windows rendszerű virtuális gép létrehozása az Azure-ban Chef használatával](chef-automation.md)