---
title: Virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: 'Útmutató: virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet'
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: ce95a2177260e97113fd5e639671075eb6ad40cd
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215016"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
Ha már rendelkezik [az első virtuális gép létrehozása](devtest-lab-create-first-vm.md), akkor valószínűleg sor került az előre betöltött [Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Most, ha a labor további virtuális gépeket ad hozzá, azt is beállíthatja egy *alap* , amely vagy egy [egyéni rendszerkép](devtest-lab-create-template.md) vagy egy [képlet](devtest-lab-manage-formulas.md). Ez az oktatóanyag végigvezeti az Azure portal használatával egy virtuális gép hozzáadása a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet.

Ez a cikk emellett bemutatja, hogyan kezelheti az összetevők a labor virtuális gép.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépései
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a tesztkörnyezetben, amelyben meg szeretné a virtuális gép létrehozásához.  
1. A laborgyakorlat **áttekintése** ablaktáblán válassza előbb **+ Hozzáadás**.  

    ![Virtuális gép gomb hozzáadása](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **vyberte bázi** ablaktáblán válassza ki a megfelelő talál a virtuális gép számára.
1. Az a **virtuális gép** ablaktáblán **virtuális gép neve** előre kitölti az Ön számára automatikusan létrehozott egyedi nevére. A felhasználónév az e-mail-címét és egy egyedi 3-jegyű szám belül felel meg a nevét. Ez a funkció menti az idő, gondolja át, hogy a számítógép nevét, és írja be, minden alkalommal, amikor a gép létrehozása. Ha szeretné felül lehet bírálni ezt automatikusan kitölti a mezőt a választott nevét. Felülbírálja a virtuális gép automatikusan kitöltött nevét, adja meg egy nevet a a **virtuális gép neve** szövegmezőben. 

    ![Lab VM panel](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. A **felhasználónév** a gép van előre kitöltött egyedi automatikusan létrehozott névvel. A név felel meg az e-mail-címét a felhasználói nevet. Ez a funkció menti az idő dönthet arról, hogy a felhasználónév minden alkalommal, amikor létrehoz egy új gépet. Felülbírálhatja ismét ezt automatikusan kitölti a mezőt egy tetszőleges felhasználónevet, ha szeretné. Felhasználónév automatikusan kitöltött értékének felülbírálásához adjon meg egy értéket a **felhasználónév** szövegmezőben. Ez a felhasználó számára biztosított **rendszergazda** jogosultságokkal a virtuális gépen.     
1. Az a **jelszó**:
    
    Ha az első virtuális gép létrehozása a lab-ben esetén adja meg a jelszó a **adjon meg egy értéket** szövegmezőben. Mentse ezt a jelszót a labor társított Azure key vaultban lévő alapértelmezett jelszóként, jelölje be a **alapértelmezett jelszó mentése**. Az alapértelmezett jelszó mentése a key vaulttal és a name: **VmPassword**. Amikor további virtuális gépek létrehozása a lab-ben próbál **VmPassword** automatikusan ki van jelölve az a **jelszó**. Bírálja felül az értéket, törölje a jelet a **mentett titkos kulcs használata** jelölőnégyzetet, majd adjon meg egy jelszót. 

    Is mentheti a titkos kulcsok a key vaultban először, és majd használata közben a virtuális gép létrehozása a lab-ben. További információkért lásd: [Store titkos kulcsok a key vault](devtest-lab-store-secrets-in-key-vault.md). A key vaultban tárolt jelszó használatához válassza **mentett titkos kulcs használata**, és adja meg a kulcs értékét, amely megfelel a titkos kód (jelszó). 
3. A **virtuálisgép-lemeztípust** határozza meg, melyik tárolólemez-típusba engedélyezett a virtuális gépek a tesztkörnyezetben.
4. Válassza ki **virtuálisgép-méret** válasszon ki egy, az előre definiált elemek, amelyek a Processzormagok, memória mérete és a merevlemez mérete, a virtuális gép létrehozásához.
5. Válassza ki **összetevők** és - összetevők – a listából válassza ki és konfigurálja az alaprendszerképet hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs, vagy konfigurálása összetevők, tekintse meg a [hozzáadása egy meglévő összetevő egy virtuális Géphez](#add-an-existing-artifact-to-a-vm) szakaszt, és ezután térjen vissza ide, ha befejeződött.
6. Válassza ki **speciális beállítások** a virtuális gép hálózati beállításai, és a lejárati beállítások konfigurálása. 

   Beállíthatja, hogy lejárati, válassza a naptár ikonra, amelyen a virtuális gép automatikusan törlődni fognak dátumot adhat meg.  Alapértelmezés szerint a virtuális gép soha nem jár le. 
1. Ha szeretné megtekintése és másolása az Azure Resource Manager-sablont, tekintse meg a [mentése az Azure Resource Manager-sablon](#save-azure-resource-manager-template) szakaszt, és térjen vissza ide, ha befejeződött.
1. Válassza ki **létrehozás** a megadott virtuális gép hozzáadása a tesztkörnyezetben.
1. A lab panelen állapotát jeleníti meg a virtuális gép létrehozása – először mint **létrehozása**, majd, mint **futó** a virtuális gép elindítása után.

> [!NOTE]
> [Igényelhető virtuális gép hozzáadása](devtest-lab-add-claimable-vm.md) mutatja be a virtuális gép igényelhető ellenőrizze, hogy a labor létrehozása a felhasználók által használható legyen.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Egy meglévő összetevő hozzáadása egy virtuális géphez
Virtuális gép létrehozásakor a meglévő összetevőket is hozzáadhat. Minden labor összetevők a nyilvános DevTest Labs Összetevőtárban, valamint a már létrehozott és a saját Összetevőtárban hozzá összetevőket tartalmazza.

* Az Azure DevTest Labs *összetevők* segítségével megadhatja, hogy *műveletek* , ha a virtuális gép ki van építve, például a Windows PowerShell-parancsfájlok futtatásakor, a Bash-parancsok futtatása és a szoftverek telepítése történik.
* Összetevő *paraméterek* segítségével testre szabhatja az adott forgatókönyvhöz összetevő

Összetevők létrehozása, tekintse meg a cikket arról, hogy miképpen [megtudhatja, hogyan hozhat létre saját összetevők a DevTest Labs segítségével](devtest-lab-artifact-author.md).

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a labor létrehozása a virtuális Gépet, amely a használni kívánt tartalmazó.  
1. Válassza ki **a virtual machines**.
1. Válassza ki a kívánt virtuális Gépet.
1. Válassza ki **az összetevők kezelése**. 
1. Válassza ki **összetevők alkalmazása**.
1. Az a **összetevők alkalmazása** ablaktáblán válassza ki a virtuális géphez hozzá kívánja az összetevőben.
1. Az a **Hozzáadás összetevő** panelen adja meg a szükséges paraméterértékeket, és opcionális paramétereket kell.  
1. Válassza ki **Hozzáadás** összetevő hozzáadása, és térjen vissza a **összetevők alkalmazása** ablaktáblán.
1. Továbbra is összetevők hozzáadása a virtuális gép igény szerint.
1. Miután hozzáadta az összetevőkhöz, is [, amelyben futnak, az összetevők sorrendjének módosítása](#change-the-order-in-which-artifacts-are-run). Is léphet vissza [megtekintése vagy módosítása egy összetevő](#view-or-modify-an-artifact).
1. Hozzáadása összetevők elkészült, válassza ki a **alkalmaz**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futnak, amelyben sorrend módosítása
Alapértelmezés szerint az összetevők végrehajtás a sorrendben, amelyben a virtuális gép kerül. A következő lépések bemutatják, hogyan módosíthatja a sorrendben, amelyben az összetevők futnak.

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális géphez hozzáadott összetevők számát jelző.
   
    ![Virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán áthúzása az összetevők a kívánt sorrendben be. **Megjegyzés:** Ha problémája van az összetevőt húzással, győződjön meg róla, hogy a lehívandó összetevő bal oldalán húzza. 
1. Kattintson az **OK** gombra, amikor végzett.  

## <a name="view-or-modify-an-artifact"></a>Megtekintése vagy módosítása egy összetevő
A következő lépések bemutatják, hogyan lehet megtekinteni, vagy módosítsa a paramétereket egy lehívandó összetevő:

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális géphez hozzáadott összetevők számát jelző.
   
    ![Virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán válassza ki a megtekinteni vagy szerkeszteni kívánt összetevő.  
1. Az a **Hozzáadás összetevő** panelen kattintson a jobb tétele bármely szükséges módosításokat, és válassza a **OK** gombra kattintva zárja be a **Hozzáadás összetevő** ablaktáblán.
1. Válassza ki **OK** gombra kattintva zárja be a **kiválasztott összetevők** ablaktáblán.

## <a name="save-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon mentése
Az Azure Resource Manager-sablonok meghatározása egy megismételhető üzembe helyezési deklaratív lehetőséget biztosít. A következő lépések bemutatják, hogyan a virtuális gép létrehozása az Azure Resource Manager-sablon mentéséhez.
A mentés után is használhatja az Azure Resource Manager-sablonja [helyezzen üzembe új virtuális gépet az Azure PowerShell-lel](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Az a **virtuális gép** ablaktáblán válassza előbb **megtekintése az Azure Resource Manager-sablon**.
2. Az a **megtekintése az Azure Resource Manager-sablon** ablaktáblán jelölje ki a sablon szöveget.
3. A kijelölt szöveg másolása a vágólapra.
4. Válassza ki **OK** gombra kattintva zárja be a **Azure Resource Manager-sablon megtekintése ablaktáblán**.
5. Nyisson meg egy szövegszerkesztőt.
6. Illessze be a sablon szöveget a vágólapról.
7. Mentse a fájlt későbbi használatra.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép ablaktáblán.
* Ismerje meg, hogyan [egyéni összetevők létrehozása a DevTest Labs virtuális gép](devtest-lab-artifact-author.md).
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
