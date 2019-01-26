---
title: Az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c72e10991e27f7d616703e635ee6e1a18122afc5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078069"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet

Ha kezdetben DevTest Labs eléréséhez, és létre szeretné hozni az első virtuális gép, akkor valószínűleg teszi ezt, használatával előre betöltött [alap Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Később az Ön is választhat egy [egyéni lemezkép és a egy képlet](devtest-lab-add-vm.md) további virtuális gépek létrehozásakor. 

Ez az oktatóanyag végigvezeti az első virtuális gép hozzáadása a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet az Azure portal használatával.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Az első virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépések
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a a **fejlesztési és üzemeltetési** szakaszban. Ha * (csillag) melletti **DevTest Labs** a a **fejlesztési és üzemeltetési** szakaszban. Ez a művelet felveszi **DevTest Labs** , a bal oldali navigációs menü, hogy hozzá tud férni egyszerűen legközelebb. Ezután kiválaszthatja **DevTest Labs** a bal oldali navigációs menüben.

    ![Minden szolgáltatás – válassza ki a DevTest Labs szolgáltatásban](./media/devtest-lab-create-lab/all-services-select.png)
1. Tesztkörnyezetek listájában jelölje ki a tesztkörnyezetben, amelyben meg szeretné a virtuális gép létrehozásához.  
2. A laborgyakorlat **áttekintése** lapon jelölje be **+ Hozzáadás**.  

    ![Virtuális gép gomb hozzáadása](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Az a **vyberte bázi** lapra, jelölje be a Piactéri lemezképet a virtuális gép számára.
1. Az a **alapbeállítások** lapján a **virtuális gép** lapon, tegye a következőket: 
    1. Adjon meg egy nevet a virtuális géphez a **virtuális gép neve** szövegmezőben. A szövegmező előre kitöltött, az Ön számára automatikusan létrehozott egyedi nevére. A felhasználónév az e-mail-címét és egy egyedi 3-jegyű szám belül felel meg a nevét. Ez a funkció menti az idő, gondolja át, hogy a számítógép nevét, és írja be, minden alkalommal, amikor a gép létrehozása. Ha szeretné felül lehet bírálni ezt automatikusan kitölti a mezőt a választott nevét. Felülbírálja a virtuális gép automatikusan kitöltött nevét, adja meg egy nevet a a **virtuális gép neve** szövegmezőben.
    2. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen. A **felhasználónév** a gép van előre kitöltött egyedi automatikusan létrehozott névvel. A név felel meg az e-mail-címét a felhasználói nevet. Ez a funkció menti az idő dönthet arról, hogy a felhasználónév minden alkalommal, amikor létrehoz egy új gépet. Felülbírálhatja ismét ezt automatikusan kitölti a mezőt egy tetszőleges felhasználónevet, ha szeretné. Felhasználónév automatikusan kitöltött értékének felülbírálásához adjon meg egy értéket a **felhasználónév** szövegmezőben. Ez a felhasználó számára biztosított **rendszergazda** jogosultságokkal a virtuális gépen.
    3. Ha az első virtuális gép létrehozása a lab-ben esetén adja meg egy **jelszó** a felhasználó számára. Mentse ezt a jelszót a labor társított Azure key vaultban lévő alapértelmezett jelszóként, jelölje be a **alapértelmezett jelszó mentése**. Az alapértelmezett jelszó mentése a key vaulttal és a neve: **VmPassword**. Amikor további virtuális gépek létrehozása a lab-ben próbál **VmPassword** automatikusan ki van jelölve az a **jelszó**. Bírálja felül az értéket, törölje a jelet a **mentett titkos kulcs használata** jelölőnégyzetet, majd adjon meg egy jelszót. 

        Is mentheti a titkos kulcsok a key vaultban először, és majd használata közben a virtuális gép létrehozása a lab-ben. További információkért lásd: [Store titkos kulcsok a key vault](devtest-lab-store-secrets-in-key-vault.md). A key vaultban tárolt jelszó használatához válassza **mentett titkos kulcs használata**, és adja meg a kulcs értékét, amely megfelel a titkos kód (jelszó).
    4. Az a **további beállítások** szakaszban jelölje be **méretének módosítása**. Válassza ki, amelyek a Processzormagok, memória mérete és a merevlemez mérete, a virtuális gép létrehozása az előre meghatározott lehetőségek közül.
    5. Válassza ki **hozzáadása vagy eltávolítása összetevők**. Válassza ki, és konfigurálja az alaprendszerképet hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs, vagy konfigurálása összetevők, tekintse meg a [hozzáadása egy meglévő összetevő egy virtuális Géphez](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és ezután térjen vissza ide, ha befejeződött.
2. Váltson a **speciális beállítások** felső részén látható, és végezze el a következő műveleteket:
    1. Ha módosítani szeretné a virtuális hálózat, amely a virtuális gép, jelölje be a **módosítsa a virtuális hálózat**. 
    2. Az alhálózat módosításához válasszon **alhálózat módosítása**. 
    3. Adja meg, hogy a virtuális gép IP-címét **nyilvános, magán és megosztott**. 
    4. A virtuális gép automatikusan törléséhez adja meg a **lejárati dátumát és időpontját**. 
    5. Válassza ki, hogy a virtuális gép igényelhető a lab-felhasználó, **Igen** a **győződjön meg arról, a gép igényelhető** lehetőséget. 
    6. Adja meg a **Virtuálisgép-példányok** , hogy szeretné-e a labor számára elérhetővé tenni. 
3. Válassza ki **létrehozás** a megadott virtuális gép hozzáadása a tesztkörnyezetben.

   A lab lap állapotát jeleníti meg a virtuális gép létrehozása – először mint **létrehozása**, majd, mint **futó** a virtuális gép elindítása után.

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép az oldalon.
* Tekintse meg [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md) további virtuális gépek hozzáadása a laborkörnyezetben található részletesebb információ.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
