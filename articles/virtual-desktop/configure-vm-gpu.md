---
title: GPU konfigurálása Windows virtuális asztalhoz – Azure
description: A GPU-kat gyorsított renderelés és kódolás engedélyezése a Windows Virtual Desktop rendszerben.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767038"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Grafikus feldolgozóegység (GPU) gyorsításának konfigurálása a Windows Virtual Desktop rendszerhez

A Windows Virtual Desktop támogatja a GPU-gyorsítású renderelést és kódolást az alkalmazások jobb teljesítménye és méretezhetősége érdekében. A GPU gyorsítása különösen fontos a nagy grafikai igényű alkalmazások esetében.

Kövesse az ebben a cikkben található utasításokat egy GPU-ra optimalizált Azure virtuális gép létrehozásához, adja hozzá a gazdakészlethez, és konfigurálja úgy, hogy a GPU-gyorsítást használja a rendereléshez és a kódoláshoz. Ez a cikk feltételezi, hogy már van konfigurálva egy Windows virtuális asztal bérlője.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Válasszon gpu-ra optimalizált Azure virtuálisgép-méretet

Az Azure számos [GPU-ra optimalizált virtuálisgép-méretet](/azure/virtual-machines/windows/sizes-gpu)kínál. A gazdakészlet megfelelő választása számos tényezőtől függ, például az adott alkalmazás-munkaterhelésektől, a kívánt felhasználói élménytől és a költségektől. Általánosságban elmondható, hogy a nagyobb és alkalmasabb GPU-k jobb felhasználói élményt nyújtanak egy adott felhasználói sűrűségnél.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Gazdagépkészlet létrehozása, a virtuális gép kiépítése és alkalmazáscsoport konfigurálása

Hozzon létre egy új gazdagép készlet segítségével a kiválasztott méretű virtuális gép. További információt az [Oktatóanyag: Állomáskészlet létrehozása az Azure Marketplace-en című témakörben talál.](/azure/virtual-desktop/create-host-pools-azure-marketplace)

A Windows Virtual Desktop a következő operációs rendszerekben támogatja a GPU-gyorsítású renderelést és kódolást:

* Windows 10 1511-es vagy újabb verzió
* Windows Server 2016 vagy újabb

Be kell állítania egy alkalmazáscsoportot is, vagy az alapértelmezett asztali alkalmazáscsoportot ("Asztali alkalmazáscsoport") kell használnia, amely automatikusan létrejön egy új gazdagépkészlet létrehozásakor. További információt az [Oktatóanyag: Alkalmazáscsoportok kezelése windowsos virtuális asztalhoz](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Támogatott grafikus illesztőprogramok telepítése a virtuális gépre

Az Azure N sorozatú virtuális gépek GPU-funkcióinak kihasználásához telepítenie kell a megfelelő grafikus illesztőprogramokat. Kövesse a [támogatott operációs rendszerek és illesztőprogramok](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) utasításokat a megfelelő grafikus gyártó illesztőprogramjainak telepítéséhez, manuálisan vagy egy Azure virtuálisgép-bővítmény használatával.

A Windows virtuális asztal csak az Azure által terjesztett illesztőprogramokat támogatja. Emellett az NVIDIA GPU-val rendelkező Azure-beli virtuális gépek esetében csak [az NVIDIA GRID-illesztőprogramok](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) támogatottak a Windows virtuális asztalon.

Az illesztőprogram telepítése után a virtuális gép újraindítása szükséges. A fenti utasítások ban található ellenőrzési lépésekkel ellenőrizze, hogy a grafikus illesztőprogramok telepítése sikeresen megtörtént-e.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazásrenderelés konfigurálása

Alapértelmezés szerint a többmunkamenetes konfigurációkban futó alkalmazások és asztalok a processzorral jelennek meg, és nem használják ki a rendelkezésre álló GPU-kat a rendereléshez. Konfigurálja a munkamenetgazda csoportházirendjét a GPU-gyorsítású renderelés engedélyezéséhez:

1. Csatlakozzon a virtuális gép asztalához helyi rendszergazdai jogosultságokkal rendelkező fiókkal.
2. Nyissa meg a Start menüt, és írja be a "gpedit.msc" parancsot a Csoportházirend-szerkesztő megnyitásához.
3. Navigáljon a fába **a Számítógép-konfiguráció** > **felügyeleti sablonjai** > **lapban A Windows-összetevők** > távoli**asztali szolgáltatások** > **távoli asztali munkamenetgazda** > **távoli munkamenetkörnyezetében.**
4. Válassza a házirend **lehetőséget: Használja a hardveres alapértelmezett grafikus adaptert az összes Távoli asztali szolgáltatás munkamenetéhez,** és állítsa ezt a házirendet **Engedélyezve értékre** a GPU-renderelés engedélyezéséhez a távoli munkamenetben.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-gyorsított képkocka-kódolás konfigurálása

A Távoli asztal kódolja az alkalmazások és asztali számítógépek által renderelt összes grafikát (akár GPU-val, akár CPU-val renderelte) a távoli asztali ügyfelekre történő átvitelhez. Alapértelmezés szerint a Távoli asztal nem használja ki a kódoláshoz rendelkezésre álló GPU-kat. Konfigurálja a munkamenetgazda csoportházirendjét a GPU-gyorsított képkocka-kódolás engedélyezéséhez. A fenti lépések folytatása:

1. Válassza **a H.264/AVC 444 grafikus mód prioritása házirendet a távoli asztali kapcsolatokhoz,** és állítsa ezt a házirendet **Engedélyezve a** H.264/AVC 444 kodek távoli munkamenetben való kényszerítésére.
2. Válassza a **H.264/AVC hardverkódolás konfigurálása a távoli asztali kapcsolatokhoz** beállítást, és állítsa ezt a házirendet **Engedélyezve értékre,** hogy engedélyezze az AVC/H.264 hardveres kódolását a távoli munkamenetben.

    >[!NOTE]
    >Windows Server 2016 rendszerben állítsa be az **AVC hardverkódolást előnyben részesítő** beállítást **a Mindig kíséreléshez.**

3. Most, hogy a csoportházirendek szerkesztésre kerültek, kényszerítse ki a csoportházirend frissítését. Nyissa meg a parancssort, és írja be a következőparancsot:

    ```batch
    gpupdate.exe /force
    ```

4. Jelentkezzen ki a Távoli asztal munkamenetből.

## <a name="verify-gpu-accelerated-app-rendering"></a>Gpu-gyorsított alkalmazásrenderelés ellenőrzése

Annak ellenőrzéséhez, hogy az alkalmazások a GPU-t használják-e a megjelenítéshez, próbálkozzon az alábbiakkal:

* NVIDIA GPU-val rendelkező Azure-beli `nvidia-smi` virtuális gépek esetén használja az [illesztőprogram telepítésének ellenőrzése](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) című segédprogramot az alkalmazások futtatásakor a GPU-használat ellenőrzéséhez.
* A támogatott operációsrendszer-verziókon a Feladatkezelő segítségével ellenőrizheti a GPU-kihasználtságot. Válassza ki a GPU-t a "Teljesítmény" lapon, hogy lássa, hogy az alkalmazások a GPU-t használnak-e.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Gpu-gyorsított képkockakódolás ellenőrzése

Annak ellenőrzése, hogy a Távoli asztal GPU-gyorsított kódolást használ-e:

1. Csatlakozzon a virtuális gép asztalához a Windows virtuális asztali ügyfélhasználatával.
2. Az Eseménynapló elindítása és a következő csomópont megnyitása: **Alkalmazások és szolgáltatások Naplózza** > **a Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational alkalmazást**
3. Annak megállapításához, hogy a GPU-gyorsított kódolást használja-e, keresse meg a 170-es eseményazonosítót. Ha az "AVC hardverkódoló engedélyezve van: 1" értéket látja, akkor a GPU kódolást használja.
4. Annak megállapításához, hogy az AVC 444 mód van-e használatban, keresse meg a 162-es eseményazonosítót. Ha az "AVC Available: 1 Initial Profile: 2048" (AVC 444) jelenik meg, akkor az AVC 444-et használja.

## <a name="next-steps"></a>További lépések

Ezek nek az utasításoknak egy munkamenet-gazdagépen (egy virtuális gépen) a GPU-gyorsítással kell rendelkezniük. Néhány további szempont a GPU-gyorsítás nagyobb gazdagépkészleten történő engedélyezéséhez:

* Fontolja meg egy [virtuális gép bővítmény](/azure/virtual-machines/extensions/overview) használatával az illesztőprogram telepítésének és frissítéseinek egyszerűsítéséhez számos virtuális gépközött. Használja az [NVIDIA GPU illesztőprogram-bővítményt](/azure/virtual-machines/extensions/hpccompute-gpu-windows) NVIDIA GPU-val rendelkező virtuális gépekhez, és használja az AMD GPU-illesztőprogram-bővítményt (hamarosan) az AMD GPU-val rendelkező virtuális gépekhez.
* Fontolja meg az Active Directory csoportházirend használatával egyszerűsíteni a csoportházirend-konfigurációt több virtuális gép között. A csoportházirend Active Directory tartományban történő központi telepítéséről a [Csoportházirend-objektumok használata](https://go.microsoft.com/fwlink/p/?LinkId=620889)című témakörben talál további információt.
