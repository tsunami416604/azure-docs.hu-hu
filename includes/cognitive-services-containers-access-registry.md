---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704173"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>A Docker parancssori felületén használják a hitelesítéshez a privát tárolóregisztrációs adatbázis

A Cognitive Services tárolók számos módon képes hitelesítést a privát tárolóregisztrációs adatbázis, de az ajánlott módszer a parancssorból, hogy használja a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Használja a [ `docker login` parancs](https://docs.docker.com/engine/reference/commandline/login/), jelentkezzen be az alábbi példában látható módon `containerpreview.azurecr.io`, a Cognitive Services-tárolók privát tárolójegyzékben. Cserélje le *\<felhasználónév\>* felhasználónévvel és *\<jelszó\>* a jelszóval, hogy melyik a hitelesítő adatait a kapott a Az Azure Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha már beállította a hitelesítő adatait a fájlt, a szöveges fájl tartalmának összefűzheti használatával a `cat` parancsra a `docker login` parancsot, az alábbi példában látható módon. Cserélje le *\<passwordFile\>* elérési útja és a jelszót tartalmazó szöveges fájl neve és *\<felhasználónév\>* felhasználónévvel a hitelesítő adatait, amely biztosítja.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
