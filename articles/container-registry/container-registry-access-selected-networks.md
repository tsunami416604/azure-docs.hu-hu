---
title: A szolgáltatás tűzfalszabály-szabályainak konfigurálása
description: Konfigurálja az IP-szabályokat úgy, hogy engedélyezze az Azure Container Registry elérését a kiválasztott nyilvános IP-címekről vagy címtartományok közül.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984615"
---
# <a name="configure-public-ip-network-rules"></a>Nyilvános IP-hálózati szabályok konfigurálása

Az Azure Container Registry alapértelmezés szerint minden hálózaton fogadja az interneten keresztüli kapcsolatokat. Ez a cikk bemutatja, hogyan konfigurálhatja a tároló-beállításjegyzéket úgy, hogy csak bizonyos nyilvános IP-címekről vagy címtartományokről engedélyezze a hozzáférést. Az Azure CLI és a Azure Portal használatával egyenértékű lépések vannak megadva.

Az IP-hálózati szabályok a nyilvános beállításjegyzékbeli végponton vannak konfigurálva. Az IP-hálózati szabályok nem vonatkoznak a [privát kapcsolattal](container-registry-private-link.md) konfigurált privát végpontokra

Az IP-hozzáférési szabályok konfigurálása a **prémium** szintű tároló beállításjegyzék-szolgáltatási szintjén érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry rétegek](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Hozzáférés a kiválasztott nyilvános hálózatról – parancssori felület

### <a name="change-default-network-access-to-registry"></a>A beállításjegyzék alapértelmezett hálózati hozzáférésének módosítása

A kiválasztott nyilvános hálózathoz való hozzáférés korlátozásához először módosítsa az alapértelmezett műveletet, hogy megtagadja a hozzáférést. Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Használja az az [ACR Network-Rule Add][az-acr-network-rule-add] parancsot egy olyan hálózati szabály hozzáadásához a beállításjegyzékhez, amely engedélyezi a hozzáférést egy nyilvános IP-címről vagy-tartományból. Helyettesítse be például a tároló beállításjegyzékének nevét és egy virtuális gép nyilvános IP-címét egy virtuális hálózatban.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Egy szabály hozzáadása után a szabály érvénybe léptetése néhány percet vesz igénybe.

## <a name="access-from-selected-public-network---portal"></a>Hozzáférés a kiválasztott nyilvános hálózatról – portál

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások**területen válassza a **hálózatkezelés**lehetőséget.
1. A **nyilvános hozzáférés** lapon válassza a lehetőséget, hogy engedélyezze a nyilvános hozzáférést a **kiválasztott hálózatokból**.
1. A **tűzfal**területen adjon meg egy nyilvános IP-címet, például egy virtuális gép nyilvános IP-címét. Vagy adjon meg egy címtartományt a CIDR-jelölésben, amely a virtuális gép IP-címét tartalmazza.
1. Kattintson a **Mentés** gombra.

![Tűzfalszabály konfigurálása a tároló-beállításjegyzékhez][acr-access-selected-networks]

> [!NOTE]
> Egy szabály hozzáadása után a szabály érvénybe léptetése néhány percet vesz igénybe.

> [!TIP]
> Ha szeretné, engedélyezze a beállításjegyzék-hozzáférést egy helyi ügyfélszámítógép vagy IP-címtartomány alapján. A hozzáférés engedélyezéséhez a számítógép nyilvános IPv4-címe szükséges. Ezt a címet a "mi az IP-cím" kifejezéssel keresheti meg egy böngészőben. Az aktuális ügyfél IPv4-címe is automatikusan megjelenik a tűzfalbeállítások konfigurálásakor a portál **hálózatkezelés** lapján.

## <a name="disable-public-network-access"></a>Nyilvános hálózati hozzáférés letiltása

Ha a virtuális hálózatokra vonatkozó forgalmat [privát kapcsolaton](container-registry-private-link.md)keresztül szeretné korlátozni, tiltsa le a nyilvános végpontot a beállításjegyzékben. A nyilvános végpont letiltása felülbírálja az összes tűzfal-konfigurációt.

### <a name="disable-public-access---portal"></a>Nyilvános hozzáférés letiltása – portál

1. A portálon navigáljon a tároló beállításjegyzékéhez, és válassza a **beállítások > hálózatkezelés**lehetőséget.
1. A **nyilvános hozzáférés** lap nyilvános **hozzáférés engedélyezése**területén válassza a **Letiltva**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

![Nyilvános hozzáférés letiltása][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása

Ha úgy szeretné visszaállítani a beállításjegyzéket, hogy az alapértelmezés szerint engedélyezze a hozzáférést, frissítse az alapértelmezett műveletet. 

### <a name="restore-default-registry-access---portal"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása – portál

1. A portálon navigáljon a tároló beállításjegyzékéhez, és válassza a **beállítások > hálózatkezelés**lehetőséget.
1. A **tűzfal**területen válassza ki az egyes címtartományt, majd kattintson a Törlés ikonra.
1. A **nyilvános hozzáférés** lap nyilvános **hozzáférés engedélyezése**területén válassza a **minden hálózat**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

![Nyilvános hozzáférés az összes hálózatról][acr-access-all-networks]

## <a name="next-steps"></a>További lépések

* Ha egy virtuális hálózat privát végpontján keresztül szeretné korlátozni a beállításjegyzékhez való hozzáférést, tekintse meg az [Azure Private-hivatkozás konfigurálása Azure Container registryhez](container-registry-private-link.md)című témakört.
* Ha be kell állítania a beállításjegyzék-hozzáférési szabályokat az ügyfél tűzfala mögött, tekintse meg a [szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)című témakört.

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
