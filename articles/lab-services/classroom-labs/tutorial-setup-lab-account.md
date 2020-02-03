---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be labor-fiókot Azure Lab Serviceskal, hozzáadhat egy labor-létrehozót, és megadhatja a Labs által a labor-fiókban használandó Piactéri lemezképeket.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 62ccda96563a53ebc77337bb2f89cfddd4410a26
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719041"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebben az oktatóanyagban megismerheti, hogyan hozhat létre tesztkörnyezetfiókot tesztkörnyezet-rendszergazdaként. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
> * A tesztkörnyezet-tulajdonosok számára elérhető rendszerképek megadása a Marketplace-en

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza a **labor Services** elemet a **DEVOPS** szakaszban. Ha a **Lab szolgáltatások**mellett a csillag (`*`) lehetőséget választja, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **Lab szolgáltatások** lehetőséget a **Kedvencek**alatt.

    ![Minden szolgáltatás – > labor Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **labor Services** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron. 

    ![Válassza a Hozzáadás lehetőséget a labor-fiókok oldalon](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **labor-fiók** oldalon hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Válasszon ki egy meglévő **megosztott képtárat** , vagy hozzon létre egyet. A sablon virtuális gépet a megosztott rendszerkép-katalógusba mentheti, amelyet mások is felhasználhatnak. A megosztott képtárakkal kapcsolatos részletes információkért lásd: [közös rendszerkép-katalógus használata Azure Lab Servicesban](how-to-use-shared-image-gallery.md). 
    6. A **társ virtuális hálózat**területen válasszon ki egy társ virtuális hálózatot (VNet) a tesztkörnyezet hálózatához. A fiókban létrehozott laborok kapcsolódnak a kiválasztott VNet, és hozzáférnek a kiválasztott VNet lévő erőforrásokhoz. 
    7. Válasszon **címtartományt** a virtuális gépek számára a laborban. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva. További információ: [címtartomány megadása virtuális gépek számára a laborban](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).
    8. Ahhoz, hogy a labor létrehozója kiválassza a **labor helye** mezőt, adja meg, hogy szeretné-e, ha a labor készítői ki tudják választani a tesztkörnyezet helyét. Alapértelmezés szerint a beállítás le van tiltva. Ha le van tiltva, a labor létrehozói nem tudnak megadni egy helyet a létrehozandó labornak. A laborok a legközelebbi földrajzi helyen a labor-fiókhoz jönnek létre. Ha engedélyezve van, a tesztkörnyezet létrehozója kiválaszthatja a kívánt helyet a labor létrehozásakor. 
    9. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Válassza a **harang ikont** az eszköztáron (**értesítések**), ellenőrizze, hogy az üzembe helyezés sikeres volt-e, majd válassza az **erőforrás keresése**lehetőséget. 

    Másik lehetőségként válassza a **labor-fiókok** lapon a **frissítés** lehetőséget, majd válassza ki a létrehozott labor-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés**lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>A tesztkörnyezet-készítők számára elérhető Piactéri lemezképek meghatározása
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

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha szeretne többet megtudni arról, hogyan hozhat létre egy tantermi labort professzorként, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

