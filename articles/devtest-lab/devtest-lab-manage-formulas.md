---
title: "A virtuális gépek létrehozásához Azure DevTest Labs szolgáltatásban képletek kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan frissítése és eltávolítása az Azure DevTest Labs képletek"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: v-craic
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 330f6ae246697d54c6bce0690346652b5f2e2dd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs képletek kezelése

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Ez a cikk bemutatja, hogyan képlet base (egyéni lemezképet, Piactéri lemezképhez vagy egy másik képlet) vagy egy meglévő virtuális gép létrehozásához. Ez a cikk is végigvezeti Önt meglévő képletek kezelése.

## <a name="create-a-formula"></a>Létrehozhat egy képletet
Bárki, aki DevTest Labs *felhasználók* engedélyek létre tudja hozni a virtuális gépek képlettel alapjaként. Képletek létrehozása két módja van: 

* A base - használja, ha be szeretné állítani a képlet jellemzői.
* Az egy meglévő Virtuálisgép - tesztkörnyezet használja, ha szeretne létrehozni egy képletet egy meglévő virtuális gép beállításai alapján.

Felhasználók és engedélyek hozzáadásával kapcsolatos további információkért lásd: [tulajdonosa és a felhasználók hozzáadása az Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>A base létrehozhat egy képletet
A következő lépések végigvezetik egy egyéni lemezképet, Piactéri lemezképhez vagy egy másik képlet képlet létrehozásának folyamatán.

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

3. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

4. A labor paneljén válassza **képletek (újrafelhasználható körrel)**.
   
    ![Képletadat menü](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Az a **képletek** panelen válassza **+ Hozzáadás**.
   
    ![A képlet hozzáadása](./media/devtest-lab-create-formulas/add-formula.png)

6. Az a **base válasszon** panelen válassza ki, amelyből el kívánja az alaposztály (egyéni lemezképet, Piactéri lemezképhez vagy képlet) létrehozása a képlet.
   
    ![Kiinduló lista](./media/devtest-lab-create-formulas/base-list.png)

7. Az a **képletet** panelen adja meg a következő értékeket:
   
    * **Képlet neve** -adja meg a képlet nevét. Ez az érték alap képek listája jelenik meg a virtuális gépek létrehozásakor. A név van hitelesítve, írja be azt, és nem érvényes, ha egy üzenet azt jelzi-e egy érvényes nevet a követelményei.
    * **Leírás** -adja meg a képlet beszédes leírást. Ezt az értéket a virtuális gép létrehozása esetén a képlet helyi menüjéből érhető el.
    * **Felhasználónév** -adjon meg egy felhasználónevet, amely rendszergazdai jogosultságokkal engedélyezett.
    * **Jelszó** – írja be - vagy a legördülő listából válassza ki - társított a titkos kulcsot (jelszó), amely a megadott felhasználó használni kívánt értéket. A titkos kulcsok kapcsolatos további információkért lásd: [Azure DevTest Labs: titkos tárolójának](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Virtuális gép lemeztípus** : Adja meg vagy HDD (merevlemez-meghajtóra), vagy SSD (SSD-meghajtóra), milyen típusú jelzi a virtuális gépek üzembe helyezve az alapjául szolgáló lemezképhez használata esetén engedélyezett.
    * ** Virtuális gép mérete ** – válasszon ki egy előre meghatározott elemek adja meg a Processzormagok, RAM memória méretét és a merevlemez mérete a virtuális gép létrehozásához. 
    * **Az összetevők** - megnyitásához válassza a **vegye fel az összetevők** panel, ahol válassza ki, és az alapjául szolgáló lemezképhez hozzáadni kívánt az összetevők konfigurálása. Az összetevők kapcsolatos további információkért lásd: [az Azure DevTest Labs szolgáltatásban virtuális gépen egyéni összetevők létrehozása](devtest-lab-artifact-author.md).
    * **Speciális beállítások** – Itt adhatja meg megnyitni a **speciális** panel, ahol konfigurálhatja a következő beállításokat:
        * **Virtuális hálózati** -adja meg a kívánt virtuális hálózatot.
        * **Alhálózati** -adja meg a kívánt alhálózatot.    
        * **IP-címkonfigurációt** -adja meg, ha a Public, Private vagy megosztott IP-címeket. További információ a megosztott IP-címek: [megértése megosztott IP-címek az Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Ellenőrizze a gép claimable** -és a gép "claimable" azt jelenti, hogy a rendszer nem hozzárendel tulajdonjoga létrehozásának időpontjában. Ehelyett labor felhasználók fognak tudni tulajdonba ("jogcím") a gép a labor panelen.     
    * **Kép** – Ez a mező neve az alapjául szolgáló lemezképhez, választotta az előző panel. 
     
       ![Képlet létrehozása](./media/devtest-lab-create-formulas/create-formula.png)

8. Válassza ki **létrehozása** a képlet létrehozásához.

9. A képlet létrehozásakor megjeleníti a listában a **képletek** panelen.

### <a name="create-a-formula-from-a-vm"></a>Létrehozhat egy képletet a virtuális gépről
A következő lépések végigvezetik a meglévő virtuális alapuló képlet létrehozásának folyamatán. 

> [!NOTE]
> A képletet a virtuális gépről, a virtuális gép kell létrehozni után 2016. március 30. 
> 
> 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez.  
4. A tesztlabor a **áttekintése** panelen válassza ki a virtuális gép, amelyből létre szeretne hozni a képlet.
   
    ![Labs virtuális gépek](./media/devtest-lab-create-formulas/my-vms.png)
5. A virtuális gép paneljén válassza **képletet (újrafelhasználható alap)**.
   
    ![Képlet létrehozása](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. A a **képletet** panelen adjon meg egy **neve** és **leírás** az új képlet.
   
    ![Képletadat panel létrehozása](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Válassza ki **OK** a képlet létrehozásához.

## <a name="modify-a-formula"></a>A képlet módosítása
A képlet módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez.  
4. A labor paneljén válassza **képletek (újrafelhasználható körrel)**.
   
    ![Képletadat menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Az a **labor képletek** panelen válassza ki a módosítani kívánt képletet.
6. Az a **képlet frissítése** panelen elvégezni a kívánt módosításokat, és válassza ki **frissítés**.

## <a name="delete-a-formula"></a>Képlet törlése
A képlet törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez.  
4. A tesztlabor a **beállítások** panelen válassza **képletek**.
   
    ![Képletadat menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Az a **labor képletek** panelen válassza ki a törölni kívánt képlet jobb a három pont.
   
    ![Képletadat menü](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Válassza ki a képletet helyi menüben, **törlése**.
   
    ![Képletadat helyi menü](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Válassza ki **Igen** a törlési megerősítés párbeszédpanel.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
Miután létrehozta a képlet használható virtuális gép létrehozásakor, a következő lépés, hogy [a virtuális gépek hozzáadása a labor](devtest-lab-add-vm.md).

