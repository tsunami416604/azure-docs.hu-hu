---
title: Projekt Akusztika beépülő modul ismert problémái
titlesuffix: Azure Cognitive Services
description: A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.
services: cognitive-services
author: kylestorck
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 02f56306110e904f41c1b94346c58b33ce0b575c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156184"
---
# <a name="known-issues"></a>Ismert problémák
A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akusztikai paraméterek elvesznek, ha átnevezi álló jelenet renderelése

Ha átnevez egy jelenetet, a akusztikai paramétereket, hogy a jelenet tartozó nem kerülnek automatikusan át az új jelenetet. Ezek még létezni fognak a régi objektumfájl azonban. Keresse meg a **SceneName_AcousticParameters.asset** belül fájlt a **szerkesztő** könyvtárat a jelenetfájl mellett. Nevezze át a fájlt, hogy tükrözzék az új helyszín neve.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Futásidejű voxels mérete jelenet előzetes voxels eltér

Ha mégis egy **Calculate** a a **mintavételek** lapon és a nézet a voxels hajtsa végre egy bake és nézet voxels azonos jelenet a futásidőben, a voxels különböző méretekre. Az üzem előtti bake látható voxels a voxels szimuláció használatban. A futási időben látható voxels mintavételi pontok közötti interpolációs szolgálnak. Emiatt előfordulhat, hogy inkonzisztencia, ahol portálok nyissa meg a futásidőben, amelyek ténylegesen nyílt nem fog megjelenni.

## <a name="unity-crashes-when-closing-project"></a>Unity összeomlik, ha a projekt lezárása

A Unity (2018.2 +) legújabb verzióját van egy ismert hiba, ahol Unity összeomlik, ha bezárja a projekthez. Ez által nyomon követett [Unity probléma](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Hiba történt az Android üzembe helyezése
Az Androidos projekt Akusztika használatához módosítsa a build target Android. Unity egyes verziói rendelkeznek hang beépülő modulok telepítése hibája – ellenőrizze, hogy nem használ egy verzió által érintett [ezen hiba](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>A "fájl nem található metaadat System.Security.dll" hibaüzenetet kapok

Győződjön meg arról, a lejátszó beállítások Scripting futtatókörnyezet verziójának beállítása **.NET 4.x egyenértékű**, és indítsa újra a Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák léptek fel az Azure-ba való csatlakozáskor

Ellenőrizze a megfelelő hitelesítő adatokat az Azure-fiókjával, hogy a fiók támogatja-e a bake a kért csomópont típusa, és hogy a rendszeróra pontos használt.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Egy bake megszakítása hagyja el a "törlése" Bake lap
Projekt Akusztika fog karbantartása az összes Azure-erőforrások egy feladat sikeres befejezése vagy törlését, amely akár 5 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
* Első lépések: [az akusztika integrálása a Unity-projektbe](getting-started.md)

