---
title: A CI/CD és az Azure Dev Spaces használata
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Megtudhatja, hogyan állíthatja be a folyamatos integrációt/folyamatos üzembe helyezést az Azure DevOps használatával az Azure Dev Spaces segítségével
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, tárolók
ms.openlocfilehash: f2eb9449518b32ab74f2dbbca6b5489aed325db7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685636"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>A CI/CD és az Azure Dev Spaces használata

Ez a cikk végigvezeti a folyamatos integráció/folyamatos üzembe helyezés (CI/CD) beállítását az Azure Kubernetes-szolgáltatáshoz (AKS), amelyen engedélyezve van a fejlesztői tárolóhelyek. Ci/CD a KS lehetővé teszi az alkalmazásfrissítések automatikus üzembe helyezése, amikor a lekötött kódot a forrástárba küldi. Ci/CD használata a fejlesztői terek engedélyezett fürttel együtt hasznos, mert az alkalmazás alapvonalát naprakészen tarthatja a csapat számára.

![Példa CI/CD-diagram](../media/common/ci-cd-simple.png)

Bár ez a cikk az Azure DevOps segítségével ismerteti, ugyanazok a fogalmak vonatkoznak a CI/CD-rendszerekre, például a Jenkinsre, a TeamCityre stb.

## <a name="prerequisites"></a>Előfeltételek
* [Azure Kubernetes-szolgáltatás (AKS) fürt, engedélyezve van az Azure Dev Spaces](../get-started-netcore.md)
* [Telepített Azure Dev Spaces CLI](upgrade-tools.md)
* [Azure DevOps-szervezet projekttel](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container-beállításjegyzék (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Az Azure Container Registry [rendszergazdai fiókjának](../../container-registry/container-registry-authentication.md#admin-account) adatai elérhetők
* [Az AKS-fürt lekérése engedélyezése az Azure Container-beállításjegyzékből](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Mintakód letöltése
Az idő kedvéért hozzon létre egy villát a mintakódGitHub-tárházunkból. Lépjen https://github.com/Azure/dev-spaces a Villára, és válassza **az Elfordát.** A villafolyamat befejezése után a tárház elágazó verzióját helyileg **klónozza.** Alapértelmezés szerint a _főág_ ki lesz véve, de néhány időtakarékos változtatást tartalmaztunk a _azds_updates_ ágban, amelyeket szintén át kellett volna vinni az elágazás során. A _azds_updates_ ág tartalmazza a frissítéseket kérjük, hogy manuálisan a Fejlesztői terek bemutató szakaszok, valamint néhány előre elkészített YAML és JSON fájlok at egyszerűsítése a CI / CD-rendszer. Használhatja a parancsot, például, `git checkout -b azds_updates origin/azds_updates` hogy nézd meg a _azds_updates_ ág a helyi tárházban.

## <a name="dev-spaces-setup"></a>Fejlesztői szóközök beállítása
Hozzon létre _dev_ egy új `azds space select` terület nevű dev a parancs segítségével. A _fejlesztői_ területet a CI/CD-folyamat fogja használni a kódmódosítások leküldéses. Azt is használják, hogy hozzon létre _gyermek terek_ alapján _dev_.

```cmd
azds space select -n dev
```

Amikor a program a szülőfejlesztési terület kiválasztását kéri, válassza a _ \<None\>_ lehetőséget.

A fejlesztői terület létrehozása után meg kell határoznia az állomásutótagot. A `azds show-context` parancs segítségével az Azure Dev Spaces ingress controller állomásutótagot jeleníthetmeg.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

A fenti példában az állomásutótag _fedcba098.eus.azds.io_. Ezt az értéket később használja a kiadási definíció létrehozásakor.

A _fejlesztői_ terület mindig tartalmazza a tárház legújabb állapotát, egy alapkonfigurációt, hogy a fejlesztők létrehozhatnak _gyermektereket_ a _fejlesztési területről,_ hogy teszteljék az elkülönített módosításokat a nagyobb alkalmazás környezetében. Ezt a koncepciót részletesebben tárgyalja a Fejlesztői terek oktatóanyagok.

## <a name="creating-the-build-definition"></a>A builddefiníció létrehozása
Nyissa meg az Azure DevOps-csapatprojektjét egy webböngészőben, és keresse meg a _Folyamatok szakaszt._ Először kattintson a profilképére az Azure DevOps-webhely jobb felső részén, nyissa meg a betekintő funkciók ablaktábláját, és tiltsa le az _új YAML-folyamat létrehozási felületét:_

![A betekintő funkcióinak megnyitása ablaktábla](../media/common/preview-feature-open.png)

A letiltás lehetősége:

![Új YAML-folyamatlétrehozási élmény](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Az Azure DevOps _új YAML-folyamat létrehozásának_ előzetes verziója funkció ütközik az előre definiált buildfolyamatok létrehozásával. Le kell tiltania azt most az előre definiált build-folyamat üzembe helyezéséhez.

A _azds_updates_ ágban egy egyszerű [Azure Pipeline YAML-t](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) tartalmaztunk, amely meghatározza a *mywebapi* és a *webfrontend*létrehozásához szükséges buildlépéseket.

A választott nyelvtől függően a YAML-folyamat a következőhöz hasonló elérési úton lett bevéve:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Folyamat létrehozása ebből a fájlból:
1. A DevOps-projekt főlapján keresse meg a folyamatok > buildek.
1. Válassza ki az **új** buildfolyamat létrehozásának lehetőségét.
1. Válassza ki a **GitHub** forrásként, engedélyezze a GitHub-fiókkal, ha szükséges, és válassza ki a _azds_updates_ ágat a _fejlesztői terek_ minta alkalmazástárház a ága.
1. Sablonként válassza a **Konfiguráció kódként**vagy **YAML**lehetőséget.
1. Most megjelenik egy konfigurációs lap a build-folyamathoz. Mint már említettük, keresse meg a **yaml fájl elérési útjának** nyelvspecifikus elérési útját a **...** gombbal. Például: `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Nyissa meg a **Változók** lapot.
1. Manuálisan adja hozzá _a dockerId-ot_ változóként, amely az [Azure Container Registry rendszergazdai fiókjának](../../container-registry/container-registry-authentication.md#admin-account)felhasználóneve. (A cikkben említett előfeltételek)
1. Manuálisan adja hozzá _a dockerPassword-ot_ változóként, amely az [Azure Container Registry rendszergazdai fiókjának](../../container-registry/container-registry-authentication.md#admin-account)a jelszava. Győződjön meg arról, hogy adja meg _dockerPassword_ titkos (a lakat ikon kiválasztásával) biztonsági okokból.
1. Válassza **& várólista mentése**lehetőséget.

Most már rendelkezik egy CI-megoldással, amely automatikusan létrehozza a *mywebapi-t* és *a webfrontendet* a GitHub-villa azds_updates ágához leadott _frissítésekhez._ Ellenőrizheti, hogy a Docker-rendszerképek lelettek-eválasztva, ha az Azure Portalra navigál, kiválasztja az Azure Container Registry-t, és böngészik az **Adattárak** lapon. A lemezképek létrehozása és a tároló beállításjegyzékében való megjelenés több percig is eltarthat.

![Az Azure Container-beállításjegyzék-adattárak](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>A kiadási definíció létrehozása

1. A DevOps-projekt főlapján keresse meg a Folyamatok > kiadások
1. Ha egy vadonatúj DevOps-projektben dolgozik, amely még nem tartalmaz kiadási definíciót, a folytatás előtt létre kell hoznia egy üres kiadási definíciót. Az Importálás beállítás nem jelenik meg a felhasználói felületen, amíg nincs meglévő kiadási definíciója.
1. A bal oldalon kattintson a **+ Új** gombra, majd a **Folyamat importálása parancsra**.
1. Kattintson a `samples/release.json` **Tallózás gombra,** és válassza ki a projektből.
1. Kattintson az **OK** gombra. Figyelje meg, hogy a Folyamat ablaktábla betöltött a kiadási definíció szerkesztési lapjával. Azt is figyelje meg, hogy vannak piros figyelmeztető ikonok, amelyek fürtspecifikus részleteket jeleznek, amelyeket még konfigurálni kell.
1. A Folyamat ablaktábla bal oldalán kattintson a **Műtermék hozzáadása** buborékra.
1. A **Forrás** legördülő menüben válassza ki a korábban létrehozott buildfolyamatot.
1. Az **Alapértelmezett verzió**esetében válassza a Legújabb lehetőséget **a buildelőfolyamat alapértelmezett ágából címkékkel**.
1. Hagyja üresen **a Címkéket.**
1. Állítsa a Forrás `drop` **aliast** . A **Forrás alias** értékét az előre definiált kiadási feladatok használják, ezért be kell állítani.
1. Kattintson a **Hozzáadás** parancsra.
1. Most kattintson a villám ikonra az újonnan létrehozott `drop` műtárgy forrás, az alábbiak szerint:

    ![A műtermék folyamatos központi telepítésének feloldása](../media/common/release-artifact-cd-setup.png)
1. Engedélyezze a **folyamatos üzembe helyezés eseményindítót.**
1. Mutasson a **Folyamat** melletti **Feladatok** fülre, és kattintson _a_ fejlesztési _szakasz_ tevékenységeinek szerkesztésére.
1. Ellenőrizze, hogy az **Azure Resource Manager** van kiválasztva a Kapcsolat típusa csoportban. **Connection Type.** és piros sal kiemelve látja a ![három legördülő vezérlőt: A feloldási definíció beállítása](../media/common/release-setup-tasks.png)
1. Válassza ki az Azure Dev Spaces használatával használt Azure-előfizetést. Előfordulhat, hogy az **Engedélyezés gombra kell kattintania.**
1. Válassza ki az Azure Dev Spaces használatával használt erőforráscsoportot és fürtöt.
1. Kattintson az **Ügynök feladatra.**
1. Válassza **a Hosztolt Ubuntu 1604** lehetőséget **az Ügynökkészlet**alatt.
1. Mutasson a **Feladatok** választóra a tetején, majd kattintson a _termékkönyvelési csoportra_ a _gyártási szakasz_ tevékenységeinek szerkesztéséhez.
1. Ellenőrizze, hogy az **Azure Resource Manager** van kiválasztva a Kapcsolat típusa csoportban. **Connection Type.** és válassza ki az Azure-előfizetést, erőforráscsoportot és fürtöt, amelyet az Azure Dev Spaces használatával használ.
1. Kattintson az **Ügynök feladatra.**
1. Válassza **a Hosztolt Ubuntu 1604** lehetőséget **az Ügynökkészlet**alatt.
1. A **változók** fülre kattintva frissítheti a kiadás változóit.
1. Frissítse a **DevSpacesHostSuffix** értékét **UPDATE_ME** a gazdagép utótagjával. Az állomásutótag akkor jelenik `azds show-context` meg, amikor korábban futtatta a parancsot.
1. Kattintson a jobb felső sarokban a **Mentés** gombra, és **az OK gombra.**
1. Kattintson **a + Kiadás** gombra (a Mentés gomb mellett), és **hozzon létre egy kiadást.**
1. A **Műtermékek csoportban**ellenőrizze, hogy a buildfolyamat legújabb buildje van-e kiválasztva.
1. Kattintson **a Létrehozás gombra.**

Egy automatikus kiadási folyamat most kezdődik, üzembe helyezése a *mywebapi* és *a webfrontend-diagramok* a Kubernetes-fürt a _fejlesztési_ legfelső szintű térben. A kiadás előrehaladását az Azure DevOps webportálon figyelheti:

1. Nyissa meg a **Kiadások szakaszt** a **Folyamatok csoportban.**
1. Kattintson a mintaalkalmazás kiadási folyamatára.
1. Kattintson a legújabb kiadás nevére.
1. Mutasson a **fejlesztői** **mezőre a Szakaszok csoportban,** és kattintson **a Naplók gombra.**

A kiadás akkor történik meg, ha minden feladat befejeződött.

> [!TIP]
> Ha a kiadás sikertelen, egy hibaüzenet, például *frissítés sikertelen: időtúlodás a feltételre várva,* próbálja meg megvizsgálni a podok a [fürtben a Kubernetes irányítópulton.](../../aks/kubernetes-dashboard.md) Ha úgy látja, hogy a podok nem indulnak el olyan hibaüzenetekkel, mint *a "azdsexample.azurecr.io/mywebapi:122" rendszer lekérése: rpc hiba: kód = Ismeretlen desc = Hibaválasz a démonból: Https:\//azdsexample.azurecr.io/v2/mywebapi/manifests/122: jogosulatlan: hitelesítés szükséges,* mert a fürt nem jogosult lekérése az Azure Container Registry-ből. Győződjön meg arról, hogy befejezte az [AKS-fürt engedélyezése az Azure Container Registry előfeltételeiből lekérése.](../../aks/cluster-container-registry-integration.md)

Most már rendelkezik egy teljesen automatizált CI/CD-folyamat a GitHub-elágazás a fejlesztői terek minta alkalmazások. Minden alkalommal, amikor véglegesíti és leküldéses kódot, a buildfolyamat jön létre, és leküldéses a *mywebapi* és *a webfrontend-rendszerképeket* az egyéni ACR-példány. Ezután a kiadási folyamat telepíti a Helm-diagram ot az egyes alkalmazások a _fejlesztői_ térben a fejlesztői terek-kompatibilis fürt.

## <a name="accessing-your-_dev_-services"></a>A _fejlesztési_ szolgáltatások elérése
A telepítés után a *webfrontend* `http://dev.webfrontend.fedcba098.eus.azds.io` _fejlesztői_ verziója nyilvános URL-címmel érhető el, például: . Ezt az URL-címet `azds list-uri` a következő paranccsal találhatja meg: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Üzembe helyezés éles környezetben

Az oktatóanyagban létrehozott CI/CD rendszer használatával manuálisan előléptetés e kiadáshoz a _termékhez:_
1. Nyissa meg a **Kiadások szakaszt** a **Folyamatok csoportban.**
1. Kattintson a mintaalkalmazás kiadási folyamatára.
1. Kattintson a legújabb kiadás nevére.
1. Mutasson az **ösztöke** mezőre **a Szakaszok csoportban,** és kattintson a **Telepítés gombra.**
    ![Előléptetés éles környezetbe](../media/common/prod-promote.png)
1. Mutasson ismét **a termékkönyvelési** csoport **fölé a Szakaszok csoportban,** és kattintson **a Naplók gombra.**

A kiadás akkor történik meg, ha minden feladat befejeződött.

A CI/CD-folyamat _prod_ fázisa terheléselosztót használ a fejlesztői tárolóhelyek ingress-vezérlője helyett a _termékszolgáltatásokhoz_ való hozzáférés biztosításához. A _termékkönyvelési_ szakaszban üzembe helyezett szolgáltatások DNS-nevek helyett IP-címként érhetők el. Éles környezetben dönthet úgy, hogy saját Ingress-vezérlőt hoz létre a szolgáltatások üzemeltetéséhez a saját DNS-konfigurációja alapján.

A webfrontend szolgáltatás IP-címének meghatározásához kattintson a **Print webfrontend nyilvános IP-lépésre** a naplókimenet bővítéséhez. Használja a naplókimenetben megjelenő IP-címet a **webfrontend** alkalmazás eléréséhez.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Fejlesztői terek műszerek a termelésben
Bár a fejlesztői terek instrumentation úgy lett kialakítva, hogy _ne_ az alkalmazás normál működésének módja, azt javasoljuk, hogy futtassa az éles számítási feladatok egy Kubernetes névtér, amely nincs engedélyezve a fejlesztői terek. Az ilyen típusú Kubernetes névtér használata azt jelenti, `kubectl` hogy vagy létre kell hoznia az éles névteret a CLI használatával, vagy engedélyeznie kell a CI/CD-rendszer számára, hogy létrehozza azt az első Helm-telepítés során. _Ha kijelöl_ egy szóközt a Fejlesztői terek eszközhasználatával, a fejlesztői terek instrumentation-t hozzáadja a névtérhez.

Íme egy példa a névtér szerkezetére, amely támogatja a szolgáltatásfejlesztést, a "fejlesztési" _környezetet és_ az éles környezetet, mindezt egyetlen Kubernetes-fürtben:

![Példa névtérszerkezetre](../media/common/cicd-namespaces.png)

> [!Tip]
> Ha már létrehozott egy `prod` helyet, és egyszerűen ki szeretné zárni a fejlesztői terek instrumentation (törlés nélkül!), ezt megteheti a következő Fejlesztői terek CLI paranccsal:
>
> `azds space remove -n prod --no-delete`
>
> Előfordulhat, hogy ezt követően `prod` törölnie kell az összes podot a névtérben, hogy azok újra létrehozhatók a Fejlesztői terek instrumentation nélkül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a csapatfejlesztésről az Azure Dev Spaces használatával](../team-development-netcore.md)