---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302329"
---
Először végezze el, és küldje el a [Cognitive Services Látástechnológia tárolók kérelem űrlap](https://aka.ms/VisionContainersPreview) hozzáférés kéréséhez a tárolóhoz. Az űrlap kérelmek, a vállalat és a felhasználói forgatókönyvet, amelynek a tároló használni kívánt információkat. Miután elküldte, az Azure Cognitive Services-csapat áttekinti az űrlap győződjön meg arról, hogy megfelel a feltételeknek a privát tárolóregisztrációs adatbázis eléréséhez.

> [!IMPORTANT]
> Egy a képernyőn egy Microsoft-fiók (MSA) vagy az Azure Active Directory (Azure AD)-fiókjához társított e-mail-címet kell használnia.

Ha jóváhagyja a kérést, majd egy e-mailt kap az utasítások a szerezze be a hitelesítő adatokat, és a privát tárolóregisztrációs adatbázis eléréséhez.

## <a name="log-in-to-the-private-container-registry"></a>Jelentkezzen be a privát tárolóregisztrációs adatbázis

Többféleképpen is a Cognitive Services-tárolók a privát tárolóregisztrációs adatbázis hitelesítéséhez, de a parancssorból az ajánlott módszer használatával a [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Használja a [docker login](https://docs.docker.com/engine/reference/commandline/login/) parancsot, amellyel majd bejelentkezik a következő példában látható módon `containerpreview.azurecr.io`, a Cognitive Services-tárolók privát tárolójegyzékben. Cserélje le *\<felhasználónév\>* felhasználónévvel és *\<jelszó\>* az Azure-tól kapott a hitelesítő adatokban megadott jelszóval Cognitive Services team.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Biztonságossá tett egy szövegfájlt a hitelesítő adatait, ha a szöveges tartalmát összefűzheti fájlt, a `cat` parancsra a `docker login` parancsot az alábbi példában látható módon. Cserélje le *\<passwordFile\>* elérési útja és a jelszót tartalmazó szöveges fájl neve és *\<felhasználónév\>* felhasználónévvel a megadott a hitelesítő adatait.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

