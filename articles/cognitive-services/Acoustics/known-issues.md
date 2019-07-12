---
title: Projekt Akusztika beépülő modul ismert problémái
titlesuffix: Azure Cognitive Services
description: A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylesto
ms.openlocfilehash: e425575c6529ac4638fc963915362e0ec03f1117
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849915"
---
# <a name="project-acoustics-known-issues"></a>Projekt Akusztika ismert problémák
A következő ismert problémák léphetnek fel a projekt Akusztika a Tervező előzetes verzió használata esetén.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akusztikai paraméterek elvesznek, ha átnevezi álló jelenet renderelése

Átnevezése a helyszín esetén az összes, hogy a jelenet tartozó akusztikai paraméterek nem fog automatikusan át az új jelenetet. Fogja továbbra is léteznek a régi adategységfájlon azonban. Keresse meg a **SceneName_AcousticParameters.asset** belül fájlt a **szerkesztő** könyvtárat a jelenetfájl mellett. Nevezze át a fájlt, hogy tükrözzék az új helyszín neve.

## <a name="unity-crashes-when-closing-project"></a>Unity összeomlik, ha a projekt lezárása

A Unity (2018.2 +) legújabb verzióját van egy ismert hiba, ahol Unity összeomlik, ha bezárja a projekthez. Ez által nyomon követett [Unity probléma](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Az Android néhány Unity verzió üzembe helyezése

Unity egyes verziói hang beépülő modulok telepítése Android hibája rendelkezik. Ellenőrizze, hogy nem használja egy verzió által érintett [ezen hiba](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>A "fájl nem található metaadat System.Security.dll" hibaüzenetet kapok

Győződjön meg arról, a lejátszó beállítások Scripting futtatókörnyezet verziójának beállítása **.NET 4.x egyenértékű**, és indítsa újra a Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák léptek fel az Azure-ba való csatlakozáskor

Ellenőrizze a megfelelő hitelesítő adatokat az Azure-fiókjával, hogy a fiók támogatja-e a bake a kért csomópont típusa, és hogy a rendszeróra pontos használt.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Egy bake megszakítása hagyja el a "törlése" Bake lap
Az összes Azure-erőforrások egy feladat sikeres befejezése vagy megszakítás projekt Akusztika fogja törölni. Ez akár 5 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Unity](unity-quickstart.md) vagy [Unreal](unreal-quickstart.md) tartalom minta

