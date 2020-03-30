---
title: Az útválasztás működése az Azure dev spaces-szel
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Az Azure dev spaces-t működtető folyamatok és az útválasztás működésének ismertetése
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241386"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Az útválasztás működése az Azure dev spaces-szel

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre egy Azure Kubernetes-szolgáltatás (AKS) fürtön. Miután a projekt egy fejlesztői térben fut, az Azure Dev Spaces további hálózati és útválasztási lehetőségeket biztosít a projekthez.

Ez a cikk ismerteti, hogyan működik az útválasztási témakörzel fejlesztői szóközök.

## <a name="how-routing-works"></a>Az útválasztás működése

A fejlesztői terület az AKS-re épül, és ugyanazokat a [hálózati fogalmakat](../aks/concepts-network.md)használja. Az Azure Dev Spaces is rendelkezik egy központi *ingressmanager* szolgáltatással, és telepíti a saját ingress controller az AKS-fürtre. A *bejövő kapcsolatkezelő* szolgáltatás figyeli az AKS-fürtök fejlesztési tereket, és bővíti az Azure Dev Spaces ingress controller a fürtben a be- és befutóobjektumok útválasztás az alkalmazás podok. A devspaces-proxy tároló minden `azds-route-as` pod http-fejlécet ad hozzá a HTTP-forgalom egy fejlesztői tér az URL-cím alapján. Az URL-címre *http://azureuser.s.default.serviceA.fedcba09...azds.io* vonatkozó kérés például http-fejlécet kapna a használatával. `azds-route-as: azureuser` A devspaces-proxy tároló nem `azds-route-as` ad hozzá fejlécet, ha már van ilyen.

Ha egy HTTP-kérés a fürtön kívülről érkezik egy szolgáltatásba, a kérelem a bejövő adategység-vezérlőhöz kerül. A be- éselőszoba-vezérlő közvetlenül a megfelelő podhoz irányítja a kérelmet a be- és küldési objektumok és szabályok alapján. A devspaces-proxy tároló a pod ban megkapja `azds-route-as` a kérést, hozzáadja a fejléc et az URL-cím alapján, majd továbbítja a kérelmet az alkalmazástárolóba.

Ha egy HTTP-kérés tanusít egy másik szolgáltatás a fürtön belül, a kérelem először a hívó szolgáltatás devspaces-proxy tároló. A devspaces-proxy tároló megnézi a `azds-route-as` HTTP-kérelmet, és ellenőrzi a fejlécet. A fejléc alapján a devspaces-proxy tároló megkeresi a fejlécértékhez társított szolgáltatás IP-címét. Ha egy IP-cím található, a devspaces-proxy tároló átirányítja a kérelmet, hogy az IP-címet. Ha nem található IP-cím, a devspaces-proxy tároló a szülőalkalmazás-tárolóhoz irányítja a kérelmet.

Például az Alkalmazások *ServiceA* és *ServiceB* telepítve vannak egy szülő fejlesztői terület nevű *alapértelmezett.* *ServiceA* támaszkodik *serviceB* és http-hívásokat kezdeményez. Az Azure User létrehoz egy gyermek fejlesztői területet az *azureuser*nevű *alapértelmezett* terület alapján. Az Azure User is telepíti a saját verzióját *serviceA* a gyermek térben. Amikor a kérelmet: *http://azureuser.s.default.serviceA.fedcba09...azds.io*

![Azure dev spaces útválasztás](media/how-dev-spaces-works/routing.svg)

1. A be- és előtér-vezérlő megkeresi az URL-címhez társított pod IP-címét, amely *serviceA.azureuser.*
1. A bejövő adatok vezérlő lekeresi a pod IP-címét az Azure User fejlesztési területén, és továbbítja a kérelmet a *serviceA.azureuser* pod.
1. A devspaces-proxy tároló a *serviceA.azureuser* pod a `azds-route-as: azureuser` kérés t, és http-fejlécként hozzáadja.
1. A *serviceA.azureuser* pod devspaces-proxy tárolója a serviceA.azureuser pod *serviceA.azureuser pod serviceA.azureuser* pod *serviceA* alkalmazástárolójának továbbítja a kérelmet.
1. A *serviceA.azureuser pod serviceA.azureuser* pod *serviceA* alkalmazás a *B szolgáltatást*hívja. A *serviceA* alkalmazás a meglévő `azds-route-as` fejléc megőrzéséhez is `azds-route-as: azureuser`tartalmaz kódot, amely ebben az esetben a .
1. A devspaces-proxy tároló a *serviceA.azureuser* pod megkapja a kérést, és megkeresi `azds-route-as` a *serviceB* IP-értéke alapján a fejléc.
1. A *serviceA.azureuser* pod devspaces-proxy tárolója nem talál IP-címet a *serviceB.azureuser*számára.
1. A *serviceA.azureuser* pod devspaces-proxy tárolója megkeresi a *B szolgáltatás* IP-címét a szülőtérben, amely *serviceB.default.*
1. A devspaces-proxy tároló a *serviceA.azureuser* pod megkeresi az IP *serviceB.default* és irányítja a kérelmet a *serviceB.default* pod.
1. A *serviceB.default* pod devspaces-proxy tárolója megkapja a kérést, és a *serviceB.default* pod *serviceB* alkalmazástárolójába irányítja a kérelmet.
1. A *serviceB.default* pod *ServiceB* alkalmazás választ ad vissza a *serviceA.azureuser* pod.
1. A *serviceA.azureuser* pod devspaces-proxy tárolója megkapja a választ, és a *serviceA.azureuser pod serviceA.azureuser* pod *serviceA* alkalmazástárolójára irányítja a választ.
1. A *serviceA* alkalmazás megkapja a választ, majd visszaadja a saját válaszát.
1. A *serviceA.azureuser* pod devspaces-proxy tárolója megkapja a választ a *serviceA* alkalmazástárolóból, és a választ a fürtön kívüli eredeti hívónak irányítja.

Minden más, nem HTTP-nek minősülő TCP-forgalom áthalad a bejövő forgalom vezérlőn és a devspaces-proxy tárolókon változatlanul.

## <a name="sharing-a-dev-space"></a>Fejlesztői terület megosztása

Amikor egy csapattal dolgozik, [megoszthatja a fejlesztői területet egy teljes csapaton keresztül,](how-to/share-dev-spaces.md) és származtatott fejlesztői tereket hozhat létre. A fejlesztői terület bárki által használható, aki közreműködői hozzáféréssel rendelkezik a fejlesztői terület erőforráscsoportjához.

Egy másik fejlesztési területből származó új fejlesztői területet is létrehozhat. Amikor létrehoz egy származtatott fejlesztői területet, a *azds.io/parent-space=PARENT-SPACE-NAME* címke hozzáadódik a származtatott fejlesztői tér névteréhez. Emellett a szülő fejlesztői terület összes alkalmazás a származtatott fejlesztői területtel van megosztva. Ha egy alkalmazás frissített verzióját telepíti a származtatott fejlesztői területre, az csak a származtatott fejlesztői térben fog létezni, és a szülő fejlesztési terület változatlan marad. A származtatott fejlesztői terek vagy *nagyszülői* terek legfeljebb három szintje lehet.

A származtatott fejlesztői terület is intelligensmódon irányítja a kérelmeket a saját alkalmazások és a szülő től megosztott alkalmazások között. Az útválasztás úgy működik, hogy megpróbálja a kérelmet a származtatott fejlesztői térben lévő alkalmazáshoz irányítani, és a szülő fejlesztői területről visszaesik a megosztott alkalmazásba. Ha az alkalmazás nem a szülőterületen található, a programterv visszakerül a nagyszülőtérben lévő megosztott alkalmazásra.

Példa:
* A fejlesztési terület *alapértelmezett szolgáltatása* *A* és *ServiceB.*
* A fejlesztési terület *azureuser* származik *alapértelmezett.*
* Az A *szolgáltatás* frissített verziója telepítve van az *azureuser.*

Az *azureuser*használatakor az *A szolgáltatásba* irányuló összes kérés az *azureuser*frissített verziójára lesz irányítva. A *B szolgáltatásnak* küldött kérés először megpróbálja átirányítani a *B szolgáltatás* *azureuser* verziójára. Mivel nem létezik, a rendszer a B *szolgáltatás* *alapértelmezett* verziójához irányítja. Ha az *Azureuser* verziója *ServiceA* törlődik, az Összes kérelmet *serviceA* lesz vissza az *alapértelmezett* verziója *serviceA.*

## <a name="next-steps"></a>További lépések

Ha példákat szeretne látni arra, hogy az Azure Dev Spaces hogyan használja az útválasztást a gyors iteráció és fejlesztés érdekében, olvassa el [A fejlesztői gép csatlakoztatása a fejlesztői térhöz való csatlakoztatása című][how-it-works-connect]témakört, [a kód Azure Dev Spaces használatával történő távoli hibakeresés működését,][how-it-works-remote-debugging] [valamint a GitHub-műveletek & az Azure Kubernetes-szolgáltatás][pr-flow]című témakört.

Az útválasztás és az Azure Dev Spaces csapatfejlesztéssel való használatának első lépései az [Azure Dev Spaces rövid útmutatójában.][quickstart-team]

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md