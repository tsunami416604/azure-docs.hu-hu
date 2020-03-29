---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704173"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>A Docker CLI használatával hitelesítheti a privát tároló beállításjegyzékét

A Cognitive Services-tárolók privát tárolóbeállítás-jegyzékében több módon is hitelesíthető, de a parancssorból ajánlott módszer a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használata.

Használja a [ `docker login` parancsot](https://docs.docker.com/engine/reference/commandline/login/), ahogy az a `containerpreview.azurecr.io`következő példában látható, jelentkezzen be a , a saját tároló rendszerleíró kognitív szolgáltatások tárolók. Cserélje * \<\> * le a felhasználónevet a felhasználónévre és * \<\> * a jelszóra az Azure Cognitive Services csapatától kapott hitelesítő adatokban megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha egy szöveges fájlban biztosította a hitelesítő adatokat, a `cat` parancs segítségével összefűzheti a parancs `docker login` tartalmát, ahogy az a következő példában látható. Cserélje le * \<a passwordFile-t\> * a jelszót és a * \<felhasználónevet\> * tartalmazó szövegfájl elérési útjának és nevének a hitelesítő adatokban megadott felhasználónévre.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
