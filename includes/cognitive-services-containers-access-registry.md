---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704173"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>A Docker parancssori felületének használata a privát tároló beállításjegyzékének hitelesítéséhez

A Cognitive Services tárolók számára több módon is végezheti a hitelesítést a tárolók beállításjegyzékében, de a parancssorban ajánlott módszer a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használata.

Használja az [ `docker login` parancsot](https://docs.docker.com/engine/reference/commandline/login/)az alábbi példában `containerpreview.azurecr.io`látható módon a Cognitive Services tárolók magánhálózati tárolójának beállításjegyzékében. Cserélje * \<le\> a username* nevet és * \<a jelszót\> * az Azure Cognitive Services csapata által kapott hitelesítő adatokban megadott jelszóra.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha a hitelesítő adatait szövegfájlban védi, a következő példában látható módon összefűzheti a szövegfájl tartalmát a parancs használatával `cat` a `docker login` parancshoz. Cserélje * \<le\> a passwordFile* a jelszót és * \<a felhasználónevet\> * tartalmazó szövegfájl elérési útjára és nevére a hitelesítő adataiban megadott felhasználónévvel.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
