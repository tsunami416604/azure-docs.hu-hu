---
title: Gyakran ismételt kérdések az Azure dev Spaces-ről
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Válaszok az Azure dev Spaces használatával kapcsolatos gyakori kérdésekre
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 964fa9ec4948bf178c310af8e35913fda5f70c0f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934184"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Gyakran ismételt kérdések az Azure dev Spaces-ről

Ez az Azure dev Spaces-re vonatkozó gyakori kérdéseket tárgyalja.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Mely Azure-régiók biztosítanak Azure dev Spaces-helyeket?

Az elérhető régiók teljes listájáért tekintse meg a [támogatott régiók][supported-regions] című témakört.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Használhatom az Azure dev Spaces szolgáltatást meglévő Dockerfiles vagy Helm-diagramok segítségével?

Igen, ha a projekt már rendelkezik egy Docker vagy egy Helm-diagrammal, ezeket a fájlokat használhatja az Azure dev Spaces használatával. `azds prep`futtatásakor használja a `--chart` paramétert, és adja meg a diagram helyét. Az Azure dev Spaces továbbra is létrehozza a *azds. YAML* és a *Docker. fejlessze* a fájlt, de nem cseréli le és nem módosítja a meglévő Docker vagy a Helm diagramot. Előfordulhat, hogy módosítania kell a *azds. YAML* és a *Docker.* fájlokat, hogy minden megfelelően működjön a meglévő alkalmazással `azds up`futtatásakor.

Saját Docker vagy Helm-diagram használata esetén a következő korlátozások érvényesek:
* Ha csak egy Docker használ, tartalmaznia kell mindazt, amire szüksége lehet a fejlesztési forgatókönyvek engedélyezéséhez, például a nyelvi SDK-t, nem csak a futtatókörnyezetet. Ha külön Docker használ az Azure dev Spaces szolgáltatáshoz, például egy Docker. fejlesszen, a fejlesztési forgatókönyvek engedélyezéséhez szükséges mindennek szerepelnie kell a Docker.
* A Helm-diagramnak támogatnia kell a vagy a teljes képcímkét a Values *. YAML*értékének átadásával.
* Ha bármit módosít a bejövő forgalomban, akkor a Helm diagramot is frissítheti az Azure dev Spaces által biztosított bejövő megoldás használatára.
* Ha az [Azure dev Spaces által biztosított útválasztási funkciókat][dev-spaces-routing]szeretné használni, az egyes projektekhez tartozó összes szolgáltatásnak egyetlen Kubernetes-névtérben kell lennie, és egyszerű elnevezéssel kell rendelkeznie, például a *Service-a szolgáltatáshoz*. A standard Helm-diagramok esetében ez az elnevezési frissítés a *fullnameOverride* tulajdonság értékének megadásával végezhető el.

Ha össze szeretné hasonlítani a saját Docker vagy Helm-diagramját az Azure dev Spaces szolgáltatással működő meglévő verzióval, tekintse [át a gyors][quickstart-cli]útmutatóban létrehozott fájlokat.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Módosíthatom az Azure dev Spaces által generált fájlokat?

Igen, módosíthatja az [Azure dev Spaces által generált][dev-spaces-prep] *azds. YAML* fájlt, Docker és Helm diagramot a projekt előkészítésekor. A fájlok módosítása megváltoztatja a projekt felépítésének és futtatásának módját.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Használhatom nyilvános IP-cím nélkül az Azure dev Spaces szolgáltatást?

Nem, nyilvános IP-cím nélkül nem lehet Azure dev-helyeket kiépíteni egy AK-fürtön. Az [Azure dev Spaces for Routing][dev-spaces-routing]szolgáltatáshoz nyilvános IP-cím szükséges.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Használhatom a saját beáramlást az Azure dev Spaces szolgáltatással?

Igen, a bejövő Azure dev Spaces létrehozásával párhuzamosan is konfigurálhatja a belépőket. Használhatja például a [traefik][ingress-traefik] vagy az [NGINX][ingress-nginx]-et.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Használhatom a HTTPS-t az Azure dev Spaces használatával?

Igen, a [traefik][ingress-https-traefik] vagy [NGINX][ingress-https-nginx]használatával konfigurálhatja saját bejövő adatait HTTPS-kapcsolaton keresztül.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Használhatom az Azure dev Spaces szolgáltatást olyan fürtön, amely nem kubenet, hanem CNI használ? 

Igen, használhatja az Azure dev Spaces szolgáltatást egy olyan AK-fürtön, amely CNI használ a hálózatkezeléshez. Használhatja például az Azure dev Spaces szolgáltatást egy AK-fürtön [meglévő Windows-tárolókkal][windows-containers], amelyek hálózati CNI használnak. A CNI és az Azure dev Spaces közötti hálózatkezeléssel kapcsolatos további információk [itt](configure-networking.md#using-azure-cni)érhetők el.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Használhatom az Azure dev Spaces szolgáltatást Windows-tárolókkal?

Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon fut, de az Azure fejlesztői tárhelyeit egy AK-fürtön futtathatja [meglévő Windows-tárolók][windows-containers]használatával.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Használhatok Azure dev Spaces-et az AK-fürtökön, ha engedélyezve vannak az API Server által engedélyezett IP-címtartományok?

Igen, az Azure dev-szóközöket az AK-fürtökön használhatja az [API-kiszolgáló által engedélyezett IP-címtartományok][aks-auth-range] engedélyezésével. További információ az Azure dev Spaces szolgáltatással rendelkező, az API Server által engedélyezett IP-címtartományok használatával rendelkező AK-fürtök használatáról [itt](configure-networking.md#using-api-server-authorized-ip-ranges)található.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Használhatom az Azure dev Spaces-t az AK-fürtökön a fürtcsomópontok korlátozott kimenő forgalmával?

Igen, a megfelelő teljes tartománynevek engedélyezése után használhatja az Azure dev-szóközöket az AK-fürtökön a [korlátozott kimenő forgalommal rendelkező fürtcsomópontok esetében][aks-restrict-egress-traffic] . [Itt](configure-networking.md#ingress-and-egress-network-traffic-requirements)talál további információt arról, hogyan használhatók a korlátozott kimenő forgalommal rendelkező AK-fürtök az Azure dev Spaces szolgáltatással engedélyezett fürtcsomópontok esetében.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md