---
title: Azure DevTest Labs képletek kezelése virtuális gépek létrehozásához | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre képletet egy Alapból (egyéni rendszerképből, Piactéri rendszerképből vagy más képletből) vagy egy meglévő virtuális gépről.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a668c1f7cf401c109c1041232d7f28dd2accd750
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760402"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs képletek kezelése

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Ez a cikk bemutatja, hogyan hozhat létre képletet egy Alapból (egyéni rendszerképből, Piactéri rendszerképből vagy más képletből) vagy egy meglévő virtuális gépről. Ez a cikk végigvezeti a meglévő képletek kezelésén is.

## <a name="create-a-formula"></a>Képlet létrehozása
A DevTest Labs *felhasználói* engedélyeivel bárki létrehozhatja a virtuális gépeket alapszintű képlet használatával. A képletek létrehozásának két módja van: 

* Alapszintű használat esetén, ha a képlet összes tulajdonságát meg szeretné adni.
* Meglévő laboratóriumi virtuális gépről – akkor használja, ha egy meglévő virtuális gép beállításai alapján szeretne képletet létrehozni.

A felhasználók és engedélyek hozzáadásával kapcsolatos további információkért lásd: [tulajdonosok és felhasználók hozzáadása a Azure DevTest Labs-ben](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Képlet létrehozása egy alapból
A következő lépések végigvezetik a képletek egyéni rendszerképből, Piactéri rendszerképből vagy más képletből való létrehozásának folyamatán.

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

3. A laborok listájából válassza ki a kívánt labort.  

4. A labor lapon válassza a **képletek (újrafelhasználható alapok)** lehetőséget.
   
    ![Képlet menü](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. A **képletek** lapon válassza a **+ Hozzáadás**lehetőséget.
   
    ![Képlet hozzáadása](./media/devtest-lab-create-formulas/add-formula.png)

6. Az **alap kiválasztása** lapon válassza ki azt az alapot (egyéni rendszerkép, Piactéri rendszerkép vagy képlet), amelyből létre szeretné hozni a képletet.
   
    ![Alaplista](./media/devtest-lab-create-formulas/base-list.png)

7. A **képlet létrehozása** lap **alapbeállítások** lapján adja meg a következő értékeket:
   
    * **Képlet neve** – adja meg a képlet nevét. Ez az érték jelenik meg az alapképek listájában, amikor létrehoz egy virtuális gépet. A nevet a beírásakor érvényesíti a rendszer, és ha nem érvényes, egy üzenet jelzi az érvényes névre vonatkozó követelményeket.
    * **Felhasználónév** – adjon meg egy rendszergazdai jogosultságokkal rendelkező felhasználónevet.
    * **Jelszó** – írja be vagy válassza ki a legördülő menüből a megadott felhasználóhoz használni kívánt titkos kulcshoz (jelszóhoz) társított értéket. Ha szeretné megtudni, hogyan mentheti a titkos kulcsokat a kulcstartóban, és hogyan használhatja őket a tesztkörnyezet létrehozásakor, olvassa el [a titkok tárolása Azure Key Vaultban](devtest-lab-store-secrets-in-key-vault.md)című témakört.
    * **Virtuális gép mérete** – a virtuális gép méretének módosításához válassza a **méret módosítása** lehetőséget. 
    * Összetevők **– válassza** az összetevők **hozzáadása vagy eltávolítása** lapot, amelyben kiválasztja és konfigurálja azokat az összetevőket, amelyeket hozzá szeretne adni az alaprendszerképhez. További információ az összetevőkről: egyéni összetevők [létrehozása a Azure DevTest Labs virtuális géphez](devtest-lab-artifact-author.md).
8. Váltson a **Speciális beállítások** lapra, és határozza meg a következő értékeket:
    - **Virtuális hálózat** – a virtuális hálózat módosításához válassza a **vnet módosítása**lehetőséget. 
    - **Alhálózat** – az alhálózat módosításához válassza az **alhálózat módosítása**lehetőséget. 
    - **IP-cím konfigurálása** – Itt adhatja meg, hogy a nyilvános, a magánhálózati vagy a megosztott IP-címeket kívánja-e használni. További információ a megosztott IP-címekről: [a Azure DevTest Labs megosztott IP-címeinek megismerése](./devtest-lab-shared-ip.md).
    - **Lejárati dátum és idő** – megadja a virtuális gép lejárati dátumát és időpontját, hogy a virtuális gép automatikusan törölve legyen. 
    - A **gép igényelhetővé tétele** – a gép "igényelhető" azt jelenti, hogy a létrehozáskor nem lesz hozzárendelve tulajdonos. Ehelyett a labor felhasználói jogosultak lesznek a gép tulajdonosára ("jogcím") a labor oldalára.     
    - **Igény szerinti példányok száma** – Itt adhatja meg, hogy hány igényelhető példányt kíván létrehozni. 
8. A képlet létrehozásához válassza a **Submit (Küldés** ) lehetőséget.

9. A képlet létrehozása után az megjelenik a **képletek** lap listájában.

### <a name="create-a-formula-from-a-vm"></a>Képlet létrehozása virtuális gépről
A következő lépések végigvezetik a képletek egy meglévő virtuális gépen alapuló létrehozásának folyamatán. 

> [!NOTE]
> Ha egy virtuális gépről szeretne képletet létrehozni, a virtuális gépet 2016. március 30-ig kell létrehozni. 
> 
> 

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.  
4. A labor **Áttekintés** lapján válassza ki azt a virtuális gépet, amelyről létre kívánja hozni a képletet.
   
    ![Labs-beli virtuális gépek](./media/devtest-lab-create-formulas/my-vms.png)
5. A virtuális gép lapján válassza a **képlet létrehozása (újrafelhasználható alap)** lehetőséget.
   
    ![Képlet létrehozása](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. A **képlet létrehozása** lapon adja meg az új képlet **nevét** és **leírását** .
   
    ![Képlet létrehozása lap](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. A képlet létrehozásához kattintson **az OK gombra** .

## <a name="modify-a-formula"></a>Képlet módosítása
A képletek módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.  
4. A labor lapon válassza a **képletek (újrafelhasználható alapok)** lehetőséget.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. A **labor-képletek** lapon válassza ki a módosítani kívánt képletet.
6. A **képlet frissítése** lapon végezze el a kívánt módosításokat, majd kattintson a **frissítés**elemre.

## <a name="delete-a-formula"></a>Képlet törlése
A képletek törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.  
4. A labor **beállításai** lapon válassza a **képletek**lehetőséget.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. A **labor-képletek** lapon válassza ki a törölni kívánt képlet jobb oldalán lévő három pontot.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. A képlet helyi menüjében válassza a **Törlés**lehetőséget.
   
    ![Képlet helyi menüje](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Válassza az **Igen** lehetőséget a törlés megerősítését kérő párbeszédablakban.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Következő lépések
Miután létrehozott egy, a virtuális gép létrehozásakor használandó képletet, a következő lépés [egy virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md).

