---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593331"
---
Töltse ki és küldje el a [Cognitive Services tárolók kérelem űrlapját](https://aka.ms/cognitivegate) , hogy hozzáférést Kérjen a tárolóhoz. Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Az űrlap elküldése után az Azure Cognitive Services csapata megtekinti, hogy megfelel-e a Private Container registryhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> Az űrlapon egy Microsoft-fiókhoz (MSA) vagy egy Azure Active Directory (Azure AD-fiókhoz) társított e-mail-címet kell használnia. Az elfogadási feltételekkel kapcsolatos további információkért lásd: [Cognitive Services-kapuzás folyamat](../articles/cognitive-services/cognitive-services-gating-process.md).

Ha jóváhagyja a kérelmét, egy e-mailt kap, amely leírja, hogyan kérheti le a hitelesítő adatokat, és hogyan érheti el a privát tároló beállításjegyzékét.

## <a name="log-in-to-the-private-container-registry"></a>Jelentkezzen be a Private Container registrybe

A Cognitive Services tárolók magánhálózati tárolójának beállításjegyzékét többféleképpen lehet hitelesíteni. Javasoljuk, hogy a parancssori módszert használja a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használatával.

Az alábbi példában látható [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs használatával jelentkezzen be `containerpreview.azurecr.io` , amely a Cognitive Services tárolók privát tárolójának beállításjegyzéke. Cserélje le a * \< felhasználónevet \> * a felhasználónévvel és a * \< jelszóval \> * az Azure Cognitive Services csapattól kapott hitelesítő adatokban megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha egy szövegfájlban biztosította a hitelesítő adatait, összefűzheti a szövegfájl tartalmát a `docker login` parancshoz. Használja az `cat` parancsot az alábbi példában látható módon. A * \< passwordFile \> * helyére írja be a jelszót tartalmazó szövegfájl elérési útját és nevét. Cserélje le a * \< username \> * nevet a hitelesítő adataiban megadott felhasználónévre.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

