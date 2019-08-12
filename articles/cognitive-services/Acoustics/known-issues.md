---
title: A Project Acoustics beépülő modullal kapcsolatos ismert problémák
titlesuffix: Azure Cognitive Services
description: Előfordulhat, hogy a következő ismert problémák merülhetnek fel a Project Acoustics tervezői előzetes verziójának használatakor.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: df5a4ce62af0405ffab8f711fb40b3d92083a3a4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706664"
---
# <a name="project-acoustics-known-issues"></a>A projekt akusztikai ismert problémái
Előfordulhat, hogy a következő ismert problémák merülhetnek fel a Project Acoustics tervezői előzetes verziójának használatakor.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Az akusztikus paraméterek elvesznek a jelenet átnevezése során

Ha átnevez egy jelenetet, a jelenethez tartozó összes akusztikai paraméter nem lesz automatikusan átmásolva az új színtérre. Azonban továbbra is léteznek a régi adatfájlban. Keresse meg a **SceneName_AcousticParameters. Asset** fájlt a saját jelenet fájlja melletti **szerkesztő** könyvtárban. Nevezze át a fájlt úgy, hogy az tükrözze az új jelenet nevét.

## <a name="unity-crashes-when-closing-project"></a>Az egység összeomlik a projekt bezárásakor

Az Unity (2018.2 +) legújabb verzióiban létezik egy ismert hiba, amelyben az egység összeomlik a projekt lezárása után. Ezt az [egységességi probléma](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)követte nyomon.

## <a name="deploying-to-android-from-some-unity-versions"></a>Üzembe helyezés az Android rendszerbe néhány Unity-verzióból

Az Unity egyes verzióiban hiba történt a hangbeépülő modulok Androidra történő telepítése során. Győződjön meg arról, hogy nem használja a [hiba](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)által érintett verziót.

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Hibaüzenet jelenik meg, amely szerint a "nem található a metaadat-fájlrendszer. Security. dll"

Győződjön meg arról, hogy a Player-beállítások parancsfájl-futtatási verziója a **.net 4. x**értékre van beállítva, majd indítsa újra az Unity műveletet.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Hitelesítési problémák történtek az Azure-hoz való csatlakozáskor

Ellenőrizze, hogy a megfelelő hitelesítő adatokat használta-e az Azure-fiókjához, és hogy a fiókja támogatja-e a sütni-ben kért csomópont típusát, valamint hogy a rendszer órája pontos-e.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>A sütni megszakítása elhagyja a "Törlés" állapotban lévő sütni fület
A Project Acoustics minden Azure-erőforrást a sikeres befejezés vagy megszakítás után takarít meg a feladatokhoz. Ez akár 5 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
* Az [Unity](unity-quickstart.md) vagy az [Unreal](unreal-quickstart.md) minta tartalmának kipróbálása

