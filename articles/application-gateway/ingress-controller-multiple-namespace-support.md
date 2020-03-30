---
title: Több névtér támogatás engedélyezése az Application Gateway ingress controllerhez
description: Ez a cikk arról nyújt tájékoztatást, hogy miként engedélyezheti a több névtér támogatását egy Kubernetes-fürtben egy Application Gateway ingress controllerrel.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279922"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Több névtértámogatás engedélyezése AKS-fürtben az Application Gateway bejövő adatszolgáltatás-vezérlővel

## <a name="motivation"></a>Motiváció
A [Kubernetes-névterek](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) lehetővé teszik a Kubernetes-fürt particionálását és egy nagyobb csoport alcsoportjai számára való lefoglalását. Ezek az alcsapatok ezután telepíthetik és kezelhetik az infrastruktúrát az erőforrások, a biztonság, a konfiguráció stb. A Kubernetes lehetővé teszi egy vagy több virtuális erőforrás egymástól függetlendefiniálását az egyes névtéren belül.

A [0.7-es verziójú Azure-alkalmazásátjáró kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) képes betöltése események és több névterek megfigyelése. Ha az AKS-rendszergazda úgy dönt, hogy [az App Gateway-t](https://azure.microsoft.com/services/application-gateway/) használja be- és be- és bekerítésként, az összes névtér az Application Gateway ugyanazon példányát fogja használni. A Beress Controller egyetlen telepítése figyeli a hozzáférhető névtereket, és konfigurálja a társított alkalmazásátjárót.

Az AGIC 0.7-es verziója továbbra `default` is kizárólag a névteret figyeli, kivéve, ha ez a Helm konfigurációban egy vagy több különböző névtérre kifejezetten módosul (lásd az alábbi szakaszt).

## <a name="enable-multiple-namespace-support"></a>Több névtér támogatásának engedélyezése
Több névtér támogatásának engedélyezése:
1. módosítsa a [helm-config.yaml](#sample-helm-config-file) fájlt az alábbi módokon:
   - törölje `watchNamespace` a kulcsot teljes egészében [helm-config.yaml](#sample-helm-config-file) - AGIC fogja betartani az összes névterek
   - üres `watchNamespace` karakterláncra állítva - Az AGIC az összes névteret figyeli
   - több névtér hozzáadása vesszővel elválasztva (`watchNamespace: default,secondNamespace`) - Az AGIC kizárólag ezeket a névtereket fogja megfigyelni
2. helm sablonmódosítás alkalmazása a következőkkel:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

A több névtér megfigyelésének lehetővé tévő telepítését követően az AGIC:
  - lista be- és be- és beérkező erőforrások listája az összes elérhető névtérből
  - szűrő tegyünk a jegyzetekkel ellátott erőforrásokhoz`kubernetes.io/ingress.class: azure/application-gateway`
  - kombinált [Application Gateway konfiguráció összeállítása](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - alkalmazza a konfigurációt a kapcsolódó Application Gateway-re az [ARM-on](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) keresztül

## <a name="conflicting-configurations"></a>Ütköző konfigurációk
Több névtérbeli [be- éséjátékos erőforrás](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) utasíthatja az AGIC-et, hogy egyetlen Application Gateway ütköző konfigurációit hozza létre. (Két be- ésvételi csoport például ugyanazt a tartományt követeli.)

A hierarchia tetején – **figyelők** (IP-cím, port és állomás) és **útválasztási szabályok** (kötésfigyelő, háttérkészlet és HTTP-beállítások) több névterek/bejövő műveletek hozhatók létre és oszthatók meg.

Másrészt – az elérési utakat, a háttérkészleteket, a HTTP-beállításokat és a TLS-tanúsítványokat csak egy névtér hozhatja létre, és az ismétlődések törlődnek.

Vegyük például a következő ismétlődő, duplikált, bekövetkező erőforrásokat definiált névterekre `staging` és `production` a következőre: `www.contoso.com`
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Annak ellenére, `www.contoso.com` hogy a két be- éserőforrás,amely forgalmat követel a megfelelő Kubernetes névterekhez, csak egy háttérrendszer tudja kiszolgálni a forgalmat. Az AGIC az egyik erőforrás "első kiszolgálása" alapján hozna létre konfigurációt. Ha egyszerre két bejövő erőforrás jön létre, az ábécé korábbi erőforrása lesz az elsőbbség. A fenti példából csak a `production` be- és a be- és áttérés beállításait tudjuk létrehozni. Az Application Gateway a következő erőforrásokkal lesz konfigurálva:

  - Hallgató:`fl-www.contoso.com-80`
  - Útválasztási szabály:`rr-www.contoso.com-80`
  - Háttérkészlet:`pool-production-contoso-web-service-80-bp-80`
  - HTTP-beállítások:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Egészségügyi szonda:`pb-production-contoso-web-service-80-websocket-ingress`

Vegye figyelembe, hogy a *figyelő* és *az útválasztási szabály*kivételével`production`a létrehozott Application Gateway-erőforrások tartalmazzák annak a névtérnek a nevét ( ), amelyhez létrehozták őket.

Ha a két bejövő forgalomerőforrás különböző időpontban kerül be az AKS-fürtbe, akkor valószínű, hogy az AGIC egy olyan forgatókönyvhöz `namespace-B` `namespace-A`vezet, amelyben újrakonfigurálja az Application Gateway-t, és átirányítja a forgalmat a rendszerbe.

Ha például `staging` először ad hozzá, az AGIC konfigurálja az Application Gateway-t, hogy a forgalmat az átmeneti háttérkészlethez irányítsa. Egy későbbi szakaszban, bevezetése `production` befelé irányuló, hatására AGIC átprogramozni Application `production` Gateway, amely elindítja a forgalmat a háttérkészlet.

## <a name="restrict-access-to-namespaces"></a>Hozzáférés korlátozása névterekhez
Alapértelmezés szerint az AGIC az Application Gateway-t bármely névtérben jegyzetekhez jegyzetekhez tartozó adatok alapján konfigurálja. Ha korlátozni szeretné ezt a viselkedést, a következő lehetőségek közül választhat:
  - korlátozza a névterek, kifejezetten definiálva névterek AGIC meg kell tartania keresztül `watchNamespace` YAML kulcs [helm-config.yaml](#sample-helm-config-file)
  - [szerepkör/szerepkörkötés](https://docs.microsoft.com/azure/aks/azure-ad-rbac) használata az AGIC adott névterekre való korlátozásához

## <a name="sample-helm-config-file"></a>Minta Helm konfigurációs fájl
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

