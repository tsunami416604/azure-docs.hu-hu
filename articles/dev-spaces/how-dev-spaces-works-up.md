---
title: A kód azure dev spaces-szel való futtatása hogyan működik?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: A kód Azure Kubernetes szolgáltatás azure dev spaces szolgáltatással való futtatásának folyamatai
keywords: azds.yaml, Azure Dev Spaces, Fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241360"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>A kód azure dev spaces-szel való futtatása hogyan működik?

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre egy Azure Kubernetes-szolgáltatás (AKS) fürtön. Miután a [projekt készen áll a fejlesztési területen való futtatásra,][how-it-works-prep]a fejlesztői terek segítségével létrehozhatja és futtathatja a projektet az AKS-fürtben.

Ez a cikk ismerteti, mi történik a kód futtatásához az AKS-ben a fejlesztői tárolók.

## <a name="run-your-code"></a>A kód futtatása

Ha a kódot fejlesztői területen `up` szeretné futtatni, adja `azds.yaml` ki a parancsot ugyanabban a könyvtárban, mint a fájlt:

```cmd
azds up
```

A `up` parancs feltölti az alkalmazás forrásfájljait és a projekt létrehozásához és futtatásához szükséges egyéb összetevőket a fejlesztési területre. Innen a fejlesztői térben lévő vezérlő:

1. Létrehozza a Kubernetes objektumokat az alkalmazás üzembe helyezéséhez.
1. Az alkalmazás tárolóját építi.
1. Az alkalmazás üzembe helyezése a fejlesztői térben.
1. Nyilvánosan elérhető DNS-nevet hoz létre az alkalmazásvégponthoz, ha konfigurálva van.
1. *Port-forward* használatával hozzáférést biztosít az alkalmazás http://localhostvégpontjához a használatával.
1. Előre stdout és stderr az ügyféloldali szerszámozás.


## <a name="starting-a-service"></a>Szolgáltatás indítása

Amikor egy fejlesztői térben indít el egy szolgáltatást, az ügyféloldali eszköz- és vezérlőegység koordinálja a forrásfájlok szinkronizálását, a tároló és a Kubernetes-objektumok létrehozását, valamint az alkalmazás futtatását.

Egy részletesebb szinten, itt van, mi történik, ha fut: `azds up`

1. [A fájlok szinkronizálása][sync-section] a felhasználó számítógépéről egy Azure-fájltárolóval történik, amely a felhasználó AKS-fürtjére egyedi. A rendszer feltölti a forráskódot, a Helm-diagramot és a konfigurációs fájlokat.
1. A vezérlő létrehoz egy új munkamenet indítására vonatkozó kérelmet. Ez a kérelem számos tulajdonságot tartalmaz, köztük egy egyedi azonosítót, a terület nevét, a forráskód elérési útját és a hibakeresési jelzőt.
1. A vezérlő lecseréli a *Helm-diagram $(tag)* helyőrzőjét az egyedi munkamenet-azonosítóra, és telepíti a szolgáltatás Helm-diagramját. Hivatkozás hozzáadása az egyedi munkamenet-azonosítóa a Helm diagram lehetővé teszi, hogy az adott munkamenet a KS-fürtben telepített tároló ta- és a kapcsolódó információkat.
1. A Helm-diagram telepítése során a Kubernetes webhook felvételi kiszolgáló további tárolókat ad hozzá az alkalmazás podjához a műszerezéshez és a projekt forráskódjához való hozzáféréshez. A devspaces-proxy és devspaces-proxy-init tárolók http-nyomkövetés és a tér útválasztás adódik hozzá. A devspaces-build tároló hozzá van adva, hogy a pod hozzáférést biztosít a Docker-példány és a projekt forráskód az alkalmazás tárolójának létrehozásához.
1. Az alkalmazás pod indításakor a devspaces-build tároló és devspaces-proxy-init tároló az alkalmazástároló létrehozásához használt. Az alkalmazástároló és a devspaces-proxy tárolók ezután elindulnak.
1. Az alkalmazástároló indítása után az ügyféloldali funkció a Kubernetes portforward funkcióval *http-hozzáférést* biztosít az alkalmazáshoz. http://localhost Ez a porttovábbítás a fejlesztői számítógépet a fejlesztői térben lévő szolgáltatáshoz csatlakoztatja.
1. Ha a pod összes tárolója elindult, a szolgáltatás fut. Ezen a ponton az ügyféloldali funkciók elkezdik streamelni a HTTP-nyomkövetéseket, az stdout-ot és az stderr-t. Ezt az információt a fejlesztő ügyféloldali funkciója jeleníti meg.

## <a name="updating-a-running-service"></a>Futó szolgáltatás frissítése

A szolgáltatás futása közben az Azure Dev Spaces frissítheti a szolgáltatást, ha a projekt forrásfájljai bármelyike megváltozik. A fejlesztői tárolóhelyek a szolgáltatás frissítését is eltérően kezelik a módosított fájl típusától függően. A fejlesztői tárolók háromféleképpen frissíthetik a futó szolgáltatást:

* Fájl közvetlen frissítése
* Az alkalmazás folyamatának újraépítése és újraindítása a futó alkalmazás tárolójában
* Az alkalmazás tárolójának újraépítése és újratelepítése

![Az Azure Dev Spaces fájlszinkronizálása](media/how-dev-spaces-works/file-sync.svg)

Bizonyos projektfájlok, amelyek statikus eszközök, mint például a html, css és cshtml fájlok, közvetlenül frissíthetők az alkalmazás tárolójában anélkül, hogy bármit újrakellene indítani. Ha egy statikus eszköz megváltozik, az új fájl szinkronizálódik a fejlesztési területtel, majd a futó tároló használja.

A fájlok, például a forráskód vagy az alkalmazáskonfigurációs fájlok módosításai az alkalmazás folyamatának újraindításával alkalmazhatók a futó tárolóban. A fájlok szinkronizálása után az alkalmazás folyamata újraindul a futó tárolón belül a *devhostagent* folyamat használatával. Amikor először hozza létre az alkalmazás tárolóját, a vezérlő lecseréli az alkalmazás indítási parancsát egy másik folyamatra, a *devhostagent-re.* Az alkalmazás tényleges folyamata ezután egy alárendelt folyamatként fut a *devhostagent*alatt, és a kimeneti piped használatával *devhostagent*'s kimenet. A *devhostagent* folyamat is része a fejlesztői szóközök, és a futó tárolóban a fejlesztői tárolóban parancsokat hajthat végre a fejlesztői tárolónevében. Újraindítás végrehajtásakor a *devhostagent*:

* Leállítja az alkalmazáshoz társított aktuális folyamatot vagy folyamatokat
* Újraépíti az alkalmazást
* Újraindítja az alkalmazáshoz társított folyamatot vagy folyamatokat

A *devhostagent* végrehajtásának módja az előző lépések [konfigurálása a. `azds.yaml` ][azds-yaml-section]

A projektfájlok, például a Dockerfiles, a csproj-fájlok vagy a Helm-diagram bármely része frissítései megkövetelik az alkalmazás tárolójának újraépítését és újratelepítését. Ha ezen fájlok egyike szinkronizálva van a fejlesztési térrel, a vezérlő futtatja a [helm upgrade][helm-upgrade] parancsot, és az alkalmazás tárolója újraépül, és újraüzembe kerül.

## <a name="file-synchronization"></a>Fájlszinkronizálás

Az első alkalommal, amikor egy alkalmazás elindul egy fejlesztői térben, az alkalmazás összes forrásfájlja feltöltésre kerül. Amíg az alkalmazás fut, és a későbbi újraindítások, csak a módosított fájlokat feltölteni. A folyamat koordinálására két fájl használható: egy ügyféloldali fájl és egy kontroller oldali fájl.

Az ügyféloldali fájl egy ideiglenes könyvtárban van tárolva, és a fejlesztői tárolóhelyeken futó projektkönyvtár kivonata alapján kapta a nevét. Windows rendszeren például olyan fájl javallt, mint *a Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef 1234567890abcdef 1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef123* Linux alatt az ügyféloldali fájl a */tmp* könyvtárban van tárolva. A címtár a macOS rendszeren `echo $TMPDIR` a parancs futtatásával található meg.

Ez a fájl JSON formátumban van, és a következőket tartalmazza:

* Minden egyes projektfájlhoz, amely szinkronizálva van a fejlesztési térrel,
* Szinkronizálási azonosító
* Az utolsó szinkronizálási művelet időbélyege

Minden projektfájl-bejegyzés tartalmazza a fájl elérési útját és időbélyegét.

A vezérlőoldali fájl az AKS-fürtön tárolódik. Ez tartalmazza a szinkronizálási azonosítót és az utolsó szinkronizálás időbélyegét.

A szinkronizálás akkor történik, ha a szinkronizálási időbélyegek nem egyeznek az ügyféloldali és a vezérlőoldali fájlok között. A szinkronizálás során az ügyféloldali eszközeszköz az ügyféloldali fájlban lévő fájlbejegyzések felett is megtérül. Ha a fájl időbélyege a szinkronizálási időbélyeg után van, a fájl szinkronizálva lesz a fejlesztési térbe. A szinkronizálás befejezése után a szinkronizálási időbélyegek mind az ügyféloldali, mind a vezérlőoldali fájlokban frissülnek.

Ha az ügyféloldali fájl nincs jelen, a program az összes projektfájlt szinkronizálja. Ez a viselkedés lehetővé teszi a teljes szinkronizálás kényszerítését az ügyféloldali fájl törlésével.

## <a name="how-running-your-code-is-configured"></a>A kód futtatásának konfigurálása

Az Azure Dev `azds.yaml` Spaces a szolgáltatás telepítéséhez és konfigurálásához használja a fájlt. A vezérlő `install` a `azds.yaml` fájlban lévő tulajdonság gal való felpróbálata segítségével telepíti a Helm-diagramot, és létrehozza a Kubernetes-objektumokat:

```yaml
...
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
...
```

Alapértelmezés szerint `prep` a parancs létrehozza a Helm diagramot. Azt is beállítja a *install.chart* tulajdonságot a Helm diagram könyvtárába. Ha egy másik helyen szeretne Helm-diagramot használni, frissítheti ezt a tulajdonságot, hogy az adott helyet használja.

A Helm-diagramok telepítésekor az Azure Dev Spaces lehetővé teszi az értékek felülbírálását a Helm-diagramban. A Helm diagram alapértelmezett értékei a. `charts/APP_NAME/values.yaml`

Az *install.values* tulajdonság használatával felsorolhat egy vagy több olyan fájlt, amely meghatározza a Helm diagramban lecserélni kívánt értékeket. Ha például kifejezetten az alkalmazás fejlesztési területen való futtatásakor szeretne állomásnevet vagy adatbázis-konfigurációt, használhatja ezt a felülbírálási funkciót. Azt is hozzá egy *?* a fájlnevek bármelyikének végén, hogy azt választhatóként állítsa be.

Az *install.set* tulajdonság lehetővé teszi egy vagy több, a Helm diagramban lecserélni kívánt érték konfigurálását. Az *install.set* fájlban konfigurált értékek felülírják az install.values fájlban felsorolt fájlokban konfigurált *értékeket.* Az *install.set* alatt lévő tulajdonságok a Helm diagram értékeitől függenek, és a létrehozott Helm-diagramtól függően eltérőek lehetnek.

A fenti példában az *install.set.replicaCount* tulajdonság közli a vezérlővel, hogy hány példányt kell futtatnia az alkalmazás a fejlesztői térben. A forgatókönyvtől függően növelheti ezt az értéket, de hatással lesz az alkalmazás podjához való hibakereső csatolása. További információt a [hibaelhárítási cikkben][troubleshooting]talál.

A létrehozott Helm-diagramban a tárolókép *értéke {{ . Values.image.repository }}:{{ . Values.image.tag }}*. A `azds.yaml` fájl az *install.set.image.tag* tulajdonságot alapértelmezés szerint *$(tag)* értékként definiálja, amely *a(z) {{ értékeként szolgál. Values.image.tag }}*. Az *install.set.image.tag* tulajdonság ily módon történő beállításával lehetővé teszi, hogy az alkalmazás tárolólemezképét az Azure Dev Spaces futtatásakor külön címkézze fel. Ebben a konkrét esetben a kép értéke az * \<image.repository>:$(tag) értékként*van címkézve. A * * változót kell használnia az *install.set.image.tag* értékeként ahhoz, hogy a fejlesztői tároló felismeri és megkeresse a tárolót az AKS-fürtben.

A fenti példában `azds.yaml` az *install.set.ingress.hosts*. Az *install.set.ingress.hosts* tulajdonság a nyilvános végpontok állomásnév-formátumát határozza meg. Ez a tulajdonság *$(spacePrefix)*, *$(rootSpacePrefix)* és *$(hostSuffix)* értéket is használ, amelyek a vezérlő által megadott értékek.

A *$(spacePrefix)* a gyermekfejlesztői terület neve, amely *spacename.s*. A *$(rootSpacePrefix)* a szülőterület neve. Ha például az *azureuser* *alapértelmezett*gyermekterület, akkor a *$(rootSpacePrefix)* értéke *az alapértelmezett,* a *$(spacePrefix)* értéke pedig *az azureuser.s*. Ha a szóköz nem gyermekterület, *a $(spacePrefix)* üres. Ha például az *alapértelmezett* területnek nincs szülőterülete, akkor a *$(rootSpacePrefix)* értéke *az alapértelmezett,* a *$(spacePrefix)* értéke pedig üres. A *$(hostSuffix)* egy DNS-utótag, amely az AKS-fürtben futó Azure dev spaces ingress controllerre mutat. Ez a DNS-utótag például * \*egy helyettesítő DNS-bejegyzésnek felel meg. RANDOM_VALUE.eus.azds.io*, amely akkor jött létre, amikor az Azure Dev Spaces vezérlő hozzá lett adva az AKS-fürthöz.

A fenti `azds.yaml` fájlban frissítheti az *install.set.ingress.hosts* fájlt is az alkalmazás állomásnevének módosításához. Ha például az alkalmazás hostname-jét *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* és *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* értékre szeretné egyszerűsíteni.

Az alkalmazás tárolójának létrehozásához a vezérlő a `azds.yaml` konfigurációs fájl alábbi szakaszait használja:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

A vezérlő egy Docker-fájlt használ az alkalmazás létrehozásához és futtatásához.

A *build.context* tulajdonság felsorolja azt a könyvtárat, ahol a Docker-fájlok léteznek. A *build.dockerfile* tulajdonság határozza meg a Dockerfile nevét az alkalmazás éles verziójának létrehozásához. A *configurations.develop.build.dockerfile* tulajdonság konfigurálja a Docker-fájl nevét az alkalmazás fejlesztői verziójához.

A különböző Docker-fájlok fejlesztési és éles környezetben lehetővé teszi, hogy bizonyos dolgokat a fejlesztés során, és tiltsa le ezeket a cikkeket éles környezetben. Például engedélyezheti a hibakeresést vagy a részletesebb naplózást a fejlesztés során, és letilthatja éles környezetben. Ezeket a tulajdonságokat akkor is frissítheti, ha a Docker-fájlok neve eltérő, vagy egy másik helyen vannak.

Annak érdekében, hogy a fejlesztés során gyorsan iterate, az Azure Dev Spaces szinkronizálja a helyi projekt módosításait, és fokozatosan frissíti az alkalmazást. A konfigurációs `azds.yaml` fájl alábbi szakasza a szinkronizálás és a frissítés konfigurálására szolgál:

```yaml
...
configurations:
  develop:
    ...
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
...
```

A módosításokat szinkronizáló fájlok és könyvtárak a *configurations.develop.container.sync* tulajdonságban jelennek meg. Ezek a könyvtárak kezdetben szinkronizálódnak a parancs futtatásakor, valamint a `up` módosítások észlelésekor. Ha további vagy különböző könyvtárakat szeretne szinkronizálni a fejlesztői térben, módosíthatja ezt a tulajdonságot.

A *configurations.develop.container.iterate.buildCommands* tulajdonság határozza meg, hogyan lehet az alkalmazást fejlesztési forgatókönyvben felépíteni. A *configurations.develop.container.command* tulajdonság biztosítja az alkalmazás fejlesztési forgatókönyvben történő futtatásának parancsát. Előfordulhat, hogy frissíteni szeretné ezeket a tulajdonságokat, ha további build- vagy futásidejű jelzők vagy paraméterek vannak a fejlesztés során.

A *configurations.develop.container.iterate.processesToKill* felsorolja azokat a folyamatokat, amelyeket az alkalmazás leállításához meg kell ölni. Érdemes lehet frissíteni ezt a tulajdonságot, ha módosítani szeretné az alkalmazás újraindítási viselkedését a fejlesztés során. Ha például frissítette a *configurations.develop.container.iterate.buildParancsok* vagy *configurations.develop.container.command* tulajdonságokat az alkalmazás felépítésének vagy elindításának módosításához, előfordulhat, hogy módosítania kell a folyamatok leállítását.

Amikor a kódot a `azds prep` parancs használatával készíti elő, lehetősége van a `--enable-ingress` jelző hozzáadására. A `--enable-ingress` jelző hozzáadása nyilvánosan elérhető URL-címet hoz létre az alkalmazásszámára. Ha kihagyja ezt a jelzőt, az alkalmazás csak a fürtön belül vagy a localhost alagút használatával érhető el. A `azds prep` parancs futtatása után módosíthatja ezt a beállítást, és módosíthatja a *bejövő kapcsolat.engedélyezéshez használható tulajdonságot* a alkalmazásban: `charts/APPNAME/values.yaml`

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a hálózatkezelésről és arról, hogyan továbbítja a kérelmeket az Azure dev spaces-ben, olvassa el [az Útválasztás és az Azure Dev Spaces közötti működést.][how-it-works-routing]

Ha többet szeretne tudni arról, hogy miként fejlesztheti és fejlesztheti az Azure Dev Spaces szolgáltatást, olvassa el [A fejlesztői számítógép csatlakoztatása a fejlesztői térhöz való csatlakoztatása][how-it-works-connect] és [a kód azure dev spaces-rel való távoli hibakeresésének működése című][how-it-works-remote-debugging]témakört.

Az Azure Dev Spaces használatával a projekt futtatásához az alábbi rövid útmutatók at kezdheti el:

* [Gyorsan iterálni és debug a Visual Studio kód és a Java][quickstart-java]
* [Gyorsan iterálni és hibakeresés a Visual Studio Code és a .NET][quickstart-netcore]
* [Gyorsan iterálni és hibakeresés a Visual Studio kód és Node.js][quickstart-node]
* [Gyorsan iterate és debug a Visual Studio és a .NET Core][quickstart-vs]
* [A CLI használata a Kubernetes alkalmazásának fejlesztéséhez][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md