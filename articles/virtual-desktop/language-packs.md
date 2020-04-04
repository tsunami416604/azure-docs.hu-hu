---
title: Nyelvi csomagok telepítése Windows 10-es virtuális gépekre a Windows Virtuális asztalon – Azure
description: A Windows 10 többmunkamenetes virtuális gépeknyelvi csomagjainak telepítése a Windows Virtual Desktop rendszerben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634034"
---
# <a name="install-language-packs"></a>Nyelvi csomagok telepítése

Ha a Windows virtuális asztal központi telepítéseit nemzetközi szinten állítja be, célszerű biztosítani, hogy a központi telepítés több nyelvet is támogatott legyen. Nyelvi csomagokat telepíthet egy Windows 10 Enterprise többmunkamenetes virtuális gép (VM) lemezképére, amely annyi nyelvet támogat, amennyire a szervezetnek szüksége van. Ez a cikk bemutatja, hogyan telepítheti a nyelvi csomagokat, és hogyan rögzítheti azokat a képeket, amelyek lehetővé teszik a felhasználók számára, hogy saját megjelenítési nyelveket válasszanak.

További információ a virtuális gépek Azure-beli üzembe helyezéséről az [Azure-portállal elérhető zónában elérhető zónában elérhető windowsos virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)című webhelyen.

>[!NOTE]
>Ez a cikk a Windows 10 Enterprise többmunkamenetes virtuális gépekre vonatkozik.

## <a name="install-a-language-pack"></a>Nyelvi csomag telepítése

Ha nyelvi csomagokkal szeretne virtuálisgép-lemezképet létrehozni, először telepítenie kell a nyelvi csomagokat egy gépre, és rögzítenie kell róla egy képet.

Nyelvi csomagok telepítése:

1. Jelentkezzen be rendszergazdaként.
2. Ellenőrizze, hogy telepítette-e a Windows és a Windows Áruház összes legújabb frissítését.
3. Nyissa meg a **Beállítások** > ideje & nyelvi**terület****lehetőséget.** > 
4. Az **Ország vagy régió**csoportban válassza ki a kívánt országot vagy régiót a legördülő menüből.
    Ebben a példában **Franciaországot**választjuk , ahogy az a következő képernyőképen látható:

    ![Képernyőkép a Régió lapról. A jelenleg kiválasztott régió Franciaország.](media/region-page-france.png)

5. Ezt követően válassza **a Nyelv**lehetőséget, majd a **Nyelv hozzáadása**lehetőséget. Válassza ki a listából a telepíteni kívánt nyelvet, majd válassza a **Tovább**gombot.
6. Amikor megnyílik a **Nyelvi szolgáltatások telepítése** ablak, jelölje be a Nyelvi csomag telepítése jelölőnégyzetet, és állítsa be a Windows **megjelenítési nyelveként.**
7. Válassza az **Install** (Telepítés) lehetőséget.
8. Ha egyszerre több nyelvet szeretne hozzáadni, válassza **a Nyelv hozzáadása**lehetőséget, majd ismételje meg a folyamatot egy nyelv hozzáadásához az 5. Ismételje meg ezt a folyamatot minden telepíteni kívánt nyelven. Egyszerre azonban csak egy nyelvet állíthat be megjelenítési nyelvként.

    Fussunk át egy gyors vizuális bemutatón. Az alábbi képeken bemutatjuk, hogyan kell telepíteni a francia és a holland nyelvi csomagokat, majd beállíthatja a francia megjelenítési nyelvet.

    ![A folyamat elején található Nyelv lap képernyőképe. A Windows kijelölt megjelenítési nyelve az angol.](media/language-page-default.png)

    ![Képernyőkép a nyelvválasztó ablakról. A felhasználó beírta a "francia" szót a keresősávba, hogy megtalálja a francia nyelvű csomagokat.](media/select-language-french.png)

    ![Képernyőkép a Nyelvi szolgáltatások telepítése lapról. A francia a kívánt nyelv. A kiválasztott beállítások a következők: "A megjelenítési nyelv beállítása", "Nyelvi csomag telepítése", "Beszédfelismerés" és "Kézírás".](media/install-language-features.png)

    A nyelvi csomagok telepítése után a nyelvi csomagok nevei megjelennek a nyelvek listájában.

    ![Képernyőkép a nyelvi oldalról, amelyen az új nyelvi csomagok vannak telepítve. A francia és a holland nyelvi csomagok a "preferált nyelvek" alatt találhatók.](media/language-page-complete.png)

9. Ha megjelenik egy ablak, amely arra kéri, hogy jelentkezzen ki a munkamenetből. Jelentkezzen ki, majd jelentkezzen be újra. A megjelenítési nyelvnek most már a kiválasztott nyelvnek kell lennie.

10.  Nyissa meg a **Vezérlőpult** > **óráját és** > **régióját.**

11.  Amikor megnyílik a **Régió** ablak, válassza a **Felügyelet** lapot, majd a **Beállítások másolása**lehetőséget.

12.  Jelölje be az Üdvözlőképernyő és a **rendszerfiókok,** valamint az **Új felhasználói fiókok jelölőnégyzetet.**

13.  Válassza **az OK gombot.**

14.  Megnyílik egy ablak, amely a munkamenet újraindítását mondja. Válassza **az Újraindítás most**lehetőséget.

15.  Miután újra bejelentkezett, lépjen vissza a Vezérlőpult > **órája és régiója** >  **Control Panel****vezérlőpulthoz.**

16.  Válassza a **Felügyelet** lapot.

17.  Válassza **a Rendszer területi beállításának módosítása**lehetőséget.

18. A **Jelenlegi rendszer területi beállításai**nak legördülő menüjében válassza ki a használni kívánt területi beállításokat. Ezután válassza az **OK gombot.**

19. Válassza **az Újraindítás most** lehetőséget a munkamenet újbóli újraindításához.

Gratulálunk, telepítette a nyelvi csomagokat!

A folytatás előtt győződjön meg arról, hogy a rendszer a Windows és a Windows Áruház legújabb verzióival rendelkezik.

## <a name="sysprep"></a>Sysprep

Ezután sysprep a gép előkészítése a képrögzítési folyamat.

A számítógép sysprep-je:

1. Nyissa meg a PowerShellt rendszergazdaként.
2. Futtassa a következő parancsmast a megfelelő könyvtárba való ugráshoz:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Ezután futtassa a következő parancsmast:
    
    ```powershell
    .\sysprep.exe
    ```

4. Amikor megnyílik a Rendszerelőkészítő eszköz ablak, jelölje be a **Generalize**feliratú jelölőnégyzetet, majd válassza a **Leállítási beállítások lehetőséget,** és válassza a legördülő menü **Leállítás** parancsát.

>[!NOTE]
>A syprep folyamat néhány percet vesz igénybe. A virtuális gép leállításakor a távoli munkamenet bontja a kapcsolatot.

### <a name="resolve-sysprep-errors"></a>Sysprep hibák megoldása

Ha a sysprep folyamat során hibaüzenet jelenik meg, a következőket kell tennie:

1. Nyissa **meg a C meghajtót,** és nyissa meg a **Windows** > **System32 Sysprep** > **Panther**alkalmazást, majd nyissa meg a **setuperr** fájlt.

   A hibafájlszöveg megmondja, hogy el kell távolítania egy adott nyelvi csomagot, ahogy az az alábbi képen látható. Másolja a következő lépés nyelvi csomagnevét.

   ![Képernyőkép a setuperr fájlról. A csomag nevét tartalmazó szöveg sötétkék színnel van kiemelve.](media/setuperr-package-name.png)

2. Nyisson meg egy új PowerShell-ablakot, és futtassa a következő parancsmamot a 2.

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Ellenőrizze, hogy eltávolította-e a `Remove-AppxPackage` csomagot a parancsmag ismételt futtatásával. Ha sikeresen eltávolította a csomagot, egy üzenetnek kell látnia, amely szerint az eltávolítani kívánt csomag nem létezik.

4. Futtassa újra a `sysprep.exe` parancsmarunt.

## <a name="capture-the-image"></a>A kép rögzítése

Most, hogy a rendszer készen áll, rögzíthet egy lemezképet, hogy más felhasználók is elkezdhessék használni a virtuális gépeket a rendszer alapján anélkül, hogy meg kellene ismételnie a konfigurációs folyamatot.

Kép rögzítése:

1. Nyissa meg az Azure Portalon, és válassza ki a [nyelvi csomag telepítése](#install-a-language-pack) és a [sysprep](#sysprep)területen konfigurált számítógép nevét.

2. Válassza a **Rögzítés**lehetőséget.

3. Írja be a kép nevét a **Név** mezőbe, és rendelje hozzá az erőforráscsoporthoz az **Erőforráscsoport** legördülő menüvel, ahogy az az alábbi képen látható.

   ![Képernyőkép a Kép létrehozása ablakról. A tesztlemezképhez adott felhasználó neve "vmwvd-image-fr", és hozzárendelték a "testwvdimagerg" erőforráscsoporthoz.](media/create-image.png)

4. Kattintson a **Létrehozás** gombra.

5. Várjon néhány percet, amíg a rögzítési folyamat befejeződik. Amikor a kép készen áll, egy üzenetnek kell lennie az Értesítési központban, amely tudatja önvel, hogy a kép rögzítésre került.

Most már üzembe helyezhet egy virtuális gép az új lemezkép használatával. A virtuális gép telepítésekor kövesse a Windows virtuális gép létrehozása az [Azure Portal rendelkezésre állási zónájában](../virtual-machines/windows/create-portal-availability-zone.md)című útmutató utasításait.

### <a name="how-to-change-display-language-for-standard-users"></a>Az általános megjelenítési nyelv módosítása általános jogú felhasználók számára

Az általános jogú felhasználók módosíthatják a virtuális gépek megjelenítési nyelvét.

A megjelenítési nyelv módosítása:

1. Nyissa meg a **Nyelvi beállítások lehetőséget.** Ha nem tudja, hogy hol van, **megadhatja** a Nyelvet a Start menü keresősávjába.

2. A Windows megjelenítési nyelvlegördülő menüjében válassza ki a megjelenítési nyelvként használni kívánt nyelvet.

3. Jelentkezzen ki a munkamenetből, majd jelentkezzen be újra. A megjelenítési nyelvnek most a 2.
