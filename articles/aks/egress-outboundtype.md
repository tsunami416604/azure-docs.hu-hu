---
title: Felhasználó által megadott útvonalak (UDR-EK) testreszabása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan határozhat meg egyéni kimenő útvonalakat az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: d108c6f49a8f483dc489fd644db6b480fc0e74fc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595807"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Fürt kilépésének testreszabása felhasználó által megadott útvonallal (előzetes verzió)

Az AK-fürtökből való kilépések testreszabhatók az adott forgatókönyvek kihasználása érdekében. Alapértelmezés szerint az AK kiépít egy szabványos SKU-Load Balancer a kimenő forgalomhoz. Előfordulhat azonban, hogy az alapértelmezett beállítás nem teljesíti az összes forgatókönyv követelményeit, ha a nyilvános IP-címek nem megengedettek, vagy további ugrásokra van szükség a kimenő forgalomhoz.

Ez a cikk bemutatja, hogyan szabhatja testre a fürt kimenő útvonalát az egyéni hálózati forgatókönyvek támogatásához, például a nyilvános IP-címek használatát, és a fürtnek a hálózati virtuális berendezés (NVA) mögött kell lennie.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló szolgáltatás, és a rendszer opt-alapon is elérhető. Az előzetes verziók az elérhető *módon* és a *rendelkezésre álló módon érhetők el* , és ki vannak zárva a szolgáltatói szerződéssel (SLA) és a korlátozott jótállással. A kétrészes előzetes verziókat az ügyfélszolgálat a *lehető legalkalmasabb* módon kezeli. A funkciók ezért nem használhatók éles környezetben. További információkért lásd a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="prerequisites"></a>Előfeltételek
* Az Azure CLI verziója 2.0.81 vagy újabb
* Azure CLI előzetes verziójú bővítmény 0.4.28 vagy újabb verziója
* `2020-01-01` vagy annál nagyobb API-verzió

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Az Azure CLI legújabb előzetes verziójának telepítése
A fürt kimenő típusának megadásához az Azure CLI-bővítmény 0.4.18 vagy újabb verziójára van szükség. Telepítse az Azure CLI AK előzetes verzióját az az Extension Add paranccsal, majd a következő az Extension Update paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Korlátozások
* Az előzetes verzió ideje alatt a `outboundType` csak a fürt létrehozásakor definiálható, és később nem frissíthető.
* Az előzetes verzióban `outboundType` AK-fürtöknek az Azure CNI-t kell használniuk. A Kubenet konfigurálható, a használathoz az útválasztási táblázat manuális társítására van szükség az AK-alhálózathoz.
* A `outboundType` beállításához AK-fürtökre van szükség a `VirtualMachineScaleSets` `vm-set-type` és a `Standard``load-balancer-sku`.
* A `outboundType` `UDR` értékre való beállításához a fürthöz érvényes kimenő kapcsolattal rendelkező felhasználó által megadott útvonal szükséges.
* Ha a `outboundType` értéket `UDR` értékre állítja be, akkor előfordulhat, hogy a terheléselosztó felé irányított bejövő forrás IP-cím **nem egyezik** a fürt kimenő kilépési céljának címével.

## <a name="overview-of-outbound-types-in-aks"></a>A kimenő típusok áttekintése az AK-ban

Az AK-fürtök testreszabhatók egyedi `outboundType` Load Balancer vagy felhasználó által definiált útválasztás használatával.

> [!IMPORTANT]
> A kimenő típus csak a fürt kimenő forgalmára van hatással. További információkért lásd: [beáramló vezérlők beállítása](ingress-basic.md) .

### <a name="outbound-type-of-loadbalancer"></a>A terheléselosztó kimenő típusa

Ha a `loadBalancer` be van állítva, az AK automatikusan végrehajtja a következő telepítést. A terheléselosztó egy AK-beli hozzárendelt nyilvános IP-címen keresztüli kimenő forgalomhoz használatos. A `loadBalancer` kimenő típusa `loadBalancer`típusú Kubernetes-szolgáltatásokat támogat, amelyek várhatóan kikerülnek az AK erőforrás-szolgáltató által létrehozott terheléselosztó felé.

A következő telepítést az AK hajtja végre.
   * Nyilvános IP-cím van kiépítve a fürt kimenő forgalmához.
   * A rendszer a terheléselosztó erőforráshoz rendeli a nyilvános IP-címet.
   * A terheléselosztó backend-készletei a fürtben található ügynök-csomópontok számára lettek beállítva.

Az alábbiakban egy olyan hálózati topológia található, amely Alapértelmezésben az AK-fürtökben van üzembe helyezve, amely `loadBalancer``outboundType`t használ.

![outboundtype – LB](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>A userDefinedRouting kimenő típusa

> [!NOTE]
> A kimenő típus használata fejlett hálózati forgatókönyv, és megfelelő hálózati konfigurációt igényel.

Ha a `userDefinedRouting` be van állítva, az AK nem konfigurálja automatikusan a kimenő útvonalakat. A **felhasználónak**a következőket kell elvégeznie.

A fürtöt egy meglévő, konfigurált alhálózattal rendelkező virtuális hálózatra kell telepíteni. A kimenő kapcsolattal rendelkező alhálózaton léteznie kell egy érvényes, felhasználó által megadott útvonalnak (UDR).

Az AK erőforrás-szolgáltató telepíti a standard Load balancert (SLB). A terheléselosztó nincs konfigurálva semmilyen szabállyal, és nem [számít fel díjat, amíg meg nem történik a szabály elhelyezése](https://azure.microsoft.com/pricing/details/load-balancer/). Az AK **nem** hoz létre automatikusan nyilvános IP-címet a SLB előtérbeli felületéhez. Az AK **nem** konfigurálja automatikusan a terheléselosztó háttér-készletét.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Fürt üzembe helyezése kimenő UDR-típussal és Azure Firewall

Egy felhasználó által megadott útvonal használatával a kimenő típusú fürt alkalmazásának szemléltetéséhez egy fürt konfigurálható egy Azure Firewall rendelkező virtuális hálózaton.

![Zárolt topológia](media/egress-outboundtype/outboundtype-udr.png)

* A bejövő forgalom a tűzfalon keresztüli szűrésre kényszerül
   * Egy elkülönített alhálózat belső terheléselosztó-készletet tart az ügynök csomópontjaiba való útválasztáshoz
   * Az ügynökök csomópontjai elkülönített alhálózatban vannak elkülönítve
* A kimenő kérelmek ügynök-csomópontokból a Azure Firewall belső IP-címekre indulnak egy felhasználó által megadott útvonal használatával
   * Az AK-ügynök csomópontjaitól érkező kérések követnek egy olyan UDR, amely az AK-fürt üzembe helyezésére szolgáló alhálózaton van elhelyezve.
   * Azure Firewall egresses a virtuális hálózatról a nyilvános IP-címről
   * Az AK vezérlő síkjával való hozzáférést egy NSG védi, amely lehetővé tette a tűzfal előtér-IP-címét.
   * A nyilvános internethez vagy más Azure-szolgáltatásokhoz való hozzáférés a tűzfal előtér-IP-címére irányuló és onnan áramlik.

### <a name="set-configuration-via-environment-variables"></a>Konfiguráció beállítása környezeti változók használatával

Adja meg az erőforrás-létrehozásokban használandó környezeti változók készletét.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Ezután állítsa be az előfizetés-azonosítókat.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Több alhálózattal rendelkező virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot három különálló alhálózattal, egyet a fürthöz, egyet a tűzfalhoz, egyet pedig a szolgáltatás bejövő beállításaihoz.

![Üres hálózati topológia](media/egress-outboundtype/empty-network.png)

Hozzon létre egy erőforráscsoportot az összes erőforrás tárolásához.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Hozzon létre két virtuális hálózatot az AK-fürt és a Azure Firewall üzemeltetéséhez. Mindegyiknek saját alhálózata lesz. Kezdjük az AK-hálózattal.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Azure Firewall létrehozása és beállítása UDR

Azure Firewall be kell állítani a bejövő és a kimenő szabályokat. A tűzfal fő célja, hogy lehetővé tegye a szervezetek számára a szemcsés bejövő és kimenő forgalmi szabályok bevezetését az AK-fürtbe.

![Tűzfal-és UDR](media/egress-outboundtype/firewall-udr.png)

Hozzon létre egy szabványos SKU nyilvános IP-erőforrást, amelyet a rendszer Azure Firewall előtér-címként fog használni.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Regisztrálja az előnézeti CLI-bővítményt Azure Firewall létrehozásához.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

A korábban létrehozott IP-cím most már hozzá lehet rendelni a tűzfal előtérbeli felületéhez.
> [!NOTE]
> A nyilvános IP-cím Azure Firewall beállítása néhány percet igénybe vehet.
> 
> Ha az alábbi parancsban többször is érkeznek hibák, törölje a meglévő tűzfalat és a nyilvános IP-címet, és helyezze üzembe a nyilvános IP-címet, és Azure Firewall a portálon keresztül egy időben.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Az előző parancs sikeres végrehajtása után mentse a tűzfal előtér-IP-címét később a konfigurációhoz.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>UDR létrehozása ugrással Azure Firewall

Az Azure automatikusan irányítja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útválasztási táblázatot.

Hozzon létre egy üres útválasztási táblázatot, amely egy adott alhálózathoz lesz társítva. Az útválasztási táblázat a következő ugrást fogja meghatározni a fent létrehozott Azure Firewall. Mindegyik alhálózattal nulla vagy egy útvonaltábla társítható.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

A [virtuális hálózati útválasztási táblázat dokumentációjában](../virtual-network/virtual-networks-udr-overview.md#user-defined) tájékozódhat arról, hogyan bírálhatja felül az Azure alapértelmezett rendszerútvonalait, vagy további útvonalakat adhat hozzá az alhálózat útválasztási táblájához.

## <a name="adding-network-firewall-rules"></a>Hálózati tűzfalszabályok hozzáadása

> [!WARNING]
> Az alábbi példa egy tűzfalszabály hozzáadását mutatja be. A [szükséges kimenő végpontokon](egress.md) definiált összes kimenő végpontot engedélyezve kell lennie az Application Firewall-szabályoknak az AK-fürtök működéséhez. Ha ezek a végpontok nem engedélyezettek, a fürt nem működhet.

Az alábbi példa egy hálózati és egy alkalmazási szabályt mutat be. Olyan hálózati szabályt adunk hozzá, amely bármilyen protokollt, forráscím, célcím és célport használatát teszi lehetővé. Egy alkalmazás-szabályt is hozzáadunk az AK által igényelt **egyes** végpontokhoz.

Éles környezetben csak a szükséges végpontokhoz való hozzáférést kell engedélyeznie az alkalmazáshoz, és az AK-ban meghatározott [kimenő](egress.md)forgalomban definiált.

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

A Azure Firewall szolgáltatással kapcsolatos további információkért tekintse meg [Azure Firewall dokumentációját](https://docs.microsoft.com/azure/firewall/overview) .

## <a name="associate-the-route-table-to-aks"></a>Az útválasztási táblázat hozzárendelése AK-hoz

Ha a fürtöt a tűzfalhoz szeretné rendelni, a fürt alhálózatához tartozó dedikált alhálózatnak a fent létrehozott útválasztási táblára kell hivatkoznia. A társítást úgy teheti meg, hogy a fürtöt és a tűzfalat tároló virtuális hálózatra vonatkozó parancs kiadásával frissíti a fürt alhálózatának útválasztási táblázatát.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Az AK üzembe helyezése a kimenő UDR-típussal a meglévő hálózatra

Most már van egy AK-fürt üzembe helyezése a meglévő virtuális hálózat telepítésekor. Ahhoz, hogy a fürt kimenő típusát felhasználó által megadott útválasztásra állítsa, egy meglévő alhálózatot kell megadni az ak-nak.

![AK – üzembe helyezés](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Egyszerű szolgáltatásnév létrehozása a meglévő virtuális hálózatban való üzembe helyezéshez

Az AK egy egyszerű szolgáltatásnevet használ a fürterőforrások létrehozásához. A létrehozáskor átadott egyszerű szolgáltatás a mögöttes AK-erőforrások, például virtuális gépek, tárolók és terheléselosztóok létrehozásához használatos. Ha túl kevés engedélyt adott meg, nem fog tudni kiépíteni egy AK-fürtöt.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Most cserélje le a `APPID`t, és `PASSWORD` az előző parancs kimenetében automatikusan generált egyszerű szolgáltatásnév-AppID és egyszerű szolgáltatás jelszava. A VNET erőforrás-AZONOSÍTÓra hivatkozunk, hogy megadja az engedélyeket az egyszerű szolgáltatásnév számára, hogy az AK-ban üzembe helyezhet erőforrásokat.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>AK üzembe helyezése

Végezetül az AK-fürt üzembe helyezhető a fürthöz dedikált meglévő alhálózaton. A rendszerbe telepítendő célként megadott alhálózat a környezeti változóval van definiálva, `$SUBNETID`.

A kimenő típust úgy adjuk meg, hogy kövessük az alhálózaton található UDR, amely lehetővé teszi az AK számára a beállítás és az IP-kiépítés kihagyása a terheléselosztó számára, amely mostantól szigorúan belső lehet.

Az [API-kiszolgáló által engedélyezett IP-tartományokhoz](api-server-authorized-ip-ranges.md) tartozó AK funkció hozzáadható az API-kiszolgáló hozzáférésének korlátozásához csak a tűzfal nyilvános végpontja számára. A hitelesítő IP-címtartományok szolgáltatás a diagramon a NSG, amelyet át kell adni a vezérlő síkja eléréséhez. Ha engedélyezi a jogosult IP-címtartomány használatát az API-kiszolgáló elérésének korlátozására, a fejlesztői eszközöknek Jumpbox kell használniuk a tűzfal virtuális hálózatáról, vagy az összes fejlesztői végpontot fel kell vennie az engedélyezett IP-tartományba.

> [!TIP]
> További funkciók is hozzáadhatók a fürt üzembe helyezéséhez, például (privát fürt) []. Ha engedélyezve van az IP-címtartományok használata, egy Jumpbox lesz szükség a fürtön belül az API-kiszolgáló eléréséhez.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Fejlesztői hozzáférés engedélyezése az API-kiszolgálóhoz

A fürt engedélyezett IP-címtartományok beállítása miatt a fejlesztői eszközök IP-címeit hozzá kell adnia a jóváhagyott IP-címtartományok AK-fürt listájához az API-kiszolgáló eléréséhez. Egy másik lehetőség, hogy a tűzfal virtuális hálózatán belül egy külön alhálózaton belüli Jumpbox konfigurálja a szükséges eszközökkel.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz a következő paranccsal

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

### <a name="setup-the-internal-load-balancer"></a>A belső terheléselosztó beállítása

Az AK üzembe helyezett egy terheléselosztó-t a fürttel, amely [belső terheléselosztóként](internal-lb.md)állítható be.

Belső terheléselosztó létrehozásához hozzon létre egy Internal-LB. YAML nevű szolgáltatási jegyzékfájlt a terheléselosztó és az Azure-Load-Balancer-belső megjegyzéssel az alábbi példában látható módon:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Helyezze üzembe a belső terheléselosztó-t a kubectl alkalmazásával, és adja meg a YAML-jegyzék nevét:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Kubernetes szolgáltatás üzembe helyezése

Mivel a fürt kimenő típusa UDR értékre van beállítva, az ügynökök csomópontjainak társítása, mivel a terheléselosztó háttér-készlete nem fejeződött be automatikusan az AK által a fürt létrehozási idejénél. A háttérbeli készlet társítását azonban a Kubernetes Azure Cloud Provider kezeli a Kubernetes szolgáltatás telepítésekor.

Telepítse az Azure szavazó app alkalmazást úgy, hogy az alábbi YAML másolja `example.yaml`nevű fájlba.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

A szolgáltatás üzembe helyezése a futtatásával:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>DNAT-szabály hozzáadása a Azure Firewall

A bejövő kapcsolat konfigurálásához egy DNAT szabályt kell írni a Azure Firewallba. A fürthöz való csatlakozás teszteléséhez egy szabály van definiálva a tűzfal előtér nyilvános IP-címéhez, hogy a belső szolgáltatás által közzétett belső IP-címhez irányítsa a rendszer.

A cél címe testreszabható, mert a tűzfal portja elérhető. A lefordított címnek a belső Load Balancer IP-címének kell lennie. A lefordított portnak a Kubernetes szolgáltatás számára elérhető portnak kell lennie.

Meg kell adnia a Kubernetes szolgáltatás által létrehozott terheléselosztó számára hozzárendelt belső IP-címet. A címek lekérése a futtatásával:

```bash
kubectl get services
```

A szükséges IP-cím a külső IP-oszlopban jelenik meg, a következőhöz hasonlóan.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!NOTE]
> Ha a Kubernetes belső szolgáltatását törli, ha a belső terheléselosztó már nem használja egyetlen szolgáltatás sem, az Azure Cloud Provider törli a belső Load balancert. A következő szolgáltatás központi telepítése esetén a terheléselosztó akkor lesz telepítve, ha nem található a kért konfigurációval.

Az Azure-erőforrások tisztításához törölje az AK-erőforráscsoport törlését.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A kapcsolat ellenőrzéséhez navigáljon a böngészőben a Azure Firewall előtér IP-címére.

Ekkor meg kell jelennie az Azure-beli szavazási alkalmazás rendszerképének.

## <a name="next-steps"></a>Következő lépések

Lásd: [Azure Networking UDR – áttekintés](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Lásd: [útválasztási táblázat létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-route-table).