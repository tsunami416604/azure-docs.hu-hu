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
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964175"
---
## <a name="cli-shell"></a>Parancssori felület

Javasoljuk, hogy használjon [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) hajtsa végre a CLI-parancsokat. **Cloud Shell** egy ingyenes, interaktív felület, amely ebben a cikkben található lépések futtatására használhatja. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. Biztosít munkamódszer leginkább megfelelő rendszerhéj-felületet válassza. A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket. 

### <a name="login"></a>Bejelentkezés

Futtassa a parancssori felület (a felhőben vagy helyileg), használatának megkezdéséhez `az login` kapcsolat létrehozása az Azure-ral.

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben kell egy böngésző lap megnyitásához, és kövesse az utasításokat a parancssorban adja meg a hozzáférési kód után ellenőrizheti, hogy a https://aka.ms/devicelogin a böngészőben.

### <a name="specify-location-of-files"></a>Adja meg a fájlok helyét

Számos Media Services CLI-parancsok lehetővé teszik a fájl nevét paramétert. Ha használ **Cloud Shell**, feltöltheti a fájlt a clouddrive (Bash vagy a PowerShell használatával). 

![Fájlok feltöltése]

Használ-e a helyi CLI vagy **Cloud Shell**, meg kell adnia a fájl elérési útja alapján az operációs rendszer vagy a Cloud Shellt (a Bash vagy a PowerShell használatával), amelyet használ. Néhány példa:

Relatív elérési utat a fájlhoz (az összes operációs rendszer)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac és Windows Rendszereken a fájl abszolút elérési útja

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

Használat `{file}` , ha a parancs által kért a fájl elérési útját. Például: `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Használat `@{file}` Ha a parancs a megadott fájl betöltése. Például: `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Fájlok feltöltése]: ./media/media-services-cli/upload-download-files.png
