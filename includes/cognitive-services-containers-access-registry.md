---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063933"
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
