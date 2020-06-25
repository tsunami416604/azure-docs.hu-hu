---
title: Nyelvi csomagok telepítése Windows 10 rendszerű virtuális gépekre a Windows Virtual Desktopban – Azure
description: Windows 10 rendszerű, többmunkamenetes virtuális gépek nyelvi csomagjainak telepítése a Windows Virtual Desktopban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361302"
---
# <a name="install-language-packs"></a>Nyelvi csomagok telepítése

A Windows rendszerű virtuális asztali környezetek nemzetközileg történő telepítése során érdemes meggyőződni arról, hogy a központi telepítés több nyelvet is támogat. A nyelvi csomagokat Windows 10 Enterprise rendszerű, többmunkamenetes virtuálisgép-(VM-) rendszerképre is telepítheti, hogy a szervezet igényeinek megfelelő számú nyelvet támogassa. Ebből a cikkből megtudhatja, hogyan telepíthet nyelvi csomagokat és rögzíthet lemezképeket, amelyek lehetővé teszik, hogy a felhasználók saját megjelenítési nyelveket válasszanak.

További információ a virtuális gépek Azure-beli üzembe helyezéséről [a Windows rendszerű virtuális gép létrehozása rendelkezésre állási zónában a Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Ez a cikk a Windows 10 Enterprise rendszerű, több munkamenetes virtuális gépekre vonatkozik.

## <a name="install-a-language-pack"></a>Nyelvi csomag telepítése

Ha nyelvi csomagokat tartalmazó virtuálisgép-lemezképet szeretne létrehozni, először telepítenie kell a nyelvi csomagokat a gépre, és rögzítenie kell a lemezképet.

Nyelvi csomagok telepítése:

1. Jelentkezzen be rendszergazdaként.
2. Győződjön meg arról, hogy telepítette a Windows és a Windows áruház legújabb frissítéseit.
3. Lépjen a **Beállítások**  >  **ideje & nyelvi**  >  **régió**menüpontra.
4. Az **ország vagy régió**területen válassza ki az előnyben részesített országot vagy régiót a legördülő menüből.
    Ebben a példában a **franciaországi**lehetőséget fogjuk kiválasztani, ahogy az alábbi képernyőképen is látható:

    > [!div class="mx-imgBorder"]
    > ![A régió oldalának képernyőképe. A jelenleg kiválasztott régió Franciaország.](media/region-page-france.png)

5. Ezután válassza a **Language (nyelv**), majd **a nyelv hozzáadása**lehetőséget. Válassza ki a listából a telepíteni kívánt nyelvet, majd kattintson a **tovább**gombra.
6. Amikor megnyílik a **nyelvi szolgáltatások telepítése** ablak, jelölje be a **nyelvi csomag telepítése és beállítása a Windows megjelenítési nyelvként**jelölőnégyzetet.
7. Válassza az **Install** (Telepítés) lehetőséget.
8. Ha egyszerre több nyelvet szeretne felvenni, válassza a **nyelv hozzáadása**lehetőséget, majd ismételje meg a folyamatot, és adjon hozzá egy nyelvet az 5. és 6. lépésben. Ismételje meg ezt a folyamatot minden telepíteni kívánt nyelvnél. Egyszerre azonban csak egy nyelvet állíthat be megjelenítési nyelvként.

    Futtassunk egy gyors vizuális bemutatót. Az alábbi képek bemutatják, hogyan telepítheti a francia és holland nyelvi csomagokat, majd beállíthatja a franciát megjelenítési nyelvként.

    > [!div class="mx-imgBorder"]
    > ![A folyamat elején található nyelvi oldal képernyőképe. A kiválasztott Windows megjelenítési nyelv az angol.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![A nyelv kiválasztása ablak képernyőképe. A felhasználó a francia nyelvi csomagok megtalálásához a "francia" kifejezést adta meg a keresősávban.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![A nyelvi szolgáltatások telepítési oldalának képernyőképe. A franciát választott nyelvként válassza ki. A kiválasztott beállítások a "saját megjelenítési nyelv beállítása", "nyelvi csomag telepítése", "beszédfelismerés" és "kézírás".](media/install-language-features.png)

    Miután telepítette a nyelvi csomagokat, megjelenik a nyelvi csomagok neve a nyelvek listájában.

    > [!div class="mx-imgBorder"]
    > ![A nyelvi oldal képernyőképe a telepített új nyelvi csomagokkal. A francia és a holland nyelvi csomagok az "előnyben részesített nyelvek" területen jelennek meg.](media/language-page-complete.png)

9. Ha megjelenik egy ablak, amely arra kéri, hogy jelentkezzen ki a munkamenetből. Jelentkezzen ki, majd jelentkezzen be újra. A megjelenítési nyelvnek most a kiválasztott nyelvnek kell lennie.

10.  Nyissa meg a **Vezérlőpult**  >  **óra és régió**  >  **régióját**.

11.  Amikor megnyílik a **régió** ablak, válassza az **Adminisztráció** fület, majd válassza a **Beállítások másolása**lehetőséget.

12.  Jelölje be az **üdvözlő képernyő és a rendszerfiókok** és az **új felhasználói fiókok**jelölőnégyzetet.

13.  Válassza az **OK** lehetőséget.

14.  Ekkor megnyílik egy ablak, és megtudhatja, hogy újraindítsa a munkamenetet. Válassza az **Újraindítás most**lehetőséget.

15.  Miután bejelentkezett, lépjen vissza a **Vezérlőpult**  >  **óra és régió**  >  **területére**.

16.  Válassza az **Adminisztráció** lapot.

17.  Válassza a **rendszerterületi beállítások módosítása**lehetőséget.

18. A **jelenlegi rendszerterületi beállítás**alatti legördülő menüben válassza ki a használni kívánt nyelvi beállításokat. Ezután kattintson **az OK gombra**.

19. A munkamenet újbóli újraindításához válassza az **Újraindítás most** lehetőséget.

Gratulálunk, telepítette a nyelvi csomagokat!

A folytatás előtt győződjön meg arról, hogy a rendszer a Windows és a Windows áruház legújabb verzióit telepítette.

## <a name="sysprep"></a>Sysprep

Ezután a számítógép Sysprep eszközét kell előkészítenie a rendszerkép-rögzítési folyamat előkészítéséhez.

A gép Sysprep:

1. Nyissa meg a PowerShellt rendszergazdaként.
2. Futtassa a következő parancsmagot, hogy a megfelelő könyvtárba lépjen:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Ezután futtassa a következő parancsmagot:

    ```powershell
    .\sysprep.exe
    ```

4. Amikor megnyílik a rendszer-előkészítő eszköz ablak, jelölje be az **általánosítás**feliratú jelölőnégyzetet, majd lépjen a **leállítási beállítások** pontra, és válassza a **Leállítás** lehetőséget a legördülő menüből.

>[!NOTE]
>A syprep folyamat eltarthat néhány percig. Ahogy a virtuális gép leáll, a távoli munkamenet leválasztja a kapcsolatot.

### <a name="resolve-sysprep-errors"></a>Sysprep-hibák elhárítása

Ha a Sysprep folyamat során hibaüzenet jelenik meg, a következőket kell tennie:

1. Nyissa meg a **C meghajtót** , és lépjen a **Windows**  >  **System32 Sysprep**  >  **Panther**elemre, majd nyissa meg a **Setuperr** fájlt.

   A hibaüzenet szövege azt jelzi, hogy el kell távolítania egy adott nyelvi csomagot, ahogy az az alábbi képen is látható. Másolja a nyelvi csomag nevét a következő lépéshez.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a Setuperr-fájlról. A csomag nevét tartalmazó szöveg ki van emelve a sötétkék színnel.](media/setuperr-package-name.png)

2. Nyisson meg egy új PowerShell-ablakot, és futtassa a következő parancsmagot a 2. lépésben átmásolt csomag nevével a nyelvi csomag eltávolításához:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Győződjön meg arról, hogy eltávolította a csomagot a `Remove-AppxPackage` parancsmag ismételt futtatásával. Ha sikeresen eltávolította a csomagot, megjelenik egy üzenet, amely szerint az eltávolítani kívánt csomag nem létezik.

4. Futtassa `sysprep.exe` újra a parancsmagot.

## <a name="capture-the-image"></a>A rendszerkép rögzítése

Most, hogy a rendszer készen áll, rögzítheti a lemezképeket, hogy más felhasználók is elkezdhetik használni a virtuális gépeket a rendszer alapján anélkül, hogy meg kellene ismételnie a konfigurációs folyamatot.

Rendszerkép rögzítése:

1. Lépjen a Azure Portal, és válassza ki annak a gépnek a nevét, amelyet a [nyelvi csomag](#install-a-language-pack) és a [Sysprep](#sysprep)telepítése című részében konfigurált.

2. Válassza a **rögzítés**lehetőséget.

3. Adja meg a rendszerkép nevét a Name ( **név** ) mezőben, és rendelje hozzá az **erőforráscsoporthoz az erőforráscsoport legördülő menüből** az alábbi ábrán látható módon.

   > [!div class="mx-imgBorder"]
   > ![A rendszerkép létrehozása ablak képernyőképe Az a név, amelyet a felhasználó adott a tesztelési képnek, a "vmwvd-képfájl-fr", és a "testwvdimagerg" erőforráscsoporthoz lett hozzárendelve.](media/create-image.png)

4. Kattintson a **Létrehozás** gombra.

5. Várjon néhány percet, amíg a rögzítési folyamat befejeződik. Ha a rendszerkép elkészült, egy üzenet jelenik meg az értesítési központban, amely értesíti, hogy a rendszerkép rögzítése megtörténik.

Most már üzembe helyezhet egy virtuális gépet az új rendszerkép használatával. A virtuális gép központi telepítésekor kövesse a [Windows rendszerű virtuális gép létrehozása rendelkezésre állási zónában található utasításokat a Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Az általános jogú felhasználók megjelenítési nyelvének módosítása

Az általános jogú felhasználók módosíthatják a virtuális gépek megjelenítési nyelvét.

A megjelenítési nyelv módosítása:

1. Lépjen a **nyelvi beállítások menüpontra**. Ha nem tudja, hol van, megadhatja a **nyelvet** a Start menü keresési sávjában.

2. A Windows megjelenítési nyelv legördülő menüben válassza ki a nyelvet, amelyet a megjelenítési nyelvként kíván használni.

3. Jelentkezzen ki a munkamenetből, majd jelentkezzen be újra. A megjelenítési nyelvnek most a 2. lépésben kiválasztottnak kell lennie.
