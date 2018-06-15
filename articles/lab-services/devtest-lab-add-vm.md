---
title: Virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: 'Útmutató: virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban'
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5f953cd6f33e5d46098566740efbf83a5fd80799
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787597"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban
Ha már rendelkezik [az első virtuális gép létrehozása](devtest-lab-create-first-vm.md), akkor valószínűleg sor került az előre betöltött [Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Mostantól, ha szeretne további virtuális gépek felvétele a labor, dönthet úgy is egy *alap* , amely vagy egy [egyéni lemezkép](devtest-lab-create-template.md) vagy egy [képlet](devtest-lab-manage-formulas.md). Ez az oktatóanyag végigvezeti az Azure portál használatával egy virtuális gép hozzáadása egy laborhoz a DevTest Labs szolgáltatásban.

Ez a cikk is bemutatja, hogyan kezelheti az összetevők a virtuális gépek a tesztkörnyezetben.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Lépések végrehajtásával adja hozzá a virtuális gép egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Labs listában jelölje ki a labor kívánja a virtuális gép létrehozása.  
1. A tesztlabor a **áttekintése** ablaktáblán válassza előbb **+ Hozzáadás**.  

    ![Adja hozzá a virtuális gép gomb](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **base válasszon** ablaktáblán válassza ki a megfelelő alapja a virtuális gép számára.
1. A a **virtuális gép** panelen adjon meg egy nevet az új virtuális gép a **virtuálisgép-nevet** szövegmezőben.

    ![Labor VM ablaktábla](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen.  
1. Ha azt szeretné tárolni a jelszó használatát a [titkos tároló](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), jelölje be **mentett titkos kulcs használata**, és adja meg a kulcs értéke, amely megfelel a titkos kulcs (jelszó). Ellenkező esetben a feliratú szövegmezőbe írja be a jelszót **írjon be egy értéket**.
1. A **virtuális gép lemeztípus** határozza meg, hogy milyen típusú a virtuális gépek a tesztkörnyezetben.
1. Válassza ki **virtuálisgép-méret** , és válassza ki a Processzormagok RAM memória méretét és a merevlemez mérete a virtuális gép létrehozásához adjon meg előre meghatározott elemek.
1. Válassza ki **összetevők** és - műtermék - listából válassza ki és konfigurálja az alapjául szolgáló lemezképhez hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha ismerkedik a DevTest Labs szolgáltatásban, vagy tekintse meg az összetevők, konfigurálása a [meglévő összetevő felvétele a virtuális gépek](#add-an-existing-artifact-to-a-vm) szakaszt, és térjen vissza ide befejezésekor.
1. Válassza ki **speciális beállítások** a virtuális gép hálózati beállításai és a lejárati beállítások konfigurálása. 

   Beállíthatja, hogy lejárati, válassza a naptár ikonra, és adjon meg egy dátum, amelyen a virtuális gép automatikusan törölve lesznek.  Alapértelmezés szerint a virtuális Gépet soha nem jár le. 
1. Ha szeretné megtekinteni, illetve az Azure Resource Manager sablon másolása, tekintse meg a [mentése Azure Resource Manager-sablon](#save-azure-resource-manager-template) szakaszt, és térjen vissza ide, ha befejeződött.
1. Válassza ki **létrehozása** a megadott virtuális gép hozzáadása a labor.
1. A labor ablaktáblán jelenik meg a virtuális gép létrehozás - állapotának először **létrehozása**, majd, mint a **futtató** a virtuális gép elindítása után.

> [!NOTE]
> [Adja hozzá a claimable virtuális gépek](devtest-lab-add-claimable-vm.md) bemutatja, hogyan végezheti el a virtuális gép claimable, hogy minden olyan felhasználó, a laborban által használható.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Meglévő összetevő felvétele a virtuális gépek
A virtuális gépek létrehozásakor a meglévő összetevőket is hozzáadhat. Minden tesztkörnyezeti tartalmazza a nyilvános DevTest Labs Összetevőtárban, valamint a már létrehozott és a saját Összetevőtárban hozzá összetevők lévő összetevők.

* Az Azure DevTest Labs *összetevők* lehetővé teszik, hogy adja meg *műveletek* , amelyek végrehajtása közben a virtuális gép ki van építve, például a Windows PowerShell-parancsfájlok futtatásakor, Bash parancsok futtatása, és a szoftver telepítése.
* Összetevő *paraméterek* lehetővé teszik, hogy az adott forgatókönyv szerint összetevő testreszabása

Az összetevők létrehozása, tekintse meg a cikket, miképpen [megtudhatja, hogyan hozhatnak létre a saját összetevők DevTest Labs szolgáltatásban való használatra](devtest-lab-artifact-author.md).

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Labs listában jelölje ki a virtuális Gépet, amelyhez a használni kívánt tartalmazó tesztkörnyezet.  
1. Válassza ki **a virtuális gépek**.
1. Válassza ki a kívánt virtuális Gépet.
1. Válassza ki **kezelheti az összetevők**. 
1. Válassza ki **összetevők alkalmazása**.
1. Az a **összetevők alkalmazása** ablaktáblán válassza ki a virtuális gép hozzá kívánja összetevő.
1. Az a **Hozzáadás összetevő** panelen adja meg a szükséges paraméterek értékeit és a választható paramétereket, amelyekre szüksége van.  
1. Válassza ki **Hozzáadás** összetevő hozzáadása, és térjen vissza a **összetevők alkalmazása** ablaktáblán.
1. Folytassa az összetevők csak a virtuális gép szükséges.
1. Miután az összetevők adott hozzá, [, amelyben az összetevők futnak sorrendjének módosítása](#change-the-order-in-which-artifacts-are-run). Azt is választhatja vissza [megtekintése vagy módosítása egy közbülső](#view-or-modify-an-artifact).
1. Amikor befejezte az összetevők hozzáadása, válassza ki a **alkalmaz**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők vannak futásának sorrendje módosítása
Alapértelmezés szerint a műtermék végrehajtás, amelyben a virtuális gép történő hozzáadásuk sorrendjében. A következő lépések bemutatják, hogyan lehet módosítani, amelyben az összetevők futnak.

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális gép hozzáadott összetevők száma.
   
    ![Virtuális gép hozzá összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán áthúzása az összetevők a kívánt sorrendbe. **Megjegyzés:** Ha gondja támad az összetevő húzással, győződjön meg arról, hogy összetevő bal oldalán húzza. 
1. Kattintson az **OK** gombra, amikor végzett.  

## <a name="view-or-modify-an-artifact"></a>Megtekintése vagy módosítása egy összetevő
A következő lépések bemutatják, hogyan lehet megtekinteni, vagy módosítsa a paramétereket, összetevő:

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális gép hozzáadott összetevők száma.
   
    ![Virtuális gép hozzá összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán válassza ki a kívánt megjelenítése és szerkesztése a hitelesítendő entitások azonosítása.  
1. Az a **Hozzáadás összetevő** ablaktáblában ellenőrizze a szükséges módosításokat, és válassza a **OK** bezárásához a **Hozzáadás összetevő** ablaktáblán.
1. Válassza ki **OK** bezárásához a **kiválasztott összetevők** ablaktáblán.

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager sablon mentése
Az Azure Resource Manager-sablon egy ismételhető telepítés meghatározásához deklaratív lehetőséget biztosít. A következő lépések bemutatják, hogyan a virtuális gép létrehozása az Azure Resource Manager sablon mentéséhez.
Miután menti, használhatja az Azure Resource Manager sablon [új virtuális gépeket az Azure PowerShell telepítése](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Az a **virtuális gép** ablaktáblán válassza előbb **ARM-sablon megtekintése**.
2. Az a **nézet Azure Resource Manager-sablon** ablaktáblán válassza ki a sablon szöveget.
3. A kijelölt szöveg másolása a vágólapra.
4. Válassza ki **OK** bezárásához a **Azure Resource Manager sablon megtekintése ablaktábla**.
5. Nyisson meg egy szövegszerkesztőt.
6. Illessze be a sablon a vágólapra másolt szöveget.
7. Mentse a fájlt későbbi használatra.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után keresztül csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép panelen.
* Megtudhatja, hogyan [egyéni összetevők létrehozása a DevTest Labs szolgáltatásban virtuális gép](devtest-lab-artifact-author.md).
* Megismerkedhet a [Office DevTest Labs Azure Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
