---
title: A Windows rendszerű virtuális asztali portál alkalmazás-csoportjainak kezelése – Azure
description: Windows rendszerű virtuális asztali alkalmazás-csoportok kezelése a Azure Portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5b0269b80cb879923f9f00861494915b6abe103e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283401"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Oktatóanyag: alkalmazás-csoportok kezelése a Azure Portal

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Az új Windows rendszerű virtuális asztali készlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes Start menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

Ha már létrehozott egy gazdagép-készletet és egy munkamenet-gazdagépet a Azure Portal vagy a PowerShell használatával, az alábbi eljárással adhat hozzá alkalmazás-csoportokat a Azure Portalból:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2.  Keresse meg és válassza ki a **Windows virtuális asztal**elemet.

3.  Az oldal bal oldalán található menüben válassza ki az **alkalmazáscsoport** elemet, majd válassza a **+ Hozzáadás**lehetőséget.

4. Az **alapvető beállítások** lapon válassza ki azt az előfizetési csoportot és erőforráscsoportot, amelyhez az alkalmazás-csoportot létre kívánja hozni. Azt is megteheti, hogy új erőforráscsoportot hoz létre, és nem választ ki egy meglévőt.

5. Válassza ki azt a gazdagépet, amely az alkalmazáskészlet melletti legördülő menüből lesz társítva az alkalmazás **csoportjához.**

    >[!NOTE]
    >Ki kell választania az alkalmazáscsoport társított készletét. Az alkalmazáscsoport olyan alkalmazásokkal vagy asztali számítógépekkel rendelkezik, amelyek egy munkamenet-gazdagépről származnak, és a munkamenet-gazdagépek a gazdagépek részét képezik. A létrehozás során az alkalmazás csoportjának társítania kell egy gazdagép-készletet.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Azure Portal alapjai lapról.](media/basics-tab.png)

6. Ha az alkalmazás-csoportokat hozzá szeretné adni a gazdagéphez, a képernyő bal oldalán található menüben válassza a **gazdagép-készletek** lehetőséget.
   
    Ezután válassza ki annak az állomásnak a nevét, amelyhez alkalmazáscsoport hozzáadására van szüksége.
   
    Ezután válassza ki az **alkalmazáscsoport** elemet a képernyő bal oldalán található menüből, majd válassza a **+ Hozzáadás**lehetőséget.

    Végül válassza ki azt az előfizetési csoportot és erőforráscsoportot, amelyben az alkalmazás-csoportot létre szeretné hozni. Kiválaszthatja egy meglévő erőforráscsoport nevét a legördülő menüből, vagy az **új létrehozása** lehetőségre kattintva újat hozhat létre.

      >[!NOTE]
      >Amikor alkalmazás-csoportokat ad hozzá a gazdagéphez, az alkalmazáscsoport kapcsolódó alkalmazáskészlete már ki van választva, mert az alkalmazásból navigált.
      > 
      > [!div class="mx-imgBorder"]
      >![Az alapbeállítások lap képernyőképe az előválasztott gazdagép készletével.](media/host-pool-selected.png)

7. Válassza ki a **RemoteApp** elemet az alkalmazáscsoport típusa területen, majd adja meg a RemoteApp nevét.

      > [!div class="mx-imgBorder"]
      > ![Az alkalmazáscsoport típusú mezők képernyőképe A "RemoteApp" ki van emelve.](media/remoteapp-button.png)

8.  Válassza a **hozzárendelések** fület.

9.  Ha egyéni felhasználókat vagy felhasználói csoportokat szeretne közzétenni az alkalmazás csoportjában, válassza az **+ Azure ad-felhasználók vagy felhasználói csoportok hozzáadása**lehetőséget.

10.  Válassza ki, hogy hány felhasználóhoz kívánja hozzáadni az alkalmazásokat. Kiválaszthat egy vagy több felhasználót és felhasználói csoportot is.

     > [!div class="mx-imgBorder"]
     > ![A felhasználó kiválasztási menüjének képernyőképe.](media/select-users.png)

11.  Válassza a **Kiválasztás** lehetőséget.

12.  Válassza az **alkalmazások** fület, majd válassza az **+ Alkalmazások hozzáadása**lehetőséget.

13.  Alkalmazás hozzáadása a Start menüből: 

      - Nyissa meg az **alkalmazás forrását** , és válassza a **Start menü** lehetőséget a legördülő menüből. Ezután nyissa meg az **alkalmazást** , és válassza ki az alkalmazást a legördülő menüből.

     > [!div class="mx-imgBorder"]
     > ![Képernyőkép az alkalmazás hozzáadása képernyőről a Start menü kiválasztásával.](media/add-app-start.png)

      - A **megjelenítendő név**mezőbe írja be annak az alkalmazásnak a nevét, amely megjelenik a felhasználó számára az ügyfélen.

      - Hagyja meg a többi beállítást, és válassza a **Mentés**lehetőséget.

14. Alkalmazás hozzáadása a fájl megadott elérési útjáról:

      - Nyissa meg az **alkalmazás forrását** , és válassza a **fájl elérési útja** elemet a legördülő menüből.

      - Adja meg az alkalmazás elérési útját a munkamenet-gazdagépen, amely regisztrálva van a társított gazdagép-készletben.

      - Adja meg az alkalmazás adatait az **alkalmazás neve**, a **megjelenítendő név**, az **ikon elérési útja**és az **ikon index** mezőiben.

      - Válassza a **Mentés** lehetőséget.

     > [!div class="mx-imgBorder"]
     > ![Az alkalmazás hozzáadása oldal képernyőképe a kijelölt fájl elérési útjával.](media/add-app-file.png)

     Ismételje meg ezt a folyamatot minden olyan alkalmazás esetében, amelyet hozzá szeretne adni az alkalmazás csoportjához.

15.  Ezután válassza a **munkaterület** lapot.

16.  Ha egy munkaterületre szeretné regisztrálni az erőforráscsoportot, lépjen az **alkalmazáscsoport regisztrálása** elemre, és válassza az **Igen**lehetőséget. Ha inkább egy későbbi időpontban regisztrálja az alkalmazást, válassza a **nem**lehetőséget.

17.  Ha az **Igen**lehetőséget választja, kiválaszthat egy meglévő munkaterületet, amellyel regisztrálhatja az alkalmazás csoportját.
       
       >[!NOTE]
       >Az alkalmazáscsoport csak abban a helyen létrehozott munkaterületekre regisztrálható, ahol a gazdagép található. Is. Ha korábban már regisztrált egy másik alkalmazást ugyanabból a készletből, mint az új alkalmazáscsoport egy munkaterületre, akkor azt a rendszer kijelöli, és nem szerkesztheti. Egy adott alkalmazáskészletben lévő összes alkalmazás-csoportot regisztrálni kell ugyanahhoz a munkaterülethez.

     > [!div class="mx-imgBorder"]
     > ![Egy már meglévő munkaterület alkalmazás-csoportjának regisztrálása lapjának képernyőképe. Az alkalmazáskészletet a rendszer előjelöli.](media/register-existing.png)

18. Ha címkéket szeretne létrehozni a munkaterület rendszerezéséhez, válassza a **címkék** lapot, és adja meg a címke nevét.

19. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lapot.

20. Várjon egy kicsit, hogy az érvényesítési folyamat befejeződjön. Ha elkészült, válassza a **Létrehozás** lehetőséget az alkalmazás csoportjának telepítéséhez.

Az üzembe helyezési folyamat a következő műveleteket végzi el:

- Hozza létre a RemoteApp-alkalmazás csoportot.
- Adja hozzá a kiválasztott alkalmazásokat az alkalmazás csoportjához.
- Tegye közzé az alkalmazáscsoport közzétételét a kiválasztott felhasználók és felhasználói csoportok számára.
- Ha ezt a lehetőséget választja, regisztrálja az alkalmazás csoportját.
- Hozzon létre egy Azure Resource Manager-sablonra mutató hivatkozást, amely a később letölthető és menthető konfiguráció alapján érhető el.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoport, hogyan tölthető be a RemoteApp-programokkal, és hogyan rendelhet hozzá felhasználókat az alkalmazás-csoporthoz. Az érvényesítési gazdagépek létrehozásáról a következő oktatóanyagban olvashat bővebben. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása a szolgáltatásfrissítések érvényesítéséhez](./create-validation-host-pool.md)
