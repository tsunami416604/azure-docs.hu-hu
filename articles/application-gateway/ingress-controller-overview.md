---
title: Mi az Azure Application Gateway inbehatolás vezérlő?
description: Ez a cikk bemutatja, hogy milyen Application Gateway beléptetési vezérlő.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668100"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Mi az Application Gateway beáramló vezérlő?
A Application Gateway beáramlási vezérlő (AGIC) egy Kubernetes-alkalmazás, amely lehetővé teszi, hogy az [Azure Kubernetes Service (ak)](https://azure.microsoft.com/services/kubernetes-service/) ügyfelei kihasználják az azure natív [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 Load-balancert a felhőalapú szoftverek interneten való elérhetővé tételéhez. A AGIC figyeli az üzemeltetett Kubernetes-fürtöt, és folyamatosan frissíti egy Application Gateway, hogy a kiválasztott szolgáltatások elérhetők legyenek az internethez.

A bejövő vezérlő a saját Pod-on fut az ügyfél AK-ban. A AGIC a Kubernetes-erőforrások egy részhalmazát figyeli a változásokhoz. Az AK-fürt állapota Application Gateway meghatározott konfigurációra van lefordítva, és a [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)alkalmazásra lett alkalmazva.

## <a name="benefits-of-application-gateway-ingress-controller"></a>A Application Gateway beáramlási vezérlő előnyei
A AGIC segít kiküszöbölni, hogy szükség van-e egy másik terheléselosztó/nyilvános IP-cím létrehozására az AK-fürt előtt, és elkerüli a több ugrást a DataPath, mielőtt a kérelmek elérnék az AK-fürtöt. A Application Gateway a saját saját IP-címével beszél a hüvelyekről, és nem igényli a NodePort vagy a KubeProxy szolgáltatás használatát. Ez az üzembe helyezések jobb teljesítményét is lehetővé teszi.

A beáramló vezérlőt kizárólag Standard_v2 és WAF_v2 SKU támogatja, ami az automatikus skálázási előnyökkel is jár. Application Gateway reagálhat a forgalom terhelésének növekedésére vagy csökkenésére, és ennek megfelelően méretezhető, anélkül, hogy az AK-fürtből erőforrásokat kellene használnia.

A AGIC mellett a Application Gateway használatával a TLS-házirend és a webalkalmazási tűzfal (WAF) funkciójának segítségével is megvédheti az AK-fürtöt.

![Azure Application Gateway + AK](./media/application-gateway-ingress-controller-overview/architecture.png)

A AGIC a Kubernetes beáramlási [erőforrással](https://kubernetes.io/docs/user-guide/ingress/), valamint a szolgáltatás és az üzembe helyezések/hüvelyek használatával konfigurálható. Számos funkciót kínál, az Azure natív Application Gateway L7 terheléselosztó használatával. Csak néhányat említsünk:
  - URL-útválasztás
  - Cookie-alapú affinitás
  - TLS-lezárás
  - Végpontok közötti TLS
  - Nyilvános, privát és hibrid webhelyek támogatása
  - Integrált webalkalmazási tűzfal

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>A Helm üzembe helyezése és az AK-bővítmény közötti különbség
Kétféle módon telepíthet AGIC az AK-fürthöz. Az első módszer a Helm; a második az AK-n keresztül bővítmény. Az AGIC-k AK-bővítményként való üzembe helyezésének elsődleges előnye, hogy sokkal egyszerűbb, mint a Helmon keresztül történő üzembe helyezés. Új telepítés esetén telepíthet egy új Application Gatewayt és egy új AK-fürtöt, amely AGIC engedélyezhető az Azure CLI egyik sorában. A bővítmény egy teljes körűen felügyelt szolgáltatás is, amely további előnyöket biztosít, például az automatikus frissítéseket és a megnövekedett támogatást. A Helm használatával központilag telepített AGIC nem támogatottak az AK-ban, de az AK-bővítményként üzembe helyezett AGIC is támogatja. 

A AGIC-bővítmény továbbra is Pod-ként van telepítve az ügyfél AK-beli fürtjében, de van néhány különbség a Helm üzembe helyezési verziója és a AGIC kiegészítő verziója között. Az alábbi lista a két verzió közötti különbségeket sorolja fel: 
  - A Helm központi telepítési értékei nem módosíthatók az AK bővítményben:
    - `verbosityLevel`Alapértelmezés szerint 5 értékre lesz állítva
    - `usePrivateIp`Alapértelmezés szerint hamis értékre lesz állítva. Ezt felülírhatja a [use-Private-IP Megjegyzés](ingress-controller-annotations.md#use-private-ip)
    - `shared`a bővítmény nem támogatja 
    - `reconcilePeriodSeconds`a bővítmény nem támogatja
    - `armAuth.type`a bővítmény nem támogatja
  - A Helm által központilag telepített AGIC támogatja a ProhibitedTargets-t, ami azt jelenti, hogy a AGIC a többi meglévő háttérrendszer befolyásolása nélkül konfigurálhatja a Application Gateway. A AGIC bővítmény jelenleg nem támogatja ezt. 
  - Mivel a AGIC-bővítmény felügyelt szolgáltatás, a rendszer automatikusan frissíti az ügyfeleket a AGIC-bővítmény legújabb verziójára, a AGIC-on keresztül központilag telepített, az ügyfél által manuálisan frissíteni kívánt AGIC. 

> [!NOTE]
> A AGIC AK-bővítményének üzembe helyezési módszere jelenleg előzetes verzióban érhető el. Nem javasoljuk, hogy éles környezetben futó számítási feladatokat még előzetes verzióban is futtasson, így ha kíváncsi rá, javasoljuk, hogy hozzon létre egy új fürtöt a kipróbálásához. 

A következő táblázatok rendezik, hogy jelenleg milyen forgatókönyvek támogatottak a Helm üzembe helyezési verziójával és a AGIC AK-bővítményének verziójával. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AK-bővítmény AGIC (egyetlen AK-fürt)
|                  |1 Application Gateway |2 + Application Gateway-átjárók |
|------------------|---------|--------|
|**1 AGIC**|Igen, ez támogatott |Nem, ez a várakozó fájlok |
|**2 + AGICs**|Nem, csak 1 AGIC támogatott/fürt |Nem, csak 1 AGIC támogatott/fürt |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm központilag telepített AGIC (egyetlen AK-fürt)
|                  |1 Application Gateway |2 + Application Gateway-átjárók |
|------------------|---------|--------|
|**1 AGIC**|Igen, ez támogatott |Nem, ez a várakozó fájlok |
|**2 + AGICs**|Megosztott ProhibitedTarget funkciót kell használnia, és meg kell néznie a különálló névtereket. |Igen, ez támogatott |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm központilag telepített AGIC (2 + AK-fürtök)
|                  |1 Application Gateway |2 + Application Gateway-átjárók |
|------------------|---------|--------|
|**1 AGIC**|N.A. |N.A. |
|**2 + AGICs**|Megosztott ProhibitedTarget funkciót kell használnia |N.A. |

## <a name="next-steps"></a>További lépések
- [**AK-bővítmény zöldmezős üzembe helyezése**](tutorial-ingress-controller-add-on-new.md): a AGIC-bővítmény, az AK és a Application Gateway telepítésére vonatkozó utasítások üres-pala infrastruktúrán.
- [**AK-bővítmény rozsdaövezetek rehabilitálása telepítése**](tutorial-ingress-controller-add-on-existing.md): telepítse a AGIC bővítményt egy AK-fürtön egy meglévő Application Gateway.
- [**Helm Greenfield üzembe helyezése**](ingress-controller-install-new.md): telepítse a AGIC-t a Helm, az új AK-fürt és az új Application Gateway az üres-pala infrastruktúrán keresztül.
- [**Helm rozsdaövezetek rehabilitálása üzembe helyezése**](ingress-controller-install-existing.md): AGIC üzembe helyezése egy meglévő AK-fürtön és Application Gateway.

