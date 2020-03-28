---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461214"
---
## <a name="use-cli-shell"></a>CLI shell használata

Cli-parancsok végrehajtásához ajánlott az [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) használata. **A Cloud Shell** egy ingyenes, interaktív rendszerhéj, amelynek segítségével futtathatja a cikkben ismertetett lépéseket. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület kiválasztásának lehetőségével. Linux-felhasználók választhatják a Bash-felületet, míg a Windows-felhasználók a PowerShellt részesíthetik előnyben.

A CLI helyileg is telepíthető. A platformra vonatkozó utasításokért [olvassa el Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című témakört.

### <a name="sign-in"></a>Bejelentkezés

A CLI helyi telepítésének használatához be kell jelentkeznie az Azure-ba. Ez a lépés nem szükséges az Azure Cloud Shell. Jelentkezzen be `az login` a paranccsal.

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőlapot, és a parancssorban található https://aka.ms/devicelogin utasításokat követve meg kell adnia egy engedélyezési kódot, miután a böngészőben navigált.

### <a name="specify-location-of-files"></a>Fájlok helyének megadása

Számos Media Services CLI parancs lehetővé teszi, hogy egy fájlnévvel rendelkező paramétert adjaát át. Ha a **Cloud Shellt**használja, feltöltheti a fájlt a clouddrive-ra (bash vagy PowerShell használatával). 

![Fájlok feltöltése]

Akár helyi CLI-t, akár **felhőbeli rendszerhéjat**használ, meg kell adnia a fájl elérési útját az operációs rendszer vagy a felhőbeli rendszerhéj (Bash vagy PowerShell) szerint. Az alábbiakban néhány példa:

A fájl relatív elérési útja (az összes operációs rendszer)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Abszolút fájlelérési út Linux /Mac és Windows operációs rendszeren

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Akkor `{file}` használja, ha a parancs a fájl elérési útját kéri. Például: `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Akkor `@{file}` használja, ha a parancs be tölti a megadott fájlt. Például: `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Fájlok feltöltése]: ./media/media-services-cli/upload-download-files.png
