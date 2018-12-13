---
title: Tárolók telepítése és futtatása
titlesuffix: Computer Vision - Azure Cognitive Services
description: Hogyan letöltése, telepítése és a Computer Vision-tárolókat futtathat az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 9964be07c578cd1cafd93328bffe972483123e03
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077035"
---
# <a name="install-and-run-containers"></a>Tárolók telepítése és futtatása

Bontás a szoftverek terjesztéséhez, amelyben egy alkalmazás vagy szolgáltatás van csomagolva, egy tárolórendszerképet egy megközelítést. A konfiguráció és az alkalmazás vagy szolgáltatás függőségeinek szerepelnek a tároló rendszerképét. A tároló rendszerképét ezután központilag telepítheti alig vagy egyáltalán nem módosítással üzemeltető tárolón futnak. Tárolók el különítve egymással és az alapjául szolgáló operációs rendszer, tárhely kisebb, mint egy virtuális gépet. A tárolók rövid távú feladatok tárolórendszerképeket a példányt, és távolítja el, amikor már nincs rá szükség.

A Computer Vision ismeri fel a szöveg részét is egy Docker-tárolóként érhető el. Lehetővé teszi, és nyomtatott szöveg kinyerése a különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások és poszterek vagy tájékoztató névjegykártyák képét.  
> [!IMPORTANT]
> A szöveg felismerése tároló jelenleg csak angol nyelven használható.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="preparation"></a>Előkészítés

A szöveg felismerése tároló használata előtt a következő előfeltételeknek kell megfelelnie:

**Docker-motor**: rendelkeznie kell helyileg telepített Docker-motor. A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), és [Windows](https://docs.docker.com/docker-for-windows/). A Windows Linux-tárolók támogatják a Docker kell konfigurálni. Docker-tárolóit közvetlenül is telepíthető [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), vagy egy [Kubernetes](https://kubernetes.io/) üzembehelyezettfürt[Az azure Stack](/azure/azure-stack/). Kubernetes az Azure Stackhez való telepítéséről további információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz.

**A Microsoft Container Registry és a Docker ismerős**: rendelkeznie kell Microsoft Container Registry és a Docker fő fogalmaira, például beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint ismerete alapvető ismeretekkel Alapszintű `docker` parancsokat.  

A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A szöveg felismerése tárolóhoz legalább 1 processzormag, legalább 2.6-os gigahertz (GHz) igényel, és gyorsabban, és 8 gigabájt (GB), a lefoglalt memória, de ajánlott legalább 2 processzormag és 8 GB lefoglalt memória.

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

Először végezze el, és küldje el a [Cognitive Services Látástechnológia tárolók kérelem űrlap](https://aka.ms/VisionContainersPreview) hozzáférés kéréséhez a szöveg felismerése tárolóba. Az űrlap kérelmek, a vállalat és a felhasználói forgatókönyvet, amelynek a tároló használni kívánt információkat. Miután elküldte, az Azure Cognitive Services-csapat áttekinti az űrlap győződjön meg arról, hogy megfelel a feltételeknek a privát tárolóregisztrációs adatbázis eléréséhez.

> [!IMPORTANT]
> Egy a képernyőn egy Microsoft-fiók (MSA) vagy az Azure Active Directory (Azure AD)-fiókjához társított e-mail-címet kell használnia.

Ha jóváhagyja a kérést, majd egy e-mailt kap az utasítások a szerezze be a hitelesítő adatokat, és a privát tárolóregisztrációs adatbázis eléréséhez.

## <a name="create-a-computer-vision-resource-on-azure"></a>Számítógépes Látástechnológia erőforrás létrehozása az Azure-ban

Ha a szöveg felismerése tárolót használni kívánt létre kell hoznia az Azure-ban a Computer Vision erőforrás. Miután létrehozta az erőforrást, majd használja az előfizetési kulcs és a végpont URL-címet az erőforrás a tároló példányosítása. Egy tároló hárítható el kapcsolatos további információkért lásd: [hozható létre egy tárolót a letöltött tárolórendszerkép](#instantiate-a-container-from-a-downloaded-container-image).

A következő lépésekkel hozhat létre, és a egy Azure-erőforrás lévő információk lekéréséhez:

1. Hozzon létre egy Azure-erőforrást az Azure Portalon.  
   Ha azt szeretné, a szöveg felismerése tároló használatára, először létre kell hoznia egy megfelelő számítógépes Látástechnológiai erőforrást az Azure Portalon. További információkért lásd: [a rövid útmutató: a Cognitive Services-fiók létrehozása az Azure Portalon](../cognitive-services-apis-create-account.md).

1. A végpont URL-cím és egy előfizetési kulcsra az Azure-beli erőforráshoz kaphat.  
   Az Azure-erőforrás létrehozása után a megfelelő szöveg felismerése tároló példányosítása ennek az erőforrásnak a végpont URL-cím és egy előfizetési kulcsra kell használnia. A végpont URL-cím és egy előfizetési kulcsra másolhatja a, a gyors üzembe helyezés és a kulcsok lapok a Computer Vision erőforrás az Azure Portalon.

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

A tároló rendszerképét a szöveg felismerése tároló érhető el a privát Docker-tárolójegyzék, nevű `containerpreview.azurecr.io`, az Azure Container Registryben. A tároló rendszerképét a szöveg felismerése tároló kell letölteni, futtassa helyileg a tárolót a tárházból.

Használja a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet a tárházból. Töltse le a legújabb szöveg felismerése tároló rendszerképét az adattárból, például használja a következő parancsot:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

A szöveg felismerése tároló rendelkezésre álló címkék teljes leírását lásd: [szöveg felismerése](https://go.microsoft.com/fwlink/?linkid=2018655) a Docker hubon.

> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Hozza létre a tároló letöltött tároló rendszerképből

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) letöltött tárolórendszerképet a tároló példányosítása parancsot. Ha például a következő parancsot:

* A szöveg felismerése tárolórendszerképet egy tárolót példányosítja.
* Foglalja le a két processzormagot és memóriát 8 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A tároló automatikusan eltávolítja az után kilép

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Példányosítani, miután a tároló gazdagép URI használatával műveleteket meghívhatja a tárolóból. A következő gazdagép URI például szöveg felismerése tárolót, amelyet az előző példában volt példányosítása jelöli:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Elérheti a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) (korábbi nevén a Swagger-specifikációra), a példányosított tárolót, a támogatott műveleteket leíró a `/swagger` tárolóban tartozó relatív URI. Például a következő URI Azonosítót tartalmaz szöveg felismerése tárolót, amelyet az előző példában volt példányosítani az OpenAPI-specifikáció való hozzáférést:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Választhatja [hívás a REST API-műveleteket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) érhető el a tárolóból az aszinkron vagy szinkron módon FELISMERVE szöveget, vagy használja a [Azure Cognitive Services számítógép Látástechnológiai SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) ügyfél a szalagtár műveletek meghívásához.  
> [!IMPORTANT]
> Rendelkeznie kell az Azure Cognitive Services számítógép Látástechnológiai SDK 3.2.0 verzió vagy újabb, ha azt szeretné, az ügyféloldali kódtár használata a tárolót.

### <a name="asynchronous-text-recognition"></a>Aszinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeText` és `GET /vision/v2.0/textOperations/*{id}*` műveletek aszinkron módon ismeri fel a képet, hogyan használja a Computer Vision service a megfelelő REST-műveletekhez hasonló nyomtatott szöveg egyeztetve. A szöveg felismerése tároló csak felismeri jelenleg nyomtatott szöveg, nem kézzel írt szöveg, ezért a `mode` a Computer Vision service műveletet a rendszer figyelmen kívül hagyja a a szöveg felismerése tároló megfelelően megadott paraméterrel.

### <a name="synchronous-text-recognition"></a>Szinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeTextDirect` művelet szinkron ismeri fel a képet nyomtatott szöveg. Mivel ez a művelet szinkron, ehhez a művelethez a kérelem törzsében megegyezik a számára, amely a `POST /vision/v2.0/recognizeText` műveletet, de a válasz törzs esetében ez a művelet nem ugyanaz, mint amellyel által visszaadott a `GET /vision/v2.0/textOperations/*{id}*` műveletet.

### <a name="billing"></a>Számlázás

A szöveg felismerése tároló számlázási adatokat küld az Azure-bA használatával egy megfelelő számítógépes Látástechnológiai erőforrást az Azure-fiókjával. Az alábbi lehetőségek a számlázás tekintetében a szöveg felismerése tároló által használt:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Computer Vision erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a kiépített számítógép Vision Azure-erőforrás megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Computer Vision erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a végpontot egy kiépített számítógép Vision Azure-erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a licencet, a tároló.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](computer-vision-resource-container-config.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a Computer Vision tárolók munkafolyamatokat. Összegezve:

* Számítógépes Látástechnológia biztosít egy Linux-tárolókat a Docker, és nyomtatott szöveg kinyerése.
* Tárolórendszerképek letöltődnek a privát tárolóregisztrációs adatbázis az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val a Computer Vision tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](computer-vision-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [számítógépes Látástechnológiai áttekintése](Home.md) tudhat meg többet nyomtatott és kézzel írt szöveg felismerése  
* Tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](FAQ.md) a Computer Vision funkcióihoz kapcsolódó problémák megoldása.
