---
title: Az Azure fejlesztői, szóközök CI/CD használatával
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Container Service, tárolók
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768280"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>CI/CD használata az Azure fejlesztői, szóközök

Ez a cikk végigvezeti azokon a folyamatos integráció/folyamatos üzembe helyezés (CI/CD) az Azure Kubernetes Service (AKS) engedélyezve van, fejlesztési szóközt tartalmazó beállítása. CI/CD – AKS lehetővé teszi, hogy az alkalmazások frissítése, amikor a véglegesített kód a forrás-tárházzal való leküldésekor automatikusan üzembe helyezni. CI/CD használatával egy fejlesztési tárolóhelyek együtt engedélyezett fürt azért hasznos, mert azt is naprakészen az alkalmazás alapvető a csapat dolgozhat.

![Példa a CI/CD-diagram](../media/common/ci-cd-simple.png)

Bár ez a cikk végigvezeti Önt az Azure DevOps, ugyanezek a fogalmak alkalmazandó rendszerek CI/CD a Jenkins-, TeamCity-, stb.

## <a name="prerequisites"></a>Előfeltételek
* [Az Azure fejlesztési szóközt engedélyezni az Azure Kubernetes Service (AKS)-fürt](../get-started-netcore.md)
* [Fejlesztői tárolóhelyek az Azure CLI telepítve van](upgrade-tools.md)
* [A projekt az Azure DevOps szervezettel](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Az Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Az Azure Container Registry [rendszergazdai fiók](../../container-registry/container-registry-authentication.md#admin-account) részletek érhetők el
* [Az AKS-fürt az Azure Container Registry lekéréshez engedélyezése](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Mintakód letöltése
Az idő hozzunk létre mintául szolgáló kód GitHub-adattár elágazásával. Lépjen a https://github.com/Azure/dev-spaces válassza **elágazás**. Az elágazásában folyamat befejeződése után **Klónozás** az elágaztatott tárház helyi verziójával. Alapértelmezés szerint a _fő_ ág a rendszer ki van véve, de bizonyos idő megtakarítására változásai bővítettük a _azds_updates_ ágat, amelyek kell is átadott során az elágazásba. A _azds_updates_ ág megkérjük, hogy a fejlesztői, szóközök oktatóanyag szakaszok, valamint néhány előre elkészített YAML és JSON-fájlok számára a CI/CD rendszert egyszerűsítésével manuális frissítést tartalmaz. Hasonló parancsot is használhatja `git checkout -b azds_updates origin/azds_updates` tekintse meg a _azds_updates_ a helyi tárház.

## <a name="dev-spaces-setup"></a>Fejlesztői szóközöket telepítő
Hozzon létre egy új tárolóhelyet nevű _fejlesztési_ használatával a `azds space select` parancsot. A _fejlesztési_ terület a CI/CD-folyamat által használandó, továbbítsa a kód módosításait. Azt is használható létrehozása _gyermek tárolóhelyek_ alapján _fejlesztési_.

```cmd
azds space select -n dev
```

Amikor a rendszer kéri, válassza ki a szülő fejlesztési szóközzel, válassza ki a  _\<nincs\>_.

A fejlesztési tárhely létrehozása után meg kell határoznia az állomásnév-utótagja. Használja a `azds show-context` parancsot az Azure fejlesztési tárolóhelyek Bejövőforgalom-vezérlőt az állomásnév-utótagja megjelenítéséhez.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

A fenti példában az állomásnév-utótagja a _fedcba098.eus.azds.io_. Ezt az értéket később a kiadási definíció létrehozása során használatos.

A _fejlesztési_ terület mindig tartalmazza a tárházban, a baseline legfrissebb állapotát, így a fejlesztők _gyermek tárolóhelyek_ a _fejlesztési_ elkülönített módosítások teszteléséhez a nagyobb alkalmazás összefüggésében. A fogalom a fejlesztői, szóközök oktatóanyagok tárgyalja részletesen.

## <a name="creating-the-build-definition"></a>A build definíció létrehozása
Egy webböngészőben nyissa meg az Azure DevOps csapata projektet, és keresse meg a _folyamatok_ szakaszban. Először kattintson a jobb felső sarkában az Azure DevOps-hely található a profilképet, az előzetes funkciók panel megnyitásához, és tiltsa le a _új YAML folyamat-létrehozási folyamatának_:

![Nyitó előzetes funkciók ablaktábla](../media/common/preview-feature-open.png)

A beállítás letiltása:

![Az új YAML-folyamat létrehozása lehetőséget élmény](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Az Azure DevOps _új YAML folyamat-létrehozási folyamatának_ előzetes funkció ütközik az előre definiált létrehozása jelenleg folyamatok létrehozása. Le kell tiltania, egyelőre az előre definiált buildelési folyamat üzembe helyezéséhez.

Az a _azds_updates_ bővítettük egy egyszerű ág [Azure folyamat YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) , amely meghatározza, hogy a szükséges létrehozási lépések *mywebapi* és *webfrontend* .

A nyelvet, úgy döntött, attól függően a folyamat YAML volt bejelentkezett egy hasonló elérési úton: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Folyamat létrehozása ebben a fájlban:
1. A DevOps-projekt fő lapján, a folyamatok navigáljon > épít.
1. Válassza a hozzon létre egy **új** folyamatát.
1. Válassza ki **GitHub** forrásaként, engedélyezze a GitHub-fiókját, ha szükséges, és válassza a _azds_updates_ ág a fejlesztési-tárolóhelyek PéldaAlkalmazás tárház az elágaztatott verziójáról.
1. Válassza ki **kódként konfigurációs**, vagy **YAML**, a sablont.
1. Mostantól lehetősége lesz a konfigurációs lapon a buildelési folyamat. A nyelvspecifikus elérési utat a fent említett a **YAML-fájl elérési útja** használatával a **...**  gombra. Például: `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Nyissa meg a **változók** fülre.
1. Adja hozzá manuálisan _dockerId_ változóként, vagyis felhasználóneve a [Azure Container Registry-rendszergazdai fiók](../../container-registry/container-registry-authentication.md#admin-account). (Az a cikk Előfeltételek között ismertetett)
1. Adja hozzá manuálisan _dockerPassword_ változóként, azaz a jelszót a [Azure Container Registry-rendszergazdai fiók](../../container-registry/container-registry-authentication.md#admin-account). Ne felejtse megadni _dockerPassword_ , (a lakat ikon kiválasztásával) titkos kulcs biztonsági okokból.
1. Válassza ki **várólistára & mentése**.

Most már a CI-megoldás, amely automatikusan hozhat létre *mywebapi* és *webfrontend* a leküldött bármilyen frissítést a _azds_updates_ a GitHub-elágazásba ága. Ellenőrizheti a Docker-rendszerképek leküldött ellenőrizheti, hogy az Azure Portalon, az Azure Container Registry kiválasztásával, és keresse a **Tárházak** fülre. A lemezképeket hozhat létre és jelennek meg a tárolóregisztrációs adatbázis több percig is eltarthat.

![Az Azure Container Registry-adattárak](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>A kiadási definíció létrehozása

1. A DevOps-projekt fő lapján, a folyamatok navigáljon > kiadások
1. Ha egy vadonatúj DevOps-projekt, amely még nem tartalmaz a kiadási definíció dolgozik, szüksége lesz, először hozzon létre egy üres kiadási definíciót, mielőtt továbblépne. Az importálási funkcióval nem jeleníti meg a felhasználói felületen, amíg nincs egy meglévő kiadási definíciót.
1. A bal oldalon kattintson a **+ új** gombra, majd kattintson a **folyamat importálása**.
1. Kattintson a **Tallózás** válassza `samples/release.json` a projektből.
1. Kattintson az **OK** gombra. Figyelje meg a folyamat ablaktábla be van töltve, és a kiadási definíció szerkesztése oldal. Figyelje meg ott is néhány piros figyelmeztető ikon jelzi a fürtre jellemző részletek, amelyek továbbra is be kell állítani.
1. A folyamat panel bal oldalán kattintson a **egy összetevő hozzáadása** buborékra.
1. Az a **forrás** legördülő menüben válassza a buildelési folyamat korábban létrehozott.
1. Az a **alapértelmezett verzió**, válassza a **legújabb ágból a buildelési folyamat alapértelmezett címkékkel**.
1. Hagyja **címkék** üres.
1. Állítsa be a **forrás alias** való `drop`. A **forrás alias** érték előre meghatározott kiadási tevékenységek által használt, úgy kell beállítani.
1. Kattintson a **Hozzáadás** parancsra.
1. Most kattintson az újonnan létrehozott villámgyors bolt ikonjára `drop` összetevő forrás, az alább látható módon:

    ![Kiadási összetevő folyamatos üzembe helyezés beállítása](../media/common/release-artifact-cd-setup.png)
1. Engedélyezze a **a folyamatos készregyártás eseményindítója**.
1. A kurzort a **feladatok** melletti lapon **folyamat** , és kattintson a _fejlesztési_ szerkesztése a _fejlesztési_ feladatok ütemezése.
1. Győződjön meg arról **Azure Resource Manager** ki van választva **kapcsolattípus.** és a három legördülő vezérlőt, vörös színnel látja: ![Kiadási definíció beállítása](../media/common/release-setup-tasks.png)
1. Válassza ki az Azure-előfizetés használata az Azure fejlesztési szóközöket. Emellett szükség lehet kattintson **engedélyezés**.
1. Válassza ki az erőforráscsoportot és a fürt az Azure fejlesztési tárolóhelyek használata esetén.
1. Kattintson a **ügynöki feladat**.
1. Válassza ki **üzemeltetett Ubuntu 1604** alatt **ügynökkészlet**.
1. A kurzort a **feladatok** választó a lap tetején kattintson _prod_ szerkesztése a _prod_ feladatok ütemezése.
1. Győződjön meg arról **Azure Resource Manager** ki van választva **kapcsolattípus.** és válassza ki az Azure-előfizetés, erőforráscsoport és használata az Azure fejlesztési tárolóhelyek fürtöt.
1. Kattintson a **ügynöki feladat**.
1. Válassza ki **üzemeltetett Ubuntu 1604** alatt **ügynökkészlet**.
1. Kattintson a **változók** fülre, és frissítse a kiadás a változókat.
1. Frissítse az értéket a **DevSpacesHostSuffix** a **UPDATE_ME** , az állomásnév-utótagja. Az állomásnév-utótagja jelenik meg, ha futtatta a `azds show-context` korábban parancsot.
1. Kattintson a **mentése** a jobb felső sarokban lévő, és **OK**.
1. Kattintson a **+ kiadás** (mellett a Mentés gombot), és **hozzon létre egy kiadási**.
1. A **összetevők**, ellenőrizze az összeállítási folyamatból legújabb buildjének van kiválasztva.
1. Kattintson a **Create** (Létrehozás) gombra.

Az automatizált kibocsátási folyamatok most elkezdi, üzembe helyezése a *mywebapi* és *webfrontend* diagramok a kubernetes-fürt a _fejlesztési_ legfelső szintű helyet. A kiadás az Azure DevOps-webportál előrehaladásának figyelése is lehetséges:

1. Keresse meg a **kiadásokban** szakaszba **folyamatok**.
1. Kattintson a kiadási folyamathoz a mintaalkalmazáshoz.
1. Kattintson a legfrissebb kiadás neve.
1. A kurzort **fejlesztési** mező alatt **szakaszok** kattintson **naplók**.

A kiadás történik, ha minden feladat befejeződött.

> [!TIP]
> Például ha a kiadás egy hibaüzenettel meghiúsul *frissítése sikertelen volt: a feltétel várakozás túllépte az időkorlátot*, próbálja meg a fürt a podok vizsgálatával [a Kubernetes-irányítópult használatával](../../aks/kubernetes-dashboard.md). Ha megjelenik a podok nem működik a kezdéshez hibaüzenetek, például *nem sikerült lekérni a lemezkép "azdsexample.azurecr.io/mywebapi:122": RPC-hiba: kód = ismeretlen desc démon válaszát hiba =: Első https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: jogosulatlan: hitelesítés szükséges*, mert a fürt nem engedélyezett az Azure Container Registry lekéréshez lehet. Győződjön meg arról, hogy végrehajtotta a [engedélyezik az AKS-fürt az Azure Container Registry lekéréshez](../../container-registry/container-registry-auth-aks.md) előfeltétel.

Most már rendelkezik egy teljesen automatizált CI/CD-folyamat számára a fejlesztői, szóközök mintaalkalmazások, a GitHub-elágazásba. Minden alkalommal, amikor véglegesítése és leküldése kód, a létrehozási folyamat elkészíti és leküldése a *mywebapi* és *webfrontend* rendszerképeket az egyéni ACR-példányba. A kiadási folyamathoz telepíteni fogja a Helm-diagram minden alkalmazáshoz, majd a _fejlesztési_ terület fejlesztési tárolóhelyek engedélyezve van a fürtön.

## <a name="accessing-your-dev-services"></a>Hozzáférés a _fejlesztési_ szolgáltatások
Az üzembe helyezést követően a _fejlesztési_ verziója *webfrontend* nyilvános URL-címmel elérhetőek legyenek: `http://dev.webfrontend.fedcba098.eus.azds.io`. Annak az URL-cím futtatásával a `azds list-uri` parancsot: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Az éles környezetben való

A manuálisan főkiszolgálóvá előléptetni egy adott kiadását _prod_ a CI/CD-rendszer ebben az oktatóanyagban létrehozott használatával:
1. Keresse meg a **kiadásokban** szakaszba **folyamatok**.
1. Kattintson a kiadási folyamathoz a mintaalkalmazáshoz.
1. Kattintson a legfrissebb kiadás neve.
1. A kurzort a **prod** mező alatt **szakaszok** , és kattintson a **üzembe helyezés**.
    ![Éles környezetbe való előléptetése](../media/common/prod-promote.png)
1. A kurzort **prod** mezőbe újra a **szakaszok** kattintson **naplók**.

A kiadás történik, ha minden feladat befejeződött.

A _prod_ a CI/CD-folyamat szakaszában terheléselosztót használ a fejlesztési tárolóhelyek Bejövőforgalom-vezérlőjéhez helyett segítségével hozzáférést biztosíthat _prod_ szolgáltatások. Szolgáltatások telepítése a _prod_ szakaszában érhetők el az IP-címek, DNS-nevek helyett. Éles környezetben dönthet úgy, hogy hozzon létre saját Bejövőforgalom-vezérlőt, a saját DNS-konfiguráció alapján a szolgáltatásokat.

Az IP-címét a webfrontend szolgáltatás határozza meg, kattintson a a **webfrontend nyilvános IP-cím nyomtatása** bontsa ki a kimenet a lépést. Használja a IP-cím jelenik meg a napló való hozzáférést a **webfrontend** alkalmazás.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>A fejlesztői, szóközök instrumentation éles környezetben
Bár fejlesztési tárolóhelyek instrumentation úgy lett kialakítva _nem_ átszállítást megakadályozzák az alkalmazás normál működés, azt javasoljuk az éles számítási feladatokat futtatnak egy Kubernetes-névtér, fejlesztői, szóközök nem engedélyezett. Az ilyen Kubernetes-névtér azt jelenti, hogy vagy hozzon létre az éles névtér használatával a `kubectl` CLI-t, vagy a CI/CD a rendszer a Helm első üzembe helyezés során engedélyezése. _Kiválasztásával_ vagy más módon területlétrehozási egy fejlesztési szóközzel azokat az eszközöket fogja üzemállapot-fejlesztői, szóközök névtéren.

Íme egy példa névtér struktúra, amely támogatja a funkcióinak fejlesztését, a "Fejl" környezet _és_ production, egy Kubernetes-fürtön található összes:

![Példa névtér struktúra](../media/common/cicd-namespaces.png)

> [!Tip]
> Ha már létrehozott egy `prod` területet, és szeretné egyszerűen hasonlóan a kizárása, fejlesztői, szóközök kialakítási (annak törlése nélkül!), megteheti a következő fejlesztői, szóközök CLI-paranccsal:
>
> `azds space remove -n prod --no-delete`
>
> Előfordulhat, hogy az összes podok törölnie kell a `prod` névtér, miután ezt, akkor azok ismételten létrehozhatók fejlesztési tárolóhelyek instrumentation nélkül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure-fejlesztési szóközzel csoportos fejlesztése](../team-development-netcore.md)