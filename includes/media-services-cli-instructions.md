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
ms.openlocfilehash: 06651b06ae84934c16e9f1ac9f604abda8b65615
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55648573"
---
## <a name="open-cli-shell"></a>Nyissa meg a parancssori felület

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


[Fájlok feltöltése]: ./media/media-services-cli/upload-download-files.png
