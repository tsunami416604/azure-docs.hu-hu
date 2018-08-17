---
title: Akusztika beépülő modul – Cognitive Services szolgáltatással kapcsolatos ismert problémák
description: A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: b0dd5e0c365c65fc7b29752f7885acb71bdb3df8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181695"
---
# <a name="known-issues"></a>Ismert problémák
A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akusztikai paraméterek elvesznek, ha átnevezi álló jelenet renderelése

Ha átnevez egy jelenetet, a akusztikai paramétereket, hogy a jelenet tartozó nem kerülnek automatikusan át az új jelenetet. Ezek még létezni fognak a régi objektumfájl azonban. Keresse meg a **SceneName_AcousticParameters.asset** belül fájlt a **szerkesztő** könyvtárat a jelenetfájl mellett. Nevezze át a fájlt, hogy tükrözzék az új helyszín neve.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Az alapértelmezett elérési utat a AcousticsData mappáját szondák fülre az abszolút elérési út

Ez kell alapértelmezett könnyebb megosztásához projektek között közreműködők relatív elérési utat. Áthidaló megoldásként viszonyított projektkönyvtár kell elérési útvonalának módosításához.

## <a name="runtime-voxels-are-a-different-size-than-design-time-voxels"></a>Futásidejű voxels, mint a tervezési idejű voxels más méretre

Ha mégis egy **Calculate** a a **mintavételek** lapon és a nézet a voxels hajtsa végre egy bake és nézet voxels azonos jelenet a futásidőben, a voxels különböző méretekre. Az üzem előtti bake látható voxels a voxels szimuláció használatban. A futási időben látható voxels mintavételi pontok közötti interpolációs szolgálnak. Emiatt előfordulhat, hogy inkonzisztencia, ahol portálok nyissa meg a futásidőben, amelyek ténylegesen nyílt nem fog megjelenni.

## <a name="uwp-builds-not-working"></a>Az UWP hoz létre, nem működik

A legfrissebb verzióit a Unity (2018.2 +) az UWP-buildek nem lezajlottak. A build futtatási fázisa – fog megrekedésének, és a "Unity-bővítmények vannak még nincs inicializálva" hibákat kap. Ez által nyomon követett [Unity probléma](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Unity összeomlik, ha a projekt lezárása

A Unity (2018.2 +) legújabb verzióját van egy ismert hiba, ahol Unity összeomlik, ha bezárja a projekthez. Ez által nyomon követett [Unity probléma](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Hiba történt az Android üzembe helyezése
Az Androidos projekt Akusztika használatához módosítsa a build target Android. Unity egyes verziói rendelkeznek hang beépülő modulok telepítése hibája – ellenőrizze, hogy nem használ egy verzió által érintett [ezen hiba](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>A "fájl nem található metaadat System.Security.dll" hibaüzenetet kapok

Győződjön meg arról, a lejátszó beállítások Scripting futtatókörnyezet verziójának beállítása **.NET 4.x egyenértékű**, és indítsa újra a Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák léptek fel az Azure-ba való csatlakozáskor

Ellenőrizze a megfelelő hitelesítő adatokat az Azure-fiókjával, hogy a fiók támogatja-e a bake a kért csomópont típusa, és hogy a rendszeróra pontos használt.

## <a name="next-steps"></a>További lépések
* Első lépések [a Unity-projektben Akusztika integrálása](getting-started.md)

