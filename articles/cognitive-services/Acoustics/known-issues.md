---
title: Project Acoustics plug-in ismert problémák
titlesuffix: Azure Cognitive Services
description: A Project Akusztika című témakörben az alábbi ismert problémák léphetnek fel.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243043"
---
# <a name="project-acoustics-known-issues"></a>Project Akusztika ismert problémák
Ez a cikk a Project Akusztika használata során felmerülő problémákat ismerteti.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Jelenet átnevezésekénél az akusztikai paraméterek elvesznek

Ha átnevez egy jelenetet, a jelenethez tartozó összes akusztikai paraméter nem kerül át automatikusan az új jelenetbe. De még mindig léteznek a régi vagyonfájlban. Keresse meg a *[SceneName]_AcousticParameters.asset* fájlt a *szerkesztő* könyvtárban a jelenetfájl mellett. Nevezze át a fájlt úgy, hogy az tükrözze az új jelenet nevét.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Deploy-to-Android hiba néhány Unity verzióból

A Unity egyes verziói [ban van](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) egy hiba abban, ahogyan a hangbeépülő modulokat androidos rendszerekre telepítik. Győződjön meg arról, hogy nem olyan verziót használ, amelyet ez a hiba érint.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"Nem található metaadatfájl System.Security.dll" hibaüzenet

Győződjön meg arról, hogy a **Lejátszó** beállításaiban a **Parancsfájlfuttatási futásidejű verzió** *.NET 4.x egyenértékű,* majd indítsa újra a Unity programot.

## <a name="authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák az Azure-hoz való csatlakozáskor

Ellenőrizze, hogy:
- Az Azure-fiókjához a megfelelő hitelesítő adatokat használta.
- Fiókja támogatja a sütni kért csomópont típusát.
- A rendszeróra megfelelően van beállítva.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>A Sütés lapon a "törlés" látható a lemondás után is
A Project Acoustics a sikeres befejezés vagy törlés után törli az összes Azure-erőforrást egy feladathoz. Ez a folyamat akár 5 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Unity](unity-quickstart.md) vagy [az Unreal](unreal-quickstart.md) mintatartalmat.
