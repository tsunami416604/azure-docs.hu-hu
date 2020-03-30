---
title: Laborfiókok kezelése az Azure Lab Servicesben | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre laborfiókot, tekintheti meg az összes tesztkörnyezet-fiókot, vagy törölhet egy tesztkörnyezet-fiókot egy Azure-előfizetésben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284290"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Laborfiókok kezelése az Azure Lab Servicesben 
Az Azure Lab Services-ben egy laborfiók egy tároló felügyelt labortípusok, például tantermi laborok. A rendszergazda beállít egy tesztkörnyezet-fiókot az Azure Lab Services szolgáltatással, és hozzáférést biztosít a labortulajdonosok, akik laborokat hozhat létre a fiókban. Ez a cikk ismerteti, hogyan hozhat létre egy tesztkörnyezet-fiókot, tekintse meg az összes tesztkörnyezet-fiókok, vagy törölje a tesztkörnyezet-fiók.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.** Válassza a **Laborfiókok a** **DevOps** szakaszban. Ha a Lab-fiókok elem mellett a csillag (`*`) lehetőséget **választja,** az a bal oldali menü **KEDVENCEK** szakaszába kerül. A következő alkalommal válassza lab **fiókok** csoport **kedvencek**.

    ![Minden szolgáltatás -> Labor-fiók](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **Lab-fiókok** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, vagy válassza a **Tesztkörnyezet-fiók létrehozása** lehetőséget a lapon. 

    ![Válassza a Hozzáadás lehetőséget a Laborfiókok lapon](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **Laborfiók létrehozása** lap **Alapjai** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot.
    5. A **Labor létrehozója válassza a labor** helymezőjét, adja meg, hogy a tesztkörnyezet alkotói ki tudják-e választani a labor helyét. Alapértelmezés szerint a beállítás le van tiltva. Ha le van tiltva, a tesztkörnyezet alkotói nem adhatnak meg helyet a létrehozott tesztkörnyezetnek. A laborok a laborfiókhoz legközelebbi földrajzi helyen jönnek létre. Ha engedélyezve van, a tesztkörnyezet létrehozója kiválaszthat egy helyet a tesztkörnyezet létrehozásakor. További információ: [A labor létrehozója kiválaszthatja a labor helyét.](allow-lab-creator-pick-lab-location.md) 

        ![Laborfiók létrehozása -> alapjai](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Válassza a **Tovább: Speciális** lehetőséget a lap alján a **Speciális** lapra való navigáláshoz, majd hajtsa végre az alábbi lépéseket: 
    1. Jelöljön ki egy meglévő **megosztott képgalériát,** vagy hozzon létre egyet. Mentheti a sablon virtuális gép a megosztott képtárba, hogy azt újra mások is. A megosztott képgalériákról az [Azure Lab Services megosztott képgalériájának használata](how-to-use-shared-image-gallery.md)című témakörben talál részletes információt.
    2. Adja meg, hogy automatikusan le szeretné-e állítani a **Windows virtuális gépeket,** amikor a felhasználók bontják a kapcsolatot velük. Adja meg, hogy a virtuális gépeknek mennyi ideig kell várniuk a felhasználó újracsatlakozására, mielőtt automatikusan leállna. 
    3. A **társ virtuális hálózat,** válasszon egy társ virtuális hálózat (VNet) a labor hálózathoz. Ebben a fiókban létrehozott laborok csatlakoznak a kiválasztott virtuális hálózathoz, és hozzáférhetnek a kiválasztott virtuális hálózat erőforrásaihoz. További információ: [A tesztkörnyezet virtuális hálózatának csatlakoztatása társvirtuális hálózattal.](how-to-connect-peer-virtual-network.md)    
    8. Adja meg a tesztkörnyezetben lévő virtuális gépek **címtartományát.** A címtartománynak az osztály nélküli tartományok közötti útválasztás (CIDR) jelölésében kell lennie (például: 10.20.0.0/23). A tesztkörnyezetben lévő virtuális gépek ebben a címtartományban jönnek létre. További [információ: Címtartomány megadása a tesztkörnyezetben lévő virtuális gépekhez.](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Laborfiók létrehozása -> Speciális](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Válassza a **Tovább: Címkék** a lap alján váltani a **Címkék** lapra. Adja hozzá a címkéket szeretne társítani a labor fiókkal. A címkék olyan név-/értékpárok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoportra alkalmazza. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](../../azure-resource-manager/management/tag-resources.md)

    ![Laborfiók létrehozása -> címkék](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Válassza a Lap alján található **Véleményezés + létrehozás** lehetőséget, ha át szeretne váltani a **Véleményezés + létrehozás** lapra. 
4. Tekintse át a lapon található összegző információkat, és válassza a **Létrehozás gombot.** 

    ![Laborfiók létrehozása -> címkék](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Várjon, amíg a központi telepítés befejeződik, bontsa ki a **Következő lépéseket**, és válassza az Ugrás **az erőforrásra** lehetőséget az alábbi képen látható módon: 

    Az eszköztáron a **csengő ikonját** is kiválaszthatja (**Értesítések**), ellenőrizheti, hogy a telepítés sikeres volt-e, majd válassza az Ugrás az **erőforrásra**lehetőséget. 

    Másik lehetőségként válassza a **Frissítés** lehetőséget a **Laborfiókok** lapon, és válassza ki a létrehozott tesztkörnyezet-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Laborfiókok megtekintése
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a menü **Minden erőforrás elemét.** 
3. Válassza a **típus**hoz a **Lab-fiókok lehetőséget.** 
    Előfizetés, erőforráscsoport, helyek és címkék szerint is szűrhet. 

    ![Minden erőforrás -> Labor-fiókok](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Laborok megtekintése és kezelése a laborfiókban

1. A **Laborfiók** lapon válassza a bal oldali menü **Minden laborja** lehetőséget.

    ![Laborok a fiókban](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. A fiókban a következő adatokat tartalmazó **laborok listája** látható: 
    1. A labor neve.
    2. A labor létrehozásának dátuma. 
    3. A labort létrehozó felhasználó e-mail címe. 
    4. A laborba beengedett felhasználók maximális száma. 
    5. A labor állapota. 
    6. Szerepkör-hozzárendelések. 

## <a name="delete-a-lab-in-the-lab-account"></a>Tesztkörnyezet törlése a laborfiókban
Kövesse az előző szakaszban található utasításokat a laborok listájának megtekintéséhez a laborfiókban.

1. Válassza ki **a ... (három ponttal) gombra,** és válassza a **Törlés gombot.** 

    ![Labor törlése - gomb](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. A figyelmeztető üzenetben válassza az **Igen** lehetőséget. 

    ![A labor törlésének megerősítése](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Tesztkörnyezet-fiók törlése
Kövesse az előző szakasz utasításait, amelyek a tesztkörnyezet-fiókokat jelenítik meg egy listában. A tesztkörnyezet-fiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a törölni kívánt **tesztkörnyezet-fiókot.** 
2. Válassza a **Törlés** gombot az eszköztáron. 

    ![Laborfiókok -> Törlés gomb](../media/how-to-manage-lab-accounts/delete-button.png)
1. A megerősítéshez írja be az **Igen** típust.
1. Válassza a **Törlés** elemet. 

    ![Laborfiók törlése - megerősítés](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Az.LabServices PowerShell-modul (előzetes verzió) segítségével is kezelheti a laborfiókokat. További információt az [Az.LabServices kezdőlapján talál a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának csatlakoztatása egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
- [Tesztkörnyezet egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)