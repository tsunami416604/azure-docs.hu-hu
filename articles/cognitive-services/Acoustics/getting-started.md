---
title: Projekt Akusztika – első lépések
titlesuffix: Azure Cognitive Services
description: A rövid útmutató bemutatja, hogyan integrálhatja a beépülő modul a Unity-projektjét, a jelenethez os és Akusztika a alkalmazni a megbízható forrásból.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 0aad231b4a57c2d9927a553a43394681282b5494
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155952"
---
# <a name="getting-started-with-project-acoustics"></a>Projekt Akusztika – első lépések
A rövid útmutató bemutatja, hogyan integrálhatja a beépülő modul a Unity-projektjét, a jelenethez os és Akusztika a alkalmazni a megbízható forrásból. Ebben a rövid útmutatóban a következőket kell tennie először hozzon létre egy [Azure batch-fiók](create-azure-account.md). Ez az útmutató a Unity bizonyos fokú ismeretét feltételezi.

## <a name="download-the-plugin"></a>A beépülő modul letöltése
Regisztráljon [Itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) a Tervező előzetes verzióra.

## <a name="supported-platforms-for-quickstart"></a>A rövid útmutató által támogatott platformok
* [Unity 2018.2 +](http://www.unity3d.com)
  * A projekt beállítást igényel a **.NET 4.x egyenértékű** scripting verze modulu runtime 
  * A Windows-alapú Unity-szerkesztő szükséges

## <a name="import-the-plugin"></a>A beépülő modul importálása
Importálja a Akusztika UnityPackage a projekthez. 
* A Unity, váltson **eszközök > csomag importálása > egyéni csomag...**

![Csomag importálása](media/ImportPackage.png)  

* Válasszon **MicrosoftAcoustics.unitypackage**

Ha a beépülő modul be egy meglévő projekt importál, a projekt már előfordulhat, hogy egy **mcs.rsp** fájlt a projekt gyökérkönyvtárában, amely azt adja meg a C# fordítóprogram lehetőségeket. Szüksége lesz a mcs.rsp fájllal, amely a projekt Akusztika beépülő modult tartalmaz a fájl tartalmát egyesíti.

## <a name="enable-the-plugin"></a>A beépülő modul engedélyezése
A Akusztika eszközkészlet bake részének .NET 4.x scripting futásidejű verzióját igényli. Csomag importálása frissíteni fogja a Unity-lejátszó beállításai. Unity-Ez a beállítás érvénybe léptetéséhez indítsa újra.

![Lejátszó beállításai](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>A navigációs háló létrehozása
Használja a standard [Unity munkafolyamat](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) navigációs rácsvonal a projekt létrehozásához. A saját navigációs rácsvonalak használatáról további információért lásd: a [végig a felhasználói felület bake](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Jelölje meg a Akusztika statikus rácsvonalak
Csatlakozva a Akusztika ablak használatával **ablak > Akusztika** a Unity-nél. Ezt az ablakot a vizsgáló mellett is rögzíthetők.

![Nyissa meg Akusztika ablak](media/WindowAcoustics.png)

A Unity-hierarchia ablakában kijelölését bármely kiválasztott cikkek. Az a Akusztika **objektum** lapon kattintson a "Akusztika geometriai" jelölőnégyzetet, hogy az összes rácsvonalak, és a jelenet Akusztika geometry, terrains.

Az a **anyagok** lapon, az akusztikai anyagok hozzárendelése a jelenet felhasznált anyagokat. A **alapértelmezett** anyag rendelkezik egyenértékű tényleges elnyelő. A saját anyagok tulajdonságok megadásával kapcsolatban további információkért lásd: a [tervezési folyamat lap](design-process.md).

![Anyagok lap](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>A mintavételezők előzetes verzió
Az a **mintavételek** lapra, majd **Calculate**. Ez a számítás, jelenet méretétől függően néhány percet is igénybe vehet. Számítási befejeződése után láthatja a jelenet nézetben területén, amely jelölje meg a helyek Akusztika szimuláció, "a pontok mintavételi" nevű lebegőpontos. Ha kellően objektumra a jelenet ablakban, láthatja a jelenet voxelization is. A zöld voxels, geometriai megjelölve az objektumokat kell illeszkedik. A mintavétel pontok és voxel jeleníti meg a Gizmos menü felső bekapcsolható a jelenet nézet, jobb.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>A helyszín os
Az a **os** lap, adja meg Azure hitelesítő adatait, és kattintson a **os**. Ha az Azure Batch-fiók nem rendelkezik, tekintse meg [az ajánlott fiók beállítása a forgatókönyv](create-azure-account.md).
Ha a bake befejeződött, az adatfájl automatikusan letöltődnek a a **eszközök/AcousticsData** könyvtárat a projekthez.

## <a name="set-up-audio-runtime-dsp"></a>Hang futásidejű DSP beállítása
Azt a hang futásidejének DSP Akusztika beágyazása a Unity spatializer keretrendszer és integrálhatja a spatialization HRTF-alapú. Akusztika feldolgozási engedélyezéséhez váltson a **Microsoft Acoustics** spatializer a **szerkesztése > Project Settings > hang**, és válassza ki **Microsoft Acoustics** mint a **Spatializer beépülő modul** a projekthez. Győződjön meg arról is, a **DSP pufferméret** van beállítva, a legjobb teljesítmény elérése érdekében.

![Projektbeállítások](media/ProjectSettings.png)  

![Projekt Akusztika Spatializer](media/ChooseSpatializer.png)

Nyissa meg a hang Mixer (**ablak > hang Mixer**). Ellenőrizze, hogy szükség van legalább egy Mixer egy csoportot. Ha nem, a jobb oldalán található "+" gombra kattintva **vonja**. Kattintson a jobb gombbal a hatások szakaszban a csatorna sáv alján, és adja hozzá a **Microsoft Akusztika Mixer** érvénybe. Vegye figyelembe, hogy egyszerre csak egy projekt Akusztika Mixer támogatott.

![Hang Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Állítsa be a Akusztika keresési tábla
Húzza át a **Microsoft Acoustics** prefab a projekt panelről a jelenethez be:

![Acoustics Prefab](media/AcousticsPrefab.png)

Kattintson a **ProjectAcoustics** játék objektum, és ugorjon a Megtekintő panelen. Adja meg a helyet a bake eredmény (a. A fájl, ACE **eszközök/AcousticsData**) által húzza és törlése a Akusztika Manager parancsfájlba, illetve a szövegbeviteli mező mellett a kör gombra kattintva.

![Akusztika Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Megbízható forrásra Akusztika alkalmazása
Hozzon létre egy hangforrásról. Kattintson a jelölőnégyzetbe, amely szerint a AudioSource vizsgáló panel alján **Spatialize**. Győződjön meg arról, hogy **térbeli Blend** teljes 3D értékre van állítva.  

![Hangbemeneti forrás](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Alkalmazása utáni bake kialakítása
A parancsfájl csatolhat a szolgáltatáskéréshez **AcousticsAdjust** kattintva engedélyezéséhez a forrás további tervezési paraméterek, a jelenet megbízható forrásra **összetevő felvétele** választva **parancsfájlok > Akusztika Állítsa be**:

![AcousticsAdjust](media/AcousticsAdjust.png)

Nincsenek is paraméterek a a **Microsoft Akusztika Mixer**. Utáni bake tervezési kapcsolatos további információkért lásd: [paraméterek tervezési](design-process.md).

## <a name="next-steps"></a>További lépések
* Próbálja ki a [minta jelenet renderelése;](sample-walkthrough.md)
* Ismerje meg a teljes körű [os funkciók](bake-ui-walkthrough.md)
* Ismerje meg a részletes [paraméterek tervezése](design-process.md)

