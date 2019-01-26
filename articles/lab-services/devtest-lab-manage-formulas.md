---
title: Az Azure DevTest Labs szolgáltatásban hozhat létre virtuális gépeket képletek kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítése és eltávolítása az Azure DevTest Labs-képletek
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 60790f0f31915a50829df09d039a4f74860a47d7
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076434"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Az Azure DevTest Labs-képletek kezelése

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Ez a cikk bemutatja, hogyan a képletet egy alap (egyéni rendszerképet, Piactéri rendszerkép vagy képlet) vagy egy meglévő virtuális Gépet. Ez a cikk is végigvezeti meglévő képletek kezelése.

## <a name="create-a-formula"></a>Képlet létrehozása
A DevTest Labs szolgáltatással bárki *felhasználók* engedélyekkel létre tudja hozni a virtuális gépek használata a képlet egy alapjaként. Képletek létrehozása két módja van: 

* Egy alapul szolgáló - használja, ha be szeretné állítani a képlet jellemzőit.
* Egy meglévő Virtuálisgép - labor a használja, ha szeretne létrehozni egy képlet alapján egy meglévő virtuális gép beállításait.

Felhasználókat és engedélyeket hozzáadásával kapcsolatos további információkért lásd: [tulajdonosai és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Egy alapul szolgáló képlet létrehozása
A következő lépések végigvezetik az egyéni rendszerkép, Piactéri rendszerkép vagy képlet egy képlet létrehozásának folyamatán.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

4. A labor oldalán válassza **képletek (újrafelhasználható bázisok)**.
   
    ![Képlet menü](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Az a **képletek** lapon jelölje be **+ Hozzáadás**.
   
    ![A képlet hozzáadása](./media/devtest-lab-create-formulas/add-formula.png)

6. Az a **vyberte bázi** lapon, válassza ki a képletet szeretne alap (egyéni rendszerképet, Piactéri rendszerkép vagy képlet).
   
    ![Kiinduló lista](./media/devtest-lab-create-formulas/base-list.png)

7. Az a **alapbeállítások** lapján a **képlet létrehozása** csoportjában adja meg a következő értékeket:
   
    * **Název vzorce** – adjon meg egy nevet a képlet. Ez az érték alaplemezképek listája jelenik meg a virtuális gép létrehozásakor. A nevet, írja be, és nem érvényes, ha egy üzenet azt jelzi, hogy érvényes nevet a követelményei érvényességét.
    * **Felhasználónév** – adjon meg egy rendszergazdai jogosultságokkal rendelkező felhasználói nevet.
    * **Jelszó** – adja meg - vagy válassza ki a legördülő listából – a titkos kulcsot (jelszó), amely a megadott felhasználó használni kívánt társított értéket. Mentése folyamatban van a titkos kulcsok a key vaultban, és használja őket, labor-erőforrások létrehozásakor kapcsolatos további információkért lásd: [Store titkos kulcsok Azure Key vaultban](devtest-lab-store-secrets-in-key-vault.md).
    * **Virtuálisgép-méret** – ki **méretének módosítása** a virtuális gép méretének módosításához. 
    * **Összetevők** – ki **hozzáadása vagy eltávolítása összetevők** oldal, ahol, illetve konfigurálhatja az alaprendszerképet hozzáadni kívánt összetevők. Összetevők kapcsolatos további információkért lásd: [az Azure DevTest Labs szolgáltatásban virtuális gép egyéni összetevők létrehozása](devtest-lab-artifact-author.md).
8. Váltson a **speciális beállítások** lapra, és adja meg a következő értékeket:
    - **Virtuális hálózat** – az válassza ki a virtuális hálózat módosítása **módosítsa a virtuális hálózat**. 
    - **Alhálózat** – az az alhálózat módosításához jelölje ki **alhálózat módosítása**. 
    - **IP-címének konfigurációja** – adja meg, ha a nyilvános, magán vagy megosztott IP-címeket. További információ a közös IP-címek: [Understand megosztott IP-címek az Azure DevTest Labs szolgáltatásban](./devtest-lab-shared-ip.md).
    - **Lejárati dátum és idő** – adja meg a lejárati dátum és idő a virtuális gép számára, hogy a virtuális Gépet a rendszer automatikusan törli. 
    - **Ez a gép igényelhető legyen** – így egy gép "igényelhető" azt jelenti, hogy a rendszer nem hozzárendel tulajdonjogát a létrehozása idején. Ehelyett labor felhasználók tudják saját tulajdonba vétel ("jogcím") a gép a labor oldalon.     
    - **Igényelhető példányok** – adja meg, hogy hány igényelhető instacnes szeretne létrehozni. 
8. Válassza ki **küldés** a képlet létrehozása.

9. Ha a képlet létrehozása után jelenik meg a listában a **képletek** lapot.

### <a name="create-a-formula-from-a-vm"></a>Képlet létrehozása egy virtuális gépről
A következő lépések végigvezetik egy képlet alapján egy meglévő virtuális gép létrehozásának folyamatán. 

> [!NOTE]
> Képlet létrehozása egy virtuális gépről, a virtuális Gépet kell létrehozni, 2016. március 30. után. 
> 
> 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
4. A laborgyakorlat **áttekintése** lapra, jelölje be a virtuális gép, amelyről szeretné létrehozni a képlet.
   
    ![Labs virtuális gépek](./media/devtest-lab-create-formulas/my-vms.png)
5. A virtuális gép oldalán válassza **(újrafelhasználható alap) képlet létrehozása**.
   
    ![Képlet létrehozása](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Az a **képlet létrehozása** lap, adja meg egy **neve** és **leírás** vonatkozóan az új képlet.
   
    ![Képlet lap létrehozása](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Válassza ki **OK** a képlet létrehozása.

## <a name="modify-a-formula"></a>Egy képlet módosítása
A képlet módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
4. A labor oldalán válassza **képletek (újrafelhasználható bázisok)**.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Az a **labor képletek** lapon, válassza ki a módosítani kívánt képlet.
6. Az a **Aktualizovat vzorec** lapon győződjön meg a kívánt módosításokat, és válassza ki **frissítés**.

## <a name="delete-a-formula"></a>Képlet törlése
A képlet törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
4. A laborgyakorlat **beállítások** lapon jelölje be **képletek**.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Az a **labor képletek** lapon, válassza ki a három pontot, jobb oldalán a képletet, hogy törölni szeretné.
   
    ![Képlet menü](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Válassza ki a képletet a helyi menüben, **törlése**.
   
    ![Képlet helyi menü](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Válassza ki **Igen** való törlési megerősítést kérő párbeszédpanel.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>További lépések
Miután létrehozott egy képletet használja a virtuális gép létrehozásakor, a következő lépés az, hogy [virtuális gép hozzáadása a labor](devtest-lab-add-vm.md).

