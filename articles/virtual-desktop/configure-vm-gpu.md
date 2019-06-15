---
title: Windows virtuális asztali előzetes verzió – Azure GPU konfigurálása
description: Hogyan lehet engedélyezni a GPU-gyorsított megjelenítése és a kódolási Windows virtuális asztal előzetes verzióban érhető el.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159570"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Grafikus processzorral (GPU) egység gyorsítását Windows virtuális asztal előzetes konfigurálása

Windows virtuális asztal előzetes GPU-gyorsított megjelenítése és továbbfejlesztett teljesítményének és méretezhetőségének kódolást támogatja. GPU-gyorsítás különösen elengedhetetlen a magas grafikai igényű alkalmazásokat.

Kövesse az utasításokat, ez a cikk a GPU-optimalizált Azure virtuális gép létrehozása, adja hozzá a gazdagép-készletet, és konfigurálja azt a GPU-gyorsítás megjelenítése és használandó kódolást. Ez a cikk feltételezi, hogy már rendelkezik egy Windows virtuális asztal bérlőhöz konfigurálva.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Válassza ki a GPU-optimalizált Azure virtuális gép méretét

Az Azure számos [GPU-optimalizált virtuális gépek méretei](/azure/virtual-machines/windows/sizes-gpu). A megfelelő választás a gazdagép-készlethez tényező, beleértve az adott alkalmazás számítási feladatokat, a felhasználói élmény és a költség kívánt minőségi függ. Általában a nagyobb, és képes a több gpu-k egy adott felhasználó sűrűség jobb felhasználói élményt nyújtanak.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Gazdagép-készlet létrehozása a virtuális gép üzembe helyezése és az alkalmazás csoport konfigurálása

Hozzon létre egy új gazdagép-készletet, a kijelölt méretű virtuális gép használatával. Útmutatásért lásd: [oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows virtuális asztal előzetes GPU-gyorsított megjelenítése és a kódolás a következő operációs rendszerek a támogatja:

* A Windows 10 1511-es vagy újabb
* Windows Server 2016 vagy újabb

Meg kell is app csoport konfigurálása, vagy csoportot használja az alapértelmezett asztali alkalmazás (más néven "Asztal alkalmazáscsoport"), amely automatikusan létrejön, amikor létrehoz egy új gazdagép-készletet. Útmutatásért lásd: [oktatóanyag: Alkalmazáscsoportok kezelése a Windows Virtual Desktop előzetes verziójához](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows virtuális asztal előzetes verzió csak a GPU-kompatibilis gazdagépet készletek esetében támogatja a "Desktop" alkalmazástípus-csoport. Alkalmazáscsoportok "RemoteApp" típusú gazdagép GPU-kompatibilis készletek nem támogatottak.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>A virtuális gépen támogatott illesztőprogramok telepítése

Kihasználhatja az Azure N-sorozatú virtuális gépek a GPU-funkciókkal Windows virtuális asztal előzetes verzióban érhető el, telepítenie kell az NVIDIA grafikus illesztőprogramok. Kövesse az utasításokat, [telepítése NVIDIA GPU-illesztőprogramokat N-sorozatú virtuális gépeken futó Windows](/azure/virtual-machines/windows/n-series-driver-setup) telepíteni az illesztőprogramokat, vagy manuálisan vagy a [NVIDIA GPU illesztőprogramjának bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Vegye figyelembe, hogy csak [NVIDIA GRID illesztőprogramok](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) Azure elosztott támogatottak a Windows Virtual Desktop előzetes verziójához.

Illesztőprogram a telepítést követően a virtuális gép újraindításra szükség. A fenti utasítások az ellenőrzési lépések segítségével ellenőrizze, hogy az illesztőprogramok sikeresen megtörtént.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazás renderelési konfigurálása

Alapértelmezés szerint alkalmazások és asztali futó több munkamenet-konfigurációk vannak leképezve, a CPU, és nem használhatja a renderelési elérhető gpu-k. A munkamenetgazda GPU-gyorsított renderelési engedélyezése a csoportházirend konfigurálásához:

1. Csatlakozzon a virtuális gép helyi rendszergazdai jogosultságokkal rendelkező fiókkal, az asztalon.
2. Nyissa meg a Start menüben, és írja be "gpedit.msc" a Helyicsoportházirend-szerkesztő megnyitásához.
3. Keresse meg a fa **számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**  >   **A távoli asztali szolgáltatások** > **távoli asztali munkamenetgazda** > **távoli munkamenet-környezet**.
4. Válassza ki a szabályzat **a hardver alapértelmezett grafikus adapter használni minden távoli asztali szolgáltatások munkamenet** , és állítsa a szabályzat **engedélyezve** GPU Renderelés, a távoli munkamenet engedélyezése.

## <a name="configure-gpu-accelerated-frame-encoding"></a>A gyorsított GPU keret kódolás konfigurálása

A távoli asztal (akár a megjelenített GPU vagy CPU), az alkalmazások és asztali számítógépek által megjelenített összes grafikai kódol esetén a távoli asztali ügyfelek történő átvitelhez. Alapértelmezés szerint a távoli asztal nem hasznosítja elérhető gpu-k ehhez a kódoláshoz. A munkamenet-gazdagép GPU-gyorsított keret kódolás engedélyezése a csoportházirend konfigurálásához. Folytatás a fenti lépéseket:

1. Válassza ki a szabályzat **rangsorolhatja H.264/AVC 444 grafikus mód távoli asztali kapcsolatok** , és állítsa a szabályzat **engedélyezve** H.264/AVC 444 kodek kényszerítése a távoli munkamenet.
2. Válassza ki a szabályzat **távoli asztali kapcsolatok kódolás hardver konfigurálása H.264/AVC** , és állítsa a szabályzat **engedélyezve** ahhoz, hogy a távoli munkamenet AVC/H.264 kódolásának hardver.

    >[!NOTE]
    >A Windows Server 2016, a beállításhoz **inkább AVC hardver kódolás** való **kísérelje meg mindig**.

3. Most, hogy a csoportházirendek folyamán szerkesztett, kényszerítheti a csoportházirend-frissítések. Nyissa meg a parancssort és írja be:

    ```batch
    gpupdate.exe /force
    ```

4. A távoli asztali munkamenetben jelentkezzen ki.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazás renderelési ellenőrzése

Győződjön meg arról, hogy alkalmazásokat használ a GPU renderelést, próbálja meg a következők egyikét:

* Használja a `nvidia-smi` segédprogram leírtak szerint [ellenőrizze az illesztőprogram-telepítőfájl](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) GPU-kihasználtság ellenőrizheti az alkalmazások futtatása közben.
* Operációs rendszerek támogatott verzióinak a Feladatkezelő segítségével ellenőrizze a grafikus Processzor kihasználtsága. A GPU válassza a "Teljesítmény" lapján megtekintheti, hogy az alkalmazások a GPU igénybe vett.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Ellenőrizze a GPU-gyorsított keret kódolás

Annak ellenőrzése, hogy a távoli asztal GPU-gyorsított kódolás használatával:

1. Csatlakozzon az asztalhoz a virtuális gép virtuális asztali Windows-ügyfél használatával.
2. Indítsa el az eseménynaplót, és keresse meg a következő csomópont: **Alkalmazások és szolgáltatásnaplók** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Működési**
3. A gyorsított GPU kódolás használata esetén határozza meg, keresse meg event ID 170. Ha megjelenik "AVC hardver kódoló engedélyezve: 1 – majd GPU-kódolás szolgál.
4. Határozza meg, ha AVC 444 módban használja, keresse meg a 162-es Azonosítójú esemény. Ha megjelenik "AVC érhető el: 1 kezdeti profil: 2048" majd AVC 444 szolgál.

## <a name="next-steps"></a>További lépések

Ezek az utasítások kell rendelkeznie, és a GPU-gyorsítás a virtuális gép gazdagépen egyetlen munkamenetben. Néhány további szempontokról nagyobb gazdagép csoportján GPU-gyorsítás engedélyezése:

* Fontolja meg a [NVIDIA GPU illesztőprogramjának bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows) illesztőprogram telepítése és a frissítések leegyszerűsíteni a virtuális gépek számos más.
* Fontolja meg az Active Directory csoportházirend segítségével egyszerűsítheti a csoportházirend-beállításai között a virtuális gépek száma. Az Active Directory-tartományban lévő csoportházirend telepítésével kapcsolatos információkért lásd: [csoportházirend-objektumok használata](https://go.microsoft.com/fwlink/p/?LinkId=620889).
