---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104983"
---
## <a name="open-cli-shell"></a>Nyissa meg a parancssori felület

Javasoljuk, hogy használjon [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) hajtsa végre a CLI-parancsokat. **Cloud Shell** egy ingyenes, interaktív felület, amely ebben a cikkben található lépések futtatására használhatja. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. Csak adja meg a Másolás gombbal másolja a vágólapra a kódot, illessze be a Cloud Shellt, és nyomja le az ENTER billentyűt a futtatáshoz. A Cloud Shell többféleképpen is megnyitható:

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket. 

### <a name="login"></a>Bejelentkezés

Futtassa a parancssori felület (a felhőben vagy helyileg), használatának megkezdéséhez `az login` kapcsolat létrehozása az Azure-ral.

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben kell egy böngésző lap megnyitásához, és kövesse az utasításokat a parancssorban adja meg a hozzáférési kód után ellenőrizheti, hogy a https://aka.ms/devicelogin a böngészőben.

### <a name="specify-location-of-files"></a>Adja meg a fájlok helyét

Számos Media Services CLI-parancsok lehetővé teszik a fájl nevét paramétert. 

Ha használ **Azure Cloud Shell**, töltse fel a fájlt **Azure Cloud Shell**. A feltöltési/letöltési fájlok gombra a rendszerhéj-ablak tetején található. Ezután hivatkozhat a fájlt ehhez hasonló: `@{FileName}.` 

![Fájlok feltöltése]

Ha az Azure CLI helyileg használ, adja meg a fájl teljes elérési útja. Például: `@c:\tracks.json`.


[Fájlok feltöltése]: ./media/media-services-cli/upload-download-files.png
