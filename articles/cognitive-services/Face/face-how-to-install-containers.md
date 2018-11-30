---
title: Hogyan telepítheti és futtathatja a tárolók
titlesuffix: Face - Cognitive Services - Azure
description: Hogyan letöltése, telepítése és a Face-tárolókat futtathat az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 1d13e2ccbbc1d5c1bc80dffc260a3759fe378d7d
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634540"
---
# <a name="install-and-run-containers"></a>Tárolók telepítése és futtatása

Bontás a szoftverek terjesztéséhez, amelyben egy alkalmazás vagy szolgáltatás van csomagolva, egy tárolórendszerképet egy megközelítést. A konfiguráció és az alkalmazás vagy szolgáltatás függőségeinek szerepelnek a tároló rendszerképét. A tároló rendszerképét ezután központilag telepítheti alig vagy egyáltalán nem módosítással üzemeltető tárolón futnak. Tárolók el különítve egymással és az alapjául szolgáló operációs rendszer, tárhely kisebb, mint egy virtuális gépet. A tárolók rövid távú feladatok tárolórendszerképeket a példányt, és távolítja el, amikor már nincs rá szükség.

Face Docker, arc, amely észleli az emberi arcok a képeken, és azonosítja az attribútumokat, (például noses és szemek) arcrészek, gender, életkor és egyéb gép – előre meghatározott arcfelismerés nevű egy szabványosított Linux-tárolót biztosít. Észlelési, mellett Face is ellenőrizze, hogy két arc ugyanazt a lemezképet vagy különböző képek egy megbízhatósági pontszám használatával azonosak, vagy olyan adatbázison annak ellenőrzéséhez, hogy a hasonló megjelenésű arcokat összehasonlítása vagy azonos face már létezik. Azt is is csoportokba rendezheti a hasonló arcokat keres, közös visual tulajdonságok használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="preparation"></a>Előkészítés

A Face tároló használata előtt a következő előfeltételeknek kell megfelelnie:

**Docker-motor**: rendelkeznie kell helyileg telepített Docker-motor. A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), és [Windows](https://docs.docker.com/docker-for-windows/). A Windows Linux-tárolók támogatják a Docker kell konfigurálni. Docker-tárolóit közvetlenül is telepíthető [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), vagy egy [Kubernetes](https://kubernetes.io/) üzembehelyezettfürt[Az azure Stack](/azure/azure-stack/). Kubernetes az Azure Stackhez való telepítéséről további információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz.

**A Microsoft Container Registry és a Docker ismerős**: rendelkeznie kell Microsoft Container Registry és a Docker fő fogalmaira, például beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint ismerete alapvető ismeretekkel Alapszintű `docker` parancsokat.  

A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Kiszolgálókövetelmények és javaslatok

A Face tárolóhoz legalább 1 processzormag, legalább 2.6-os gigahertz (GHz) igényel, és gyorsabban, és 4 gigabájt (GB) lefoglalt memória, de ajánlott legalább 2 processzormagot és 6 GB lefoglalt memória.

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services Látástechnológia tárolók kérelem űrlap](https://aka.ms/VisionContainersPreview) hozzáférés kéréséhez a Face tárolóba. Az űrlap kérelmek, a vállalat és a felhasználói forgatókönyvet, amelynek a tároló használni kívánt információkat. Miután elküldte, az Azure Cognitive Services-csapat áttekinti az űrlap győződjön meg arról, hogy megfelel a feltételeknek a privát tárolóregisztrációs adatbázis eléréséhez.

> [!IMPORTANT]
> Egy a képernyőn egy Microsoft-fiók (MSA) vagy az Azure Active Directory (Azure AD)-fiókjához társított e-mail-címet kell használnia.

Ha jóváhagyja a kérést, majd egy e-mailt kap az utasítások a szerezze be a hitelesítő adatokat, és a privát tárolóregisztrációs adatbázis eléréséhez.

## <a name="create-a-face-resource-on-azure"></a>Face erőforrás létrehozása az Azure-ban

Ha azt szeretné, a Face tároló használatához létre kell hoznia az Azure-ban Face erőforrás. Miután létrehozta az erőforrást, majd használja az előfizetési kulcs és a végpont URL-címet az erőforrás a tároló példányosítása. Egy tároló hárítható el kapcsolatos további információkért lásd: [hozható létre egy tárolót a letöltött tárolórendszerkép](#instantiate-a-container-from-a-downloaded-container-image).

A következő lépésekkel hozhat létre, és a egy oldallal erőforrás lévő információk lekéréséhez:

1. Hozzon létre egy Face erőforrást az Azure Portalon.  
   Ha azt szeretné, a Face tároló használatára, először létre kell hoznia egy megfelelő ARC-erőforrást az Azure Portalon. További információkért lásd: [a rövid útmutató: a Cognitive Services-fiók létrehozása az Azure Portalon](../cognitive-services-apis-create-account.md).

1. A végpont URL-cím és egy előfizetési kulcsra az Azure-beli erőforráshoz kaphat.  
   Az Azure-erőforrás létrehozása után a megfelelő Face tároló példányosítása ennek az erőforrásnak a végpont URL-cím és egy előfizetési kulcsra kell használnia. A végpont URL-CÍMÉT és az előfizetési kulcs, a gyors üzembe helyezés és a kulcsok lapok a Face erőforrás az Azure Portalon a másolhatja.

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

## <a name="download-container-images-from-the-private-container-registry"></a>Töltse le a tárolórendszerképek a privát tárolóregisztrációs adatbázisból

A tároló rendszerképét a Face tároló érhető el a privát Docker-tárolójegyzék, nevű `containerpreview.azurecr.io`, az Azure Container Registryben. A tároló rendszerképét a Face tároló kell letölteni, futtassa helyileg a tárolót a tárházból.

Használja a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet a tárházból. Töltse le a legújabb arc-tároló rendszerképét az adattárból, például használja a következő parancsot:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

A Face tároló rendelkezésre álló címkék teljes leírását lásd: [szöveg felismerése](https://go.microsoft.com/fwlink/?linkid=2018655) a Docker hubon.

> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Hozza létre a tároló letöltött tároló rendszerképből

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) letöltött tárolórendszerképet a tároló példányosítása parancsot. Ha például a következő parancsot:

* A Face tárolórendszerképet egy tárolót példányosítja.
* Foglalja le a két processzormagot és memóriát 6 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A tároló automatikusan eltávolítja az után kilép

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló példányosítása; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

Példányosítani, miután a tároló gazdagép URI használatával műveleteket meghívhatja a tárolóból. A következő gazdagép URI például Face tárolót, amelyet az előző példában volt példányosítása jelöli:

```http
http://localhost:5000/
```

> [!TIP]
> Elérheti a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) (korábbi nevén a Swagger-specifikációra), a példányosított tárolót, a támogatott műveleteket leíró a `/swagger` tárolóban tartozó relatív URI. Például az a következő URI Azonosítót a Face tároló, amely az előző példában volt példányosítani az OpenAPI-specifikáció hozzáférést biztosít:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Választhatja [hívás a REST API-műveleteket](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) érhető el a tárolót, vagy használja a [Azure Cognitive Services Face ügyféloldali kódtárának](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) ügyféloldali kódtár, ezek a műveletek meghívásához.  
> [!IMPORTANT]
> Az Azure Cognitive Services Face ügyféloldali kódtár 2.0-s vagy újabb verzióját kell rendelkeznie, ha azt szeretné, az ügyféloldali kódtár használata a tároló.

Az egyetlen különbség a között egy adott művelet meghívása a tárolóból, és hív-e, hogy a műveletben megfelelő service-ből az Azure-ban, hogy fogja használni a gazdagépet a tároló URI-t, nem pedig a gazdagép egy Azure-régióban, az URI-t hívja meg a műveletet. Például ha egy példányt kíván használni Arcfelismerési West US Azure régióban fut, arcfelismerés, meghívta az alábbi REST API-művelet:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Ha szeretne használni az alapértelmezett konfiguráció szerint a helyi gépen futó Face tárolók arcfelismerési funkciókat használni, az alábbi REST API-művelet lenne hívja meg:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Számlázás

A Face tároló számlázási adatokat küld az Azure-bA használatával egy megfelelő ARC-erőforrást az Azure-fiókjával. Az alábbi parancssori kapcsolók használhatók a Face tároló által számlázás szempontjából:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Face erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a kiépített Face Azure erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Face erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett Face Azure erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a licencet, a tároló.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](face-resource-container-config.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és a futó tárolók Face munkafolyamat. Összegezve:

* Face Docker, arc, arcfelismerés, vagy személyek adatbázis arcokat azonosíthat nevű egy Linux-tárolót biztosít.
* Tárolórendszerképek letöltődnek a privát tárolóregisztrációs adatbázis az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val Face tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.
* ** A cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.  

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](face-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [Face áttekintése](Overview.md) további észlelése és arcok azonosítása  
* Tekintse meg a [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](FAQ.md) Arcfelismerési funkciókat kapcsolatos problémák megoldásához.
