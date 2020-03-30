---
title: Hogyan működik egy projekt előkészítése az Azure Dev Spaces számára?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: A projekt előkészítése az Azure Dev Spaces használatával
keywords: azds.yaml, Azure Dev Spaces, Fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241633"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Hogyan működik egy projekt előkészítése az Azure Dev Spaces számára?

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre egy Azure Kubernetes-szolgáltatás (AKS) fürtön. A fejlesztői tárolók docker-fájlokat és helm-diagramokat hozhatnak létre a projekthez. A Fejlesztői tárolók konfigurációs fájlt is létrehoz és használ a Kubernetes-alkalmazások AKS-ben történő üzembe helyezéséhez, futtatásához és hibakereséséhez. Mindezek a fájlok az alkalmazás kódjával vannak eltaszatolva, és hozzáadhatóak a verziókezelő rendszerhez.

Ez a cikk ismerteti, mi történik, hogyan készítse elő a projektet az AKS-ben a fejlesztői szóközök.

## <a name="prepare-your-code"></a>A kód előkészítése

Annak érdekében, hogy az alkalmazás futtatásához egy fejlesztői térben, azt kell konténerezett, és meg kell határoznia, hogyan kell telepíteni a Kubernetes. Az alkalmazás konténeresítéséhez egy Docker-fájlszükséges. Annak meghatározásához, hogy az alkalmazás hogyan van telepítve a Kubernetes-ben, helm [diagramra](https://docs.helm.sh/)van szüksége. Az alkalmazás Dockerfile és Helm diagramjának létrehozásához az ügyféloldali `prep` eszközök biztosítják a következő parancsot:

```cmd
azds prep --enable-ingress
```

A `prep` parancs megvizsgálja a fájlokat a projektben, és megpróbálja létrehozni a Dockerfile és a Helm diagram fut az alkalmazás Kubernetes. Jelenleg a `prep` parancs létrehoz egy Dockerfile és Helm diagramot a következő nyelveken:

* Java
* Node.js
* .NET Core

A *must* `prep` parancsot forráskódot tartalmazó könyvtárból kell futtatni. A `prep` parancs futtatása a megfelelő könyvtárból lehetővé teszi, hogy az ügyféloldali eszközelem azonosítsa a nyelvet, és hozzon létre egy megfelelő Dockerfile az alkalmazás konténeres. A parancsot `prep` java *projektekhez pom.xml* fájlt tartalmazó könyvtárból is futtathatja.

Ha a `prep` parancsot olyan címtárból futtatja, amely nem tartalmaz forráskódot, az ügyféloldali eszköznem hoz létre Docker-fájlt. A következő hibaüzenet jelenik meg: *A Nem támogatott nyelv miatt a Dockerfile nem jött létre.* Ez a hiba akkor is előfordul, ha az ügyféloldali eszköznem ismeri fel a projekt típusát.

A `prep` parancs futtatásakor megadhatja a `--enable-ingress` jelzőt. Ez a jelző arra utasítja a vezérlőt, hogy hozzon létre egy interneten elérhető végpontot ehhez a szolgáltatáshoz. Ha nem adja meg ezt a jelzőt, a szolgáltatás csak a fürtből vagy az ügyféloldali eszközhasználat által létrehozott localhost alagút használatával érhető el. Ezt a viselkedést a `prep` parancs futtatása után engedélyezheti vagy letilthatja a létrehozott Helm-diagram frissítésével.

A `prep` parancs nem helyettesíti a projektben meglévő Dockerfiles vagy Helm diagramokat. Ha egy meglévő Docker-fájl vagy Helm diagram ugyanazt az `prep` elnevezési `prep` konvenciót használja, mint a parancs által létrehozott fájlok, a parancs kihagyja a fájlok generálását. Ellenkező esetben `prep` a parancs saját Dockerfile vagy Helm diagramot hoz létre a meglévő fájlok mellett.

> [!IMPORTANT]
> Az Azure Dev Spaces a Dockerfile és a Helm diagramot használja a projekthez a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

A `prep` parancs egy `azds.yaml` fájlt is létrehoz a projekt gyökerében. Az Azure Dev Spaces ezt a fájlt használja az alkalmazás létrehozásához, telepítéséhez, konfigurálásához és futtatásához. Ez a konfigurációs fájl felsorolja a Dockerfile és a Helm diagram helyét, és további konfigurációt is biztosít az összetevők tetején.

Íme egy példa a [.NET Core mintaalkalmazással](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)létrehozott azds.yaml fájl:

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

A `azds.yaml` `prep` parancs által létrehozott fájl egyszerű, egyetlen projektfejlesztési forgatókönyvhöz használható. Ha az adott projekt összetettsége megnőtt, előfordulhat, hogy `prep` a parancs futtatása után frissítenie kell ezt a fájlt. Előfordulhat például, hogy a projekt a fejlesztési vagy hibakeresési igények alapján módosítani szeretné a build- vagy indítási folyamatot. Előfordulhat, hogy több alkalmazás is van a projektben, amelyek több buildfolyamatot vagy eltérő buildtartalmat igényelnek.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogy miként futtathatja a kódot a fejlesztői tárhelyen, olvassa el [a Kód azure dev spaces használatával való futtatása.][how-it-works-up]

Az Azure Dev Spaces használatával előkészítheti a projektet az Azure Dev Space számára, olvassa el az alábbi rövid útmutatókat:

* [Gyorsan iterálni és debug a Visual Studio kód és a Java][quickstart-java]
* [Gyorsan iterálni és hibakeresés a Visual Studio Code és a .NET][quickstart-netcore]
* [Gyorsan iterálni és hibakeresés a Visual Studio kód és Node.js][quickstart-node]
* [Gyorsan iterate és debug a Visual Studio és a .NET Core][quickstart-vs]
* [A CLI használata a Kubernetes alkalmazásának fejlesztéséhez][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md