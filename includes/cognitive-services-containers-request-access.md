---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229290"
---
Töltse ki és küldje el a [Cognitive Services vision tárolók kérelem űrlapot](https://aka.ms/VisionContainersPreview) a tárolóhoz való hozzáférés kéréséhez. Az űrlap adatokat kér Önről, a vállalatról, valamint arról a felhasználói forgatókönyvről, amelyhez a tárolót használni fogja. Miután elküldte az űrlapot, az Azure Cognitive Services csapata áttekinti azt, hogy megbizonyosodjon arról, hogy megfelel a privát tároló beállításjegyzékhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> Az űrlapon egy Microsoft-fiókhoz (MSA) vagy egy Azure Active Directory(Azure AD) fiókhoz társított e-mail címet kell használnia.

Ha a kérés tértek jóvá, kap egy e-mailt, amely leírja, hogyan szerezheti be a hitelesítő adatait, és hogyan érheti el a privát tároló beállításjegyzékét.

## <a name="log-in-to-the-private-container-registry"></a>Bejelentkezés a magántároló rendszerleíró adatbázisába

A Cognitive Services-tárolók privát tárolóbeállítás-beállításjegyzékével többféleképpen is hitelesíthető. Javasoljuk, hogy a parancssori módszert használja a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)használatával.

Használja a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancsot, ahogy az `containerpreview.azurecr.io`a következő példában látható, jelentkezzen be, amely a Cognitive Services-tárolók privát tárolóbeállításjegyzéke. Cserélje * \<\> * le a felhasználónevet a felhasználónévre és * \<\> * a jelszóra az Azure Cognitive Services csapatától kapott hitelesítő adatokban megadott jelszóval.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha a hitelesítő adatokat egy szöveges fájlban biztosította, a szöveges fájl `docker login` tartalmát összefűzheti a parancsban. Használja `cat` a parancsot, ahogy az a következő példában látható. Cserélje le * \<a passwordFile\> * fájlt a jelszót tartalmazó szövegfájl elérési parancsára és nevére. Cserélje * \<\> * le a felhasználónevet a hitelesítő adatokban megadott felhasználónévre.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

