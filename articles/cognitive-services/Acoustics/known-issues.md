---
title: A projekt akusztikai beépülő modul ismert problémái
titlesuffix: Azure Cognitive Services
description: A Project akusztikai szolgáltatásban a következő ismert problémák merülhetnek fel.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243043"
---
# <a name="project-acoustics-known-issues"></a>A projekt akusztikai ismert problémái
Ez a cikk azokat a problémákat ismerteti, amelyekkel a Project Acoustics használatakor előfordulhatnak.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Az akusztikus paraméterek elvesznek a jelenet átnevezése során

Ha átnevez egy jelenetet, a jelenethez tartozó összes akusztikai paraméter nem kerül automatikusan át az új színtérre. De továbbra is léteznek a régi adatfájlban. Keresse meg a *[SceneName] _AcousticParameters. Asset* fájlt a saját jelenet fájlja melletti *szerkesztő* könyvtárban. Nevezze át a fájlt, hogy az tükrözze az új jelenet nevét.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Központilag telepítheti az Android-alapú hibákat bizonyos Unity-verziókból

Az Unity egyes verzióiban [hiba](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) történt az audio beépülő modulok androidba való üzembe helyezése során. Győződjön meg arról, hogy nem használja a hiba által érintett verziót.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"A metaadat-fájlrendszer. Security. dll fájl nem található" hiba

Győződjön meg arról, hogy a **Player** -beállításokban a **parancsfájl-futtatókörnyezet verziója** *.net 4. x egyenértékű*, majd indítsa újra az egységet.

## <a name="authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák az Azure-hoz való csatlakozáskor

Győződjön meg a következőket:
- A megfelelő hitelesítő adatokat használta az Azure-fiókhoz.
- A fiókja támogatja a sütni-ben kért csomópont típusát.
- A rendszer órája helyesen van beállítva.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>A sütni lap továbbra is a "Törlés" kifejezést jeleníti meg a megszakítás után
A Project Acoustics az összes Azure-erőforrást a sikeres befejezés vagy megszakítás után megtisztítja a feladatokhoz. Ez a folyamat akár 5 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
* Próbálja ki az [Unity](unity-quickstart.md) vagy az [Unreal](unreal-quickstart.md) Sample tartalmat.
