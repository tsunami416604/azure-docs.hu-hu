---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 11a336bbcf75c6c4de61f1bb681ab6ee7aa05650
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741166"
---
Először végezze el, és küldje el a [Cognitive Services Látástechnológia tárolók kérelem űrlap](https://aka.ms/VisionContainersPreview) hozzáférés kéréséhez a tárolóhoz. Az űrlap kérelmek, a vállalat és a felhasználói forgatókönyvet, amelynek a tároló használni kívánt információkat. Miután elküldte, az Azure Cognitive Services-csapat áttekinti az űrlap győződjön meg arról, hogy megfelel a feltételeknek a privát tárolóregisztrációs adatbázis eléréséhez.

> [!IMPORTANT]
> Egy a képernyőn egy Microsoft-fiók (MSA) vagy az Azure Active Directory (Azure AD)-fiókjához társított e-mail-címet kell használnia.

Ha jóváhagyja a kérést, majd egy e-mailt kap az utasítások a szerezze be a hitelesítő adatokat, és a privát tárolóregisztrációs adatbázis eléréséhez.

## <a name="log-in-to-the-private-container-registry"></a>Jelentkezzen be a privát tárolóregisztrációs adatbázis

Többféleképpen is a Cognitive Services-tárolók a privát tárolóregisztrációs adatbázis hitelesítéséhez, de a parancssorból az ajánlott módszer használatával a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Használja a [docker login](https://docs.docker.com/engine/reference/commandline/login/) parancsot, amellyel majd bejelentkezik a következő példában látható módon `containerpreview.azurecr.io`, a Cognitive Services-tárolók privát tárolójegyzékben. Cserélje le *\<felhasználónév\>* felhasználónévvel és *\<jelszó\>* az Azure-tól kapott a hitelesítő adatokban megadott jelszóval Cognitive Services team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Biztonságossá tett egy szövegfájlt a hitelesítő adatait, ha a szöveges tartalmát összefűzheti fájlt, a `cat` parancsra a `docker login` parancsot az alábbi példában látható módon. Cserélje le *\<passwordFile\>* elérési útja és a jelszót tartalmazó szöveges fájl neve és *\<felhasználónév\>* felhasználónévvel a megadott a hitelesítő adatait.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

