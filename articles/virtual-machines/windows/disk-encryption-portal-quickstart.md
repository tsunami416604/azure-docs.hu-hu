---
title: Windows rendszerű virtuális gép létrehozása és titkosítása a Azure Portal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és titkosíthatja a Windows rendszerű virtuális gépeket a Azure Portal használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: bde0c483eacec7fb8e076530bba594eb33a32a12
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553441"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Gyors útmutató: Windows rendszerű virtuális gép létrehozása és titkosítása a Azure Portal

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Az Azure Portal egy böngészőalapú felhasználói felület a virtuális gépek és a társított erőforrások létrehozására. Ebben a rövid útmutatóban az Azure Portal egy Ubuntu 18,04 LTS rendszert futtató Windows rendszerű virtuális gép (VM) üzembe helyezéséhez, a titkosítási kulcsok tárolásához és a virtuális gép titkosításához használandó kulcstartó létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra
1. A keresőmezőbe írja be a **Key Vault** kifejezést.
1. Az eredmények listából válassza a **Key Vault**lehetőséget.
1. A Key Vault szakaszban válassza a **Létrehozás**elemet.
1. A **Key Vault létrehozása** képernyőn válassza ki az új kulcstartó egyedi nevét.

    > [!Important]
    > Minden Key Vault egyedi névvel kell rendelkeznie. Az alábbi példa egy *myADEKV*nevű Key Vault hoz létre, de a tiéd nevet kell megadnia.

1. Válasszon egy **előfizetést**.
1.  Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget. Az előugró ablakban adja meg a *myResourceGroup* sztringet az erőforráscsoport neveként, majd kattintson az **OK** gombra. 

    ![Erőforráscsoport-létrehozási képernyő](../media/disk-encryption/portal-qs-keyvaultcreation.png)

1. A **hely** legördülő menüben válassza az **USA keleti**régiója lehetőséget.
1. A többi beállítást hagyja az alapértelmezett értéken.
1. Válassza a "hozzáférési házirendek" lehetőséget, amely egy új képernyőre mutat.
1. Jelölje be a "hozzáférés engedélyezése Azure Disk Encryption számára a kötetek titkosításához" jelölőnégyzetet.

    ![ResourceGroup-létrehozási képernyő](../media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. A hozzáférési szabályzatok képernyő alján kattintson a "felülvizsgálat + létrehozás" gombra.
1. A felülvizsgálás után kattintson a "létrehozás" gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.

1. Az új lap népszerű területén válassza a **Windows Server 2016 Datacenter**elemet.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
1. Az **erőforráscsoport**mezőben válassza ki azt az erőforráscsoportot, amelyet a fenti kulcstartó létrehozásakor hozott létre (például **myResourceGroup**)
1. A **virtuális gép neve** mezőbe írja be a *MyVM*, majd válassza a 
1. A **régió**mezőben válassza ki ugyanazt a régiót, amelyet a fenti kulcstartó létrehozásakor használt (például az **USA keleti**régiója).
1. Győződjön meg arról, hogy a **méret** *szabványos D2s v3*.
1. A **rendszergazdai fiók**területen válassza a **jelszó**lehetőséget. Adja meg a felhasználónevet és a jelszót.
    ![ResourceGroup létrehozási képernyő ](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Válassza a "felügyelet" fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az "új létrehozása" lehetőséget, adja meg az új fiók nevét, és válassza az "OK" ![ResourceGroup létrehozás képernyőt ](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kattintson a "felülvizsgálat + létrehozás" gombra.
1. A **Virtuális gép létrehozása** lapon láthatja a létrehozandó virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

1. Ha a virtuális gép üzembe helyezése befejeződött, válassza az **Ugrás az erőforráshoz**lehetőséget.
1. A bal oldali oldalsávon válassza a **lemezek**lehetőséget.
1. A lemezek képernyőn válassza a **titkosítás**lehetőséget. 

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. A titkosítás képernyőn a **titkosítani kívánt lemezek**területen válassza az **operációs rendszer és az adatlemezek**lehetőséget.
1. A **titkosítási beállítások**területen kattintson a "kulcstartó és kulcs kiválasztása a titkosításhoz" elemre.
1. A jobb oldali oldalsávon válassza ki a **Key Vault**értékeként korábban létrehozott kulcstartó nevét, majd kattintson a **kiválasztás**gombra.

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. A titkosítás képernyő felső részén kattintson a Mentés gombra. A felugró ablak figyelmezteti, hogy a virtuális gép újraindul. Kattintson a **Yes** (Igen) gombra.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a Törlés lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, amely engedélyezte a titkosítási kulcsokat, létrehozta a virtuális gépet, és engedélyezte a virtuális gép titkosítását.  

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
