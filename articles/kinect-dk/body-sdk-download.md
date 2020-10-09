---
title: Azure Kinect Body Tracking SDK letöltése
description: Ismerje meg, hogyan töltheti le az Azure Kinect Sensor SDK egyes verzióit Windows vagy Linux rendszeren.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, frissítés letöltése, legújabb, elérhető, telepítés, törzs, nyomon követés
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277584"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Az Azure Kinect Body Tracking SDK letöltése

Ez a dokumentum az Azure Kinect Body Tracking SDK egyes verzióinak telepítésére mutató hivatkozásokat tartalmaz.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Az Azure Kinect Body Tracking SDK tartalma

- Fejlécek és kódtárak egy Body Tracking-alkalmazás létrehozásához az Azure Kinect DK használatával.
- A Body Tracking Applications által az Azure Kinect DK használatával szükséges újraterjeszthető DLL-ek.
- Minta törzs nyomon követésére szolgáló alkalmazások.

## <a name="windows-download-links"></a>Windows letöltési hivatkozások

Verzió       | Letöltés
--------------|----------
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100636) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100415) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100307) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100128) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100063) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=58402) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linuxos telepítési utasítások

Jelenleg az egyetlen támogatott disztribúció az Ubuntu 18,04. További terjesztések támogatásának kéréséhez tekintse meg [ezt a lapot](https://aka.ms/azurekinectfeedback).

Először konfigurálnia kell a [Microsoft Package repositoryját](https://packages.microsoft.com/), az [itt](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software)található utasításokat követve.

A `libk4abt<major>.<minor>-dev` csomag tartalmazza a felépíteni kívánt fejléceket és CMAK-fájlokat `libk4abt` .
A `libk4abt<major>.<minor>` csomag tartalmazza a végrehajtható fájlok futtatásához szükséges megosztott objektumokat, valamint `libk4abt` a példa megjelenítőjét.

Az alapszintű oktatóanyagokhoz szükség van a `libk4abt<major>.<minor>-dev` csomagra. A telepítéséhez futtassa a következőt

`sudo apt install libk4abt1.0-dev`

Ha a parancs sikeres, az SDK készen áll a használatra.

> [!NOTE]
> Az SDK telepítésekor jegyezze fel a telepítési útvonalat. Például: "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Az ezen az elérési úton található cikkekben hivatkozott mintákat fogja megtalálni.
> A szövegtörzs-követési minták az Azure-Kinect-Samples adattár [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) mappájában találhatók. A cikkekben hivatkozott mintákat itt találja.

## <a name="change-log"></a>Változási napló

### <a name="v101"></a>v 1.0.1
* [Hibajavítás] Javítsa ki a problémát, hogy az SDK összeomlik, ha onnxruntime.dll betöltését a Windows Build 19025 vagy újabb verziójának elérési útjáról: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Vonás Adja hozzá a C# burkolót az MSI-telepítőhöz.
* [Hibajavítás] Javítsa ki a hibát, hogy a fej forgása nem észlelhető helyesen: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Hibajavítás] Javítsa ki a problémát, hogy a CPU-használat 100%-kal növekszik a Linux rendszerű gépen: [hivatkozás](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Minták Vegyen fel két mintát a minta tárházba. Az 1. minta bemutatja, hogyan alakíthatja át a törzs nyomon követésének eredményét a mélységi területről a színtérre [mutató hivatkozásig](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample). a 2. minta azt mutatja be, hogyan lehet felderíteni a padló síkja [hivatkozását](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

### <a name="v095"></a>v 0.9.5
* Vonás C#-támogatás. A C# burkoló a nuget csomagban van csomagolva.
* Vonás Többszörös nyomkövetés támogatása. Több Tracker létrehozása is engedélyezett. A felhasználók a különböző Azure Kinect-eszközökből származó szervek nyomon követéséhez több nyomkövetést is létrehozhatnak.
* Vonás Multi-thread Processing-támogatás a CPU-üzemmódhoz. Ha CPU-módban fut, az összes magot a rendszer a sebesség maximalizálása érdekében használja fel.
* Vonás Hozzáadás `gpu_device_id` a `k4abt_tracker_configuration_t` struct-hoz. Annak engedélyezése, hogy a felhasználók az alapértelmezetttől eltérő GPU-eszközt adjanak meg a törzs nyomon követési algoritmusának futtatásához.
* [Hibajavítás/változás megszakítása] Egy közös névvel javítsa ki az elírást. Módosítsa a közös nevet a verzióról a verzióra `K4ABT_JOINT_SPINE_NAVAL` `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Vonás Adja hozzá a kézzel összekötések támogatást. Az SDK a következő három további kötésről nyújt információkat: HAND, HANDTIP, THUMB.
* Vonás Előrejelzési megbízhatósági szint hozzáadása minden észlelt kötéshez.
* Vonás Adja hozzá a CPU-mód támogatását. A `cpu_only_mode` értékének módosításával `k4abt_tracker_configuration_t` mostantól az SDK futhat CPU-módban, amelyhez nem szükséges, hogy a felhasználó hatékony grafikus kártyával rendelkezzen.

### <a name="v093"></a>v 0.9.3
* Vonás Tegyen közzé egy új DNN modellt dnn_model_2_0. Onnx, amely nagy mértékben javítja a törzs nyomon követésének megbízhatóságát.
* Vonás Alapértelmezés szerint tiltsa le az ideiglenes simítást. A követett ízületek rugalmasabbak lesznek.
* Vonás Javítsa ki a törzs index-térképének pontosságát.
* [Hibajavítás] Javítsa ki a hibát, hogy az érzékelő tájolási beállítása nem érvényes.
* [Hibajavítás] Módosítsa a body_index_map típusát K4A_IMAGE_FORMAT_CUSTOMról K4A_IMAGE_FORMAT_CUSTOM8re.
* [Ismert probléma] Két zárt szerv egyesíthető egypéldányos szegmensben.

### <a name="v092"></a>v 0.9.2
* [A változás megszakítása] A frissítés a legújabb Azure Kinect Sensor SDK-1.2.0 függ.
* [API-változás] `k4abt_tracker_create` a függvény elkezdi bevenni a `k4abt_tracker_configuration_t` bemenetet. 
* [API-változás] Módosítsa az API-t úgy, `k4abt_frame_get_timestamp_usec` hogy konkrétabb `k4abt_frame_get_device_timestamp_usec` és konzisztens legyen az Sensor SDK 1.2.0.
* Vonás Lehetővé teszi a felhasználók számára, hogy megadják az érzékelő csatlakoztatási irányát a Szemléző létrehozásakor, hogy a törzs a különböző szögekből való csatlakoztatáskor pontosabb eredményeket érjen el.
* Vonás Adja meg az új API `k4abt_tracker_set_temporal_smoothing` -t a felhasználó által alkalmazni kívánt időbeli simítás mértékének módosításához.
* Vonás Adja hozzá a C++ burkoló K4ABT. HPP.
* Vonás Adja hozzá a k4abtversion. h verziószám-definíciós fejlécét.
* [Hibajavítás] A rendkívül magas CPU-használatot okozó hibák elhárítása.
* [Hibajavítás] A naplózó összeomlási hibájának javítása.

### <a name="v091"></a>v 0.9.1
* [Hibajavítás] Memóriavesztés javítása a Szemléző megsemmisítése során
* [Hibajavítás] Jobb hibaüzenetek a hiányzó függőségekhez
* [Hibajavítás] Összeomlás nélkül meghiúsul egy második nyomkövetési példány létrehozásakor
* [Hibajavítás] A naplózó környezeti változók mostantól megfelelően működnek
* Linux-támogatás

### <a name="v090"></a>v 0.9.0

* [A változás megszakítása] Visszaminősítette az SDK-függőséget a CUDA 10,0-re (a CUDA 10,1-ből). A ONNX Runtime hivatalosan csak a CUDA 10,0-es verzióját támogatja.
* [A változás megszakítása] A Tensorflow futtatókörnyezet helyett a ONNX futtatókörnyezetre vált. Csökkenti az első keret indításának időpontját és a memóriahasználat használatát. Emellett csökkenti az SDK bináris méretét is.
* [API-változás] Átnevezve erre `k4abt_tracker_queue_capture()``k4abt_tracker_enqueue_capture()`
* [API-változás] `k4abt_frame_get_body()` Két különálló függvénybe tört: `k4abt_frame_get_body_skeleton()` és `k4abt_frame_get_body_id()` . Most lekérdezheti a törzs AZONOSÍTÓját anélkül, hogy mindig a teljes csontváz-struktúrát másolja.
* [API-változás] Hozzáadott  `k4abt_frame_get_timestamp_usec()` függvény, amely leegyszerűsíti a felhasználók számára a törzsi keret időbélyegének lekérdezéséhez szükséges lépéseket.
* A Body Tracking algoritmus pontosságának növelése és a megbízhatóság nyomon követése

## <a name="next-steps"></a>További lépések

- [Az Azure Kinect DK áttekintése](about-azure-kinect-dk.md)

- [Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)

- [Az Azure Kinect Body követésének beállítása](body-sdk-setup.md)
