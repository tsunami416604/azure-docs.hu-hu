---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229290"
---
Töltse ki és küldje el a [Cognitive Services Látástechnológia tárolók kérelem űrlap](https://aka.ms/VisionContainersPreview) hozzáférés kéréséhez a tárolóhoz. Az űrlap kérelmek, a vállalat és a felhasználói forgatókönyvet, amelynek a tároló használni kívánt információkat. Az űrlap mentése után az Azure Cognitive Services-csapat áttekinti a annak ellenőrzésére, hogy megfelel a feltételeknek a privát tárolóregisztrációs adatbázis eléréséhez.

> [!IMPORTANT]
> Társított vagy egy Microsoft fiók (MSA), vagy a képernyőn egy Azure Active Directory (Azure AD-) fiók egy e-mail-címet kell használnia.

Ha jóváhagyja a kérelmét, kap egy e-mailt az utasításokat, amelyek bemutatják, hogyan szerezze be a hitelesítő adatokat, és a privát tárolóregisztrációs adatbázis eléréséhez.

## <a name="log-in-to-the-private-container-registry"></a>Jelentkezzen be a privát tárolóregisztrációs adatbázis

Többféleképpen is lehet hitelesíteni a privát tárolóregisztrációs adatbázis, a Cognitive Services-tárolók. Azt javasoljuk, hogy használja-e a parancssori metódus használatával a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Használja a [docker login](https://docs.docker.com/engine/reference/commandline/login/) paranccsal jelentkezzen be az alábbi példában látható módon `containerpreview.azurecr.io`, vagyis a Cognitive Services-tárolók privát tárolójegyzékben. Cserélje le *\<felhasználónév\>* felhasználónévvel és *\<jelszó\>* az Azure-tól kapott a hitelesítő adatokban megadott jelszóval Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Ha beállította a hitelesítő adatait egy szövegfájlba, összefűzheti a szöveges fájl tartalmát a `docker login` parancsot. Használja a `cat` parancsot, az alábbi példában látható módon. Cserélje le *\<passwordFile\>* elérési útja és a jelszót tartalmazó szöveges fájl neve. Cserélje le *\<felhasználónév\>* a hitelesítő adatait a megadott felhasználónévvel.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

