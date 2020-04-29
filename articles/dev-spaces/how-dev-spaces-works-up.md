---
title: A kód futtatása az Azure dev Spaces működésével
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: A kód Azure Kubernetes Service-ben való futtatásának folyamatait ismerteti az Azure dev Spaces szolgáltatással
keywords: azds. YAML, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241360"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>A kód futtatása az Azure dev Spaces működésével

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. Miután a [projekt készen áll a fejlesztői tárhelyen való futtatásra][how-it-works-prep], a fejlesztői terek használatával felépítheti és futtathatja a projektjét az AK-fürtben.

Ez a cikk azt ismerteti, hogy mi történik a kód futtatása az AK-ban a dev Spaces szolgáltatással.

## <a name="run-your-code"></a>A kód futtatása

Ha a kódot egy fejlesztői térben szeretné futtatni, adja ki `up` a parancsot a `azds.yaml` fájllal megegyező könyvtárban:

```cmd
azds up
```

A `up` parancs feltölti az alkalmazás forrásfájljait és a projekt létrehozásához és futtatásához szükséges egyéb összetevőket a fejlesztői területhez. A fejlesztői terület vezérlője:

1. Létrehozza a Kubernetes objektumokat az alkalmazás üzembe helyezéséhez.
1. Létrehozza az alkalmazás tárolóját.
1. Üzembe helyezi az alkalmazást a fejlesztői térben.
1. Nyilvánosan elérhető DNS-nevet hoz létre az alkalmazás-végponthoz, ha konfigurálva van.
1. A *porton keresztül továbbítja* az alkalmazás-végponthoz való hozzáférést http://localhosta használatával.
1. Az stdout és a stderr továbbítása az ügyféloldali eszközökhöz.


## <a name="starting-a-service"></a>Szolgáltatás indítása

Amikor egy szolgáltatást egy fejlesztői térben indít el, az ügyféloldali szerszámozás és vezérlő koordinálja a forrásfájlok szinkronizálását, létrehozza a tárolót és a Kubernetes objektumokat, és futtatja az alkalmazást.

Részletesebben a futtatáskor `azds up`a következő történik:

1. A [fájlok szinkronizálása][sync-section] a felhasználó számítógépéről egy olyan Azure file Storage-ba történik, amely egyedi a felhasználó AK-fürtjén. A rendszer feltölti a forráskódot, a Helm-diagramot és a konfigurációs fájlokat.
1. A vezérlő új munkamenet elindítására vonatkozó kérelmet hoz létre. Ez a kérelem több tulajdonságot tartalmaz, beleértve az egyedi azonosítót, a tárhely nevét, a forráskód elérési útját és egy hibakeresési jelzőt.
1. A vezérlő lecseréli az *$ (tag)* helyőrzőt a Helm diagramon az egyedi munkamenet-azonosítóval, és telepíti a Helm-diagramot a szolgáltatáshoz. Az egyedi munkamenet-AZONOSÍTÓra mutató hivatkozás hozzáadása a Helm diagramhoz lehetővé teszi, hogy az AK-fürtön üzembe helyezett tároló a munkamenet-kérelemhez és a kapcsolódó információkhoz legyen kötve.
1. A Helm diagram telepítése során a Kubernetes webhook-felvételi kiszolgáló további tárolókat hoz létre az alkalmazás Pod eszközéhez, és hozzáfér a projekt forráskódhoz. A rendszer hozzáadja a devspaces-proxy és a devspaces-proxy-init tárolókat a HTTP-nyomkövetés és a térköz-útválasztás biztosításához. A rendszer hozzáadja a devspaces tárolót, amely hozzáférést biztosít a Docker-példányhoz és a projekt forráskódját az alkalmazás tárolójának létrehozásához.
1. Az alkalmazás Pod indításakor a devspaces-Build tároló és a devspaces-proxy-init tároló az alkalmazás-tároló összeállítására szolgál. Az alkalmazás-tároló és a devspaces-proxy tárolók elindulnak.
1. Az alkalmazás-tároló elindítása után az ügyféloldali funkció a Kubernetes *Port-Forward* funkciót használja az alkalmazáshoz való http-hozzáférés biztosításához http://localhost. Ennek a portnak a továbbítása a fejlesztési számítógépet a fejlesztői térben található szolgáltatáshoz kapcsolja.
1. Ha a pod összes tárolója elindult, a szolgáltatás fut. Ezen a ponton az ügyféloldali funkciók elkezdik továbbítani a HTTP-nyomkövetéseket, az stdout-t és a stderr. Ezt az információt a fejlesztő ügyféloldali funkciója jeleníti meg.

## <a name="updating-a-running-service"></a>Futó szolgáltatás frissítése

A szolgáltatás futása közben az Azure dev Spaces képes frissíteni a szolgáltatást, ha a projekt bármelyik fájlja megváltozik. A dev Spaces emellett a módosított fájl típusától függően eltérő módon frissíti a szolgáltatást. A fejlesztői helyek háromféle módon frissíthetik a futó szolgáltatásokat:

* Fájl közvetlen frissítése
* Az alkalmazás folyamatának újraépítése és újraindítása a futó alkalmazás tárolóján belül
* Az alkalmazás tárolójának újraépítése és újbóli üzembe helyezése

![Azure dev Spaces-fájlok szinkronizálása](media/how-dev-spaces-works/file-sync.svg)

Bizonyos olyan projektfájlok, amelyek statikus eszközök, például HTML-, CSS-és cshtml-fájlok, közvetlenül az alkalmazás tárolójában frissíthetők anélkül, hogy bármit újraindítanak. Ha egy statikus eszköz megváltozik, az új fájl szinkronizálva lesz a fejlesztői területtel, majd a futó tároló használja.

A fájlok, például a forráskód vagy az alkalmazás konfigurációs fájljainak módosításai az alkalmazás folyamatának a futó tárolón belüli újraindításával alkalmazhatók. A fájlok szinkronizálása után az alkalmazás folyamata újraindul a futó tárolón belül a *devhostagent* folyamat használatával. Amikor először hozza létre az alkalmazás tárolóját, a vezérlő lecseréli az alkalmazás indítási parancsát egy másik, *devhostagent*nevű folyamattal. Az alkalmazás tényleges folyamata ezután alárendelt folyamatként fut a *devhostagent*alatt, és a kimenete a *devhostagent*kimenetének használatával van leválasztva. A *devhostagent* folyamat a fejlesztői terek részét képezi, és a futó tárolóban parancsokat is végrehajthat a fejlesztői szóközök nevében. Újraindítás esetén a *devhostagent*:

* Leállítja az alkalmazáshoz társított aktuális folyamatot vagy folyamatokat.
* Újraépíti az alkalmazást
* Újraindítja az alkalmazáshoz társított folyamatot vagy folyamatokat.

A *devhostagent* hajtja végre az előző lépéseket a [alkalmazásban `azds.yaml` ][azds-yaml-section].

A projektfájlok, például a Dockerfiles, a csproj-fájlok vagy a Helm diagram bármely részének frissítéseihez újra kell építeni és újra telepíteni az alkalmazás tárolóját. Ha a fájlok egyike szinkronizálva van a fejlesztői területtel, a vezérlő futtatja a [Helm upgrade][helm-upgrade] parancsot, és az alkalmazás tárolóját újraépíti és újból üzembe helyezi.

## <a name="file-synchronization"></a>Fájl szinkronizálása

Amikor első alkalommal indít el egy alkalmazást egy fejlesztői térben, a rendszer az alkalmazás összes forrásfájljait feltölti. Amíg az alkalmazás fut, és a későbbi újraindítások során csak a módosított fájlok lesznek feltöltve. Ennek a folyamatnak a koordinálására két fájl használható: egy ügyféloldali fájl és egy vezérlő oldali fájl.

Az ügyféloldali fájlt egy ideiglenes könyvtárban tárolja a rendszer, és a fejlesztői tárhelyeken futó projekt könyvtárának kivonata alapján nevezi el. Előfordulhat például, hogy a Windowsban egy fájl, például a *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* a projekthez. Linux rendszeren az ügyféloldali fájlt a */tmp* könyvtárban tárolja a rendszer. A (z) `echo $TMPDIR` parancs futtatásával megkeresheti a MacOS-es könyvtárat.

A fájl JSON formátumú, és a következőket tartalmazza:

* A fejlesztői területtel szinkronizált egyes projektfájlok bejegyzései
* Szinkronizálási azonosító
* A legutóbbi szinkronizálási művelet időbélyege

Minden projektfájl-bejegyzés tartalmazza a fájl elérési útját és az időbélyegét.

A vezérlő oldali fájl tárolása az AK-fürtön történik. Tartalmazza a szinkronizálási azonosítót és a legutóbbi szinkronizálás időbélyegét.

Szinkronizálás történik, ha a szinkronizálási időbélyegek nem egyeznek az ügyféloldali és a vezérlő oldali fájlok között. A szinkronizálás során az ügyféloldali eszköz megismétli az ügyféloldali fájlban lévő fájlok bejegyzéseit. Ha a fájl időbélyege a szinkronizálási időbélyeg után van, a fájl szinkronizálva lesz a fejlesztői területtel. A szinkronizálás befejeződése után a szinkronizálási időbélyegek az ügyféloldali és a vezérlő oldali fájlokon is frissülnek.

Az összes projektfájl szinkronizálva van, ha az ügyféloldali fájl nincs jelen. Ez a viselkedés lehetővé teszi a teljes szinkronizálás kényszerítését az ügyféloldali fájl törlésével.

## <a name="how-running-your-code-is-configured"></a>A kód futtatásának beállítása

Az Azure dev Spaces `azds.yaml` a fájlt használja a szolgáltatás telepítéséhez és konfigurálásához. A vezérlő a `install` `azds.yaml` fájl tulajdonságát használja a Helm diagram telepítéséhez és a Kubernetes objektumok létrehozásához:

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

Alapértelmezés szerint a `prep` parancs létrehozza a Helm diagramot. Emellett beállítja a *install. chart* tulajdonságot is a Helm diagram könyvtárába. Ha egy másik helyen szeretné használni a Helm-diagramot, ezt a tulajdonságot az adott hely használatára is frissítheti.

A Helm-diagramok telepítésekor az Azure dev Spaces lehetővé teszi az értékek felülbírálását a Helm diagramon. A Helm diagram alapértelmezett értékei a következőben találhatók `charts/APP_NAME/values.yaml`:.

A *install. Values* tulajdonság használatával listázhat egy vagy több olyan fájlt, amely a Helm diagramon lecserélni kívánt értékeket határozza meg. Ha például azt szeretné, hogy egy állomásnév vagy adatbázis-konfiguráció kifejezetten az alkalmazás egy fejlesztői térben való futtatásakor fusson, ezt a felülbírálási funkciót használhatja. Hozzáadhat egy-t is *?* a fájlnevek bármelyikének végén adja meg azt opcionálisként.

A *install. set* tulajdonság lehetővé teszi egy vagy több lecserélni kívánt érték konfigurálását a Helm diagramon. A *install. set* fájlban konfigurált értékek felülbírálják a *install. Values*fájlban felsorolt fájlokban konfigurált értékeket. A *telepítési. set* tulajdonság tulajdonságai a Helm diagram értékeitől függenek, és a generált Helm diagramtól függően eltérőek lehetnek.

A fenti példában az *install. set. replicaCount* tulajdonság azt jelzi, hogy az alkalmazás hány példánya fut a fejlesztői térben. A forgatókönyvtől függően növelheti ezt az értéket, de hatással lehet a hibakereső az alkalmazás Pod-ra való csatolására. További információ: [hibaelhárítási cikk][troubleshooting].

A generált Helm diagramon a tároló képe a következőre van beállítva: *{{. Values. rendszerkép. adattár}}: {{. Values. rendszerkép. tag}}*. A `azds.yaml` fájl az *install. set. file. tag* tulajdonságot a *$ (címke)* értékként adja meg alapértelmezés szerint, amelyet a rendszer a *{{értékeként használ. Values. rendszerkép. tag}}*. Az *install. set. rendszerkép. tag* tulajdonság beállításával így az alkalmazáshoz tartozó tároló-rendszerkép az Azure dev Spaces futtatásakor eltérő módon címkézhető. Ebben a konkrét esetben a rendszerkép a * \<rendszerkép. adattár>: $ (címke) értékként*van megjelölve. A *$ (tag)* változót a *install. set. rendszerkép. tag* értékként kell használni a fejlesztői szóközök felismeréséhez, és meg kell keresnie a tárolót az AK-fürtben.

A fenti példában `azds.yaml` az *install. set. beáramló. hosts*. A *install. set. beáramló. hosts* tulajdonság a nyilvános végpontok állomásnév-formátumát határozza meg. Ez a tulajdonság a *$ (spacePrefix)*, a *$ (rootSpacePrefix)* és a *$ (hostSuffix)* értéket is használja, amely a vezérlő által megadott érték.

A *$ (spacePrefix)* a gyermek fejlesztői terület neve, amely a *SPACENAME. s*formátum formáját ölti. A *$ (rootSpacePrefix)* a szülő terület neve. Ha például az *Azureus* egy *alapértelmezett*gyermekobjektum, a *$ (rootSpacePrefix)* érték értéke *alapértelmezett* , a *$ (spacePrefix)* pedig az *Azureus. s*. Ha a szóköz nem gyermek, a *$ (spacePrefix)* üres. Ha például az *alapértelmezett* szóköz nem rendelkezik szülő területtel, a *$ (rootSpacePrefix)* érték értéke *alapértelmezett* , a *$ (spacePrefix)* érték pedig üres. A *$ (hostSuffix)* egy olyan DNS-utótag, amely az AK-fürtben futó Azure dev Spaces beléptetési vezérlőre mutat. Ez a DNS-utótag a helyettesítő karakteres DNS-bejegyzésnek felel meg, például * \*:. RANDOM_VALUE. EUs. azds. IO*, amely akkor jött létre, amikor az Azure dev Spaces-vezérlőt hozzáadták az AK-fürthöz.

A fenti `azds.yaml` fájlban azt is megteheti, hogy frissíti a *install. set. beáramló. hosts* fájlt, hogy megváltoztassa az alkalmazás állomásnevét. Ha például azt szeretné, hogy az alkalmazás állomásneve leegyszerűsítse a $ ( *spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* értéket a *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*) webverzióra.

Az alkalmazáshoz tartozó tároló létrehozásához a vezérlő a `azds.yaml` konfigurációs fájl alábbi részeit használja:

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

A vezérlő Docker használ az alkalmazás létrehozásához és futtatásához.

A *Build. Context* tulajdonság felsorolja azt a könyvtárat, ahol a Dockerfiles létezik. A *Build. Docker* tulajdonság határozza meg az alkalmazás éles verziójának létrehozásához használt Docker nevét. A *konfigurációk. Development. Build. Docker* tulajdonság az alkalmazás fejlesztői verziójának Docker nevét konfigurálja.

A fejlesztés és a gyártás különböző Dockerfiles lehetővé teszi bizonyos dolgok használatát a fejlesztés során, és letiltja ezeket az elemeket az éles környezetekben való üzembe helyezéshez. Például engedélyezheti a hibakeresést vagy részletesebb naplózást a fejlesztés során, és letilthatja azt éles környezetben. Ezeket a tulajdonságokat akkor is frissítheti, ha a Dockerfiles neve másként van, vagy egy másik helyen található.

A fejlesztés során a gyors iteráció elősegítése érdekében az Azure dev Spaces szinkronizálja a helyi projekt változásait, és fokozatosan frissíti az alkalmazást. A szinkronizálás és a frissítés `azds.yaml` konfigurálásához a konfigurációs fájl alábbi szakasza szolgál:

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

A szinkronizálni kívánt fájlok és könyvtárak megjelennek a *konfigurációk. fejlesztői. Container. Sync* tulajdonságban. A rendszer először szinkronizálja ezeket a címtárakat, `up` amikor futtatja a parancsot, valamint a módosítások észlelését. Ha vannak olyan további vagy különböző könyvtárak, amelyeket szinkronizálni szeretne a fejlesztői területtel, módosíthatja ezt a tulajdonságot.

A *konfigurációk. Development. Container. iteráció. buildCommands* tulajdonság azt adja meg, hogyan kell felépíteni az alkalmazást egy fejlesztési forgatókönyvben. A *konfigurációk. Development. Container. Command* tulajdonság biztosítja az alkalmazás fejlesztési forgatókönyvben való futtatásához szükséges parancsot. Előfordulhat, hogy frissítenie kell ezeket a tulajdonságokat, ha további Build vagy Runtime jelzőket vagy paramétereket szeretne használni a fejlesztés során.

A *konfigurációk. Build. Container. iteráció. processesToKill* felsorolja az alkalmazás leállításához szükséges folyamatokat. Érdemes lehet frissíteni ezt a tulajdonságot, ha módosítani szeretné az alkalmazás újraindítási viselkedését a fejlesztés során. Ha például frissítette a *konfigurációkat. fejlessze a. Container. iteráció. buildCommands* vagy a configurations. Build. *Container. Command* tulajdonságokat az alkalmazás létrehozásának vagy indításának megváltoztatásához, előfordulhat, hogy módosítania kell a leállított folyamatokat.

Ha a `azds prep` parancs használatával készíti elő a kódot, lehetősége van a `--enable-ingress` jelző hozzáadására. A `--enable-ingress` jelző hozzáadása nyilvánosan elérhető URL-címet hoz létre az alkalmazáshoz. Ha kihagyja ezt a jelzőt, az alkalmazás csak a fürtön belül vagy a localhost-alagút használatával érhető el. A `azds prep` parancs futtatása után megváltoztathatja ezt a beállítást a *bejövő. enabled* tulajdonság módosításával a következőben `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelésről és a kérelmek az Azure dev Spaces szolgáltatásban való továbbításáról: [Hogyan működik az Útválasztás az Azure dev Spaces][how-it-works-routing]használatával.

Ha többet szeretne megtudni arról, hogyan használható az Azure dev Spaces a gyors iteráció és a fejlesztés érdekében, tekintse meg a [fejlesztői számítógép és a fejlesztői terület összekapcsolásának módját][how-it-works-connect] , valamint azt, [hogy a kód hogyan működik együtt az Azure dev Spaces][how-it-works-remote-debugging]használatával.

Az Azure dev Spaces a projekt futtatásához való használatának megkezdéséhez tekintse meg az alábbi rövid útmutatókat:

* [Gyors iteráció és hibakeresés a Visual Studio Code és a Java révén][quickstart-java]
* [Gyors iteráció és hibakeresés a Visual Studio Code és a .NET használatával][quickstart-netcore]
* [Gyors iteráció és hibakeresés a Visual Studio Code és a Node. js-sel][quickstart-node]
* [Gyors iteráció és hibakeresés a Visual Studióval és a .NET Core-val][quickstart-vs]
* [Alkalmazás fejlesztése a Kubernetes-on a CLI használatával][quickstart-cli]


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