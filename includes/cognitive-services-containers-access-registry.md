---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704173"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>A Docker parancssori felületének használata a privát tároló beállításjegyzékének hitelesítéséhez

A Cognitive Services tárolók számára több módon is végezheti a hitelesítést a tárolók beállításjegyzékében, de a parancssorban ajánlott módszer a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használata.

Használja a [`docker login` parancsot](https://docs.docker.com/engine/reference/commandline/login/)az alábbi példában látható módon, hogy bejelentkezzen a `containerpreview.azurecr.io`ba, Cognitive Services tárolók privát tároló-beállításjegyzékében. Cserélje le *\<username\>* nevet a felhasználónévvel és a *\<jelszavával\>* az Azure Cognitive Services csapattól kapott hitelesítő adatokban megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha a hitelesítő adatait szövegfájlban védi, akkor a `cat` parancs használatával összefűzheti a szövegfájl tartalmát a `docker login` parancshoz az alábbi példában látható módon. Cserélje le *\<passwordFile\>* a jelszót tartalmazó szövegfájl elérési útjára és nevére, és *\<a felhasználónevet\>* a hitelesítő adataiban megadott felhasználónévvel.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
