---
title: Képletek kezelése az Azure DevTest Labs ben virtuális gépek létrehozásához | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan hozhat létre egy képletet egy alap (egyéni lemezkép, Marketplace-lemezkép vagy egy másik képlet) vagy egy meglévő virtuális gép.
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
ms.openlocfilehash: 4f8c8d4ff0a8014fe0b9a6ae4aead35ec7df4bf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501981"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Az Azure DevTest Labs képleteinek kezelése

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy képletet egy alap (egyéni lemezkép, Marketplace-lemezkép vagy egy másik képlet) vagy egy meglévő virtuális gép. Ez a cikk a meglévő képletek kezelésével is ismerteti.

## <a name="create-a-formula"></a>Képlet létrehozása
Bárki, aki DevTest Labs *felhasználók* engedélyekkel képes létrehozni virtuális gépek et képlet et alapként. A képletek kétféleképpen hozhatnak létre: 

* Alapból – Akkor használja, ha a képlet összes jellemzőjét meg szeretné határozni.
* Egy meglévő tesztkörnyezet virtuális gép – akkor használja, ha egy meglévő virtuális gép beállításai alapján szeretne létrehozni egy képletet.

A felhasználók és engedélyek hozzáadásáról további információt a [Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs ben című témakörben talál.](./devtest-lab-add-devtest-user.md)

### <a name="create-a-formula-from-a-base"></a>Képlet létrehozása alapból
A következő lépések végigvezetik a képlet ek egyéni lemezképből, Piactér-lemezképből vagy más képletből történő létrehozásának folyamatán.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

3. A laborok listájából válassza ki a kívánt labort.  

4. A tesztkörnyezet lapján válassza a Bal oldali menü **Képletek (újrafelhasználható alapok)** parancsát.
5. A **Képletek** lapon válassza a **+ Hozzáadás lehetőséget.**
   
    ![Képlet hozzáadása](./media/devtest-lab-create-formulas/add-formula.png)

6. A **Válasszon egy alaplapot,** válassza ki azt az alapképet (egyéni lemezképet vagy Piactér-képet), amelyből létre szeretné hozni a képletet.
7. A **Képlet létrehozása** lap **Alapbeállítások** lapján adja meg a következő értékeket:
   
    * **Képlet neve** – Adja meg a képlet nevét. Ez az érték az alaplemezképek listájában jelenik meg, amikor virtuális gép létrehozásakor. A beíráskor a rendszer érvényesíti a nevet, és ha nem érvényes, akkor egy üzenet jelzi az érvényes név reklamálását.
    - Adja meg a képlet nem kötelező **leírását.** 
    * **Felhasználónév** – Adjon meg rendszergazdai jogosultságokkal rendelkező felhasználónevet.
    * **Jelszó** – Adjon meg – vagy válasszon a legördülő menüből – a megadott felhasználóhoz használni kívánt titkos (jelszó) értékhez társított érték. Ha többet szeretne megtudni a titkos kulcsok kulcstárolóban való mentéséről és használatáról a laborerőforrások létrehozásakor, olvassa [el az Azure Key Vault ban található titkos kulcsok tárolása című témakört.](devtest-lab-store-secrets-in-key-vault.md)

        **Válassza a Mentett titkos kulcs használata** lehetőséget, ha jelszó használata helyett az Azure Key Vault titkos kulcsát szeretné használni. 
    * **Virtuális gép mérete** – Válassza a **Méret módosítása** a virtuális gép méretének módosításához. 
    - **Operációs rendszer lemezének típusa** – válassza ki a használni kívánt lemez típusát (standard HDD, Standard SSD vagy Premium SSD).
    * **Összetevők** – Válassza **az Összetevők hozzáadása vagy eltávolítása** lapot, amelyben kijelöli és konfigurálja az alaplemezképhez hozzáadni kívánt összetevőket. Az összetevőkről további információt az [Azure DevTest Labs virtuális géphez egyéni összetevők létrehozása című témakörben](devtest-lab-artifact-author.md)talál.

        ![Alapbeállítások lap](./media/devtest-lab-create-formulas/basic-settings.png)
8. Váltson a **Speciális beállítások** lapra, és adja meg a következő értékeket:
    - **Virtuális hálózat** - A virtuális hálózat módosításához válassza a **Vnet módosítása**lehetőséget. 
    - **Alhálózat** - Az alhálózat módosításához válassza az **Alhálózat módosítása**lehetőséget. 
    - **IP-cím konfigurációja** – Adja meg, hogy a nyilvános, a privát vagy a megosztott IP-címeket szeretné-e használni. A megosztott IP-címekről a [Megosztott IP-címek ismertetése az Azure DevTest Labs alkalmazásban](./devtest-lab-shared-ip.md)című témakörben talál további információt.
    - **Lejárati dátum és idő** – Ez a mező nem szerkeszthető. 
    - **Legyen ez a gép igényelhető** - A gép "követelhetővé tétele" azt jelenti, hogy a létrehozás időpontjában nem lesz hozzárendelve tulajdonjoghoz. Ehelyett a labor felhasználók képesek lesznek a számítógép tulajdonjogát ("jogcím") a labor oldalán.  

        ![Alapbeállítások lap](./media/devtest-lab-create-formulas/advanced-settings.png)
8. A képlet létrehozásához válassza a **Küldés** lehetőséget.

9. A képlet létrehozása után megjelenik a **Képletek** lap listájában.

### <a name="create-a-formula-from-a-vm"></a>Képlet létrehozása virtuális gépből
A következő lépések végigvezetik a folyamat egy meglévő virtuális gép alapú képlet létrehozásának folyamatát. 

> [!NOTE]
> Ha egy virtuális gépből hozzon létre egy képletet, a virtuális gép nek létre kell hoznia március 30 után, 2016. 
> 
> 

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.  
4. A labor **áttekintése** lapon válassza ki a virtuális gép, amelyből létre kívánja hozni a képletet.
   
    ![Labs virtuális gépek](./media/devtest-lab-create-formulas/my-vms.png)
5. A virtuális gép lapján válassza a **Képlet létrehozása (újrafelhasználható alap)** lehetőséget.
   
    ![Képlet létrehozása](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. A **Képlet létrehozása** lapon adja meg az új képlet **nevét** és **leírását.**
   
    ![Képletlap létrehozása](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. A képlet létrehozásához válassza az **OK gombot.**

## <a name="modify-a-formula"></a>Képlet módosítása
Képlet módosításához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.  
4. A tesztkörnyezet lapján válassza a **Képletek (újrafelhasználható bázisok)** lehetőséget.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. A **Lab képletek** lapon jelölje ki a módosítani kívánt képletet.
6. A **Képlet frissítése** lapon tegye meg a kívánt módosításokat, és válassza a **Frissítés gombot.**

## <a name="delete-a-formula"></a>Képlet törlése
Képlet törléséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.  
4. A tesztkörnyezet **beállításai** lapon válassza a **Képletek**lehetőséget.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. A **Lab képletek** lapon jelölje ki a törölni kívánt képlet jobb oldalán található három pontot.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. A képlet helyi menüjében válassza a **Törlés parancsot.**
   
    ![Képlet helyi menüje](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Válassza az **Igen** lehetőséget a törlés-megerősítéspárbeszédpanelhez.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
Miután létrehozott egy képletet a virtuális gép létrehozásakor, a következő lépés az, hogy [hozzá egy virtuális gép a laborban.](devtest-lab-add-vm.md)

