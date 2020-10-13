---
title: Első lépések és Azure Lab Services labor létrehozása csapatokból
description: Megtudhatja, hogyan kezdheti el és hozhat létre Azure Lab Services laborokat a csapatokból.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946662"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból

Ez a cikk bemutatja, hogyan adhat hozzá egy Azure Lab Services alkalmazást a csapatokhoz. Ezután hozzon létre egy labort a csapatokból.

## <a name="add-a-lab-services-app-to-teams"></a>Labor Services-alkalmazás hozzáadása a csapatokhoz

A labor-szolgáltatásokat közvetlenül a csapatok csatornájában is hozzáadhatja, majd az alkalmazás a csapatban mindenki számára elérhető lesz. Kövesse az alábbi három lépést:

1. Navigáljon a csapatok csatornához, ahová hozzá kívánja adni az alkalmazást, és válassza ki a **+** fület a "..." gombra kattintva. a jobb oldali ablak elejétől. 
1. Keresse meg **Azure Lab Services** a lap beállításai között, és adja hozzá az alkalmazást. 

    > [!NOTE]
    > Csak a csapat **tulajdonosai** hozhatnak létre Labs-t a csapat számára.
1. Válassza ki a labor Services-fiókot, amelyet az osztályterem Labs létrehozásához szeretne használni ebben a csapatban. 

    Azure Lab Services egyszeri bejelentkezést használ a [Azure Lab Services webhelyre](https://labs.azure.com) , és lekéri az összes olyan labor-fiókot, amelyhez hozzáféréssel rendelkezik. 

    Azok a fiókok, amelyek ugyanabban a bérlőben találhatók, mint a csapatok, és amelyek **tulajdonosa**, **közreműködői**vagy **létrehozói** hozzáférése van jelen. 

   ![Üdvözöljük az ALS-ben](./media/integrate-with-teams/welcome.png) 
1. Nyomja meg a **Save (Mentés** ) gombot, és az alkalmazás bekerül a csapatokhoz, és a lap bekerül a csatornába. 

    Most kiválaszthatja a csatorna **Azure Lab Services** lapját, és megkezdheti a laborok kezelését a következő lépésben leírtak szerint.

    Ha egy csapat tagja felveszi a fület, az megjelenik a csatornán mindenki számára. Az alkalmazáshoz hozzáféréssel rendelkező felhasználók egyszeri bejelentkezéses hozzáférést kapnak a Microsoft Teams szolgáltatáshoz használt hitelesítő adatokhoz. Azok a felhasználók, akik nem rendelkeznek hozzáféréssel az alkalmazáshoz, megtekinthetik a csapat lapjait, de csak akkor vannak letiltva, ha nem ad nekik engedélyeket a helyszíni alkalmazáshoz és az alkalmazás Azure Portal közzétett verziójához.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

A labor-fiók kiválasztása után a csapat tulajdonosai létrehozhatnak Labs-t a csapat számára. A teljes tesztkörnyezet-létrehozási folyamat és a labor szinten található összes feladat elvégezhető a csapatokon belül. A felhasználók több labort is létrehozhatnak ugyanazon a csapaton belül, és a csapat tulajdonosa, a megfelelő hozzáféréssel a labor fiók szintjén, csak az adott csapathoz társított Labs-t fogja látni.

## <a name="giving-access-to-users-of-the-lab-account"></a>Hozzáférés biztosítása a labor-fiók felhasználói számára

Az [Azure](https://ms.portal.azure.com/) Portalon kell kiépíteni a felhasználók hozzáférését a labor fiók szintjén.

1. A Azure Portal navigáljon a Azure Lab Services-fiókjához. 
1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés**lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Tantermi laborok létrehozása

A tantermi Labs-létrehozási folyamat ugyanaz, hogy a laborokat a csapatokból vagy a [labor Services webhelyéről](https://labs.azure.com)hozza létre. 

Ebben a cikkben a vázlat Labs létrehozási folyamata a következő témakörben található: az [osztályterem Labs kezelése Azure Lab Servicesban](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Tantermi laborok törlése

A Teams-ben létrehozott laborokat a labor [Services webhelyén](https://labs.azure.com) törölheti, ha közvetlenül törli a labort a [Azure Lab Servicesban](how-to-manage-classroom-labs.md). 

A labor törlése a csapat törlésekor is aktiválódik. Ha az a csapat, amelyben a labor létrejött, törölve lesz, a labor automatikusan törlődik 24 órával az automatikus felhasználói lista szinkronizálásának elindítása után. 

A lap törlése vagy az alkalmazás eltávolítása nem eredményezi a labor törlését. Ha a lap törölve van, akkor a csoport tagsága listán szereplő felhasználók továbbra is hozzáférhetnek a virtuális gépekhez a [labor Services webhelyén](https://labs.azure.com) , kivéve, ha a labor törlését explicit módon aktiválja a tesztkörnyezet törlésével vagy a csapat törlésével. 

## <a name="next-steps"></a>További lépések

Ha labort hoz létre a csapatokon belül, a rendszer automatikusan kitölti és szinkronizálja a labor felhasználói listáját a csoport tagságával. A csapat minden tagja, beleértve a tulajdonosokat, a tagokat és a vendégeket is, automatikusan hozzáadja a labor felhasználói listához. Az Azure Lab Services 24 óránként szinkronizálja a csoport tagságát, és automatikus szinkronizálást indít el. Részletes információ:

[A labor Services felhasználói listájainak kezelése a csapatokból](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Lásd még

Lásd még a következő cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Virtuálisgép-készlet kezelése a labor Servicesben a csapatoktól](how-to-manage-vm-pool-within-teams.md)
- [Labor Services-ütemtervek létrehozása a csapatokból](how-to-create-schedules-within-teams.md)
- [Hozzáférés egy virtuális géphez (Student View) a labor Servicesben a csapatoktól](how-to-access-vm-for-students-within-teams.md)

