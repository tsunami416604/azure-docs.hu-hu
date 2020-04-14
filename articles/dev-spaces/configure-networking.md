---
title: Hálózatkezelés konfigurálása az Azure dev spaces-hez különböző hálózati topológiákban
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Az Azure Kubernetes-szolgáltatásokban az Azure Dev Spaces futtatásához vonatkozó hálózati követelmények ismertetése
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, CNI, kubenet, SDN, hálózat
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262043"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Hálózatkezelés konfigurálása az Azure dev spaces-hez különböző hálózati topológiákban

Az Azure Dev Spaces az Azure Kubernetes-szolgáltatás (AKS) fürtjein fut az alapértelmezett hálózati konfigurációval. Ha módosítani szeretné az AKS-fürt hálózati konfigurációját, például a fürt tűzfal mögé helyezését, hálózati biztonsági csoportok használatát vagy hálózati házirendek használatát, további szempontokat kell figyelembe vennie az Azure Dev Spaces futtatásához.

![Virtuális hálózat konfigurációja](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Virtuális hálózat vagy alhálózati konfigurációk

Előfordulhat, hogy az AKS-fürt más virtuális hálózati vagy alhálózati konfigurációval rendelkezik az AKS-fürt be- vagy kimenő forgalomának korlátozására. Előfordulhat például, hogy a fürt tűzfal mögött van, például az Azure Tűzfal mögött, vagy használhat hálózati biztonsági csoportokat vagy egyéni szerepköröket a hálózati forgalom korlátozására. A [GitHub Azure Dev Spaces mintatárában][sample-repo]találhat egy példa hálózati konfigurációt.

Az Azure dev spaces bizonyos követelményekkel rendelkezik a *be- és kimenő forgalom,* valamint *a csak a be- ésátállítási* forgalom. Ha az Azure Dev Spaces egy AKS-fürt egy virtuális hálózati vagy alhálózati konfiguráció, amely korlátozza a forgalmat az AKS-fürt, csak követnie kell a következő bejövő forgalom csak és a bejövő és kimenő forgalmi követelmények az Azure Dev Spaces megfelelő működéséhez.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Be- és ki- és ki- és ki- és ki- és ki- és kilépési hálózati forgalmi követelmények

Az Azure dev spaces-nek be- és kimenő forgalomra van szüksége a következő teljes tartományn-műveletekhez:

| FQDN                       | Port       | Használat      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-lemezképek lekérése az Azure Dev Spaces-hez |
| gcr.io                     | HTTPS: 443 | Helm-lemezképek lekérése az Azure Dev Spaces-hez |
| storage.googleapis.com     | HTTPS: 443 | Helm-lemezképek lekérése az Azure Dev Spaces-hez |
| azds-*.azds.io             | HTTPS: 443 | Az Azure Dev Spaces háttérszolgáltatásokkal való kommunikáció az Azure Dev Spaces vezérlőhöz. A pontos Teljes tartományjelző n megtalálható az *Fqdn adatsíkban* a`USERPROFILE\.azds\settings.json` |

Frissítse a tűzfalat vagy a biztonsági konfigurációt, hogy a fenti teljes tartományhálózatokba irányuló és onnan érkező hálózati forgalmat engedélyezze. Ha például tűzfalat használ a hálózat védelmére, a fenti teljes tartományneveket hozzá kell adni a tűzfal alkalmazásszabályához, hogy lehetővé tegye a forgalmat ezekre a tartományokra.

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények be- és be- és visszahálózaton

Az Azure Dev Spaces a Kubernetes névtérszintű útválasztást, valamint a saját teljes qdn-t használó szolgáltatásokhoz való nyilvános hozzáférést biztosít. Mindkét funkció működéséhez frissítse a tűzfalat vagy a hálózati konfigurációt, hogy a fürt azure dev spaces-vezérlőkülső IP-címére nyilvános be- és be- és be- és be- és be- és be- és be- és be- és hálózat- és hálózat- és hálózat- és hálózat- és hálózat- cím. Másik lehetőségként létrehozhat egy [belső terheléselosztót,][aks-internal-lb] és hozzáadhat egy NAT-szabályt a tűzfalhoz, hogy lefordítsa a tűzfal nyilvános IP-címét a belső terheléselosztó IP-címére. A [traefik][traefik-ingress] vagy [az NGINX][nginx-ingress] segítségével egyéni bejövő adatbeviteli vezérlőt is létrehozhat.

## <a name="aks-cluster-network-requirements"></a>AKS-fürt hálózati követelményei

Az AKS lehetővé teszi, hogy [a hálózati házirendek][aks-network-policies] segítségével szabályozhatja a fürtpodok közötti és kimenő forgalom, valamint a podról érkező kimenő forgalom. Az Azure dev spaces bizonyos követelményekkel rendelkezik a *be- és kimenő forgalom,* valamint *a csak a be- ésátállítási* forgalom. Ha az Azure Dev Spaces egy AKS-fürt aKS-hálózati szabályzatok, a következő bejövő forgalom csak és a bejövő és kimenő forgalmi követelmények az Azure Dev Spaces megfelelő működéséhez.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Be- és ki- és ki- és ki- és ki- és ki- és kilépési hálózati forgalmi követelmények

Az Azure Dev Spaces lehetővé teszi, hogy közvetlenül kommunikáljon egy pod a fürt önfejlesztési területen a hibakeresés. Ahhoz, hogy ez a szolgáltatás működjön, adjon hozzá egy hálózati házirendet, amely lehetővé teszi a be- és kilépési kommunikációt az Azure Dev Spaces-infrastruktúra IP-címeivel, amelyek [régiónként változnak.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények be- és be- és visszahálózaton

Az Azure Dev Spaces biztosítja a podok közötti névterek közötti útválasztást. Például az Azure Dev Spaces engedélyezve lévő névterek szülő-gyermek kapcsolattal rendelkezhetnek, amely lehetővé teszi a hálózati forgalom továbbítását a szülő- és gyermeknévterek között. Az Azure Dev Spaces is elérhetővé teszi a szolgáltatás végpontok saját teljes qdn használatával. A szolgáltatások kiexposálásának különböző módjainak és a névtérszintű útválasztás hatásainak konfigurálásához [lásd: A különböző végpontbeállítások használata.][endpoint-options]

## <a name="using-azure-cni"></a>Az Azure CNI használata

Alapértelmezés szerint az AKS-fürtök [kubenet][aks-kubenet] használatára vannak konfigurálva a hálózatkezeléshez, amely együttműködik az Azure Dev Spaces használatával. Az AKS-fürt az [Azure Container Networking Interface (CNI) használatára][aks-cni]is konfigurálható. Az Azure Dev Spaces és az Azure CNI használatához az AKS-fürtön legfeljebb 10 privát IP-címhelyet engedélyezhet az Azure Dev Spaces által üzembe helyezett podok számára. A privát IP-címek engedélyezésével kapcsolatos további részletek az [AKS Azure CNI dokumentációjában][aks-cni-ip-planning]találhatók.

## <a name="using-api-server-authorized-ip-ranges"></a>AZ API-kiszolgáló engedélyezett IP-tartományai használata

Az AKS-fürtök lehetővé teszik további biztonság konfigurálását, amely korlátozza, hogy mely IP-cím kommunikálhat a fürtökkel, például egyéni virtuális hálózatok használatával, vagy [az API-kiszolgálóhoz való hozzáférés biztonságossá tétele engedélyezett IP-tartományok használatával.][aks-ip-auth-ranges] Az Azure Dev Spaces használatához a további biztonság fürt [létrehozása][aks-ip-auth-range-create] során további tartományokat kell [engedélyeznie a régió alapján.][dev-spaces-ip-auth-range-regions] Egy meglévő fürt [is frissíthető,][aks-ip-auth-range-update] hogy ezek a további tartományok is engedélyezhetők. Emellett engedélyeznie kell az AKS-fürthöz csatlakozó fejlesztőgépek IP-címét az API-kiszolgálóhoz való csatlakozáshoz.

## <a name="using-aks-private-clusters"></a>Az AKS privát fürtjei

Jelenleg az Azure dev spaces nem támogatott [AKS-alapú privát fürtök.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Különböző végpontbeállítások használata

Az Azure Dev Spaces rendelkezik azzal a lehetőséggel, hogy az AKS-en futó szolgáltatások végpontjait elérhetővé tegye. Ha engedélyezi az Azure Dev Spaces-t a fürtön, a következő lehetőségek közül választhat a fürt végponttípusának konfigurálásához:

* A *nyilvános* végpont, amely az alapértelmezett, nyilvános IP-címmel rendelkező be- és be- és áttérési vezérlőt telepít. A nyilvános IP-cím regisztrálva van a fürt DNS-én, így nyilvános hozzáférést biztosít a szolgáltatásokhoz egy URL-cím használatával. Ezt az URL-címet a segítségével `azds list-uris`tekintheti meg.
* A *privát* végpont egy privát IP-címmel rendelkező be- és be- és áttérési vezérlőt telepít. Magánhálózati IP-cím esetén a fürt terheléselosztója csak a fürt virtuális hálózatán belül érhető el. A terheléselosztó privát IP-címe regisztrálva van a fürt DNS-én, így a fürt virtuális hálózatán belüli szolgáltatások URL-cím használatával érhetők el. Ezt az URL-címet a segítségével `azds list-uris`tekintheti meg.
* Ha *nincs* beállítást állít be a végpontbeállításhoz, a rendszer nem telepíti a be- és éi vezérlőt. A be- ésnagykezelési vezérlő telepítése nélkül az [Azure Dev Spaces útválasztási képességei][dev-spaces-routing] nem fognak működni. Opcionálisan megvalósíthatja saját bejövő kapcsolatvezérlő-megoldását [a traefik][traefik-ingress] vagy [az NGINX][nginx-ingress]használatával, amely lehetővé teszi az útválasztási képességek újbóli működését.

A végpont beállításkonfigurálásához használja *az -e* vagy *--végpontot* az Azure Dev Spaces fürtön való engedélyezésekor. Például:

> [!NOTE]
> A végpont beállítás megköveteli, hogy az Azure CLI 2.2.0-s vagy újabb verzióját használja. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Ügyfélkövetelmények

Az Azure Dev Spaces ügyféloldali eszközök, például az Azure Dev Spaces CLI-bővítmény, a Visual Studio-kód bővítmény és a Visual Studio-bővítmény segítségével kommunikál az AKS-fürtdel a hibakereséshez. Az Azure Dev Spaces ügyféloldali eszközhasználatának használatához engedélyezze a fejlesztői gépekről az *\*azds- .azds.io* tartományba irányuló forgalmat. Lásd *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` a pontos Teljes tartomány. Ha [AZ API-kiszolgáló által engedélyezett IP-tartományokat][auth-range-section]használ, akkor engedélyeznie kell az AKS-fürthöz csatlakozó fejlesztőgépek IP-címét is az API-kiszolgálóhoz való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md