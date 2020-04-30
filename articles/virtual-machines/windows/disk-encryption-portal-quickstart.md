---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és titkosíthatja a Windows rendszerű virtuális gépeket a Azure Portal használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 3a5fb354ab32dd560a94875dd84d891ce0b9d68c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82081744"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Gyors útmutató: Windows rendszerű virtuális gép létrehozása és titkosítása a Azure Portal

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Az Azure Portal egy böngészőalapú felhasználói felület a virtuális gépek és a társított erőforrások létrehozására. Ebben a rövid útmutatóban az Azure Portal egy Ubuntu 18,04 LTS rendszert futtató Windows rendszerű virtuális gép (VM) üzembe helyezéséhez, a titkosítási kulcsok tárolásához és a virtuális gép titkosításához használandó kulcstartó létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az új lap népszerű területén válassza a **Windows Server 2016 Datacenter**elemet.
1. Az alapvető beállítások lap projekt részletei területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva, majd válassza az **Új erőforráscsoport létrehozása**lehetőséget. Adja meg a *myResourceGroup* nevet.
1. A **virtuális gép neve**mezőbe írja be a következőt: *MyVM*.
1. A **régió**mezőben válassza ki ugyanazt a régiót, amelyet a fenti kulcstartó létrehozásakor használt (például az *USA keleti*régiója).
1. Győződjön meg arról, hogy a **méret** *szabványos D2s v3*.
1. A **rendszergazdai fiók**területen válassza a **jelszó**lehetőséget. Adja meg a felhasználónevet és a jelszót.
    ![ResourceGroup-létrehozási képernyő](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Válassza a "felügyelet" fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az "új létrehozása" lehetőséget, adja meg az új fiók nevét, és válassza az !["OK" ResourceGroup-létrehozási képernyőt.](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kattintson a "felülvizsgálat + létrehozás" gombra.
1. A **Virtuális gép létrehozása** lapon láthatja a létrehozandó virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

1. Ha a virtuális gép üzembe helyezése befejeződött, válassza az **Ugrás az erőforráshoz**lehetőséget.
1. A bal oldali oldalsávon válassza a **lemezek**lehetőséget.
1. A lemezek képernyőn válassza a **titkosítás**lehetőséget. 

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. A titkosítás képernyőn a **titkosítani kívánt lemezek**területen válassza az **operációs rendszer és az adatlemezek**lehetőséget.
1. A **titkosítási beállítások**területen válassza **a Key Vault és a kulcs kiválasztása a titkosításhoz**lehetőséget.
1. A **válasszon kulcsot Azure Key Vault** képernyőn válassza az **új létrehozása**lehetőséget.

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-keyvault-create.png)

1. A **Key Vault létrehozása** képernyőn győződjön meg arról, hogy az erőforráscsoport megegyezik azzal, amelyet a virtuális gép létrehozásához használt.
1. Adja meg a kulcstartó nevét.  Minden Azure-beli Key vaultnak egyedi névvel kell rendelkeznie.
1. A **hozzáférési házirendek** lapon jelölje be a **Azure Disk Encryption a kötet titkosítása** jelölőnégyzetet.

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.  
1. Miután a kulcstartó érvényesítése sikeres volt, válassza a **Létrehozás**lehetőséget. Ezzel a beállítással visszatérhet a **Select kulcsra Azure Key Vault** képernyőről.
1. Hagyja üresen a **kulcsmező** mezőt, és válassza a **kiválasztás**lehetőséget.
1. A titkosítás képernyő felső részén kattintson a **Mentés**gombra. A felugró ablak figyelmezteti, hogy a virtuális gép újraindul. Kattintson az **Igen**gombra.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a Törlés elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, létrehozta a virtuális gépet, és engedélyezte a virtuális gép titkosítását.  

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
