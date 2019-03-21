---
title: A Chef-ügyfél telepítése az Azure Portalról
description: Ismerje meg, hogyan helyezheti üzembe, és az Azure Portalról a Chef-ügyfél konfigurálása
keywords: Azure, chef, a devops, ügyfél, telepítés, portál
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: cf7afb50006fb273b4d685f9e4259be1cb60fe4e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084742"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>A Chef-ügyfél telepítése az Azure Portalról
A Chef ügyféloldali bővítmény közvetlenül be egy Linux vagy Windows gépen is hozzáadhat az Azure Portalról. Ez a cikk végigvezeti a folyamatot egy új Linux rendszerű virtuális gép használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

- **Chef**: Ha nem rendelkezik aktív Chef-fiókkal, regisztráljon egy [ingyenes próbaverzióra, Chef üzemeltetett](https://manage.chef.io/signup). Kövesse a jelen cikkben lévő utasítások együtt, a következő értékeket a Chef fiókból lesz szüksége:
  - organization_validation kulcs
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Új Linux rendszerű virtuális gépen a Chef bővítmény telepítése
Ebben a szakaszban először hozhat létre Linux rendszerű gépen fogja használni az Azure Portalon. A folyamat során is látni fogja az új virtuális gép a Chef-bővítmény telepítése.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. A bal oldali menüben válassza a **virtuális gépek** lehetőséget. Ha a **virtuális gépek** beállítás nincs jelen, jelölje be **minden szolgáltatás** majd **virtuális gépek**.

1. Az a **virtuális gépek** lapon jelölje be **Hozzáadás**.

    ![Adjon hozzá egy új virtuális gépet az Azure Portalon](./media/chef-extension-portal/add-vm.png)

1. Az a **számítási** lapra, válassza ki a kívánt operációs rendszerrel. Ebben a bemutatóban **Ubuntu Server** van kiválasztva.

1. Az a **Ubuntu Server** lapon jelölje be **Ubuntu Server 16.04 LTS**.

    ![Egy Ubuntu virtuális gép létrehozásakor adja meg a szükséges verziót](./media/chef-extension-portal/ubuntu-server-version.png)

1. Az a **Ubuntu Server 16.04 LTS** lapon jelölje be **létrehozás**.

    ![Ubuntu termékük további információkat tartalmaz.](./media/chef-extension-portal/create-vm.png)

1. Az a **hozzon létre virtuális gépet** lapon jelölje be **alapjai**.

1. Az a **alapjai** lapot, adja meg a következő értékeket, majd **OK**.

   - **Név** – adja meg az új virtuális gép nevét.
   - **Virtuális merevlemez típusa** -vagy **SSD** vagy **HDD** tartozó tárolólemez típusának. Azure-beli virtuális gép lemeztípusokkal kapcsolatos további információkért tekintse meg a cikket [válassza ki a lemez típusát](../virtual-machines/windows/disks-types.md).
   - **Felhasználónév** – adja meg a virtuális gép rendszergazdai jogosultsággal rendelkező felhasználó nevét.
   - **Hitelesítési típus** – ki **jelszó**. Lehetőség kiválasztásával **nyilvános SSH-kulcs**, és adja meg az SSH nyilvános kulcs értékét. A bemutatóhoz (és a képernyőképeket), célokra **jelszó** van kiválasztva.
   - **Jelszó** és **jelszó megerősítése** – adjon meg egy jelszót a felhasználó számára.
   - **Jelentkezzen be az Azure Active Directory** – ki **letiltott**.
   - **Előfizetés** – válassza ki a kívánt Azure-előfizetéssel, ha egynél több.
   - **Erőforráscsoport** – adja meg az erőforráscsoport nevét.
   - **Hely** – ki **USA keleti Régiójában**.

     ![Alapvető beállítások lapon a virtuális gép létrehozása](./media/chef-extension-portal/add-vm-basics.png)

1. Az a **méret kiválasztása** lapot, válassza ki a virtuális gép méretét, majd **kiválasztása**.

1. Az a **beállítások** lapon, az értékek a legtöbb elkészül, az előző lapon kiválasztott értékek alapján. Kattintson az **Extensions** (Bővítmények) gombra.

     ![Bővítmények hozzáadódnak a beállítások lapon-n keresztül a virtuális gépek](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Az a **bővítmények** lapon jelölje be **bővítmény hozzáadása**.

     ![Egy bővítmény hozzáadása egy virtuális gép hozzáadása bővítmény kiválasztása](./media/chef-extension-portal/add-vm-add-extension.png)

1. Az a **új erőforrás** lapon jelölje be **Linux Chef bővítmény (1.2.3)**.

     ![Chef rendelkezik a virtuális gépek Linux és Windows-bővítmények](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Az a **Linux Chef bővítmény** lapon jelölje be **létrehozás**.

1. Az a **bővítmény telepítése** lapot, adja meg a következő értékeket, majd **OK**.

    - **Chef-kiszolgáló URL-címe** – adja meg a Chef URL-címe, amely magában foglalja a szervezet nevét, például *https://api.chef.io/organization/mycompany*.
    - **Chef csomópontnév** – adja meg a Chef csomópont nevét. Ez bármilyen érték lehet.
    - **Futtatási listához** – adja meg, hogy a gép bekerül a Chef futtatása listáját. Ez lehet üresen hagyni.
    - **Érvényesítési Ügyfélnév** – adja meg a Chef érvényesítési ügyfél nevét. Ha például *tarcher-érvényesítő*.
    - **Érvényesítési kulcs** – válassza ki az érvényesítési kulcs használatos, ha a gépek rendszerindítása tartalmazó fájlt.
    - **Ügyfél-konfigurációs fájl** – válassza ki a konfigurációs fájl chef-ügyfélnek. Ez lehet üresen hagyni.
    - **Chef ügyfélverzió** – adja meg a verziót, a chef-ügyfél telepítéséhez. Ez lehet üresen hagyni. Üres érték telepíti a legújabb verzióra.
    - **SSL-ellenőrzési mód** – ezek közül bármelyikre **nincs** vagy **társ**. *Nincs* szeretne a bemutatóra lett kiválasztva.
    - **Chef környezet** – adja meg a Chef környezet ezen a csomóponton, tagjának kell lennie. Ez lehet üresen hagyni.
    - **Titkosított titok Databag** – válasszon ki egy fájlt, a titkosított Databag ezen a számítógépen hozzáféréssel kell rendelkeznie, amely tartalmazza a titkos kulcsot. Ez lehet üresen hagyni.
    - **Chef-kiszolgáló SSL-tanúsítvány** – válassza ki a Chef kiszolgálóhoz rendelt SSL-tanúsítványt. Ez lehet üresen hagyni.

      ![A Chef-kiszolgáló telepítése egy Linux rendszerű virtuális gépre](./media/chef-extension-portal/install-extension.png)

1. Amikor visszatér a **bővítmények** lapon jelölje be **OK**.

1. Amikor visszatér a **beállítások** lapon jelölje be **OK**.

1. Amikor visszatér a **létrehozás** lap (ezt választotta, és a megadott beállítások összesítését jelölő), ellenőrizze az adatokat, valamint a **használati feltételeket tartalmazó fájl**, és válassza ki **létrehozás**.

A folyamat létrehozásának és a Chef kiterjesztésű a virtuális gép üzembe helyezésének befejeződése után egy értesítés azt jelzi, hogy sikeres vagy sikertelen a művelet. Emellett az új virtuális gép erőforrás-oldalon automatikusan megnyílik az Azure Portalon, a létrehozása után.

![A Chef-kiszolgáló telepítése egy Linux rendszerű virtuális gépre](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>További lépések

- [Windows virtuális gép létrehozása Chef használatával Azure-ban](/azure/virtual-machines/windows/chef-automation)
