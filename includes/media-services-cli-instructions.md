---
title: fájlbefoglalás
description: fájlbefoglalás
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79461214"
---
## <a name="use-cli-shell"></a>A CLI-rendszerhéj használata

A CLI-parancsok végrehajtásához a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) használata javasolt. A **Cloud Shell** egy ingyenes, interaktív felület, amelyet a cikkben szereplő lépések futtatására használhat. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület kiválasztásának lehetőségével. Linux-felhasználók választhatják a Bash-felületet, míg a Windows-felhasználók a PowerShellt részesíthetik előnyben.

A CLI-t helyileg is telepítheti. A platformra vonatkozó utasításokért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) .

### <a name="sign-in"></a>Bejelentkezés

A CLI helyi telepítésének használatához be kell jelentkeznie az Azure-ba. Ez a lépés nem szükséges a Azure Cloud Shellhoz. Jelentkezzen be a `az login` paranccsal.

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőablakot, és a parancssor utasításait követve meg kell adnia egy engedélyezési kódot a böngészőben való navigálás után https://aka.ms/devicelogin .

### <a name="specify-location-of-files"></a>Fájlok helyének meghatározása

Számos Media Services CLI-parancs lehetővé teszi, hogy egy paramétert egy fájlnévvel adjon át. Ha **Cloud Shell**használ, feltöltheti a fájlt a clouddrive (a bash vagy a PowerShell használatával). 

![Fájlok feltöltése]

Akár helyi CLI-t, akár **Cloud Shell**használ, meg kell adnia a fájl elérési útját az Ön által használt operációs rendszer vagy Cloud shell (bash vagy PowerShell) alapján. Néhány példa:

A fájl relatív elérési útja (minden operációs rendszer)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

A fájl abszolút elérési útja Linux/Mac és Windows operációs rendszeren

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Akkor használja, `{file}` Ha a parancs a fájl elérési útját kéri. Például: `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Akkor használja, `@{file}` Ha a parancs betölti a megadott fájlt. Például: `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Fájlok feltöltése]: ./media/media-services-cli/upload-download-files.png
