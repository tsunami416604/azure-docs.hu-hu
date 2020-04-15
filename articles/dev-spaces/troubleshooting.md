---
title: Hibaelhárítás
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Megtudhatja, hogy miként háríthatja el és oldhatja meg a gyakori problémákat az Azure Dev Spaces engedélyezése és használata során
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: 9fcf14bf42fc843a126fea269038087ee7fb0c6c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382048"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Az Azure Dev Spaces hibaelhárítása

Ez az útmutató az Azure Dev Spaces használata során felmerülő gyakori problémákkal kapcsolatos információkat tartalmazza.

Ha problémája van az Azure Dev Spaces használatakor, hozzon létre egy [problémát az Azure Dev Spaces GitHub-tárházban.](https://github.com/Azure/dev-spaces/issues)

## <a name="before-you-begin"></a>Előkészületek

A problémák hatékonyabb elhárításához segíthet részletesebb naplók at létrehozni ellenőrzésre.

A Visual Studio bővítmény `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` esetén állítsa a környezeti változót 1-re. Mindenképpen indítsa újra a Visual Studio alkalmazást, hogy a környezeti változó érvénybe lépjen. Ha engedélyezve van, részletes `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` naplók at írt a könyvtárba.

A CLI-ben a parancs végrehajtása során további `--verbose` információkat adhat ki a kapcsoló használatával. A alkalmazásban is böngészhet `%TEMP%\Azure Dev Spaces`részletesebb naplók között. Mac en a *TEMP* könyvtár terminálablakból való futtatással `echo $TMPDIR` érhető el. Linux rendszerű számítógépen *TEMP* a TEMP `/tmp`könyvtár általában . Ellenőrizze továbbá, hogy engedélyezve van-e a naplózás az [Azure CLI konfigurációs fájljában.](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)

Az Azure Dev Spaces is működik a legjobban, ha egyetlen példány vagy pod hibakeresés. A `azds.yaml` fájl tartalmaz egy beállítást, *replicaCount*, amely azt jelzi, hogy a Kubernetes a szolgáltatáshoz futtatott podok számát. Ha módosítja a *replicaCount* konfigurálása az alkalmazás több podok egy adott szolgáltatás hoz, a hibakereső csatolja az első pod, ha betűrendben felsorolt. A hibakereső egy másik podhoz csatlakozik, amikor az eredeti pod újrahasznosítja, ami valószínűleg váratlan viselkedést eredményez.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Gyakori problémák az Azure Dev Spaces engedélyezésekor

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Hiba: "Nem sikerült létrehozni az Azure dev spaces vezérlőjét"

Ez a hibaüzenet akkor jelenhet meg, ha valami elromlik a vezérlő létrehozásával. Ha átmeneti hiba történt, törölje, majd hozza létre újra a vezérlőt a javításhoz.

Megpróbálhatja a vezérlő törlését is:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Az Azure Dev Spaces CLI segítségével törölheti a vezérlőt. Nem lehet vezérlőt törölni a Visual Studio alkalmazásból. Az Azure Dev Spaces CLI-t nem telepítheti az Azure Cloud Shellben, így nem törölhet vezérlőket az Azure Cloud Shellből.

Ha nincs telepítve az Azure Dev Spaces CLI, először telepítheti a következő paranccsal, majd törölheti a vezérlőt:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A vezérlő újbóli létrehozása a CLI-ből vagy a Visual Studio-ból végezhető el. Tekintse meg a [csapatfejlesztést](quickstart-team-development.md) vagy a [fejlesztést a .NET Core rövid útmutatókkal.](quickstart-netcore-visualstudio.md)

### <a name="controller-create-failing-because-of-controller-name-length"></a>A vezérlő létrehozása sikertelen a vezérlő nevének hossza miatt

Az Azure Dev Spaces-vezérlő neve nem lehet hosszabb 31 karakternél. Ha a vezérlő neve meghaladja a 31 karaktert, amikor engedélyezi a fejlesztői szóközöket egy AKS-fürtön, vagy létrehoz egy vezérlőt, hibaüzenetet fog kapni. Például:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

A probléma megoldásához hozzon létre egy másik nevű vezérlőt. Például:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>A fejlesztői tárolóhelyek sikertelensítése, ha AKS-fürthöz hozzáadja a Windows csomópontkészleteket

Jelenleg az Azure Dev Spaces célja, hogy csak Linux-podokon és csomópontokon fusson. Ha egy Windows-csomópontkészlettel rendelkező AKS-fürttel rendelkezik, biztosítania kell, hogy az Azure Dev Spaces podok csak Linux-csomópontokon legyenek ütemezve. Ha egy Azure Dev Spaces pod a tervek szerint egy Windows-csomóponton fut, hogy a pod nem indul el, és a fejlesztői terek engedélyezése sikertelen lesz.

A probléma megoldásához [adjon hozzá egy romlottakat](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AKS-fürthöz, hogy a Linux-podok ne fussanak Windows-csomóponton.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Hiba: "Nem található romlatlan Linux-csomópont a fürt Ready állapotában. A podok "azds" névtérben történő üzembe helyezéséhez ready állapotban legalább egy romlatlan Linux-csomópontnak kell lennie."

Az Azure dev spaces nem tudott létrehozni egy vezérlőt az AKS-fürtön, mert nem talált egy romlatlan csomópontot *kész* állapotban a podok ütemezéséhez. Az Azure Dev Spaces legalább egy Linux-csomópontot igényel *egy Ready* állapotban, amely lehetővé teszi a podok ütemezését a tűrések megadása nélkül.

A probléma megoldásához [frissítse a romlott konfigurációt](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AKS-fürtön annak érdekében, hogy legalább egy Linux-csomópont lehetővé tegye a podok ütemezését a tűrések megadása nélkül. Győződjön meg arról is, hogy legalább egy Linux-csomópont, amely lehetővé teszi a podok ütemezését a tűrések megadása nélkül, *készen áll.* Ha a csomópont hosszú időt vesz igénybe, hogy elérje a *Ready* állapotot, megpróbálhatja újraindítani a csomópontot.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Hiba az "Azure Dev Spaces CLI nincs megfelelően telepítve" hiba az aks use-dev-spaces futtatásakor

Az Azure Dev Spaces CLI frissítése megváltoztatta a telepítési útvonalát. Ha az Azure CLI 2.0.63-nál korábbi verzióját használja, ez a hiba jelenhet meg. Az Azure CLI verziójának megjelenítéséhez használja a használatát. `az --version`

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Annak ellenére, `az aks use-dev-spaces` hogy a hibaüzenet az Azure CLI 2.0.63 előtti verziójával való futtatáskor hibaüzenetet tartalmaz, a telepítés sikeres. Továbbra is használhatja `azds` gond nélkül.

A probléma megoldásához frissítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) telepítését 2.0.63-as vagy újabb verzióra. A frissítés megoldja a futás során `az aks use-dev-spaces`kapott hibaüzenetet. Azt is megteheti, hogy továbbra is használja az Azure CLI és az Azure Dev Spaces CLI aktuális verzióját.

### <a name="error-unable-to-reach-kube-apiserver"></a>Hiba: "Nem érhető el a kube-apiserver"

Ez a hiba akkor jelenhet meg, ha az Azure Dev Spaces nem tud csatlakozni az AKS-fürt API-kiszolgálójához.

Ha az AKS-fürt API-kiszolgálójához való hozzáférés zárolva van, vagy ha az [API-kiszolgáló által engedélyezett IP-címtartományok](../aks/api-server-authorized-ip-ranges.md) engedélyezve vannak az AKS-fürthöz, [akkor a fürtöt](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) is létre kell hoznia vagy [frissítenie](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) kell, hogy [a régión alapuló további tartományokat engedélyezhessen.](https://github.com/Azure/dev-spaces/tree/master/public-ips)

Győződjön meg arról, hogy az API-kiszolgáló elérhető a kubectl parancsok futtatásával. Ha az API-kiszolgáló nem érhető el, forduljon az AKS ügyfélszolgálatához, és próbálkozzon újra, amikor az API-kiszolgáló működik.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Gyakori problémák a projekt Azure Dev Spaces-re való előkészítésekor

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Figyelmeztetés: "A Dockerfile nem érhető el a nem támogatott nyelv miatt"
Az Azure Dev Spaces natív támogatást nyújt a C# és node.js. Amikor egy `azds prep` ilyen nyelven írt kóddal rendelkező könyvtárban fut, az Azure Dev Spaces automatikusan létrehoz egy megfelelő Docker-fájlt.

Továbbra is használhatja az Azure Dev Spaces más nyelveken írt kóddal, `azds up` de manuálisan kell létrehoznia a Docker-fájlt, mielőtt először futna.

Ha az alkalmazás olyan nyelven íródott, amelyet az Azure Dev Spaces natív módon nem támogat, meg kell adnia egy megfelelő Dockerfile-t a kódot futtató tárolórendszerkép létrehozásához. A Docker a [Docker-fájlok írásával kapcsolatos gyakorlati tanácsok listáját](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) és egy [Docker-fájlhivatkozást](https://docs.docker.com/engine/reference/builder/) tartalmaz, amely segítséget nyújt az igényeinek megfelelő Docker-fájl írásában.

Miután rendelkezik egy megfelelő Docker-fájllal, futtathatja `azds up` az alkalmazást az Azure Dev Spaces-ben.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Gyakori problémák a szolgáltatások indításakor és leállításakor az Azure Dev Spaces szolgáltatással

### <a name="error-config-file-not-found"></a>Hiba: "A konfigurációs fájl nem található:"

A `azds up`futás során ez a hibaüzenet jelenhet meg. `azds up` Mindkettő, `azds prep` és a fejlesztői területen futtatni kívánt projekt gyökérkönyvtárából kell futtatni.

A probléma megoldása:
1. Módosítsa az aktuális könyvtárat a szolgáltatáskódot tartalmazó gyökérmappára. 
1. Ha nincs _azds.yaml_ fájl a kódmappában, `azds prep` futtassa a Docker, a Kubernetes és az Azure Dev Spaces-eszközök létrehozásához.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Időhívás a "Várakozás a tárolókép felépítésére..." lépés AKS virtuális csomópontokkal

Ez az időkorlát akkor fordul elő, amikor a fejlesztői tárolóhelyek használatával olyan szolgáltatást próbál futtatni, amely [egy AKS virtuális csomóponton](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)való futtatásra van konfigurálva. A fejlesztői tárolóhelyek jelenleg nem támogatják a virtuális csomópontokon lévő szolgáltatások kiépítését vagy hibakeresését.

Ha a `azds up` kapcsolóval fut, vagy engedélyezi a `--verbose` részletes naplózást a Visual Studióban, további részletek jelennek meg:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

A fenti parancs azt mutatja, hogy a szolgáltatás pod volt rendelve *a virtuális-csomópont-aci-linux*, amely egy virtuális csomópont.

A probléma megoldásához frissítse a Helm diagramot a szolgáltatásszámára, hogy eltávolítson minden olyan *csomópontválasztó* vagy *tűrésjelet,* amely lehetővé teszi a szolgáltatás virtuális csomóponton való futtatását. Ezek az értékek általában a diagram `values.yaml` fájljában vannak definiálva.

Továbbra is használhatja az AKS-fürt, amely a virtuális csomópontok funkció engedélyezve van, ha a fejlesztői terek en keresztül létre kívánt szolgáltatás vagy hibakeresés virtuálisgép-csomóponton fut. A szolgáltatás virtuálisgép-csomóponton fejlesztői szóközökkel való futtatása az alapértelmezett konfiguráció.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Hiba "Nem található a kész talajművelő pod" indításakor dev spaces

Ez a hiba akkor fordul elő, ha a Helm-ügyfél már nem tud beszélni a tiller pod a fürtben futó.

A probléma megoldásához indítsa újra a fürt ügynökcsomópontjait.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Hiba :"release azds-\>-\<\>-\<\<identifier\> spacename servicename failed: services\<' servicename ' 'servicename'\>already exists' or "Pull access denied for \<servicename\>, repository does not exist or may require 'docker login'"

Ezek a hibák akkor fordulhatnak elő, `helm install`ha `helm upgrade`a `helm delete`közvetlen Helm parancsokat `azds up` (például a , vagy ) a fejlesztői szóközök parancsokkal (például és `azds down`) ugyanazon a fejlesztői területen belül keveri. Ezek azért fordulnak elő, mert a Fejlesztői zel-tároló saját Tiller-példány, amely ütközik a saját Tiller példány fut ugyanabban a fejlesztői térben.

A Helm parancsok és a fejlesztői terek parancsok használata ugyanazon AKS-fürt ellen, de minden fejlesztői tér-engedélyezésű névtér kell használni az egyik vagy a másik.

Tegyük fel például, hogy egy Helm paranccsal futtatja a teljes alkalmazást egy szülő fejlesztési területen. Gyermekfejlesztői tereket hozhat létre az adott szülőből, a fejlesztői tárolók használatával futtathatja az egyes szolgáltatásokat a gyermekfejlesztői tereken belül, és együtt tesztelheti a szolgáltatásokat. Ha készen áll a módosítások beadására, a Helm paranccsal telepítse a frissített kódot a szülő fejlesztési területre. Ne használja `azds up` a frissített szolgáltatás futtatásához a szülő fejlesztési területen, mert ütközik a helm használatával eredetileg futtatott szolgáltatással.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>A meglévő Docker-fájl nem használható tároló létrehozásához

Az Azure dev spaces konfigurálható, hogy pont egy adott _Docker-fájl_ a projektben. Ha úgy tűnik, hogy az Azure Dev Spaces nem használja a tárolók létrehozásához várt _Docker-fájlt,_ előfordulhat, hogy explicit módon meg kell mondania az Azure Dev Spaces-nek, hogy melyik Dockerfile melyik Dockerfile-t használja. 

A probléma megoldásához nyissa meg az Azure Dev Spaces által a projektben létrehozott _azds.yaml_ fájlt. Konfigurációk *frissítése: fejlesztés: build: dockerfile* a használni kívánt Docker-fájlra mutat. Például:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Hiba "jogosulatlan: hitelesítés szükséges" amikor docker-lemezképet próbál használni egy privát beállításjegyzékből

Egy hitelesítést igénylő magánbeállítás-nyilvántartásból származó Docker-lemezképet használ.

A probléma megoldásához engedélyezheti, hogy a fejlesztői tárolók hitelesítsék és lekérik a képeket ebből a privát beállításjegyzékből [az imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)használatával. Az imagePullSecrets használatához [hozzon létre egy Kubernetes-titkot](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) a névtérben, ahol a lemezképet használja. Ezután adja meg a titkot imagePullSecret néven a rendszerben. `azds.yaml`

Az alábbiakban egy példa egy meghatározó `azds.yaml`imagePullSecrets a.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Az imagePullSecrets `azds.yaml` beállítása felülírja a `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Hiba: "A szolgáltatás nem indítható el."

Ez a hiba akkor jelenhet meg, ha a szolgáltatáskód nem indul el. Az ok gyakran a felhasználói kódban van. További diagnosztikai információk, engedélyezze a részletesebb naplózás a szolgáltatás indításakor.

A parancssorból használja `--verbose` a részletesebb naplózást. A használatával `--output`kimeneti formátumot is megadhat. Például:

```cmd
azds up --verbose --output json
```

A Visual Studio-ban:

1. Nyissa **meg az Eszközök > beállításokat,** és a Projektek és **megoldások**csoportban válassza a Build and **Run (Létrehozás és futtatás) lehetőséget.**
2. Módosítsa az **MSBuild projektbuild-kimeneti részletességének** beállításait **Részletes** vagy **Diagnosztikai**beállításra.

    ![Képernyőkép az Eszközök beállításai párbeszédpanelről](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Szolgáltatás újrafuttatása a vezérlő újbóli létrehozása után

Szolgáltatás nem *indítható el* hiba, amikor megpróbálja újrafuttatni a szolgáltatást, miután eltávolította, majd újra létrehozta a fürthöz társított Azure Dev Spaces-vezérlőt. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ez a hiba azért fordul elő, mert a fejlesztői tárolóhelyek vezérlő eltávolítása nem távolítja el az adott vezérlő által korábban telepített szolgáltatásokat. A vezérlő újbóli létrehozása, majd a szolgáltatások futtatásának megkísérlése az új vezérlőhasználatával sikertelen, mert a régi szolgáltatások még mindig érvényben vannak.

A probléma megoldásához `kubectl delete` a paranccsal manuálisan távolítsa el a régi szolgáltatásokat a fürtből, majd futtassa újra a fejlesztői szóközöket az új szolgáltatások telepítéséhez.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Hiba: "A szolgáltatás nem indítható el." többfokozatú Docker-fájlok használata esetén

Szolgáltatás nem *indítható el* hiba többlépcsős Docker-fájl használataesetén. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ez a hiba azért fordul elő, mert az Azure Dev Spaces jelenleg nem támogatja a többlépcsős buildek. A többlépcsős buildek elkerülése érdekében írja át a Docker-fájlt.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>A hálózati forgalom nem lesz továbbítva az AKS-fürthöz a fejlesztői gép csatlakoztatásakor

Ha az Azure Dev Spaces használatával [csatlakoztatja az AKS-fürta fejlesztői géphez,](how-to/connect.md)előfordulhat, hogy a hálózati forgalom nem továbbító dörgésa a fejlesztői gép és az AKS-fürt között.

Amikor a fejlesztői gép az AKS-fürthöz csatlakozik, az Azure Dev Spaces továbbítja a hálózati forgalmat az AKS-fürt és a fejlesztői gép között a fejlesztői gép fájljának `hosts` módosításával. Az Azure Dev Spaces `hosts` létrehoz egy bejegyzést a kubernetes szolgáltatás címét, amelyet állomásnévként helyettesít. Ez a bejegyzés porttovábbítással irányítja a fejlesztői gép és az AKS-fürt közötti hálózati forgalmat. Ha a fejlesztői gépen egy szolgáltatás ütközik a kubernetes szolgáltatás által helyettesített portjával, az Azure Dev Spaces nem továbbíthatja a Kubernetes szolgáltatás hálózati forgalmát. A Windows *BranchCache* szolgáltatás például általában *a 0.0.0:80-hoz*van kötve, ami ütközést okoz a 80-as portnál az összes helyi IP-n.

A probléma megoldásához le kell állítania minden olyan szolgáltatást vagy folyamatot, amely ütközik a cserélni kívánt Kubernetes szolgáltatás portjával. Az eszközök, például *a netstat*segítségével megvizsgálhatja, hogy a fejlesztői gépen milyen szolgáltatások vagy folyamatok ütköznek.

Például a *Windows BranchCache* szolgáltatás leállításához és letiltásához:
* Futtassa `services.msc` a parancssorból.
* Kattintson a jobb gombbal a *BranchCache elemre,* és válassza *a Tulajdonságok parancsot.*
* Kattintson *a Leállítás gombra.*
* Szükség esetén letilthatja azt, ha az *Indítás típusát* Letiltott értékre *állítja.*
* Kattintson az *OK* gombra.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Hiba "nincs AzureAssignedIdentity található pod:azds/azds-webhook-deployment-\<id\> hozzárendelt állapotban"

Ha egy szolgáltatást azure dev spaces egy AKS-fürt [felügyelt identitás](../aks/use-managed-identity.md) és [pod felügyelt identitások](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) telepítve, a folyamat lefagyhat a *diagram telepítési* lépés után. Ha megvizsgálja az *azds-injektor-webhook* az *azds* névtér, akkor ez a hiba.

Az Azure Dev Spaces szolgáltatások a fürtön fut a fürt felügyelt identitását, hogy beszéljen az Azure Dev Spaces háttérszolgáltatások a fürtön kívül. A pod felügyelt identitásának telepítésekor a fürt csomópontjain hálózati szabályok vannak konfigurálva, hogy a felügyelt identitáshitelesítő adatok összes hívását átirányítsák [a fürtre telepített Node Managed Identity (NMI) DaemonSet daemonSet rendszerbe.](https://github.com/Azure/aad-pod-identity#node-managed-identity) Ez az NMI-daemonSet azonosítja a hívó podot, és biztosítja, hogy a pod megfelelően van címkézve a kért felügyelt identitás eléréséhez. Az Azure Dev Spaces nem észleli, ha egy fürt pod felügyelt identitás telepítve van, és nem tudja végrehajtani a szükséges konfigurációt, hogy az Azure Dev Spaces-szolgáltatások hozzáférjenek a fürt felügyelt identitásához. Mivel az Azure Dev Spaces-szolgáltatások nincsenek konfigurálva a fürt felügyelt identitásának eléréséhez, az NMI DaemonSet nem teszi lehetővé számukra, hogy a felügyelt identitás Hozegy AAD-jogkivonatot, és nem kommunikálnak az Azure Dev Spaces háttérszolgáltatásokkal.

A probléma megoldásához alkalmazzon egy [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) az *azds-injektor-webhook* és frissítse podok az Azure Dev Spaces által a felügyelt identitás eléréséhez.

Hozzon létre egy *webhookException.yaml* nevű fájlt, és másolja a következő YAML-definíciót:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

A fenti fájl létrehoz egy *AzurePodIdentityException* objektumot az *azds-injector-webhook*számára. Az objektum központi `kubectl`telepítéséhez használja a következőket:

```cmd
kubectl apply -f webhookException.yaml
```

Az Azure Dev Spaces által a felügyelt identitás eléréséhez műszeres podok frissítéséhez frissítse `kubectl` a *névteret* az alábbi YAML-definícióban, és használja az egyes fejlesztési területekhez való alkalmazáshoz.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Másik lehetőségként *azureidentity* és *AzureIdentityBinding* objektumokat hozhat létre, és frissítheti az Azure Dev Spaces által irányított terekben futó számítási feladatok podcímkéit az AKS-fürt által létrehozott felügyelt identitás eléréséhez.

A felügyelt identitás részleteinek listázásához futtassa a következő parancsot az AKS-fürthöz:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

A fenti parancs a felügyelt identitás *ügyfélazonosítóját* és *erőforrásazonosítóját* adja ki. Például:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

*AzureIdentity-objektum* létrehozásához hozzon létre egy *fürtidentitás.yaml* nevű fájlt, és használja a következő YAML-definíciót, amely az előző parancs felügyelt identitásának részleteivel frissült:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

*AzureIdentityBinding* objektum létrehozásához hozzon létre egy *fürtidentitybinding.yaml* nevű fájlt, és használja a következő YAML-definíciót:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

*Az AzureIdentity* és az *AzureIdentityBinding* objektumok üzembe helyezéséhez használja a következőket: `kubectl`

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Az *AzureIdentity* és az *AzureIdentityBinding-objektumok* üzembe helyezése után az *aadpodidbinding: my-label-value* label használatával rendelkező munkaterhelés hozzáférhet a fürt felügyelt identitásához. Adja hozzá ezt a címkét, és telepítse újra az összes számítási feladatok bármely fejlesztői térben futó. Például:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Gyakori problémák a Visual Studio és a Visual Studio kódhasználatával az Azure Dev Spaces szolgáltatással

### <a name="error-required-tools-and-configurations-are-missing"></a>Hiba : "A szükséges eszközök és konfigurációk hiányoznak"

Ez a hiba a VS-kód indításakor fordulhat elő: "[Azure Dev Spaces] A "[projektnév] létrehozásához és hibakereséséhez szükséges eszközök és konfigurációk hiányoznak."
A hiba azt jelenti, hogy az azds.exe nem szerepel a PATH környezeti változóban, ahogy az a VS-kódban látható.

Próbálja meg a VS Code parancssorból elindítani, ahol a PATH környezeti változó megfelelően van beállítva.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>A "Projektnév létrehozásához és hibakereséséhez szükséges eszközök" hiba elavult."

Ez a hiba a Visual Studio-kód, ha az Azure Dev Spaces VS-kódbővítmény újabb verzióját használja, de az Azure Dev Spaces CLI régebbi verzióját.

Próbálja meg letölteni és telepíteni az Azure Dev Spaces CLI legújabb verzióját:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hiba: "Nem sikerült megtalálni a hibakereső bővítményt a type:coreclr-hez"

Ez a hiba a Visual Studio kódhiba-keresőjének futtatásakor jelenhet meg. Előfordulhat, hogy a fejlesztői gépen nincs telepítve a C# VS code bővítménye. A C# kiterjesztés tartalmazza a .NET Core (CoreCLR) hibakeresési támogatását.

A probléma megoldásához telepítse a [VS Code bővítményt a C# értékre.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Hiba "Configured debug type 'coreclr' is not supported"

Ez a hiba a Visual Studio kódhiba-keresőjének futtatásakor jelenhet meg. Előfordulhat, hogy a vs kód bővítmény az Azure Dev Spaces telepítve van a fejlesztői gépen.

A probléma megoldásához telepítse a [VS Code bővítményt az Azure Dev Spaces szolgáltatáshoz.](get-started-netcore.md)

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Hiba : "Érvénytelen " cwd' érték '/src'. A rendszer nem találja a megadott fájlt." vagy "launch: program "/src/[path to project binary]" nem létezik"

Ez a hiba a Visual Studio kódhiba-keresőjének futtatásakor jelenhet meg. Alapértelmezés szerint a VS-kód bővítmény a tárolón lévő projekt munkakönyvtáraként szolgál. `src` Ha frissítette a `Dockerfile` számítógépet, hogy másik munkakönyvtárat adjon meg, akkor ez a hiba jelenhet meg.

A probléma megoldásához `launch.json` frissítse a `.vscode` fájlt a projektmappa alkönyvtára alatt. Módosítsa `configurations->cwd` az utasítást úgy, hogy `WORKDIR` ugyanarra a könyvtárra mutasson, mint a projektben `Dockerfile`definiált könyvtár. Előfordulhat, hogy az `configurations->program` irányelvet is frissítenie kell.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba "A cső program "azds" váratlanul kilépett a kód 126."

Ez a hiba a Visual Studio kódhiba-keresőjének futtatásakor jelenhet meg.

A probléma megoldásához zárja be és nyissa meg újra a Visual Studio-kódot. Indítsa újra a hibakeresőt.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Hiba :"A belső óra nem sikerült: watch ENOSPC" hibakeresés kor egy Node.js alkalmazáshoz

Ez a hiba akkor fordul elő, ha a podot futtató csomópont a hibakeresővel csatolni kívánt Node.js alkalmazással túllépte az *fs.inotify.max_user_watches* értéket. Bizonyos esetekben előfordulhat, hogy [az *fs.inotify.max_user_watches* alapértelmezett értéke túl kicsi ahhoz, hogy a hibakeresőt közvetlenül a podhoz csatolja.](https://github.com/Azure/AKS/issues/772)

A probléma ideiglenes megoldása az *fs.inotify.max_user_watches* értékének növelése a fürt minden csomópontján, és a módosítások életbe léptetéséhez indítsa újra a csomópontot.

## <a name="other-common-issues"></a>Egyéb gyakori problémák

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Az "azds" hiba nem ismerhető fel belső vagy külső parancsként, működőképes programként vagy kötegfájlként

Ez a hiba `azds.exe` akkor fordulhat elő, ha nincs megfelelően telepítve vagy konfigurálva.

A probléma megoldása:

1. Ellenőrizze a helyet %ProgramFiles%/Microsoft SDKs\Azure\Azure `azds.exe`Dev Spaces CLI for. Ha ott van, adja hozzá ezt a helyet a PATH környezeti változóhoz.
2. Ha `azds.exe` nincs telepítve, futtassa a következő parancsot:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>"Microsoft.DevSpaces/register/action" engedélyezési hiba

Az Azure Dev Spaces kezeléséhez *tulajdonosi* vagy *közreműködői* hozzáférésre van szüksége az Azure-előfizetésben. Ha fejlesztői tárolókat próbál kezelni, és nem rendelkezik *tulajdonosi* vagy *közreműködői* hozzáféréssel a társított Azure-előfizetéshez, előfordulhat, hogy engedélyezési hiba jelenik meg. Például:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

A probléma megoldásához az Azure-előfizetés *tulajdonosi* vagy *közreműködői* hozzáféréssel rendelkező fiók használatával manuálisan regisztrálja a `Microsoft.DevSpaces` névteret:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nem indulnak el az új kapszulák

A Kubernetes inicializáló nem tudja alkalmazni a PodSpec új podok miatt RBAC engedély változások a *fürt-felügyeleti* szerepkör a fürtben. Előfordulhat, hogy az új pod is rendelkezik egy érvénytelen PodSpec, például a podhoz társított szolgáltatásfiók már nem létezik. Az inicializáló miatt *függőben lévő* állapotban lévő `kubectl get pods` podok megtekintéséhez használja a következő parancsot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ez a probléma hatással lehet a podok a fürt *összes névtere,* beleértve a névterek, ahol az Azure Dev Spaces nincs engedélyezve.

A probléma megoldásához [frissítse a fejlesztői tereket CLI-t a legújabb verzióra,](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) majd törölje az *azds InitializerConfiguration-t* az Azure Dev Spaces vezérlőből:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Miután eltávolította az *azds InitializerConfiguration* az `kubectl delete` Azure Dev Spaces vezérlő, segítségével távolítsa el a podok *egy függőben lévő* állapotban. Miután az összes függőben lévő podok el lettek távolítva, telepítse újra a podok.

Ha az új podok továbbra is *függőben* lévő `kubectl delete` állapotban vannak az átcsoportosítás után, távolítsa el a *függőben lévő* állapotban lévő podokat. Miután az összes függőben lévő podot eltávolította, törölje a vezérlőt a fürtből, és telepítse újra:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

A vezérlő újratelepítése után telepítse újra a podokat.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Helytelen RBAC-engedélyek a fejlesztői tárolóhelyek vezérlőjének és API-knak a hívására

Az Azure Dev Spaces-vezérlőhöz hozzáférő felhasználónak hozzáféréssel kell rendelkeznie az AKS-fürt *adminkubeconfig* olvasásához. Ez az engedély például a [beépített Azure Kubernetes-szolgáltatásfürt felügyeleti szerepkörében](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)érhető el. Az Azure Dev Spaces-vezérlőeléréséhez hozzáférő felhasználónak is rendelkeznie kell a *közreműködői* vagy *tulajdonosi* RBAC szerepkörrel a vezérlőhöz. Az AKS-fürthöz tartozó felhasználói engedélyek frissítésével kapcsolatos további részletek [itt](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)találhatók.

A felhasználó RBAC szerepkörének frissítése a vezérlőhöz:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Keresse meg a vezérlőt tartalmazó erőforráscsoportot, amely általában megegyezik az AKS-fürttel.
1. Engedélyezze a *Rejtett típusok megjelenítése* jelölőnégyzetet.
1. Kattintson a vezérlőre.
1. Nyissa meg a *hozzáférés-vezérlési (IAM)* ablaktáblát.
1. Kattintson a *Szerepkör-hozzárendelések* fülre.
1. Kattintson a *Hozzáadás,* majd *a Szerepkör-hozzárendelés hozzáadása gombra.*
    * A *Szerepkör csoportban*válassza a *Közreműködő* vagy *a Tulajdonos*lehetőséget.
    * A *Hozzáférés hozzárendelése a területen*válassza az Azure *AD felhasználó, csoport vagy egyszerű szolgáltatás*lehetőséget.
    * A *Select területen*keresse meg az engedélyeket adni kívánt felhasználót.
1. Kattintson a *Save* (Mentés) gombra.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>A DNS-névfeloldás sikertelen a fejlesztői tárolóhelyek szolgáltatáshoz társított nyilvános URL-cím esetén

A szolgáltatás nyilvános URL-végpontját a `--enable-ingress` `azds prep` parancsra való váltás megadásával vagy `Publicly Accessible` a Visual Studio jelölőnégyzetének bejelölésével állíthatja be. A nyilvános DNS-név automatikusan regisztrálva lesz, amikor a szolgáltatás fejlesztői tárolóhelyeken futtatja. Ha ez a DNS-név nincs regisztrálva, akkor azt látja, hogy *a lap nem jeleníthető meg,* vagy a *Webhely nem érhető el* hiba a webböngészőben, amikor a nyilvános URL-hez csatlakozik.

A probléma megoldása:

* Ellenőrizze a fejlesztői terek szolgáltatásaihoz társított összes URL állapotát:

  ```console
  azds list-uris
  ```

* Ha egy URL *függőben lévő* állapotban van, a fejlesztői tárolóhelyek továbbra is a DNS-regisztráció befejezésére várnak. Néha néhány percet vesz igénybe a regisztráció befejezéséhez. A Fejlesztői tárolóhelyek is megnyit egy localhost-alagutat minden szolgáltatáshoz, amelyet a DNS-regisztrációra való várakozás közben használhat.
* Ha egy URL-cím 5 percnél hosszabb *ideig függőben* marad, az a nyilvános végpontot létrehozó külső DNS-poddal vagy a nyilvános végpontot beszerző nginx-vezérlőpodtal kapcsolatos problémára utalhat. A következő parancsokkal törölheti ezeket a podokat, és engedélyezheti, hogy az AKS automatikusan újra létrehozza őket:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba "upstream connect error or disconnect/reset before headers"

Ez a hibaüzenet akkor jelenhet meg, amikor a szolgáltatás elérésére próbál. Ha például a szolgáltatás URL-címére lép egy böngészőben. Ez a hiba azt jelenti, hogy a tárolóport nem érhető el. Ennek okai a következők lehetnek:

* A tároló még mindig folyamatban van a létrehozás és üzembe helyezés. Ez a probléma akkor `azds up` merülhet fel, ha futtatja vagy elindítja a hibakeresőt, majd megpróbálja elérni a tárolót, mielőtt az sikeresen üzembe helyezné.
* A portkonfiguráció nem konzisztens a _Docker-fájlban,_ a Helm-diagramban és a portot megnyitó kiszolgálókódokban.

A probléma megoldása:

1. Ha a tároló épül/üzembe helyezve van, 2-3 másodpercet várhat, és megpróbálhatja újra elérni a szolgáltatást. 
1. Ellenőrizze a portkonfigurációt a következő eszközökben:
    * **[Helm diagram](https://docs.helm.sh):** A `deployment.containerPort` `azds prep` `service.port` parancs által állványozott és értékben megadott.
    * Az alkalmazáskódban megnyitott portok, például a Node.js-ben:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>A "MyLibrary" típus- vagy névtérnév nem található.

A használt könyvtárprojekt nem található. A fejlesztői tárolók, a build környezet alapértelmezés szerint a projekt/szolgáltatás szintjén.  

A probléma megoldása:

1. Módosítsa `azds.yaml` a fájlt úgy, hogy a buildkörnyezet a megoldás szintjére állítsa.
2. Módosítsa `Dockerfile` a `Dockerfile.develop` és a fájlokat, hogy `.csproj`a projektfájlokra hivatkozzon, például helyesen az új buildkörnyezethez képest.
3. Adjon `.dockerignore` hozzá egy fájlt `.sln` ugyanabban a könyvtárban, mint a fájlt.
4. Szükség `.dockerignore` szerint frissítse a további bejegyzésekkel.

Itt talál egy [példát.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>A vízszintes pod automatikus skálázása nem működik a fejlesztői térben

Amikor egy szolgáltatás futtatásához egy fejlesztői térben, hogy a szolgáltatás pod [injektált további tárolók műszerek](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) és a pod minden tárolók kell rendelkeznie erőforrás-korlátok és a kérelmek horizontális pod automatikus skálázás.

A probléma megoldásához alkalmazzon erőforrás-kérelmet, és korlátozza a beadott fejlesztői tárolók. Erőforrás-kérelmek és korlátok a injektált tároló (devspaces-proxy) a jegyzet hozzáadása a `azds.io/proxy-resources` pod spec. Az értéket egy JSON-objektumra kell állítani, amely a proxy tárolóspecifikációjának erőforrásszakaszát képviseli.

Az alábbiakban egy példa egy proxy-erőforrások jegyzet, amely a pod spec kell alkalmazni.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Az Azure Dev Spaces engedélyezése meglévő névtérben futó podokkal

Előfordulhat, hogy egy meglévő AKS-fürt és névtér futó podok, ahol engedélyezni szeretné az Azure Dev Spaces.

Az Azure Dev Spaces engedélyezéséhez egy AKS-fürt `use-dev-spaces` meglévő `kubectl` névterén futtassa és használja az adott névtérben lévő összes pod újraindítását.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Miután a podok újraindult, megkezdheti a meglévő névtér használatával az Azure Dev Spaces használatával.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Az Azure dev spaces engedélyezése az AKS-fürtön korlátozott kimenő forgalommal a fürtcsomópontok számára

Ha engedélyezni szeretné az Azure dev spaces-t egy Olyan AKS-fürtön, amelynél a fürtcsomópontokból érkező kimenő forgalom korlátozott, a következő teljes tartománynokat kell engedélyeznie:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Linux alpine és más Azure Dev Spaces-képek lekérése |
| gcr.io | HTTP:443 | Helm/kormányrúd képek húzása|
| storage.googleapis.com | HTTP:443 | Helm/kormányrúd képek húzása|
| azds-<guid>. <location>azds.io | HTTPS:443 | Kommunikáció az Azure Dev Spaces háttérszolgáltatásokkal a vezérlőhöz. A pontos teljes tartománykapcsolati szint a "userprofile%\.azds\settings.json" "dataplaneFqdn" területen található.|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Hiba: "Nem található \<\> a \<fürtfürt\>az előfizetési azonosítóban"

Ez a hiba akkor jelenhet meg, ha a kubeconfig fájl egy másik fürtöt vagy előfizetést céloz meg, mint amelyet az Azure Dev Spaces ügyféloldali eszközhasználatával próbál használni. Az Azure Dev Spaces ügyféloldali eszközhasználat replikálja a *kubectl*viselkedését, amely [egy vagy több kubeconfig fájlt](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) használ a fürt kiválasztásához és a fürttel való kommunikációhoz.

A probléma megoldása:

* Az `az aks use-dev-spaces -g <resource group name> -n <cluster name>` aktuális környezet frissítéséhez használható. Ez a parancs is engedélyezi az Azure Dev Spaces az AKS-fürtön, ha még nincs engedélyezve. Azt is megteheti, `kubectl config use-context <cluster name>` hogy frissíti az aktuális környezetet.
* A `az account show` célzás jelenlegi Azure-előfizetésének megjelenítésére és annak ellenőrzésére használható, hogy helyes-e. A segítségével módosíthatja a célzást megcélzó előfizetést. `az account set`

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Hiba a fejlesztői tárolók használatakor az AKS-tanúsítványok elforgatása után

Az [AKS-fürt tanúsítványainak elforgatása](../aks/certificate-rotation.md)után bizonyos `azds space list` műveletek, például a műveletek `azds up` sikertelenek lesznek. Az Azure Dev Spaces-vezérlő tanúsítványait is frissítenie kell, miután elforgatta a tanúsítványokat a fürtön.

A probléma megoldásához győződjön meg arról, `az aks get-credentials` hogy a `azds controller refresh-credentials` *kubeconfig* rendelkezik a frissített tanúsítványokkal, majd futtassa a parancsot. Például:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
