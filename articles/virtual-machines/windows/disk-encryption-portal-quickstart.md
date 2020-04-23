---
title: Windows rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és titkosíthat windowsos virtuális gépet az Azure Portal használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 3a5fb354ab32dd560a94875dd84d891ce0b9d68c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081744"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Rövid útmutató: Windows-alapú virtuális gép létrehozása és titkosítása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Az Azure Portal egy böngészőalapú felhasználói felület a virtuális gépek és a társított erőforrások létrehozására. Ebben a rövid útmutatóban az Azure Portalon telepíthet egy Ubuntu 18.04 LTS rendszerű Windows virtuális gépet (VM), hozzon létre egy kulcstartót a titkosítási kulcsok tárolásához, és titkosítsa a virtuális gépet.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az Új lap Népszerű csoportban válassza a **Windows Server 2016 Datacenter lehetőséget.**
1. Az Alapok lap Project részletei csoportjában győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, majd válassza **az Új erőforráscsoport létrehozása**lehetőséget. Adja meg *a myResourceGroup* nevet.
1. A **Virtuálisgép nevéhez**írja be a *MyVM értéket.*
1. A **Régió**területen válassza ki ugyanazt a régiót, amelyet a fenti kulcstartó (pl. *USA keleti régió)* készítésekekénél használt.
1. Győződjön meg **róla,** hogy a méret *standard D2s v3*.
1. A **Rendszergazdafiók csoportban**válassza a **Jelszó**lehetőséget. Adja meg a felhasználónevet és a jelszót.
    ![A ResourceGroup létrehozási képernyője](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Válassza a "Kezelés" lapot, és ellenőrizze, hogy rendelkezik-e diagnosztikai tárfiókkal. Ha nincsenek tárfiókkal, válassza az "Új létrehozása" lehetőséget, adjon ![nevet az új fióknak, és válassza az "Ok" erőforráscsoport-létrehozási képernyőt.](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kattintson a "Véleményezés + Létrehozás" gombra.
1. A **Virtuális gép létrehozása** lapon láthatja a létrehozandó virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

1. Amikor a virtuális gép telepítése befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.
1. A bal oldali oldalsávon válassza a **Lemezek**lehetőséget.
1. A Lemezek képernyőn válassza a **Titkosítás**lehetőséget. 

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. A titkosítási képernyőn a **Titkosítandó lemezek**csoportban válassza az **operációs rendszer és az adatlemezek lehetőséget.**
1. A **Titkosítási beállítások csoportban**válassza **a Kulcstartó és kulcs kiválasztása titkosításhoz**lehetőséget.
1. Az **Azure Key Vaultból kulcs kiválasztása** képernyőn válassza **az Új létrehozása lehetőséget.**

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-keyvault-create.png)

1. A **Key Vault létrehozása** képernyőn győződjön meg arról, hogy az erőforráscsoport megegyezik a virtuális gép létrehozásához használt.
1. Adjon nevet a kulcstartónak.  Az Azure minden kulcstartójának egyedi névvel kell rendelkeznie.
1. Az **Access Policies** lapon ellenőrizze az **Azure Disk Encryption kötettitkosítási** jelölőnégyzetet.

    ![lemezek és titkosítás kiválasztása](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.  
1. Miután a key vault megfelelt az ellenőrzésen, válassza **a Létrehozás gombot.** Ez visszaadja a **Select kulcs az Azure Key Vault** képernyőn.
1. Hagyja üresen a **Kulcs** mezőt, és válassza **a Kijelölés gombot.**
1. A titkosítási képernyő tetején kattintson a **Mentés gombra.** Egy felugró ablak figyelmezteti, hogy a virtuális gép újraindul. Kattintson **az Igen gombra.**


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a Törlés elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vaultot, amely engedélyezte a titkosítási kulcsokat, létrehozott egy virtuális gépet, és engedélyezte a virtuális gépet a titkosításhoz.  

> [!div class="nextstepaction"]
> [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)
