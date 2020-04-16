---
title: Gyakori kérdések az Azure dev spaces-ről
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Válaszok az Azure Dev Spaces-sel kapcsolatos gyakori kérdésekre
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
ms.openlocfilehash: b5a380f20640b9bc328aa30289ff7f915cc0b73c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414314"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Gyakori kérdések az Azure dev spaces-ről

Ez az Azure Dev Spaces-sel kapcsolatos gyakori kérdéseket címek.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>A Kubernetes mely verziói támogatottak az Azure dev spaces- ben?

Az Azure Dev Spaces támogatja a [Kubernetes összes jelenleg támogatott általános rendelkezésre állási (GA) verzióját az AKS-ben.][aks-supported-k8s]

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Mely Azure-régiók biztosítják jelenleg az Azure dev spaces-t?

Az elérhető régiók teljes listáját a [támogatott régiókban][supported-regions] láthatja.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Áttelepíthetem az AKS-fürtömet az Azure fejlesztői terezési területekkel egy másik régióba?

Igen, ha az AKS-fürtöt az Azure dev spaces-szel egy másik [támogatott régióba][supported-regions]szeretné áthelyezni, azt javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse és konfigurálja az Azure Dev Spaces-t, és telepítse az erőforrásokat és alkalmazásokat az új fürtre. Az AKS áttelepítésével kapcsolatos további információkért [lásd: Áttelepítés az Azure Kubernetes szolgáltatásba (AKS).][aks-migration]

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Használhatom az Azure dev spaces meglévő Dockerfiles vagy Helm diagramok?

Igen, ha a projekt már rendelkezik egy Dockerfile vagy egy Helm-diagrammal, használhatja ezeket a fájlokat az Azure Dev Spaces használatával. Futtatáskor `azds prep`használja a `--chart` paramétert, és adja meg a diagram helyét. Az Azure dev spaces továbbra is létrehoz egy *azds.yaml* és *Dockerfile.develop* fájlt, de nem helyettesíti vagy módosítja a meglévő Dockerfile vagy helm diagram. Előfordulhat, hogy módosítania kell az *azds.yaml* és *Dockerfile.develop* fájlokat, hogy `azds up`minden megfelelően működjön a meglévő alkalmazással futás közben.

Ha saját Dockerfile vagy Helm diagramot használ, a következő korlátozások vannak:
* Ha csak egy Docker-fájlt használ, tartalmaznia kell mindent, amire szüksége van a fejlesztési forgatókönyvek engedélyezéséhez, például az SDK nyelvét nem csak a futásidejű. Ha egy külön Docker-fájlt használ az Azure Dev Spaces, például egy Dockerfile.develop, mindent, amire szükség van a fejlesztési forgatókönyvek engedélyezéséhez szerepelnie kell a Docker-fájlban.
* A Helm diagramnak támogatnia kell a *value.yaml*.
* Ha módosítja a belső be- és a be- és bájt, frissítheti a Helm-diagramot az Azure Dev Spaces által biztosított be- ésbe- és bájkezelő megoldás használatához.
* Ha az Azure [Dev Spaces által biztosított útválasztási lehetőségeket][dev-spaces-routing]szeretné használni, az egyes projektek hez tartozó összes szolgáltatásnak egyetlen Kubernetes névtérbe kell illeszkednie, és egyszerű elnevezéssel kell üzembe helyezni, például *service-a.* A szabványos Helm-diagramokban ez az elnevezési frissítés a *fullnameOverride* tulajdonság értékének megadásával végezhető el.

Ha össze szeretné hasonlítani saját Docker-fájl- vagy Helm-diagramját egy azure dev spaces-szel működő meglévő verzióval, tekintse át a rövid útmutatóban létrehozott [fájlokat.][quickstart-cli]


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Módosíthatom az Azure Dev Spaces által létrehozott fájlokat?

Igen, módosíthatja az Azure Dev Spaces által létrehozott *azds.yaml* fájlt, Dockerfile és Helm diagramot [a projekt előkészítésekor.][dev-spaces-prep] A fájlok módosítása megváltoztatja a projekt felépítését és futtatását.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Használhatom az Azure Dev Spaces-t nyilvános IP-cím nélkül?

Nem, nyilvános IP-cím nélkül nem építheti ki az Azure dev spaces-t egy AKS-fürtön. Az Azure Dev Spaces nyilvános IP-címre [van szükség az útválasztáshoz.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Használhatom a saját be- ésémet az Azure dev spaces-szel?

Igen, konfigurálhatja a saját be- éséi a be- ésaz Azure Dev Spaces létrehozása mellett. Használhatja például [a traefik][ingress-traefik] vagy [az NGINX .][ingress-nginx]

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Használhatom a HTTPS-t az Azure dev spaces-szel?

Igen, beállíthatja saját bejövő kapcsolatát HTTPS-lel [a traefik][ingress-https-traefik] vagy [az NGINX][ingress-https-nginx]használatával.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Használhatom az Azure Dev Spaces egy fürtön, amely használja a CNI helyett kubenet? 

Igen, használhatja az Azure Dev Spaces egy AKS-fürt, amely cni hálózati használatával. Használhatja például az Azure Dev Spaces-t egy Meglévő [Windows-tárolóval][windows-containers]rendelkező AKS-fürtön, amely cni-t használ a hálózatkezeléshez. A CNI azure dev spaces-alapú hálózatba való használatáról itt olvashat [bővebben.](configure-networking.md#using-azure-cni)

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Használhatom az Azure Dev Spaces-t Windows-tárolókkal?

Jelenleg az Azure Dev Spaces célja, hogy csak Linux podokon és csomópontokon fusson, de az Azure Dev Spaces futtatható egy Meglévő [Windows-tárolókkal][windows-containers]rendelkező AKS-fürtön.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Használhatom az Azure dev spaces-t aKS-fürtökön, ha az API-kiszolgáló engedélyezett IP-címtartományai engedélyezve vannak?

Igen, használhatja az Azure Dev Spaces az AKS-fürtök [API-kiszolgáló engedélyezett IP-címtartományok][aks-auth-range] engedélyezve van. Az Azure Dev Spaces használatával engedélyezett API-kiszolgáló által engedélyezett IP-címtartományokkal rendelkező AKS-fürtök használatáról itt olvashat [bővebben.](configure-networking.md#using-api-server-authorized-ip-ranges)

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Használhatom az Azure dev spaces-t korlátozott kimenő forgalommal rendelkező AKS-fürtökön a fürtcsomópontok számára?

Igen, használhatja az Azure Dev Spaces aKS-fürtök [korlátozott kimenő forgalom fürtcsomópontok][aks-restrict-egress-traffic] engedélyezve, ha a megfelelő fqdns engedélyezve lett. Az Azure Dev Spaces szolgáltatással engedélyezett fürtcsomópontok korlátozott kimenő forgalommal rendelkező AKS-fürtök használatáról itt olvashat [bővebben.](configure-networking.md#ingress-and-egress-network-traffic-requirements)

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Használhatom az Azure dev spaces-t RBAC-kompatibilis AKS-fürtökön?

Igen, használhatja az Azure Dev Spaces-t az AKS-fürtökön, rbac engedélyezve vagy anélkül.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Mi történik, ha engedélyezem a projekthez való be- és bevitelt a Visual Studióban?

Ha a Visual Studio segítségével készíti elő a projektet, lehetősége van engedélyezni a szolgáltatás betöltését. A be- ésfürt engedélyezése nyilvános végpontot hoz létre a szolgáltatás eléréséhez, amikor az AKS-fürtön fut, ami nem kötelező. Ha nem engedélyezi a be- és énekbben, a szolgáltatás csak az AKS-fürtből érhető el.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Használhatom pod felügyelt identitások az Azure dev spaces?

Igen, pod [felügyelt identitások][aks-pod-managed-id] az AKS-fürtök en az Azure Dev Spaces engedélyezve van, de vannak [további konfigurációs lépések,][dev-spaces-pod-managed-id-steps] miután engedélyezte az Azure Dev Spaces a fürtpod által felügyelt identitások. Ha telepítve van a pod által felügyelt identitások, és szeretné eltávolítani, további részleteket az [eltávolítási megjegyzésekben][aks-pod-managed-id-uninstall]talál.

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Használhatom az Azure Dev Spaces-t több mikroszolgáltatással egy alkalmazásban?

Igen, használhatja az Azure Dev Spaces egy alkalmazás több mikroszolgáltatások, de elő kell készítenie, és az egyes mikroszolgáltatások a gyökér. Az Azure Dev Spaces CLI, az Azure Dev Spaces VS Code bővítmény és a Visual Studio Azure Development számítási feladatok elvárják, hogy az *azds.yaml* fájl legyen a mikroszolgáltatás gyökere a futtatás és hibakeresés érdekében. Tekintse meg a [Bike Sharing mintaalkalmazás][bike-sharing] egy példa több mikroszolgáltatások egyetlen alkalmazásban.

A Visual Studio-kódban lehetőség van [külön projektek megnyitására egyetlen munkaterületen,][vs-code-multi-root-workspaces] és az Azure Dev Spaces-en keresztül külön-külön hibakeresésük. Minden projektnek önállónak kell lennie, és elő kell készítenie az Azure Dev Spaces számára.

A Visual Studio-ban konfigurálható a .NET Core megoldások az Azure Dev Spaces-en keresztüli hibakereséshez.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Használhatom az Azure Dev Spaces szolgáltatáshálóval?

Jelenleg nem használhatja az Azure Dev Spaces szolgáltatást nyújtó szemekkel, például [Istio][istio] vagy [Linkerd.][linkerd] Futtathatja az Azure Dev Spaces és a szolgáltatásháló ugyanazon az AKS-fürtön, de nem lehet mind az Azure Dev Spaces és a szolgáltatás háló javunkra ugyanabban a névtérben.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#list-currently-supported-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md