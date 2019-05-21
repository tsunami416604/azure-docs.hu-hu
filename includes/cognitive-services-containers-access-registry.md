---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65885007"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Docker CLI használják a hitelesítéshez a privát tárolóregisztrációs adatbázis

Többféleképpen is a Cognitive Services-tárolók a privát tárolóregisztrációs adatbázis hitelesítéséhez, de a parancssorból az ajánlott módszer használatával a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Használja a [docker login](https://docs.docker.com/engine/reference/commandline/login/) parancsot, amellyel majd bejelentkezik a következő példában látható módon `containerpreview.azurecr.io`, a Cognitive Services-tárolók privát tárolójegyzékben. Cserélje le *\<felhasználónév\>* felhasználónévvel és *\<jelszó\>* az Azure-tól kapott a hitelesítő adatokban megadott jelszóval Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Biztonságossá tett egy szövegfájlt a hitelesítő adatait, ha a szöveges tartalmát összefűzheti fájlt, a `cat` parancsra a `docker login` parancsot az alábbi példában látható módon. Cserélje le *\<passwordFile\>* elérési útja és a jelszót tartalmazó szöveges fájl neve és *\<felhasználónév\>* felhasználónévvel a megadott a hitelesítő adatait.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
