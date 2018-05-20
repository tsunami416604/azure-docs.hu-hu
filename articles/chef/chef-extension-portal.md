---
title: A Chef ügyfél telepítése az Azure-portálon
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Chef ügyfél az Azure-portálon
keywords: Azure, a chef, a devops, a ügyfél, a telepítés, a portal
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 52f34361d7c1f3dff47f2571a714b8be7764cc6f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>A Chef ügyfél telepítése az Azure-portálon
Létrehozásakor, vagy egy Linux vagy a Windows virtuális gép az Azure-portálon módosítja, a Chef bővítményt a virtuális gép is hozzáadhat. Ez a cikk bemutatja, hogyan adott folyamatot használja egy új Linux virtuális gépet.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Chef**: Ha nincs egy aktív Chef fiókja, regisztráljon egy [üzemeltetett Chef ingyenes](https://manage.chef.io/signup). Az alábbi lépések a jelen cikkben lévő utasítások követése, a következő értékek Chef fiókjából lesz szüksége: 
    - organization_validation kulcs
    - RB
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Új Linux virtuális gépen a Chef-kiterjesztés telepítése
Ebben a szakaszban először fogjuk az Azure-portálon a Linux-gépek létrehozásához. A folyamat során a Chef bővítmény telepítése az új virtuális gép is láthatja.

1. Keresse meg a [Azure-portálon](http://portal.azure.com).

1. A bal oldali menüben válassza ki a **virtuális gépek** lehetőséget. Ha a **virtuális gépek** , nincs lehetőség van, jelölje be **minden szolgáltatás** majd **virtuális gépek**.

1. Az a **virtuális gépek** lapon jelölje be **Hozzáadás**.

    ![Adja hozzá az új virtuális gépet az Azure portálon](./media/chef-extension-portal/add-vm.png)

1. Az a **számítási** lapra, válassza ki a kívánt operációs rendszerrel. Ebben a bemutatóban **Ubuntu Server** van kiválasztva.

1. Az a **Ubuntu Server** lapon jelölje be **Ubuntu Server 16.04 LTS**.

    ![Egy Ubuntu virtuális gép létrehozásakor adja meg a szükséges verziót](./media/chef-extension-portal/ubuntu-server-version.png)

1. Az a **Ubuntu Server 16.04 LTS** lapon jelölje be **létrehozása**.

    ![Ubuntu a termék további információkkal szolgál.](./media/chef-extension-portal/create-vm.png)

1. Az a **hozzon létre virtuális gépet** lapon jelölje be **alapjai**.

1. Az a **alapjai** lapon adja meg a következő értékeket, és válassza ki **OK**.

    - **Név** -adja meg az új virtuális gép nevét.
    - **Virtuális gép lemeztípus** -adja meg **SSD** vagy **HDD** a lemez tárolási típusát. Az Azure virtuális gép lemeztípusokkal kapcsolatos további információkért lásd: a cikk [prémium szintű Storage nagy teljesítményű és a virtuális gépek felügyelt lemezek](/azure/virtual-machines/windows/premium-storage).
    - **Felhasználónév** -adjon meg egy felhasználónevet, amely rendszergazdai jogosultságokkal a virtuális gépen engedélyezett.
    - **Hitelesítés típusa** – Itt adhatja meg **jelszó**. Igény szerint kiválaszthatja **nyilvános SSH-kulcs**, és adja meg az SSH nyilvános kulcs értékét. Ez a bemutató (és a képernyőfelvételeken), a **jelszó** van kiválasztva.
    - **Jelszó** és **jelszó megerősítése** -adjon meg egy jelszót a felhasználó számára.
    - **Bejelentkezés Azure Active Directoryval** – Itt adhatja meg **letiltott**.
    - **Előfizetés** -válassza ki a kívánt Azure-előfizetéssel, ha egynél több.
    - **Erőforráscsoport** -adja meg az erőforráscsoport nevét.
    - **Hely** – Itt adhatja meg **USA keleti régiója**.

    ![Alapvető beállítások lapján egy virtuális gép létrehozása](./media/chef-extension-portal/add-vm-basics.png)

1. Az a **méret kiválasztása** lapra, válassza ki a virtuális gép méretét, és válassza **válasszon**.

1. Az a **beállítások** lapon, az értékek a legtöbb az előző lapon kiválasztott értékei alapján lesz kitöltve. Válassza ki **bővítmények**.

    ![Bővítmények hozzáadódnak a virtuális gépek használatával a beállítások lap](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Az a **bővítmények** lapon jelölje be **felvenni a bővítményt**.

    ![Egy bővítmény hozzáadása a virtuális gépek hozzáadása bővítmény kiválasztása](./media/chef-extension-portal/add-vm-add-extension.png)

1. Az a **új erőforrás** lapon jelölje be **Linux Chef bővítmény (1.2.3)**.

    ![Chef tartozik a Linux és a Windows virtuális gépek bővítmények](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Az a **Linux Chef bővítmény** lapon jelölje be **létrehozása**.

1. Az a **bővítményének telepítése** lapon adja meg a következő értékeket, és válassza ki **OK**.

    - **Chef URL-címe** -adja meg a Chef URL-címe, amely a szervezet nevét tartalmazza. Használt *https://api.chef.io/organization/hessco* a bemutató a.
    - **Chef csomópontnév** -adja meg a Chef csomópont nevét. Ez tetszőleges érték lehet.
    - **Futtatásához** -adja meg a Futtatás Chef listáját adnak, akkor a gép. Ez lehet üres.
    - **Érvényesítési Ügyfélnév** -adja meg a Chef érvényesítési ügyfél neve. Használt *tarcher-érvényesítő* a bemutató a.
    - **Érvényesítési kulcs** -válassza ki az érvényesítési kulcs használható, ha a gépek rendszerindítása tartalmazó fájlt. 
    - **Ügyfél-konfigurációs fájl** -chef-ügyfél-konfigurációs fájl kiválasztása. Ez lehet üres.
    - **Chef ügyfélverzió** -adja meg a chef ügyfél, telepítendő verzióját. Üres, akkor telepíteni kell a legújabb verzióra. Ez lehet üres.
    - **SSL-hitelesítési mód** -válassza **nincs** vagy **társ**. A kiválasztott *nincs* a bemutató a.
    - **Chef környezet** -adja meg a Chef környezet ebben a csomópontban tagjának kell lennie. Ez lehet üres.
    - **Databag titkos kulcs titkosított** -válasszon egy fájlt, a titkosított Databag ezen a számítógépen hozzáféréssel kell rendelkeznie, amely tartalmazza a titkos kulcsot. Ez lehet üres.
    - **Chef kiszolgáló SSL-tanúsítvány** -Chef-kiszolgálóhoz rendelve SSL-tanúsítvány kiválasztása. Ez lehet üres.

    ![A Chef kiszolgáló telepítése egy Linux virtuális gépen](./media/chef-extension-portal/install-extension.png)

1. Amikor visszatér a **bővítmények** lapon jelölje be **OK**.

1. Amikor visszatér a **beállítások** lapon jelölje be **OK**.

1. Amikor vissza a **létrehozása** (a beállításokat, és a megadott összegzését jelölő) lapon ellenőrizze az adatokat, valamint a **használati feltételek**, és válassza ki **létrehozása**.

Ha befejeződött a folyamat létrehozásának és központi telepítése a virtuális gép Chef kiterjesztésű, értesítést azt jelzi, a sikeres vagy sikertelen volt, a művelet. Továbbá az erőforrás lap az új virtuális gép automatikusan megnyílik az Azure-portálon van a létrehozása után.

![A Chef kiszolgáló telepítése egy Linux virtuális gépen](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>További lépések
* [Windows virtuális gép létrehozása Azure-ban Chef](/azure/virtual-machines/windows/chef-automation)