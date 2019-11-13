---
title: A GPU konfigurálása a Windows rendszerű virtuális asztali környezethez – Azure
description: A GPU-gyorsított renderelés és a kódolás engedélyezése a Windows Virtual Desktopban.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a0965dc4011b449e617f6dbaeafb68bfa796b620
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953950"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>A grafikus processzor (GPU) gyorsításának beállítása a Windows rendszerű virtuális asztalhoz

A Windows virtuális asztal támogatja a GPU-gyorsított renderelést és kódolást az alkalmazások teljesítményének és méretezhetőségének növelése érdekében. A GPU-gyorsítás különösen fontos a nagy grafikai igényű alkalmazások esetében.

Az ebben a cikkben található utasítások alapján hozzon létre egy GPU-ra optimalizált Azure-beli virtuális gépet, adja hozzá a gazdagéphez, és konfigurálja úgy, hogy GPU-gyorsítást használjon a rendereléshez és a kódoláshoz. Ez a cikk azt feltételezi, hogy már van konfigurálva a Windows rendszerű virtuális asztali bérlő.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>A GPU-ra optimalizált Azure-beli virtuális gép méretének kiválasztása

Az Azure számos GPU-val [optimalizált virtuálisgép-méretet](/azure/virtual-machines/windows/sizes-gpu)kínál. A gazdagép-készlet megfelelő választása számos tényezőtől függ, többek között az alkalmazás számítási feladataitól, a felhasználói élmény kívánt minőségétől és a költséghatékonyságtól. Általánosságban elmondható, hogy a nagyobb és jobban használható GPU-k jobb felhasználói élményt nyújtanak egy adott felhasználói sűrűségben.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Címkészlet létrehozása, a virtuális gép üzembe helyezése és az alkalmazás csoportjának konfigurálása

Hozzon létre egy új címkészletet a kiválasztott méretű virtuális géppel. Útmutatásért lásd [: oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](/azure/virtual-desktop/create-host-pools-azure-marketplace).

A Windows virtuális asztal támogatja a GPU-gyorsított renderelést és kódolást a következő operációs rendszereken:

* Windows 10 1511-es vagy újabb verzió
* Windows Server 2016 vagy újabb

Az új címkészlet létrehozásakor is konfigurálnia kell egy alkalmazás-csoportot, vagy az alapértelmezett asztali alkalmazást ("asztali alkalmazáscsoport") kell használnia. Útmutatásért lásd [: oktatóanyag: alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztali gépeken](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Támogatott grafikus illesztőprogramok telepítése a virtuális gépen

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához a Windows Virtual Desktopban telepítenie kell az NVIDIA grafikus illesztőprogramokat. Kövesse az [NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken](/azure/virtual-machines/windows/n-series-driver-setup) című témakör utasításait az illesztőprogramok manuális vagy az [NVIDIA GPU-illesztőprogram bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows)használatával történő telepítéséhez.

Vegye figyelembe, hogy csak az Azure által terjesztett [NVIDIA Grid-illesztőprogramok](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) támogatottak a Windows rendszerű virtuális asztali gépeken.

Az illesztőprogram telepítése után szükség van egy virtuális gép újraindítására. A fenti utasításokban található ellenőrzési lépések segítségével ellenőrizze, hogy a grafikus illesztőprogramok telepítése sikeres volt-e.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazások renderelésének konfigurálása

Alapértelmezés szerint a többmunkamenetes konfigurációkban futó alkalmazások és asztali számítógépek a PROCESSZORral jelennek meg, és nem használják fel a rendelkezésre álló GPU-ket a rendereléshez. Csoportházirend konfigurálása a munkamenet-gazdagép számára a GPU-gyorsított renderelés engedélyezéséhez:

1. Kapcsolódjon a virtuális gép asztalához helyi rendszergazdai jogosultságokkal rendelkező fiókkal.
2. Nyissa meg a Start menüt, és írja be a "gpedit. msc" parancsot a Csoportházirend-szerkesztő megnyitásához.
3. Navigáljon a fában a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **Windows-összetevők** > **Távoli asztali szolgáltatások** > távoli asztal **munkamenet-gazdagép** > **távoli munkamenet-környezet**.
4. Válassza a házirend lehetőséget, majd **a hardver alapértelmezett grafikus adapterét használja az összes távoli asztali szolgáltatások munkamenethez** , és állítsa be ezt a házirendet **úgy, hogy engedélyezze** a GPU renderelést a távoli munkamenetben.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-gyorsított keret kódolásának konfigurálása

Távoli asztal kódolja az alkalmazások és az asztali számítógépek által megjelenített összes grafikát (akár GPU-val, akár CPU-val) a Távoli asztal ügyfeleknek való továbbításhoz. Alapértelmezés szerint a Távoli asztal nem használ elérhető GPU-ket ehhez a kódoláshoz. Konfigurálja Csoportházirend a munkamenet-gazdagép számára a GPU-gyorsított keret kódolásának engedélyezéséhez. Folytassa a fenti lépéseket:

1. Válassza ki a szabályzatot **prioritásként h. 264/avc 444 grafikus módban távoli asztal kapcsolatokhoz** , és állítsa be ezt a házirendet úgy, hogy az **engedélyezze** a h. 264/AVC 444 kodek kényszerítését a távoli munkamenetben.
2. Válassza a házirend a **H. 264/AVC hardveres kódolás beállítása Távoli asztal kapcsolatok számára** lehetőséget, és állítsa be ezt a házirendet **úgy,** hogy engedélyezze az AVC/H. 264 hardveres kódolást a távoli munkamenetben.

    >[!NOTE]
    >A Windows Server 2016-ben állítsa be a beállítást, hogy a rendszer mindig az **AVC hardveres kódolást** **próbálja**meg használni.

3. Most, hogy a csoportházirendek szerkesztése megtörtént, kényszerítse a csoportházirend frissítését. Nyissa meg a parancssort és írja be a következőt:

    ```batch
    gpupdate.exe /force
    ```

4. Jelentkezzen ki a Távoli asztal-munkamenetből.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazások megjelenítésének ellenőrzése

Annak ellenőrzéséhez, hogy az alkalmazások a GPU-t használják a rendereléshez, próbálkozzon a következők bármelyikével:

* Az alkalmazások futtatásakor használja a `nvidia-smi` segédprogramot az [illesztőprogram-telepítés ellenőrzése](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) a GPU-használat ellenőrzéséhez című témakörben leírtak szerint.
* A támogatott operációsrendszer-verziók esetében a Feladatkezelő segítségével keresse meg a GPU-kihasználtságot. Válassza ki a GPU-t a "teljesítmény" lapon annak megtekintéséhez, hogy az alkalmazások használják-e a GPU-t.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU-gyorsított keret kódolásának ellenőrzése

Annak ellenőrzése, hogy a Távoli asztal GPU-gyorsított kódolást használ-e:

1. Kapcsolódjon a virtuális gép asztalához a Windows Virtual Desktop ügyfélprogram használatával.
2. Indítsa el a Eseménynapló, és nyissa meg a következő csomópontot: **alkalmazások és szolgáltatások naplói** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** > **Operational**
3. Annak megállapításához, hogy a GPU-gyorsított kódolás használatban van-e, keresse meg a 170-es AZONOSÍTÓJÚ eseményt. Ha a "AVC hardveres kódoló engedélyezve: 1" látható, akkor a rendszer GPU-kódolást használ.
4. Annak megállapításához, hogy az AVC 444 mód használatban van-e, keresse meg a 162-es AZONOSÍTÓJÚ eseményt. Ha a "AVC elérhető: 1 kezdeti profil: 2048" jelenik meg, akkor az AVC 444 használatos.

## <a name="next-steps"></a>Következő lépések

Ezeknek az utasításoknak a GPU-gyorsítással együtt kell működniük egyetlen munkamenet-gazda virtuális gépen. Néhány további megfontolandó szempont a GPU-gyorsítás nagyobb gazdagép-készleten való engedélyezéséhez:

* Érdemes lehet az [NVIDIA GPU illesztőprogram-bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows) használatával leegyszerűsíteni az illesztőprogramok telepítését és a frissítéseket számos virtuális gépen.
* Vegye fontolóra Active Directory Csoportházirend használatát a csoportházirend konfigurálásának leegyszerűsítéséhez több virtuális gép között. További információ a Csoportházirend telepítéséről a Active Directory tartományban: [csoportházirend objektumok használata](https://go.microsoft.com/fwlink/p/?LinkId=620889).
