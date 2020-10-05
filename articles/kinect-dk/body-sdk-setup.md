---
title: Rövid útmutató – Azure Kinect Body-követés beállítása
description: Ebben a rövid útmutatóban az Azure Kinect-hez készült Body Tracking SDK-t fogja beállítani.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, szenzor, hozzáférés, mélység, SDK, törzs, nyomon követés, közös, beállítás, CUDA, NVIDIA
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277961"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Gyors útmutató: az Azure Kinect-törzs nyomon követésének beállítása

Ez a rövid útmutató végigvezeti Önt az Azure Kinect DK-on futó szövegtörzs-követés folyamatán.

## <a name="system-requirements"></a>Rendszerkövetelmények

A Body Tracking SDK használatához a gazdagép SZÁMÍTÓGÉPén telepítve kell lennie egy NVIDIA GPU-nak. A Body Tracking Host PC-re vonatkozó követelményt a [rendszerkövetelmények](system-requirements.md) lapon találja.

## <a name="install-software"></a>Szoftvertelepítés

### <a name="install-the-latest-nvidia-driver"></a>[A legújabb NVIDIA-illesztőprogram telepítése](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Töltse le és telepítse a legújabb NVIDIA-illesztőprogramot a grafikus kártyához. Előfordulhat, hogy a régebbi illesztőprogramok nem kompatibilisek a Body Tracking SDK-val újraterjesztett CUDA bináris fájlokkal.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[A Visual Studio 2015 szoftverhez készült Visual C++ terjeszthető változata](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Töltse le és telepítse a Visual C++ terjeszthető csomagját a Visual Studio 2015-es verzióra. 

## <a name="set-up-hardware"></a>Hardver beállítása

### <a name="set-up-azure-kinect-dk"></a>[Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)

Indítsa el az [Azure Kinect Viewert](azure-kinect-viewer.md) annak ellenőrzéséhez, hogy az Azure Kinect DK helyesen van-e beállítva.

## <a name="download-the-body-tracking-sdk"></a>A Body Tracking SDK letöltése
 
1. Válassza ki a hivatkozást a [Body Tracking SDK letöltéséhez](body-sdk-download.md)
2. Telepítse a Body Tracking SDK-t a számítógépre.

## <a name="verify-body-tracking"></a>Törzs követésének ellenőrzése

Indítsa el az **Azure Kinect Body Tracking Viewert** , és győződjön meg arról, hogy a Body Tracking SDK helyesen van beállítva. A megjelenítőt az SDK MSI telepítője telepíti. A Start menüben vagy a-on is megtalálhatja `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` .

Ha nem rendelkezik elég nagy teljesítményű GPU-val, és továbbra is tesztelni szeretné az eredményt, az alábbi parancs futtatásával elindíthatja az **Azure Kinect Body Tracking Viewert** a parancssorban: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Ha minden megfelelően be van állítva, a rendszer egy 3D pont felhővel és nyomon követett törzstel rendelkező ablakot jelenít meg.


![Body Tracking 3D megjelenítője](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Példák

A Body Tracking SDK használatáról [itt](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)talál példákat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Az első testkövetési alkalmazás összeállítása](build-first-body-app.md)

