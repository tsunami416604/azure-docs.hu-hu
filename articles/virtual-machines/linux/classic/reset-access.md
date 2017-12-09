---
title: "Linux virtuális gép jelszó és a parancssori SSH-kulcs visszaállítása |} Microsoft Docs"
description: "Linux virtuális gép jelszó vagy SSH-kulcs visszaállítása, javítsa ki az SSH-konfigurációt és lemez konzisztenciájának ellenőrzése a VMAccess bővítmény a az Azure parancssori felület (CLI) használatával"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: cad36fdaf0e7b151809cf07ede10abd1abec99d0
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Linux virtuális gép jelszó vagy SSH-kulcs visszaállítása, javítsa ki az SSH-konfigurációt, és a VMAccess bővítmény használatával lemez konzisztenciájának ellenőrzése
Ha nem csatlakozik egy Linux virtuális gépet az Azure miatt elfelejtett jelszó, nem megfelelő Secure Shell (SSH) billentyűt, vagy az SSH-konfigurációt, a probléma a VMAccessForLinux bővítmény használni az Azure parancssori felület a jelszó vagy SSH-kulcs visszaállítása, javítsa ki az SSH konfiguráció, és a lemez-konzisztencia ellenőrzése. 

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.

Az Azure CLI használata a **azure virtuálisgép-bővítmény készlet** parancsot a parancssori felület (Bash, terminál, parancssor) parancsainak elérésére. Futtatás **azure súgó virtuálisgép-bővítmény készlet** részletes bővítmény használatra.

Az Azure parancssori felület segítségével teheti meg a következő feladatokat:

* [A jelszó alaphelyzetbe állítása](#pwresetcli)
* [Az SSH-kulcs visszaállítása](#sshkeyresetcli)
* [A jelszó és az SSH-kulcs visszaállítása](#resetbothcli)
* [Sudo új felhasználói fiók létrehozása](#createnewsudocli)
* [Az SSH-konfigurációjának visszaállítása](#sshconfigresetcli)
* [Felhasználó törlése](#deletecli)
* [A VMAccess bővítmény állapotának megjelenítése](#statuscli)
* [Felvett lemezek egységességének ellenőrzése](#checkdisk)
* [A Linux virtuális gépén felvett lemezek javítása](#repairdisk)

## <a name="prerequisites"></a>Előfeltételek
Szüksége lesz a következőkre:

* Szüksége lesz a [az Azure parancssori felület telepítése](../../../cli-install-nodejs.md) és [csatlakozás az előfizetéshez](/cli/azure/authenticate-azure-cli) a fiókjához társított Azure-erőforrások használatára.
* Állítsa be a klasszikus üzembe helyezési modellben a megfelelő módba írja be a következő parancsot a parancssorba:
    ``` 
        azure config mode asm
    ```
* Rendelkezik egy új jelszó vagy SSH-kulcsok, ha alaphelyzetbe állítja a vagy az egyik. Ezek nem szükséges, ha azt szeretné, hogy visszaállítja az SSH-konfigurációt.

## <a name="pwresetcli"></a>A jelszó alaphelyzetbe állítása
1. A helyi számítógépen, ezek a sorok PrivateConf.json nevű fájl létrehozása. Cserélje le **sajátfelhasználónév** és  **myP@ssW0rd**  a saját felhasználói névvel és jelszóval és saját lejárati dátumának beállítása.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Futtassa a parancsot, és a virtuális gép neve **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Az SSH-kulcs visszaállítása
1. Ezek a tartalmak PrivateConf.json nevű fájl létrehozása. Cserélje le a **sajátfelhasználónév** és **mySSHKey** értékeket a saját adataival.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Futtassa a parancsot, és a virtuális gép neve **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>A jelszó és az SSH-kulcs visszaállítása
1. Ezek a tartalmak PrivateConf.json nevű fájl létrehozása. Cserélje le a **sajátfelhasználónév**, **mySSHKey** és  **myP@ssW0rd**  értékeket a saját adataival.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Futtassa a parancsot, és a virtuális gép neve **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Sudo új felhasználói fiók létrehozása

Ha elfelejti a felhasználónevét, vmaccess bővítmény használatával hozzon létre egy újat a sudo hitelesítésszolgáltatóval. Ebben az esetben a meglévő felhasználónevet és jelszót nem módosulnak.

Jelszó-hozzáféréssel rendelkező új felhasználót sudo létrehozásához használja a parancsfájl [a jelszó alaphelyzetbe állítása](#pwresetcli) , és adja meg az új felhasználónevet.

SSH-kulcs hozzáféréssel rendelkező új felhasználót sudo létrehozásához használja a parancsfájl [az SSH-kulcs visszaállítása](#sshkeyresetcli) , és adja meg az új felhasználónevet.

Is [alaphelyzetbe állítja a jelszót és az SSH-kulcs](#resetbothcli) új felhasználó létrehozása a jelszó és a SSH-kulcs hozzáférési.

## <a name="sshconfigresetcli"></a>Az SSH-konfigurációjának visszaállítása
Ha az SSH-konfigurációt nem várt állapotban van, előfordulhat is elveszíti a hozzáférést a virtuális géphez. A VMAccess bővítmény használatával állítsa a konfigurációját az alapértelmezett állapotába. Ehhez csak be kell a "reset_ssh" kulcs "True". A bővítmény indítsa újra az SSH-kiszolgálót, nyissa meg az SSH-portot a virtuális gépen, és visszaállítja az SSH-konfigurációt az alapértelmezett értékekre. A felhasználói fiók (név, jelszó vagy SSH-kulcsok) nem lehet módosítani.

> [!NOTE]
> Az SSH konfigurációs fájl, amely lekérdezi alaphelyzetbe /etc/ssh/sshd_config helyezkedik el.
> 
> 

1. Hozzon létre egy fájlt PrivateConf.json ehhez a tartalomhoz.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Futtassa a parancsot, és a virtuális gép neve **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Felhasználó törlése
Ha egy felhasználói fiókot, jelentkezzen be a virtuális géphez közvetlenül nélkül törölni szeretné ezt a parancsfájlt is használhatja.

1. Hozzon létre egy fájlt PrivateConf.json ehhez a tartalomhoz, és a felhasználók neve a **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Futtassa a parancsot, és a virtuális gép neve **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>A VMAccess bővítmény állapotának megjelenítése
A VMAccess bővítmény állapotának megjelenítéséhez futtassa a parancsot.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Felvett lemezek egységességének ellenőrzése
Fsck futtathatnak a Linux virtuális gép összes lemezt, akkor tegye a következőket:

1. Hozzon létre egy fájlt PublicConf.json ehhez a tartalomhoz. Ellenőrizze a lemez időt vesz igénybe, hogy ellenőrizze-e a virtuális géphez csatolt lemezek a logikai érték. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Futtassa a parancs végrehajtása a virtuális gép nevét **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Lemezek javítása
Lemezek, amelyeknek nem csatlakoztat, vagy csatlakoztatási konfigurációs hibák javításához használja a VMAccess bővítmény visszaállítani a Linux virtuális gép csatlakoztatási konfigurációját. A lemez nevét **myDisk**.

1. Hozzon létre egy fájlt PublicConf.json ehhez a tartalomhoz. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Futtassa a parancs végrehajtása a virtuális gép nevét **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Következő lépések
* Ha szeretne alaphelyzetbe állítani a jelszó vagy SSH-kulcs Azure PowerShell-parancsmagok vagy Azure Resource Manager-sablonok segítségével, javítsa ki az SSH konfigurációs, és ellenőrizze a lemez konzisztencia, tekintse meg a [VMAccess bővítmény dokumentációnkat a Githubon](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Használhatja a [Azure-portálon](https://portal.azure.com) alaphelyzetbe állítja a jelszó vagy SSH-kulcsot a Linux virtuális gépek telepítése a klasszikus üzembe helyezési modellben. Jelenleg nem használható a portál ne, ez a Linux virtuális gépek telepítve a Resource Manager üzembe helyezési modellben.
* Lásd: [virtuálisgép-bővítmények és szolgáltatásokkal kapcsolatban](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) több Virtuálisgép-bővítmények az Azure virtuális gépeken való használatáról.

