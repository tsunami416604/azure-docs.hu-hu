---
title: Hálózatkezelés konfigurálása az Azure dev Spaces szolgáltatáshoz különböző hálózati topológiákban
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Ismerteti az Azure dev Spaces Azure Kubernetes Servicesben való futtatásának hálózati követelményeit
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, CNI, kubenet, SDN, hálózat
ms.openlocfilehash: 51604e2862a4d2ff575906fa2ba480ddd10504ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897922"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Hálózatkezelés konfigurálása az Azure dev Spaces szolgáltatáshoz különböző hálózati topológiákban

Az Azure dev Spaces az alapértelmezett hálózati konfigurációval rendelkező Azure Kubernetes Service (ak) fürtökön fut. Ha módosítani szeretné az AK-fürt hálózati konfigurációját, például a fürt tűzfal mögötti üzembe helyezését, hálózati biztonsági csoportok használatával vagy hálózati házirendek használatával, további szempontokat kell figyelembe vennie az Azure dev Spaces futtatásához.

![Virtuális hálózati konfiguráció](media/configure-networking/virtual-network-clusters.svg)

## <a name="aks-clusters-with-different-virtual-network-or-subnet-configurations"></a>AK-fürtök eltérő virtuális hálózattal vagy alhálózat-konfigurációval

Az AK-fürt eltérő virtuális hálózattal vagy alhálózat-konfigurációval rendelkezhet, hogy korlátozza a bejövő vagy kimenő forgalmat az AK-fürt számára. Előfordulhat például, hogy a fürt tűzfal mögött található, például a Azure Firewall, vagy hálózati biztonsági csoportokat vagy egyéni szerepköröket használ a hálózati forgalom korlátozásához.

Az Azure dev Spaces szolgáltatásban a *bejövő és a kimenő* hálózati forgalomra, valamint a *csak* forgalomra vonatkozó követelmények vonatkoznak. Ha az Azure dev Spaces szolgáltatást egy AK-fürtön használja, és olyan virtuális hálózatot vagy alhálózati konfigurációt használ, amely korlátozza az AK-fürt forgalmát, csak a következő bejövő adatokat és a bejövő forgalomra vonatkozó követelményeket kell követnie ahhoz, hogy az Azure dev Spaces megfelelően működik.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Bejövő és kimenő hálózati forgalomra vonatkozó követelmények

Az Azure dev Spaces szolgáltatásnak a következő teljes tartománynevek felé irányuló bejövő és kimenő forgalomra van szüksége:

| FQDN                       | Port       | Használat      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-rendszerképek lekérése az Azure dev Spaces szolgáltatásban |
| gcr.io                     | HTTPS: 443 | Helm-lemezképek lekérése az Azure dev Spaces szolgáltatásban |
| storage.googleapis.com     | HTTPS: 443 | Helm-lemezképek lekérése az Azure dev Spaces szolgáltatásban |
| azds-*. azds. IO             | HTTPS: 443 | Kommunikáció az Azure dev Spaces háttér-szolgáltatásaival az Azure dev Spaces vezérlőhöz. A pontos teljes tartománynevet a *dataplaneFqdn* -ben találja `USERPROFILE\.azds\settings.json` |

Frissítse a tűzfalat vagy a biztonsági konfigurációt, hogy engedélyezze a fenti teljes tartománynevek és azok közötti hálózati forgalmat. Ha például tűzfalat használ a hálózat biztonságossá tételéhez, a fenti FQDN-ket hozzá kell adni a tűzfal alkalmazási szabályához, hogy engedélyezzék az ezen tartományokra irányuló és onnan érkező forgalmat.

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények beáramlása

Az Azure dev Spaces lehetővé teszi a Kubernetes, valamint a szolgáltatásokhoz való nyilvános hozzáférést a saját FQDN használatával. Mindkét funkció működéséhez frissítse a tűzfalat vagy a hálózati konfigurációt, hogy lehetővé tegye a nyilvános behatolást a fürtön lévő Azure dev Spaces bemenő vezérlő külső IP-címére. Azt is megteheti, hogy létrehoz egy [belső][aks-internal-lb] terheléselosztó szolgáltatást, és felvesz egy NAT-szabályt a tűzfalon a TŰZFAL nyilvános IP-címének lefordításához a belső terheléselosztó IP-címére. A [traefik][traefik-ingress] és az [NGINX][nginx-ingress] használatával is létrehozhat egy egyéni bejövő vezérlőt.

## <a name="aks-cluster-network-requirements"></a>AK-fürt hálózati követelményei

Az AK lehetővé teszi, hogy [hálózati házirendekkel][aks-network-policies] vezérelje a bejövő és kimenő adatforgalmat a fürtben lévő hüvelyek között, valamint a kimenő forgalmat egy Pod-ból. Az Azure dev Spaces szolgáltatásban a *bejövő és a kimenő* hálózati forgalomra, valamint a *csak* forgalomra vonatkozó követelmények vonatkoznak. Ha az Azure dev Spaces-t egy AK-os hálózati házirendekkel rendelkező AK-fürtön használja, akkor az Azure dev Spaces megfelelő működéséhez az alábbi bejövő adatokat és a kimenő forgalomra vonatkozó követelményeket is be kell tartania.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Bejövő és kimenő hálózati forgalomra vonatkozó követelmények

Az Azure dev Spaces lehetővé teszi, hogy a hibakereséshez közvetlenül kommunikáljon a fürt fejlesztői területein található Pod-mel. Ennek a funkciónak a működéséhez adjon hozzá egy hálózati házirendet, amely lehetővé teszi a bejövő és kimenő kommunikációt az Azure dev Spaces infrastruktúrájának IP-címeire, amelyek [régiónként eltérőek][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények beáramlása

Az Azure dev Spaces lehetővé teszi a hüvelyek közötti útválasztást a névterek között. Például az Azure dev Spaces szolgáltatásban engedélyezett névterek szülő/gyermek kapcsolattal rendelkezhetnek, ami lehetővé teszi a hálózati forgalom átirányítását a hüvelyek között a szülő és a gyermek névterek között. Ahhoz, hogy ez a funkció működjön, adjon hozzá egy hálózati házirendet, amely lehetővé teszi a hálózati forgalom irányítása alatt álló névterek, például a szülő/gyermek névterek közötti adatforgalmat. Továbbá, ha a beléptetési vezérlő a *azds* -névtérbe van telepítve, akkor a bejövő adatkezelőnek kommunikálnia kell az Azure fejlesztői területtel rendelkező, másik névtérben található hüvelyekkel. Ahhoz, hogy a bejövő vezérlő megfelelően működjön, a hálózati forgalmat engedélyezni kell a *azds* névtérből arra a névtérre, ahol a műszeres hüvely fut.

## <a name="using-azure-container-networking-with-azure-dev-spaces"></a>Az Azure Container hálózatkezelés használata az Azure dev Spaces használatával

Alapértelmezés szerint az AK-fürtök úgy vannak konfigurálva, hogy a [kubenet][aks-kubenet] használják a hálózatkezeléshez, ami az Azure dev Spaces szolgáltatással működik. Azt is megteheti, hogy az AK-fürtöt az [Azure Container Network Interface (CNI)][aks-cni]használatára konfigurálja. Ha az Azure dev Spaces-t az AK-fürtön lévő Azure CNI szeretné használni, engedélyezze a virtuális hálózat és az alhálózati címtartomány akár 10 magánhálózati IP-címet az Azure dev Spaces által üzembe helyezett hüvelyek számára. A magánhálózati IP-címek engedélyezésével kapcsolatos további részletek az [AK Azure CNI dokumentációjában][aks-cni-ip-planning]találhatók.

## <a name="using-api-server-authorized-ip-ranges-with-azure-dev-spaces"></a>Az API Server által jóváhagyott IP-címtartományok használata az Azure dev Spaces használatával

Az AK-fürtök lehetővé teszik olyan további biztonsági beállítások konfigurálását, amelyek korlátozzák, hogy az IP-címek hogyan kezelhetik a fürtöket, például egyéni virtuális hálózatok használatával vagy [az API-kiszolgálóhoz engedélyezett IP-címtartományok használatával történő hozzáférés biztosításával][aks-ip-auth-ranges]. Ha a fürt [létrehozása][aks-ip-auth-range-create] során további biztonságot kíván használni az Azure dev Spaces szolgáltatásban, akkor a [régión alapuló további tartományokat][dev-spaces-ip-auth-range-regions]is engedélyeznie kell. Egy meglévő fürtöt is [frissíthet][aks-ip-auth-range-update] , hogy engedélyezze ezeket a további tartományokat. Emellett engedélyeznie kell az AK-fürthöz csatlakozó fejlesztői gépek IP-címét, hogy hibakeresést végezzenek az API-kiszolgálóhoz való kapcsolódáshoz.

## <a name="using-aks-private-clusters-with-azure-dev-spaces"></a>Az AK-beli privát fürtök használata az Azure dev Spaces használatával

Ebben az esetben az Azure fejlesztői tárhelyek nem támogatottak az AK-beli [privát fürtökön][aks-private-clusters].

## <a name="azure-dev-spaces-client-requirements"></a>Az Azure dev Spaces ügyfélre vonatkozó követelmények

Az Azure dev Spaces ügyféloldali eszközöket használ, például az Azure dev Spaces CLI-bővítményt, a Visual Studio Code-bővítményt és a Visual Studio-bővítményt, hogy a hibakereséshez kommunikáljon az AK-fürttel. Az Azure dev Spaces ügyféloldali eszközeinek használatához engedélyezze a fejlesztési gépekről érkező forgalmat a *azds-\*. azds.IO* tartományba. A teljes tartománynevet a `USERPROFILE\.azds\settings.json` *dataplaneFqdn* című részben tekintheti meg. Ha az [API-kiszolgáló által engedélyezett IP-tartományokat][auth-range-section]használja, engedélyeznie kell az AK-fürthöz csatlakozó fejlesztői gépek IP-címét is, hogy hibakeresést VÉGEZZEN az API-kiszolgálóhoz való kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges-with-azure-dev-spaces
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md