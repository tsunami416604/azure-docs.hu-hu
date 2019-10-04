---
title: Tesztkörnyezetfiókok konfigurálása az Azure Lab Services |} A Microsoft Docs
description: Útmutató a létrehozást követően tesztkörnyezetfiók konfigurálandó.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414042"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Tesztkörnyezetfiókok konfigurálása az Azure Lab Services 
Az Azure Lab Services tesztkörnyezetfiók az felügyelt tesztkörnyezet-típusok, például osztályterem-tesztkörnyezetek tárolója. A rendszergazda állítja be az Azure Lab Services tesztkörnyezetfiók és labortulajdonosok labs hozhat létre a fiók számára hozzáférést biztosít. Ez a cikk bemutatja, hogyan tesztkörnyezetfiók létrehozása, megtekintheti az összes tesztkörnyezetfiókok, vagy törölheti a labor.

## <a name="connect-with-a-peer-virtual-network"></a>A társ virtuális hálózat összekapcsolása
Virtuális hálózat összekapcsolása egy társ hálózatként a labor virtuális hálózathoz, kövesse az alábbi lépéseket:

1. Az a **labor fiók** lapon jelölje be **Labs konfigurációs** a bal oldali menüben.

    ![Laborok konfigurációs lapja](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. A **társ virtuális hálózatnak**válassza **engedélyezve** vagy **letiltott**. Az alapértelmezett érték **letiltott**. Ahhoz, hogy a társ virtuális hálózatnak, tegye a következőket: 
    1. Válassza ki **engedélyezve**.
    2. Válassza ki a **VNet** a legördülő listából. 
3. Válassza az eszköztár **Save** (Mentés) elemét. 

Ehhez a fiókhoz a létrehozott Labs csatlakozik a kiválasztott virtuális hálózaton. A kiválasztott virtuális hálózaton lévő erőforrások eléréséhez. További információkért lásd: [a tesztkörnyezet hálózati csatlakozás és a társ virtuális hálózat az Azure Lab Services](how-to-connect-peer-virtual-network.md).

Amikor kiválaszt egy virtuális hálózatot a **társ virtuális hálózatnak** mező, a **engedélyezése tesztkörnyezet létrehozója, labor helyre** lehetőség le van tiltva. Fontos, mivel labs labor-fiókban, hogy a társ virtuális hálózatnak az erőforrásokhoz való kapcsolódás labor fiókként ugyanabban a régióban kell lennie. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Tesztkörnyezet létrehozója a tesztkörnyezethez helyre való engedélyezése
Tesztkörnyezet létrehozója, a tesztkörnyezetek létrehozása a lab-fiók helye eltérő helyre a következő lépésekkel engedélyezheti: 

1. Az a **labor fiók** lapon jelölje be **Labs konfigurációs** a bal oldali menüben.
2. Az a **engedélyezése tesztkörnyezet létrehozója, labor helyre**, jelölje be **engedélyezve** Ha azt szeretné, hogy a tesztkörnyezet létrehozója tudják válasszon ki egy helyet a tesztkörnyezethez. Ha le van tiltva, a rendszer automatikusan létrehozza a labs ugyanazon a helyen, amelyben a labor fiók létezik. 
    
    Ez a mező le van tiltva, a virtuális hálózat kiválasztásakor a **társ virtuális hálózatnak** mező. Fontos, mivel labs labor-fiókban, hogy a társ virtuális hálózaton lévő erőforrások eléréséhez a labor fiókként ugyanabban a régióban kell lennie. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Hely laboratóriumban konfigurálása](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>-Címtartományt ad meg a virtuális gépek a lab-ben
Az alábbi eljárás lépéseit-címtartományt ad meg a virtuális gépek a laborban rendelkezik. A korábban megadott tartományt frissít, a módosított címtartomány csak vonatkozik a módosítás után létrehozott virtuális gépeket. 

A címtartományt, amely szem előtt kell tartania megadásakor az alábbiakban néhány korlátozás. 

- Az előtag maximális megengedett értéke – 23 kell lennie. 
- Ha egy virtuális hálózat társviszonyban áll a labor-fiókjába, a megadott címtartomány társviszonyban lévő virtuális hálózati címtartományt, nem lehet átfedésben.

1. Az a **labor fiók** lapon jelölje be **Labs konfigurációs** a bal oldali menüben.
2. Az a **címtartomány** mezőben adja meg a címtartományt a virtuális gépek, a laborban létrehozni. A címtartomány a classless inter-domain routing (CIDR) formátumban kell lennie (Példa: 10.20.0.0/23). A lab-ben a virtuális gépek jönnek a címtartományt.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Cím-tartomány konfigurálása](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. A a **labor fiók** lapon válassza ki **hozzáférés-vezérlés (IAM)** , és kattintson a **+ szerepkör-hozzárendelés hozzáadása** az eszköztáron. 

    ![Hozzáférés-vezérlés -> szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Az a **szerepkör-hozzárendelés hozzáadása** lapon jelölje be **tesztkörnyezet létrehozója** a **szerepkör**, válassza ki a felhasználót, adja hozzá a labor létrehozó szerepkörhöz, és válassza ki a kívánt **mentése**. 

    ![Tesztkörnyezet létrehozója hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Adja meg a Marketplace-rendszerképek labor létrehozói számára elérhető
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

1. Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 
    
    ![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
        
    - Egyetlen virtuális gépet hoznak létre.
    - A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
    - Nincs szükség külön licenccsomag vásárlására.
2. Egy engedélyezett Marketplace-beli rendszerkép **letiltásának** a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)** , majd a **Rendszerkép letiltása** lehetőségre. 

        ![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek letiltása** lehetőségre. 

        ![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. A letiltott Marketplace-beli rendszerképek **engedélyezésének** a fentiekhez hasonlóan a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)** , majd a **Rendszerkép engedélyezése** lehetőségre. 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek engedélyezése** lehetőségre. 




## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)
