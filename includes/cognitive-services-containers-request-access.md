---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68229290"
---
Töltse ki és küldje el a [Cognitive Services jövőkép tárolói kérelem űrlapját](https://aka.ms/VisionContainersPreview) , hogy hozzáférést Kérjen a tárolóhoz. Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Az űrlap elküldése után az Azure Cognitive Services csapata megtekinti, hogy megfelel-e a Private Container registryhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> Az űrlapon egy Microsoft-fiókhoz (MSA) vagy egy Azure Active Directory (Azure AD-fiókhoz) társított e-mail-címet kell használnia.

Ha jóváhagyja a kérelmét, egy e-mailt kap, amely leírja, hogyan kérheti le a hitelesítő adatokat, és hogyan érheti el a privát tároló beállításjegyzékét.

## <a name="log-in-to-the-private-container-registry"></a>Jelentkezzen be a Private Container registrybe

A Cognitive Services tárolók magánhálózati tárolójának beállításjegyzékét többféleképpen lehet hitelesíteni. Javasoljuk, hogy a parancssori módszert használja a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használatával.

Az alábbi példában látható [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs használatával jelentkezzen be `containerpreview.azurecr.io`, amely a Cognitive Services tárolók privát tárolójának beállításjegyzéke. Cserélje * \<le\> a felhasználónevet* a felhasználónévvel és * \<a jelszóval\> * az Azure Cognitive Services csapattól kapott hitelesítő adatokban megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha egy szövegfájlban biztosította a hitelesítő adatait, összefűzheti a szövegfájl tartalmát a `docker login` parancshoz. Használja az `cat` parancsot az alábbi példában látható módon. A * \<passwordFile\> * helyére írja be a jelszót tartalmazó szövegfájl elérési útját és nevét. Cserélje * \<le\> a username* nevet a hitelesítő adataiban megadott felhasználónévre.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

