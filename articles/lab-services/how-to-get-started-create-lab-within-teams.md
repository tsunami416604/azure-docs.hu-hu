---
title: Első lépések és Azure Lab Services labor létrehozása csapatokból
description: Megtudhatja, hogyan kezdheti el és hozhat létre Azure Lab Services laborokat a csapatokból.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 0604e2934ff6b011acfa9dd4a4b25fa58193e69b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044445"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból

Ez a cikk bemutatja, hogyan adhatja hozzá a **Azure Lab Services** alkalmazást egy csoporthoz, majd hogyan hozhat létre labort az MS Teams-környezetben.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban egy labort állít be a csapatához tartozó virtuális gépekkel. Ha labor-fiókban szeretné beállítani a labort, akkor az egyik szerepkör tagjának kell lennie a labor-fiókban: tulajdonos, labor létrehozó vagy közreműködő. A rendszer automatikusan hozzáadja a tulajdonosi szerepkörhöz a labor-fiók létrehozásához használt fiókot. Tehát használhatja azt a felhasználói fiókot, amelyet a labor-fiók létrehozásához használt a tesztkörnyezet létrehozásához.

A Azure Lab Services a csapaton belül a tipikus munkafolyamat

1. A felhasználó [létrehoz egy Lab-fiókot](tutorial-setup-lab-account.md#create-a-lab-account) a Azure Portal.
1. A [labor-fiók létrehozója más felhasználókat is felvesz](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) a **labor létrehozói** szerepkörbe. Például a labor-fiók létrehozója/rendszergazdája felveszi az oktatókat a **labor létrehozói** szerepkörbe, hogy a laborokat a saját osztályaik számára is létrehozzák.
1. Ezután a pedagógusok létrehoznak laborokat, előre konfigurálják a sablon virtuális gépet, és közzéteszik a labort, hogy a virtuális gép mindenki számára elérhető legyen a csapatban.
1. Miután a labor közzé lett téve, egy virtuális gépet rendelnek mindenkihöz a csoporttagság listán az első bejelentkezéskor Azure Lab Servicesre, vagy kattintson a Teams (SSO) **Azure Lab Services** alkalmazást tartalmazó lapra, vagy a [Labs webhelyének](https://labs.azure.com)elérésére. A felhasználók ezt követően a virtuális gép használatával elvégezhetik az osztály munkahelyi és munkahelyi feladatait.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Azure Lab Services alkalmazás hozzáadása egy csapathoz lap

Csapatának tulajdonosaként közvetlenül is hozzáadhat **Azure Lab Services** alkalmazást a csapatok csatornájában, majd az alkalmazás a csapatban mindenki számára elérhető lesz. Kövesse az alábbi három lépést:

1. Navigáljon a csapatok csatornához, ahová hozzá kívánja adni az alkalmazást, és válassza ki a **+** lapot. 
1. Keresse meg **Azure Lab Services** a lap beállításai között, és adja hozzá az alkalmazást. 

    > [!NOTE]
    > Csak a csapat **tulajdonosai** hozhatnak létre Labs-t a csapat számára.
1. Válassza ki a labor Services-fiókot, amelyet az osztályterem Labs létrehozásához szeretne használni ebben a csapatban. 

    Azure Lab Services egyszeri bejelentkezést használ a [Azure Lab Services webhelyre](https://labs.azure.com) , és lekéri az összes olyan labor-fiókot, amelyhez hozzáféréssel rendelkezik. 

    Azok a fiókok, amelyek ugyanabban a bérlőben találhatók, mint a csapatok, és amelyek **tulajdonosa**, **közreműködői**vagy **létrehozói** hozzáférése van jelen. 

   ![Üdvözöljük az ALS-ben](./media/integrate-with-teams/welcome.png) 
1. Nyomja meg a **Save (Mentés** ) gombot, és a lap bekerül a csatornába.

    Most kiválaszthatja a csatorna **Azure Lab Services** lapját, és megkezdheti a laborok kezelését a következő lépésben leírtak szerint.

A labor-fiók kiválasztása után a csapat tulajdonosai létrehozhatnak Labs-t a csapat számára. A teljes tesztkörnyezet-létrehozási folyamat és a labor szinten található összes feladat elvégezhető a csapatokon belül. A felhasználók több labort is létrehozhatnak ugyanazon a csapaton belül, és a csapat tulajdonosa, a megfelelő hozzáféréssel a labor fiók szintjén, csak az adott csapathoz társított Labs-t fogja látni.

## <a name="deleting-classroom-labs"></a>Tantermi laborok törlése

A Teams-ben létrehozott laborokat a labor [Services webhelyén](https://labs.azure.com) törölheti, ha közvetlenül törli a labort a [Azure Lab Servicesban](how-to-manage-classroom-labs.md). 

A labor törlése a csapat törlésekor is aktiválódik. Ha az a csapat, amelyben a labor létrejött, törölve lesz, a labor automatikusan törlődik 24 órával az automatikus felhasználói lista szinkronizálásának elindítása után. 

A lap törlése vagy az alkalmazás eltávolítása nem eredményezi a labor törlését. Ha a lap törölve van, akkor a csoport tagsága listán szereplő felhasználók továbbra is hozzáférhetnek a virtuális gépekhez a [labor Services webhelyén](https://labs.azure.com) , kivéve, ha a labor törlését explicit módon aktiválja a tesztkörnyezet törlésével vagy a csapat törlésével. 

## <a name="next-steps"></a>Következő lépések

Ha labort hoz létre a csapatokon belül, a rendszer automatikusan kitölti és szinkronizálja a labor felhasználói listáját a csoport tagságával. A csapat minden tagja, beleértve a tulajdonosokat, a tagokat és a vendégeket is, automatikusan hozzáadja a labor felhasználói listához. Az Azure Lab Services 24 óránként szinkronizálja a csoport tagságát, és automatikus szinkronizálást indít el. Részletes információ:

[A labor Services felhasználói listájainak kezelése a csapatokból](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Lásd még

Lásd még a következő cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Tesztkörnyezet felhasználói listájainak kezelése a csapatokon belül](how-to-manage-user-lists-within-teams.md)
- [A labor virtuálisgép-készletének kezelése a csapatokon belül](how-to-manage-vm-pool-within-teams.md)
- [Labor-ütemtervek létrehozása és kezelése a csapatokon belül](how-to-create-schedules-within-teams.md)
- [Hozzáférés a csapatokon belüli virtuális gépekhez – tanuló nézet](how-to-access-vm-for-students-within-teams.md)

