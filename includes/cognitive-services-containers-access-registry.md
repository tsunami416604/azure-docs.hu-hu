---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704173"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>A Docker parancssori felületének használata a privát tároló beállításjegyzékének hitelesítéséhez

A Cognitive Services tárolók számára több módon is végezheti a hitelesítést a tárolók beállításjegyzékében, de a parancssorban ajánlott módszer a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használata.

Használja az [ `docker login` parancsot](https://docs.docker.com/engine/reference/commandline/login/)az alábbi példában látható módon a `containerpreview.azurecr.io` Cognitive Services tárolók magánhálózati tárolójának beállításjegyzékében. Cserélje le az *\<username\>* elemet a felhasználónévvel és *\<password\>* az Azure Cognitive Services csapattól kapott hitelesítő adatokkal megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha a hitelesítő adatait szövegfájlban védi, a következő példában látható módon összefűzheti a szövegfájl tartalmát a parancs használatával a `cat` `docker login` parancshoz. A helyére írja *\<passwordFile\>* be a jelszót tartalmazó szövegfájl elérési útját és nevét, valamint *\<username\>* a hitelesítő adataiban megadott felhasználónevet.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
