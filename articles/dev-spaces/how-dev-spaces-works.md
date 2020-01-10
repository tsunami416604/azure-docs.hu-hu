---
title: Az Azure dev Spaces működése és konfigurálása
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Ismerteti azokat a folyamatokat, amelyekkel a Power Azure dev Spaces és hogyan vannak konfigurálva a azds. YAML konfigurációs fájlban
keywords: azds. YAML, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771138"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Az Azure dev Spaces működése és konfigurálása

A Kubernetes-alkalmazások fejlesztése kihívást jelenthet. A Docker és a Kubernetes konfigurációs fájljaira van szüksége. Meg kell határoznia, hogyan tesztelheti az alkalmazást helyileg, és hogyan dolgozhat más függő szolgáltatásokkal. Előfordulhat, hogy egyszerre több szolgáltatás fejlesztését és tesztelését kell kezelnie, és a fejlesztői csapatot kell használnia.

Az Azure dev Spaces segítségével közvetlenül az Azure Kubernetes szolgáltatásban (ak) fejlesztheti, helyezheti üzembe és kereshet Kubernetes-alkalmazásokat. Az Azure dev Spaces lehetővé teszi a csapat számára a fejlesztői terület megosztását is. A fejlesztői területek egy csoporton belüli megosztása lehetővé teszi az egyes csapattagok számára, hogy a függőségek és a fürtben lévő más alkalmazások replikálásának vagy modellezésének elvégzése nélkül is elkülönítve fejlesszenek.

Az Azure dev Spaces egy konfigurációs fájlt hoz létre és használ a Kubernetes-alkalmazások AK-beli üzembe helyezéséhez, futtatásához és hibakereséséhez. Ez a konfigurációs fájl az alkalmazás kódjával együtt található, és hozzáadható a verziókövetés rendszeréhez.

Ez a cikk azokat a folyamatokat ismerteti, amelyekkel a Power Azure dev Spaces és ezek a folyamatok konfigurálhatók az Azure dev Spaces konfigurációs fájljában. Ha gyorsan szeretné lekérni az Azure dev-helyeket, és a gyakorlatban szeretné megtekinteni, hajtsa végre a következő lépések egyikét:

* [Java a parancssori felület és a Visual Studio Code használatával](quickstart-java.md)
* [.NET Core parancssori felület és Visual Studio Code használatával](quickstart-netcore.md)
* [.NET Core a Visual Studióval](quickstart-netcore-visualstudio.md)
* [Node. js a CLI és a Visual Studio Code használatával](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Az Azure Dev Spaces működése

Az Azure dev Spaces két különböző összetevővel rendelkezik, amelyeket Ön kezel: a vezérlőt és az ügyféloldali eszközöket.

![Azure dev Spaces-összetevők](media/how-dev-spaces-works/components.svg)

A vezérlő a következő műveleteket hajtja végre:

* Felügyeli a fejlesztési terület létrehozását és a kijelölést.
* Telepíti az alkalmazás Helm-diagramját, és létrehozza a Kubernetes objektumokat.
* Létrehozza az alkalmazás tárolójának rendszerképét.
* Üzembe helyezi az alkalmazást az AK-ban.
* A növekményes buildek és újraindítások a forráskód módosításakor.
* A naplókat és a HTTP-nyomkövetéseket kezeli.
* Az stdout és a stderr továbbítása az ügyféloldali eszközökhöz.
* Lehetővé teszi a csapattagok számára, hogy a szülő fejlesztői területből származtatott gyermek fejlesztési helyeket hozzanak létre.
* Az útválasztást a szóközen belüli alkalmazások, valamint a szülő és a gyermek szóközök között konfigurálja.

A vezérlő az AK-n kívül található. Az ügyféloldali eszközök és az AK-fürt közötti viselkedést és kommunikációt vezérli. A vezérlő az Azure CLI-vel van engedélyezve, amikor előkészíti a fürtöt az Azure dev Spaces használatára. Ha engedélyezve van, használhatja az ügyféloldali szerszámozás használatával.

Az ügyféloldali eszközök lehetővé teszik a felhasználó számára a következőket:
* Docker, Helm-diagram és Azure dev Spaces konfigurációs fájl létrehozása az alkalmazáshoz.
* Hozzon létre szülő és gyermek fejlesztői szóközöket.
* Kérje meg a vezérlőt, hogy hozza létre és indítsa el az alkalmazást.

Az alkalmazás futása közben az ügyféloldali eszköz is:
* Az stdout és a stderr fogadása és megjelenítése az alkalmazásban, az AK-ban fut.
* A [Port-Forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) használatával engedélyezi az alkalmazás webes elérését http-n keresztül:\//localhost.
* Egy hibakeresőt csatlakoztat a futó alkalmazáshoz az AK-ban.
* Szinkronizálja a forráskódot a fejlesztői területére, amikor változást észlel a növekményes buildek esetében, ami lehetővé teszi a gyors iterációt.

A `azds` parancs részeként használhatja az ügyféloldali eszközt a parancssorból. Az ügyféloldali eszközt az alábbiakkal is használhatja:

* Visual Studio Code az [Azure dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)használatával.
* Visual Studio [Kubernetes-hez készült Visual Studio-eszközökkel](https://aka.ms/get-vsk8stools).

Az Azure dev Spaces beállításának és használatának alapszintű folyamata:
1. Az AK-fürt előkészítése az Azure dev Spaces szolgáltatáshoz
1. Készítse elő a kódot az Azure dev Spaces-on való futtatáshoz
1. A kód futtatása egy fejlesztői tárhelyen
1. A kód hibakeresése egy fejlesztői tárhelyen
1. Fejlesztői terület megosztása

Az alábbi részekben részletesen ismertetjük, hogy az Azure dev Spaces hogyan működik.

## <a name="prepare-your-aks-cluster"></a>Az AK-fürt előkészítése

Az AK-fürt előkészítése a következőket foglalja magában:
* Az AK-fürt ellenőrzése az [Azure dev Spaces által támogatott][supported-regions]régióban található.
* Annak ellenőrzése, hogy a Kubernetes 1.10.3 vagy újabb verzióját futtatja-e.
* Az Azure dev-helyek engedélyezése a fürtön `az aks use-dev-spaces` használatával

A következő témakörben talál további információt arról, hogyan hozhat létre és konfigurálhat egy AK-fürtöt az Azure dev Spaces szolgáltatásban: az első lépéseket ismertető útmutató
* [Ismerkedés az Azure fejlesztői Spaces és Javával](get-started-java.md)
* [Ismerkedés az Azure dev Spaces szolgáltatással a .NET Core és a Visual Studio használatával](get-started-netcore-visualstudio.md)
* [Ismerkedés az Azure dev Spaces és a .NET Core használatával](get-started-netcore.md)
* [Ismerkedés az Azure fejlesztői Spaces és a Node. js-szel](get-started-nodejs.md)

Ha az Azure dev-helyek engedélyezve vannak az AK-fürtön, akkor a fürthöz telepíti a vezérlőt. A vezérlő egy különálló Azure-erőforrás a fürtön kívül, és a fürt erőforrásaihoz a következő:

* Létrehoz vagy kijelöl egy Kubernetes-névteret, amelyet fejlesztői területként kíván használni.
* Eltávolítja a *azds*nevű Kubernetes-névteret, ha létezik, és létrehoz egy újat.
* Üzembe helyez egy Kubernetes webhook-konfigurációt.
* Üzembe helyez egy webhook-beléptetési kiszolgálót.
    

Ugyanezt a szolgáltatást használja, amelyet az AK-fürt más Azure dev Spaces-összetevőkhöz használ.

![Azure dev Spaces – fürt előkészítése](media/how-dev-spaces-works/prepare-cluster.svg)

Az Azure dev Spaces használatához legalább egy fejlesztői területnek kell lennie. Az Azure dev Spaces Kubernetes-névtereket használ az AK-fürtön belül a dev Spaces szolgáltatásban. A vezérlő telepítésekor a rendszer felszólítja, hogy hozzon létre egy új Kubernetes-névteret, vagy válasszon egy meglévő névteret, amelyet az első fejlesztői területként kíván használni. Ha egy névtér fejlesztői területként van kijelölve, a vezérlő hozzáadja a *azds.IO/Space=True* címkét a névtérhez, hogy az a fejlesztői területként azonosítható legyen. A létrehozás vagy a kijelölés kezdeti fejlesztői területe alapértelmezés szerint ki van választva a fürt előkészítése után. Ha kijelöl egy szóközt, az Azure dev Spaces új munkaterhelések létrehozásához használja.

Alapértelmezés szerint a vezérlő egy *alapértelmezett* nevű fejlesztői helyet hoz létre a meglévő *alapértelmezett* Kubernetes-névtér frissítésével. Az ügyféloldali eszközkészlet használatával új fejlesztői tárhelyeket hozhat létre, és eltávolíthatja a meglévő fejlesztői helyeket. A Kubernetes korlátozásai miatt az *alapértelmezett* fejlesztői terület nem távolítható el. A vezérlő eltávolítja a *azds* nevű meglévő Kubernetes-névtereket is, hogy elkerülje az ügyféloldali eszközök által használt `azds` parancs ütközését.

A Kubernetes webhook-belépésvezérlés a hüvelyek három tárolóval való beadására szolgál a rendszerállapot-kialakítás során: egy devspaces-tárolót, egy devspaces-proxy-init tárolót és egy devspaces-tárolót. **Mindhárom tároló a root hozzáféréssel fut az AK-fürtön.** Ugyanazt a szolgáltatásnevet is használják, amelyet az AK-fürt más Azure dev Spaces-összetevőkhöz használ.

![Azure dev Spaces Kubernetes webhook-beléptetési kiszolgáló](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

A devspaces-proxy tároló egy olyan oldalkocsi-tároló, amely az alkalmazás-tárolón belüli és kívüli összes TCP-forgalmat kezeli, és segít az útválasztásban. A devspaces-proxy tároló a HTTP-üzeneteket átirányítja, ha vannak ilyenek. Például segíthet a HTTP-üzenetek továbbításában a szülő és a gyermek szóközökben lévő alkalmazások között. Az összes nem HTTP-forgalom áthalad a devspaces-proxyn keresztül. A devspaces-proxy tároló az összes bejövő és kimenő HTTP-üzenetet is naplózza, és a nyomkövetésként elküldi őket az ügyféloldali eszközöknek. Ezeket a nyomkövetéseket a fejlesztő megtekintheti, hogy megvizsgálja az alkalmazás viselkedését.

A devspaces-proxy-init tároló egy olyan [init-tároló](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , amely további útválasztási szabályokat is feltesz az alkalmazás tárolójának terület-hierarchiája alapján. Az útválasztási szabályok hozzáadásával az alkalmazás-tároló */etc/resolv.conf* -fájlját és az iptables-konfigurációt az indítás előtt frissíti. A */etc/resolv.conf* -frissítések lehetővé teszik a szolgáltatások DNS-feloldását a fölérendelt helyeken. Az iptables-konfiguráció frissítései biztosítják, hogy az alkalmazás tárolóján belüli és onnan kifelé irányuló összes TCP-forgalom átirányítva legyen, bár a devspaces-proxy. A devspaces-proxy-init összes frissítése a Kubernetes által hozzáadott szabályok mellett történik.

A devspaces-Build tároló egy init tároló, amelynek a projekt forráskódja és a Docker-szoftvercsatorna csatlakoztatva van. A projekt forráskódja és a Docker-hozzáférés lehetővé teszi, hogy az alkalmazás-tárolót közvetlenül a pod-hoz lehessen felépíteni.

> [!NOTE]
> Az Azure dev-helyek ugyanazt a csomópontot használják az alkalmazás tárolójának létrehozásához és futtatásához. Ennek eredményeképpen az Azure dev Spaces szolgáltatásnak nincs szüksége külső tároló beállításjegyzékre az alkalmazás létrehozásához és futtatásához.

A Kubernetes webhook-beléptetési kiszolgáló az AK-fürtben létrehozott összes új Pod-t figyeli. Ha a hüvely bármely, a *azds.IO/Space=True* címkével rendelkező névtérbe van telepítve, akkor a pod a további tárolókat is beadja. A devspaces-Build tároló csak akkor van befecskendezve, ha az alkalmazás tárolója az ügyféloldali eszköz használatával fut.

Miután felkészítette az AK-fürtöt, az ügyféloldali eszközkészlet segítségével előkészítheti és futtathatja a kódot a fejlesztői térben.

## <a name="prepare-your-code"></a>A kód előkészítése

Ahhoz, hogy az alkalmazás egy fejlesztői térben fusson, a tárolónak kell lennie, és meg kell határoznia, hogyan kell telepíteni a Kubernetes. Az alkalmazás tárolóba helyezése szüksége lesz egy Docker. Ha meg szeretné határozni, hogy az alkalmazás hogyan legyen központilag telepítve a Kubernetes-re, [Helm-diagramra](https://docs.helm.sh/)van szüksége. Az Docker és a Helm diagram alkalmazáshoz való létrehozásához az ügyféloldali eszközök biztosítják a `prep` parancsot:

```cmd
azds prep --public
```

A `prep` parancs megtekinti a projektben lévő fájlokat, és megpróbálja létrehozni a Docker és a Helm diagramot az alkalmazás futtatásához a Kubernetes-ben. A `prep` parancs jelenleg a következő nyelvekkel hoz majd Docker és Helm diagramot:

* Java
* Node.js
* .NET Core

A `prep` parancsot olyan könyvtárból *kell* futtatnia, amely forráskódot tartalmaz. A `prep` parancs a megfelelő könyvtárból való futtatása lehetővé teszi, hogy az ügyféloldali eszköz azonosítsa a nyelvet, és megfelelő Docker hozzon létre az alkalmazás tárolóba helyezése. A `prep` parancsot egy olyan könyvtárból is futtathatja, amely tartalmazza a Java-projektek *Pom. XML* fájlját.

Ha a `prep` parancsot olyan könyvtárból futtatja, amely nem tartalmaz forráskódot, az ügyféloldali eszközkészlet nem hoz majd Docker. Emellett a következő hibaüzenetet is megjeleníti: *a Docker nem hozható létre a nem támogatott nyelv miatt*. Ez a hiba akkor is előfordulhat, ha az ügyféloldali eszközkészlet nem ismeri fel a projekt típusát.

A `prep` parancs futtatásakor lehetősége van megadnia a `--public` jelzőt. Ez a jelző arra utasítja a vezérlőt, hogy hozzon létre egy internetről elérhető végpontot ehhez a szolgáltatáshoz. Ha nem adja meg ezt a jelzőt, a szolgáltatás csak a fürtön belülről, vagy az ügyféloldali eszközök által létrehozott localhost-alagút használatával érhető el. A `prep` parancs futtatása után engedélyezheti vagy letilthatja ezt a viselkedést, ha frissíti a generált Helm-diagramot.

A `prep` parancs nem cseréli le a projektben lévő meglévő Dockerfiles-vagy Helm-diagramokat. Ha egy meglévő Docker vagy Helm-diagram ugyanazt az elnevezési konvenciót használja, mint a `prep` parancs által generált fájlok, a `prep` parancs kihagyja a fájlok létrehozását. Ellenkező esetben az `prep` parancs létrehozza a saját Docker vagy Helm-diagramját a meglévő fájlok oldalára.

A `prep` parancs a projekt gyökérkönyvtárában is létrehozhat egy `azds.yaml` fájlt. Az Azure dev Spaces ezt a fájlt használja az alkalmazás létrehozásához, telepítéséhez, konfigurálásához és futtatásához. Ez a konfigurációs fájl felsorolja a Docker és a Helm diagram helyét, valamint további konfigurációkat is biztosít ezen összetevők felett.

Íme egy példa az azds. YAML fájlra, amelyet a [.net Core Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)használatával hoztak létre:

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

A `prep` parancs által létrehozott `azds.yaml` fájlnak egy egyszerű, egyetlen projekt-fejlesztési forgatókönyvhöz kell működnie. Ha az adott projektnek nagyobb a bonyolultsága, akkor előfordulhat, hogy a `prep` parancs futtatása után frissítenie kell ezt a fájlt. Előfordulhat például, hogy a projekt a fejlesztési vagy hibakeresési igényeknek megfelelően megnöveli a létrehozási vagy indítási folyamatát. Több alkalmazás is lehet a projektben, amelyhez több fordítási folyamat vagy más Build-tartalom szükséges.

## <a name="run-your-code"></a>A kód futtatása

A kód fejlesztői tárhelyen való futtatásához adja ki a `up` parancsot a `azds.yaml` fájllal megegyező könyvtárban:

```cmd
azds up
```

A `up` parancs feltölti az alkalmazás forrásfájljait és a projekt létrehozásához és futtatásához szükséges egyéb összetevőket a fejlesztői területhez. A fejlesztői terület vezérlője:

1. Létrehozza a Kubernetes objektumokat az alkalmazás üzembe helyezéséhez.
1. Létrehozza az alkalmazás tárolóját.
1. Üzembe helyezi az alkalmazást a fejlesztői térben.
1. Nyilvánosan elérhető DNS-nevet hoz létre az alkalmazás-végponthoz, ha konfigurálva van.
1. A *porton keresztül továbbítja* az alkalmazás-végpont elérését http://localhost használatával.
1. Az stdout és a stderr továbbítása az ügyféloldali eszközökhöz.


### <a name="starting-a-service"></a>Szolgáltatás indítása

Amikor egy szolgáltatást egy fejlesztői térben indít el, az ügyféloldali szerszámozás és vezérlő koordinálja a forrásfájlok szinkronizálását, létrehozza a tárolót és a Kubernetes objektumokat, és futtatja az alkalmazást.

Részletesebben a `azds up`futtatásakor a következő történik:

1. A rendszer a felhasználó számítógépéről szinkronizálja a fájlokat egy Azure file Storage-ba, amely egyedi a felhasználó AK-fürtjén. A rendszer feltölti a forráskódot, a Helm-diagramot és a konfigurációs fájlokat. A szinkronizálási folyamat további részletei a következő szakaszban találhatók.
1. A vezérlő új munkamenet elindítására vonatkozó kérelmet hoz létre. Ez a kérelem több tulajdonságot tartalmaz, beleértve az egyedi azonosítót, a tárhely nevét, a forráskód elérési útját és egy hibakeresési jelzőt.
1. A vezérlő lecseréli az *$ (tag)* helyőrzőt a Helm diagramon az egyedi munkamenet-azonosítóval, és telepíti a Helm-diagramot a szolgáltatáshoz. Az egyedi munkamenet-AZONOSÍTÓra mutató hivatkozás hozzáadása a Helm diagramhoz lehetővé teszi, hogy az AK-fürtön üzembe helyezett tároló a munkamenet-kérelemhez és a kapcsolódó információkhoz legyen kötve.
1. A Helm diagram telepítése során a Kubernetes webhook-felvételi kiszolgáló további tárolókat hoz létre az alkalmazás Pod eszközéhez, és hozzáfér a projekt forráskódhoz. A rendszer hozzáadja a devspaces-proxy és a devspaces-proxy-init tárolókat a HTTP-nyomkövetés és a térköz-útválasztás biztosításához. A rendszer hozzáadja a devspaces tárolót, amely hozzáférést biztosít a Docker-példányhoz és a projekt forráskódját az alkalmazás tárolójának létrehozásához.
1. Az alkalmazás Pod indításakor a devspaces-Build tároló és a devspaces-proxy-init tároló az alkalmazás-tároló összeállítására szolgál. Az alkalmazás-tároló és a devspaces-proxy tárolók elindulnak.
1. Az alkalmazás-tároló elindítása után az ügyféloldali funkció a Kubernetes *Port-Forward* funkciót használja az alkalmazáshoz való http-hozzáférés biztosításához http://localhost on keresztül. Ez a port továbbítása a fejlesztési gépet a fejlesztői térben található szolgáltatáshoz köti.
1. Ha a pod összes tárolója elindult, a szolgáltatás fut. Ezen a ponton az ügyféloldali funkciók elkezdik továbbítani a HTTP-nyomkövetéseket, az stdout-t és a stderr. Ezt az információt a fejlesztő ügyféloldali funkciója jeleníti meg.

### <a name="updating-a-running-service"></a>Futó szolgáltatás frissítése

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

A *devhostagent* hajtja végre az előző lépéseket a `azds.yaml` konfigurációs fájlban. Ez a konfiguráció egy későbbi szakaszban van részletezve.

A projektfájlok, például a Dockerfiles, a csproj-fájlok vagy a Helm diagram bármely részének frissítéseihez újra kell építeni és újra telepíteni az alkalmazás tárolóját. Ha a fájlok egyike szinkronizálva van a fejlesztői területtel, a vezérlő futtatja a [Helm upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) parancsot, és az alkalmazás tárolóját újraépíti és újból üzembe helyezi.

### <a name="file-synchronization"></a>Fájl szinkronizálása

Amikor első alkalommal indít el egy alkalmazást egy fejlesztői térben, a rendszer az alkalmazás összes forrásfájljait feltölti. Amíg az alkalmazás fut, és a későbbi újraindítások során csak a módosított fájlok lesznek feltöltve. Ennek a folyamatnak a koordinálására két fájl használható: egy ügyféloldali fájl és egy vezérlő oldali fájl.

Az ügyféloldali fájlt egy ideiglenes könyvtárban tárolja a rendszer, és a fejlesztői tárhelyeken futó projekt könyvtárának kivonata alapján nevezi el. Előfordulhat például, hogy a Windowsban egy fájl, például a *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* a projekthez. Linux rendszeren az ügyféloldali fájlt a */tmp* könyvtárban tárolja a rendszer. A (z) `echo $TMPDIR` parancs futtatásával megtalálhatja a macOS-es könyvtárat.

A fájl JSON formátumú, és a következőket tartalmazza:

* A fejlesztői területtel szinkronizált egyes projektfájlok bejegyzései
* Szinkronizálási azonosító
* A legutóbbi szinkronizálási művelet időbélyege

Minden projektfájl-bejegyzés tartalmazza a fájl elérési útját és az időbélyegét.

A vezérlő oldali fájl tárolása az AK-fürtön történik. Tartalmazza a szinkronizálási azonosítót és a legutóbbi szinkronizálás időbélyegét.

Szinkronizálás történik, ha a szinkronizálási időbélyegek nem egyeznek az ügyféloldali és a vezérlő oldali fájlok között. A szinkronizálás során az ügyféloldali eszköz megismétli az ügyféloldali fájlban lévő fájlok bejegyzéseit. Ha a fájl időbélyege a szinkronizálási időbélyeg után van, a fájl szinkronizálva lesz a fejlesztői területtel. A szinkronizálás befejeződése után a szinkronizálási időbélyegek az ügyféloldali és a vezérlő oldali fájlokon is frissülnek.

Az összes projektfájl szinkronizálva van, ha az ügyféloldali fájl nincs jelen. Ez a viselkedés lehetővé teszi a teljes szinkronizálás kényszerítését az ügyféloldali fájl törlésével.

### <a name="how-routing-works"></a>Az útválasztás működése

A fejlesztői terület az AK-ra épül, és ugyanazokat a [hálózatkezelési fogalmakat](../aks/concepts-network.md)használja. Az Azure dev Spaces egy központi *ingressmanager* -szolgáltatással is rendelkezik, és üzembe helyezi a saját bejövő vezérlőjét az AK-fürtön. A *ingressmanager* szolgáltatás FIGYELI az AK-fürtöket a dev Spaces-ben, és kibővíti az Azure dev Spaces-ba való belépést a fürtben a bejövő objektumokkal az Application hüvelyek útválasztásához. Az egyes Pod-devspaces-tárolók egy `azds-route-as` HTTP-fejlécet biztosítanak az URL-cím alapján a fejlesztői területhez való HTTP-forgalomhoz. Az URL-címre *http://azureuser.s.default.serviceA.fedcba09...azds.io* kérelem például egy HTTP-fejlécet kap, amely `azds-route-as: azureuser`. A devspaces tároló nem ad hozzá `azds-route-as` fejlécet, ha az egyik már létezik.

Amikor HTTP-kérést végeznek a fürtön kívüli szolgáltatásra, a kérés a bejövő vezérlőre lép. A beáramló vezérlő közvetlenül a megfelelő Pod-ra irányítja át a kérést a bejövő objektumai és szabályai alapján. A pod devspaces-proxy tároló fogadja a kérést, hozzáadja a `azds-route-as` fejlécet az URL-cím alapján, majd átirányítja a kérést az alkalmazás-tárolóba.

Amikor HTTP-kérést végez egy másik szolgáltatásból a fürtön belül, a kérelem először a hívó szolgáltatás devspaces-tárolóján halad végig. A devspaces-proxy tároló a HTTP-kérést tekinti át, és ellenőrzi a `azds-route-as` fejlécét. A fejléc alapján a devspaces tároló a fejléc értékéhez tartozó szolgáltatás IP-címét fogja megkeresni. Ha a rendszer IP-címet talál, a devspaces tároló átirányítja a kérést az adott IP-címhez. Ha nem található IP-cím, a devspaces tároló továbbítja a kérést a szülő alkalmazás tárolójába.

Az Applications *servicea* és a *serviceB* például az *alapértelmezett*nevű szülő fejlesztői területre van telepítve. a *servicea* a *serviceB* -ra TÁMASZKODik, és http-hívásokat tesz lehetővé. Az Azure-felhasználó létrehoz egy gyermek-fejlesztési helyet az *azureuser*nevű *alapértelmezett* hely alapján. Az Azure User a *servicea* saját verzióját is üzembe helyezi a gyermek területére. Kérelem *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Az Azure dev Spaces útválasztása](media/how-dev-spaces-works/routing.svg)

1. A bejövő vezérlő megkeresi az URL-címhez társított Pod IP-címét, amely a *servicea. azureuser*.
1. A beáramló vezérlő megkeresi az Azure-felhasználó fejlesztői területén található Pod IP-címét, és átirányítja a kérést a *servicea. azureuser* Pod-ra.
1. A *servicea. azureuser* Pod devspaces-tárolója fogadja a kérést, és hozzáadja a `azds-route-as: azureuser` http-fejlécként.
1. A servicea devspaces-tárolója *. az azureuser* Pod a servicea *. azureuser* POD szolgáltatásban továbbítja a kérést *a servicea alkalmazás* tárolójába.
1. A servicea *. azureuser* *Pod szolgáltatásbeli alkalmazása* meghívja a *serviceB*. A *servicea* alkalmazás olyan kódot is tartalmaz, amely megőrzi a meglévő `azds-route-as` fejlécet, amely ebben az esetben `azds-route-as: azureuser`.
1. A devspaces tároló a *servicea. az azureuser* Pod megkapja a kérést, és megkeresi a *serviceB* IP-címét a `azds-route-as` fejlécének értéke alapján.
1. A *servicea. azureuser* Pod devspaces-proxy tárolója nem talál IP-címet a *serviceB. azureuser*számára.
1. A *servicea. azureuser* Pod devspaces-proxy tárolója megkeresi a *serviceB* lévő IP-címet a szülő térben, amely a *serviceB. default*.
1. A servicea devspaces-tárolója *. az azureuser* Pod megkeresi a *serviceB. default* IP-címét, és átirányítja a kérést a *serviceB. default* Pod-ra.
1. A *serviceB. default* Pod devspaces-tárolója megkapja a kérést, és átirányítja a kérést a *serviceB. default* Pod *serviceB* -alkalmazás tárolójába.
1. A *serviceB* alkalmazás a *serviceB. az alapértelmezett* Pod a *servicea. azureuser* Pod-re adott választ ad vissza.
1. A *servicea. azureuser* Pod devspaces-tárolója megkapja a választ, és körözteti a választ a servicea *. azureuser* POD szolgáltatásban található *servicea* alkalmazás-tárolóba.
1. A *servicea* alkalmazás fogadja a választ, majd visszaadja a saját válaszát.
1. A *servicea. azureuser* Pod devspaces-tárolója fogadja a *servicea* alkalmazás-tárolótól érkező választ, és a fürtön kívülre irányítja a választ az eredeti hívónak.

Az összes többi TCP-forgalom, amely nem HTTP-továbbítást hajt végre a bejövő és a devspaces tárolóban, nem módosult.

### <a name="how-running-your-code-is-configured"></a>A kód futtatásának beállítása

Az Azure dev Spaces az `azds.yaml` fájlt használja a szolgáltatás telepítéséhez és konfigurálásához. A vezérlő a `azds.yaml` fájl `install` tulajdonságát használja a Helm diagram telepítéséhez és a Kubernetes objektumok létrehozásához:

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

A Helm-diagramok telepítésekor az Azure dev Spaces lehetővé teszi az értékek felülbírálását a Helm diagramon. A Helm diagram alapértelmezett értékei `charts/APP_NAME/values.yaml`.

A *install. Values* tulajdonság használatával listázhat egy vagy több olyan fájlt, amely a Helm diagramon lecserélni kívánt értékeket határozza meg. Ha például azt szeretné, hogy egy állomásnév vagy adatbázis-konfiguráció kifejezetten az alkalmazás egy fejlesztői térben való futtatásakor fusson, ezt a felülbírálási funkciót használhatja. Hozzáadhat egy-t is *?* a fájlnevek bármelyikének végén adja meg azt opcionálisként.

A *install. set* tulajdonság lehetővé teszi egy vagy több lecserélni kívánt érték konfigurálását a Helm diagramon. A *install. set* fájlban konfigurált értékek felülbírálják a *install. Values*fájlban felsorolt fájlokban konfigurált értékeket. A *telepítési. set* tulajdonság tulajdonságai a Helm diagram értékeitől függenek, és a generált Helm diagramtól függően eltérőek lehetnek.

A fenti példában az *install. set. replicaCount* tulajdonság azt jelzi, hogy az alkalmazás hány példánya fut a fejlesztői térben. A forgatókönyvtől függően növelheti ezt az értéket, de hatással lehet a hibakereső az alkalmazás Pod-ra való csatolására. További információ: [hibaelhárítási cikk](troubleshooting.md).

A generált Helm diagramon a tároló képe a következőre van beállítva: *{{. Values. rendszerkép. adattár}}: {{. Values. rendszerkép. tag}}* . A `azds.yaml` fájl a *install. set. rendszerkép. tag* tulajdonságot a *$ (címke)* értékre állítja be, amely alapértelmezés szerint a következő lesz: *{{. Values. rendszerkép. tag}}* . Az *install. set. rendszerkép. tag* tulajdonság beállításával így az alkalmazáshoz tartozó tároló-rendszerkép az Azure dev Spaces futtatásakor eltérő módon címkézhető. Ebben a konkrét esetben a rendszerkép\<értékként van megjelölve a *rendszerképből. adattár >: $ (tag)* . A *$ (tag)* változót a *install. set. rendszerkép. tag* értékként kell használni a fejlesztői szóközök felismeréséhez, és meg kell keresnie a tárolót az AK-fürtben.

A fenti példában `azds.yaml` határozza meg a *install. set. beáramló. hosts*. A *install. set. beáramló. hosts* tulajdonság a nyilvános végpontok állomásnév-formátumát határozza meg. Ez a tulajdonság a *$ (spacePrefix)* , a *$ (rootSpacePrefix)* és a *$ (hostSuffix)* értéket is használja, amely a vezérlő által megadott érték. 

A *$ (spacePrefix)* a gyermek fejlesztői terület neve, amely a *SPACENAME. s*formátum formáját ölti. A *$ (rootSpacePrefix)* a szülő terület neve. Ha például az *Azureus* egy *alapértelmezett*gyermekobjektum, a *$ (rootSpacePrefix)* érték értéke *alapértelmezett* , a *$ (spacePrefix)* pedig az *Azureus. s*. Ha a szóköz nem gyermek, a *$ (spacePrefix)* üres. Ha például az *alapértelmezett* szóköz nem rendelkezik szülő területtel, a *$ (rootSpacePrefix)* érték értéke *alapértelmezett* , a *$ (spacePrefix)* érték pedig üres. A *$ (hostSuffix)* egy olyan DNS-utótag, amely az AK-fürtben futó Azure dev Spaces beléptetési vezérlőre mutat. Ez a DNS-utótag a helyettesítő karakteres DNS-bejegyzésnek felel meg, például *\*. RANDOM_VALUE. EUs. azds. IO*, amely akkor jött létre, amikor az Azure dev Spaces-vezérlőt hozzáadták az AK-fürthöz.

A fenti `azds.yaml` fájlban a *install. set. beáramló. hosts* fájlt is frissítheti az alkalmazás állomásneve módosításához. Ha például azt szeretné, hogy az alkalmazás állomásneve leegyszerűsítse a $ ( *spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* értéket a *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*) webverzióra.

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

A fejlesztés során a gyors iteráció elősegítése érdekében az Azure dev Spaces szinkronizálja a helyi projekt változásait, és fokozatosan frissíti az alkalmazást. A szinkronizálás és a frissítés konfigurálásához a `azds.yaml` konfigurációs fájl alábbi szakasza szolgál:

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

A szinkronizálni kívánt fájlok és könyvtárak megjelennek a *konfigurációk. fejlesztői. Container. Sync* tulajdonságban. A rendszer először szinkronizálja ezeket a címtárakat a `up` parancs futtatásakor, valamint a módosítások észlelésekor. Ha vannak olyan további vagy különböző könyvtárak, amelyeket szinkronizálni szeretne a fejlesztői területtel, módosíthatja ezt a tulajdonságot.

A *konfigurációk. Development. Container. iteráció. buildCommands* tulajdonság azt adja meg, hogyan kell felépíteni az alkalmazást egy fejlesztési forgatókönyvben. A *konfigurációk. Development. Container. Command* tulajdonság biztosítja az alkalmazás fejlesztési forgatókönyvben való futtatásához szükséges parancsot. Előfordulhat, hogy frissítenie kell ezeket a tulajdonságokat, ha további Build vagy Runtime jelzőket vagy paramétereket szeretne használni a fejlesztés során.

A *konfigurációk. Build. Container. iteráció. processesToKill* felsorolja az alkalmazás leállításához szükséges folyamatokat. Érdemes lehet frissíteni ezt a tulajdonságot, ha módosítani szeretné az alkalmazás újraindítási viselkedését a fejlesztés során. Ha például frissítette a *konfigurációkat. fejlessze a. Container. iteráció. buildCommands* vagy a configurations. Build. *Container. Command* tulajdonságokat az alkalmazás létrehozásának vagy indításának megváltoztatásához, előfordulhat, hogy módosítania kell a leállított folyamatokat.

A kód a `azds prep` paranccsal történő előkészítésekor lehetősége van a `--public` jelző hozzáadására. A `--public` jelző hozzáadása nyilvánosan elérhető URL-címet hoz létre az alkalmazáshoz. Ha kihagyja ezt a jelzőt, az alkalmazás csak a fürtön belül vagy a localhost-alagút használatával érhető el. A `azds prep` parancs futtatása után módosíthatja ezt a beállítást a *bejövő. enabled* tulajdonság módosításával `charts/APPNAME/values.yaml`ban:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>A kód hibakeresése

A Java, a .NET és a Node. js alkalmazások esetében a Visual Studio Code vagy a Visual Studio használatával közvetlenül a fejlesztői térben futtathatja az alkalmazást. A Visual Studio Code és a Visual Studio eszközöket biztosít a fejlesztői területhez való kapcsolódáshoz, az alkalmazás elindításához és egy hibakereső csatolásához. `azds prep`futtatása után megnyithatja a projektet a Visual Studio Code vagy a Visual Studio alkalmazásban. A Visual Studio Code vagy a Visual Studio létrehozza a saját konfigurációs fájljait a csatlakozáshoz, amely elkülönül a futó `azds prep`. A Visual Studio Code-ból vagy a Visual studióból töréspontokat állíthat be, és elindíthatja az alkalmazást a fejlesztői területére.

![A kód hibakeresése](media/get-started-node/debug-configuration-nodejs2.png)

Ha a Visual Studio Code vagy a Visual Studio használatával indítja el az alkalmazást a hibakereséshez, akkor a `azds up`futtatásával megegyező módon kezeli az indítást és a csatlakozást a fejlesztői területhez. A Visual Studio Code-ban és a Visual Studióban található ügyféloldali eszközök további paramétereket is biztosítanak a hibakereséshez. A paraméter tartalmazza a hibakereső rendszerképének nevét, a hibakereső lemezképében található hibakereső helyét, valamint az alkalmazás tárolójában lévő célhelyet a hibakereső mappa csatlakoztatásához.

A hibakereső rendszerképét a rendszer automatikusan meghatározza az ügyféloldali eszközkészlet alapján. A Docker és a Helm diagram létrehozásakor használt módszerhez hasonló módszert használ `azds prep`futtatásakor. Miután a hibakereső csatlakoztatva lett az alkalmazás rendszerképéhez, `azds exec`használatával fut.

## <a name="sharing-a-dev-space"></a>Fejlesztői terület megosztása

Ha csapattal dolgozik, [megoszthat egy fejlesztői területet egy teljes csapaton belül](how-to/share-dev-spaces.md) , és származtatott fejlesztői szóközöket hozhat létre. A fejlesztői területet bárki használhatja, aki közreműködői hozzáféréssel rendelkezik a fejlesztői terület erőforráscsoporthoz.

Létrehozhat egy másik fejlesztői területből származtatott új fejlesztői helyet is. Származtatott fejlesztői terület létrehozásakor a rendszer hozzáadja a *azds.IO/Parent-Space=PARENT-Space-Name* címkét a származtatott fejlesztői terület névteréhez. Emellett a szülő fejlesztői területről származó összes alkalmazás meg van osztva a származtatott fejlesztői területtel. Ha egy alkalmazás frissített verzióját telepíti a származtatott fejlesztői területre, akkor csak a származtatott fejlesztői területen fog megjelenni, és a szülő fejlesztői terület érintetlen marad. A származtatott fejlesztői szóközök és a *nagyszülő* szóközök közül legfeljebb három szint adható meg.

A származtatott fejlesztői terület is intelligens módon irányítja a kérelmeket a saját alkalmazásai és a szülőtől megosztott alkalmazások között. Az Útválasztás úgy működik, hogy megpróbál átirányítani egy alkalmazást a származtatott fejlesztői területen, és visszakerül a megosztott alkalmazásra a szülő fejlesztői területről. Ha az alkalmazás nincs a szülő térben, az Útválasztás visszakerül a szülő terület megosztott alkalmazására.

Példa:
* A fejlesztői terület *alapértelmezett értéke* az Applications *Servicea* és a *serviceB* .
* A dev Space *Azureus* az *alapértelmezett értékből*származik.
* A *servicea* frissített verziója van üzembe helyezve az *azureuser*-ben.

Az *azureuser*használatakor a *servicea szolgáltatásnak* küldött összes kérelem a frissített verzióra lesz irányítva az *azureuser*-ben. A *serviceB* iránti kérés először a *serviceB* *azureuser* -verziójára lesz irányítva. Mivel nem létezik, a rendszer a *serviceB* *alapértelmezett* verziójára irányítja át. Ha a *servicea szolgáltatáshoz* tartozó *azureuser* verziója el lett távolítva, a *servicea* szolgáltatáshoz intézett összes kérelem vissza fog térni a *servicea* *alapértelmezett* verziójának használatára.

## <a name="next-steps"></a>Következő lépések

Az Azure dev Spaces használatának megkezdéséhez tekintse meg az alábbi rövid útmutatókat:

* [Java a parancssori felület és a Visual Studio Code használatával](quickstart-java.md)
* [.NET Core parancssori felület és Visual Studio Code használatával](quickstart-netcore.md)
* [.NET Core a Visual Studióval](quickstart-netcore-visualstudio.md)
* [Node. js a CLI és a Visual Studio Code használatával](quickstart-nodejs.md)

A csapatmunka fejlesztésének megkezdéséhez tekintse meg a következő útmutató cikkeket:

* [Team Development – Java a CLI-vel és a Visual Studio Code-ban](team-development-java.md)
* [Team Development – .NET Core parancssori felülettel és Visual Studio Code használatával](team-development-netcore.md)
* [Team Development – .NET Core a Visual Studióval](team-development-netcore-visualstudio.md)
* [Team Development – Node. js parancssori felülettel és Visual Studio Code-val](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
