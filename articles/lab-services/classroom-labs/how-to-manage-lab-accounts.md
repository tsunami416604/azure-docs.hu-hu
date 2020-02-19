---
title: Labor-fiókok kezelése a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes laboratóriumi fiókot, vagy törölhet egy Lab-fiókot egy Azure-előfizetésben.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443316"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Lab-fiókok kezelése a Azure Lab Servicesban 
Azure Lab Services a labor-fiók a felügyelt labor típusok, például a tanterem Labs tárolója. A rendszergazda létrehoz egy Azure Lab Services, és hozzáférést biztosít a labor tulajdonosai számára, akik a fiókban létrehozhatnak laborokat. Ez a cikk bemutatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes tesztkörnyezet fiókját, vagy törölhet egy labor-fiókot.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. A **DevOps** szakaszban válassza a **labor-fiókok** lehetőséget. Ha a csillag (`*`) lehetőséget választja a **labor-fiókok**mellett, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **labor-fiókok** lehetőséget a **Kedvencek**alatt.

    ![Minden szolgáltatás – > Lab-fiókok](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **labor-fiókok** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, vagy **hozzon létre egy Lab-fiókot** az oldalon. 

    ![Válassza a Hozzáadás lehetőséget a labor-fiókok oldalon](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **labor-fiók létrehozása** lap **alapok** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot.
    5. Ahhoz, hogy a labor létrehozója kiválassza a **labor helye** mezőt, adja meg, hogy szeretné-e, ha a labor készítői ki tudják választani a tesztkörnyezet helyét. Alapértelmezés szerint a beállítás le van tiltva. Ha le van tiltva, a labor létrehozói nem tudnak megadni egy helyet a létrehozandó labornak. A laborok a legközelebbi földrajzi helyen a labor-fiókhoz jönnek létre. Ha engedélyezve van, a tesztkörnyezet létrehozója kiválaszthatja a kívánt helyet a labor létrehozásakor. További információ: [a tesztkörnyezet létrehozójának engedélyezése a tesztkörnyezet helyének kiválasztásához](allow-lab-creator-pick-lab-location.md). 

        ![Lab-fiók létrehozása – > alapjai](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Válassza a **Tovább: speciális** lehetőséget az oldal alján a **speciális** lapra való ugráshoz, majd hajtsa végre a következő lépéseket: 
    1. Válasszon ki egy meglévő **megosztott képtárat** , vagy hozzon létre egyet. A sablon virtuális gépet a megosztott rendszerkép-katalógusba mentheti, amelyet mások is felhasználhatnak. A megosztott képtárakkal kapcsolatos részletes információkért lásd: [közös rendszerkép-katalógus használata Azure Lab Servicesban](how-to-use-shared-image-gallery.md).
    2. Itt adhatja meg, hogy a rendszer **automatikusan leállítsa-e a Windows virtuális gépeket** , amikor a felhasználók lecsatlakoznak róluk. Annak megadása, hogy a virtuális gépek meddig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt. 
    3. A **társ virtuális hálózat**területen válasszon ki egy társ virtuális hálózatot (VNet) a tesztkörnyezet hálózatához. A fiókban létrehozott laborok kapcsolódnak a kiválasztott VNet, és hozzáférnek a kiválasztott VNet lévő erőforrásokhoz. További információ: [a tesztkörnyezet virtuális hálózatának összekötése egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md).    
    8. Válasszon **címtartományt** a virtuális gépek számára a laborban. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva. További információ: [címtartomány megadása virtuális gépekhez a tesztkörnyezetben](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Labor-fiók létrehozása – > Advanced](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Kattintson a **Next (tovább** ) gombra a lap alján a **címkék** lapra való váltáshoz. adja meg a labor-fiókhoz társítandó címkéket. A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md).

    ![Lab-fiók létrehozása – > Címkék](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Kattintson a lap alján található **felülvizsgálat + létrehozás** gombra a **felülvizsgálat + létrehozás** lapra való váltáshoz. 
4. Tekintse át az ezen a lapon található összegző információkat, majd kattintson a **Létrehozás**gombra. 

    ![Lab-fiók létrehozása – > Címkék](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Várjon, amíg befejeződik az üzembe helyezés, bontsa ki a **következő lépések**elemet, majd válassza az **Ugrás az erőforráshoz** lehetőséget az alábbi képen látható módon: 

    A **harang ikont** is kiválaszthatja az eszköztáron (**értesítések**), ellenőrizheti, hogy az üzembe helyezés sikeres volt-e, majd válassza az **erőforrás keresése**lehetőséget. 

    Másik lehetőségként válassza a **labor-fiókok** lapon a **frissítés** lehetőséget, majd válassza ki a létrehozott labor-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Tesztkörnyezet-fiókok megtekintése
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza ki az **összes erőforrás** elemet a menüből. 
3. Válassza a **típushoz**tartozó **labor-fiókok** lehetőséget. 
    Az előfizetés, az erőforráscsoport, a helyszínek és a címkék alapján is szűrhető. 

    ![Minden erőforrás – > Lab-fiókok](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Labs megtekintése és kezelése a labor-fiókban

1. A **labor-fiók** lapon válassza a bal oldali menüben az **összes labor** elemet.

    ![A fiókban található Labs](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. A fiókban található **Labs-lista** a következő információkat tartalmazza: 
    1. A labor neve.
    2. A labor létrehozásának dátuma. 
    3. A labort létrehozó felhasználó e-mail-címe. 
    4. A laborban engedélyezett felhasználók maximális száma. 
    5. A labor állapota. 
    6. Szerepkör-hozzárendelések. 

## <a name="delete-a-lab-in-the-lab-account"></a>Tesztkörnyezet törlése a labor-fiókban
Kövesse az előző szakasz utasításait a labor-fiókban található laborok listájának megtekintéséhez.

1. Válassza a **... lehetőséget. (három pont)** , majd válassza a **Törlés**lehetőséget. 

    ![Tesztkörnyezet törlése](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Válassza az **Igen** lehetőséget a figyelmeztető üzenetben. 

    ![Tesztkörnyezet törlésének megerősítése](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Labor-fiók törlése
Kövesse az előző szakasz utasításait, amely a listában szereplő Lab-fiókokat jeleníti meg. A labor-fiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a törölni kívánt **labor-fiókot** . 
2. Válassza a **Törlés** lehetőséget az eszköztáron. 

    ![Labor-fiókok – > Törlés gomb](../media/how-to-manage-lab-accounts/delete-button.png)
1. Erősítse meg az **Igen** értéket.
1. Válassza a **Törlés** elemet. 

    ![Labor-fiók törlése – megerősítés](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> A Lab-fiókok kezeléséhez az az. LabServices PowerShell-modult (előzetes verzió) is használhatja. További információ: az az [. LabServices Kezdőlap a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet helyének engedélyezése a labor Creator számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának összekötése egy társ virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)