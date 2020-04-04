---
title: Felhasználó által definiált útvonalak (UDR) testreszabása az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan definiálhat egyéni kimenő útvonalat az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 30b7b6bae92221b268d40977f5b299e9b0b267b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637829"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Fürtforgalom testreszabása felhasználó által definiált útvonallal (előzetes verzió)

Az AKS-fürtből való kijutás testreszabható adott forgatókönyvek nek megfelelően. Alapértelmezés szerint az AKS kiépít egy szabványos termékváltozat-terheléselosztót, amelyet be kell állítani, és a kimenő forgalomhoz kell használni. Előfordulhat azonban, hogy az alapértelmezett beállítás nem felel meg az összes forgatókönyv követelményeinek, ha nyilvános IP-cím nem engedélyezett, vagy további ugrásokra van szükség a kimenő forgalomhoz.

Ez a cikk bemutatja, hogyan szabhatja testre a fürt kimenő útvonalegyéni hálózati forgatókönyvek, például azok, amelyek nem engedélyezik a nyilvános IP-címeket, és megköveteli, hogy a fürt egy hálózati virtuális berendezés (NVA) mögött üljön.

> [!IMPORTANT]
> Az AKS előzetes verzió funkciói önkiszolgálóak, és opt-in alapon vehetők igénybe. Az előzetes verziók a *rendelkezésre* állás szerint és *a rendelkezésre állás* szerint érhetők el, és nem tartoznak a szolgáltatásiszint-szerződés (SLA) és a korlátozott jótállás hatálya alól. Az AKS-előnézeteket részben az ügyfélszolgálat fedezi a *legjobb erőfeszítés* alapján. Ezért a funkciók nem éles használatra szántak. További információt az alábbi támogatási cikkekben talál:
>
> * [Az AKS támogatási irányelvei](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="prerequisites"></a>Előfeltételek
* Az Azure CLI 2.0.81-es vagy újabb verziója
* Az Azure CLI Preview 0.4.28-as vagy újabb verziója
* API-verzió `2020-01-01` vagy nagyobb

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>A legújabb Azure CLI AKS előzetes verzióbővítmény telepítése
A fürt kimenő típusának beállításához az Azure CLI AKS preview-bővítmény 0.4.18-as vagy újabb verziójára van szükség. Telepítse az Azure CLI AKS preview bővítményt az az bővítmény hozzáadása paranccsal, majd ellenőrizze az elérhető frissítéseket a következő az extension update paranccsal:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Korlátozások
* Az előzetes `outboundType` verzió során csak fürtlétrehozási időpontban adható meg, és később nem frissíthető.
* Az előzetes `outboundType` verzió során az AKS-fürtöknek az Azure CNI-t kell használniuk. Kubenet konfigurálható, használati szükséges manuális társítások az útvonaltábla az AKS-alhálózathoz.
* A `outboundType` beállításhoz `vm-set-type` a. `VirtualMachineScaleSets` `load-balancer-sku` `Standard`
* A `outboundType` beállításhoz `UDR` a fürt höz érvényes kimenő kapcsolattal rendelkező, felhasználó által definiált útvonalszükséges.
* A `outboundType` beállítás azt `UDR` jelenti, hogy a terheléselosztóhoz irányított kimenő kimenő forrás IP-cím **nem egyezik** meg a fürt kimenő kimenő célcímével.

## <a name="overview-of-outbound-types-in-aks"></a>Kimenő típusok áttekintése az AKS-ben

Az AKS-fürtök egyedi `outboundType` típusú terheléselosztóval vagy felhasználó által definiált útválasztással testreszabhatók.

> [!IMPORTANT]
> A kimenő típus csak a fürt kimenő forgalomra van hatással. További információt a [be- és nagyadat-szabályozók beállítása](ingress-basic.md) című témakörben talál.

### <a name="outbound-type-of-loadbalancer"></a>A terheléselosztás kimenő típusa

Ha `loadBalancer` be van állítva, az AKS automatikusan befejezi a következő beállítást. A terheléselosztó az AKS-hez rendelt nyilvános IP-című en keresztül történő kilépéshez használatos. Egy kimenő típusú `loadBalancer` támogatja a Kubernetes `loadBalancer`típusú szolgáltatások, amelyek várhatóan kiesnek az AKS-erőforrás-szolgáltató által létrehozott terheléselosztó.

A következő beállítást az AKS végzi.
   * Nyilvános IP-cím van kiépítve a fürt kimenő.
   * A nyilvános IP-cím hozzá van rendelve a terheléselosztó erőforráshoz.
   * A terheléselosztó háttérkészletei a fürt ügynökcsomópontjaihoz vannak beállítva.

Az alábbiakban az AKS-fürtökben alapértelmezés szerint üzembe `outboundType` helyezett `loadBalancer`hálózati topológiát talál, amely a .

![kimenő típus-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Kimenő felhasználóDefinedRouting típusa

> [!NOTE]
> A kimenő típus használata speciális hálózati forgatókönyv, amely megfelelő hálózati konfigurációt igényel.

Ha `userDefinedRouting` be van állítva, az AKS nem konfigurálja automatikusan a kimenő útvonalakat. A felhasználó várhatóan **a**következőt végzi el.

A fürtöt egy konfigurált alhálózattal rendelkező meglévő virtuális hálózatba kell telepíteni. Érvényes, felhasználó által definiált útvonalnak (UDR) kell léteznie a kimenő kapcsolattal rendelkező alhálózaton.

Az AKS-erőforrás-szolgáltató egy szabványos terheléselosztót (SLB) telepít. A terheléselosztó nincs beállítva semmilyen szabállyal, és [nem számít fel díjat, amíg egy szabályt el nem helyeznek.](https://azure.microsoft.com/pricing/details/load-balancer/) Az AKS **nem** hoz ki automatikusan nyilvános IP-címet az SLB előtérhez. Az AKS **nem** konfigurálja automatikusan a terheléselosztó háttérkészletét.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Fürt központi telepítése kimenő típusú UDR-t és Azure tűzfalat használva

A felhasználó által definiált útvonal használatával kimenő típusú fürt alkalmazásának szemléltetése érdekében a fürt konfigurálható egy Azure-tűzfallal társviszonyba helyezett virtuális hálózaton.

![Zárolt topológia](media/egress-outboundtype/outboundtype-udr.png)

* A behatolás kénytelen átfolyni a tűzfalszűrőkön
   * Egy elkülönített alhálózat belső terheléselosztót tart az ügynökcsomópontokba történő útválasztáshoz
   * Az ügynökcsomópontok egy dedikált alhálózatban vannak elkülönítve
* A kimenő kérelmek az ügynökcsomópontokból indulnak az Azure tűzfal belső IP-címére egy felhasználó által definiált útvonal használatával
   * Az AKS-ügynökcsomópontok tól érkező kérelmek egy UDR-t követnek, amely az AKS-fürt által üzembe helyezett alhálózaton van elhelyezve.
   * Az Azure Firewall kikerül a virtuális hálózatból egy nyilvános IP-előtérből
   * Az AKS vezérlősíkhoz való hozzáférést egy NSG védi, amely engedélyezte a tűzfal előtér IP-címét
   * A nyilvános internethez vagy más Azure-szolgáltatásokhoz való hozzáférés a tűzfal előtérének IP-címére és onnan

### <a name="set-configuration-via-environment-variables"></a>Konfiguráció beállítása környezeti változókon keresztül

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

Üzembe létesíteni egy virtuális hálózat három különálló alhálózat, egy a fürt, egy a tűzfal, és egy szolgáltatás be- és be- és be- és szolgáltatás be- és ress.

![Üres hálózati topológia](media/egress-outboundtype/empty-network.png)

Hozzon létre egy erőforráscsoportot az összes erőforrás tárolására.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Hozzon létre egy két virtuális hálózatot az AKS-fürt és az Azure tűzfal üzemeltetéséhez. Mindegyiknek saját alhálózata lesz. Kezdjük az AKS hálózattal.

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

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Azure-tűzfal létrehozása és beállítása UDR-rel

Az Azure Firewall bejövő és kimenő szabályokat kell konfigurálni. A tűzfal fő célja, hogy lehetővé tegye a szervezetek számára a részletes be- és kilépési forgalmi szabályok beállítását az AKS-fürtbe és az AKS-fürtből.

![Tűzfal és UDR](media/egress-outboundtype/firewall-udr.png)

Hozzon létre egy szabványos termékváltozat nyilvános IP-erőforrást, amely az Azure Firewall előtér-címként lesz használva.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Regisztrálja az előzetes cli-bővítményt az Azure tűzfal létrehozásához.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

A korábban létrehozott IP-cím mostmár hozzárendelhető a tűzfal előtéréhez.
> [!NOTE]
> A nyilvános IP-cím beállítása az Azure tűzfalra eltarthat néhány percet.
> 
> Ha az alábbi parancs ismételten hibákat kap, törölje a meglévő tűzfalat és a nyilvános IP-címet, és egyidejűleg hozza létre a nyilvános IP-címet és az Azure tűzfalat a portálon keresztül.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Ha az előző parancs sikeres volt, mentse a tűzfal előtér IP-címét későbbi konfigurációra.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>UDR létrehozása ugrással az Azure tűzfalra

Az Azure automatikusan irányítja az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok közötti forgalmat. Ha módosítani szeretné az Azure bármelyik alapértelmezett útválasztását, akkor hozzon létre egy útvonaltáblát.

Hozzon létre egy üres útvonaltáblát egy adott alhálózathoz társítva. Az útvonaltábla határozza meg a következő ugrás, mint az Azure Firewall fent létrehozott. Mindegyik alhálózattal nulla vagy egy útvonaltábla társítható.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Tekintse meg [a virtuális hálózati útvonaltábla dokumentációját](../virtual-network/virtual-networks-udr-overview.md#user-defined) arról, hogyan bírálhatja felül az Azure alapértelmezett rendszerútvonalait, és hogyan adhat hozzá további útvonalakat az alhálózat útvonaltáblájához.

## <a name="adding-network-firewall-rules"></a>Hálózati tűzfalszabályok hozzáadása

> [!WARNING]
> Az alábbi példa egy tűzfalszabály hozzáadására. A [szükséges kimenő végpontokban](egress.md) definiált összes kimenő végpontot az AKS-fürtök működéséhez alkalmazástűzfal-szabályoknak kell engedélyezniük. Ha ezek a végpontok engedélyezve vannak, a fürt nem tud működni.

Az alábbiakban egy példa a hálózati és alkalmazásszabály. Hozzáadunk egy hálózati szabályt, amely lehetővé teszi a protokollt, a forráscímet, a célcímet és a célportokat. Az AKS által igényelt végpontok **hoz** unk hozzá egy alkalmazásszabályt is.

Éles környezetben csak az alkalmazás és az [AKS szükséges kimenő forgalomban](egress.md)meghatározott végpontokhoz való hozzáférést kell engedélyeznie.

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

Tekintse meg [az Azure Firewall dokumentációját](https://docs.microsoft.com/azure/firewall/overview) az Azure Firewall szolgáltatásról.

## <a name="associate-the-route-table-to-aks"></a>Az útvonaltábla társítása az AKS-hez

A fürt nek a tűzfalhoz való társításához a fürt alhálózatának dedikált alhálózatának hivatkoznia kell a fent létrehozott útvonaltáblára. A társítás a fürt és a tűzfal számára egy olyan virtuális hálózatnak ad ki parancsot, amely a fürt alhálózatának útvonaltábláját is frissíti.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Az AKS telepítése kimenő típusú UDR-rel a meglévő hálózatra

Most egy AKS-fürt telepíthető a meglévő virtuális hálózati beállításba. Ahhoz, hogy egy fürt kimenő típusát felhasználó által definiált útválasztásra állítsa, egy meglévő alhálózatot kell biztosítani az AKS számára.

![aks-telepítés](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Szolgáltatásnév létrehozása a meglévő virtuális hálózaton belüli kiépítéshez való hozzáféréssel

Az AKS egyszerű szolgáltatást használ fürterőforrások létrehozásához. A létrehozási időben átadott szolgáltatás alapjául szolgáló AKS-erőforrások, például az AKS által használt virtuális gépek, storage és terheléselosztók létrehozására szolgál. Ha túl kevés engedélyt kap, nem tud aKS-fürt ötözővé tenni.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Most cserélje `APPID` `PASSWORD` le a és az alábbi a szolgáltatás egyszerű appid és a szolgáltatás egyszerű jelszó automatikusan generált az előző parancs kimenet. A vnet-erőforrás-azonosítóra hivatkozunk, hogy megadja az engedélyeket az egyszerű szolgáltatásnak, hogy az AKS erőforrásokat telepíthessen bele.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>AKS telepítése

Végül az AKS-fürt telepíthető a fürthöz dedikált meglévő alhálózatba. A telepítendő célalhálózat a környezeti változóval van `$SUBNETID`definiálva. Az előző lépésekben `$SUBNETID` nem határoztuk meg a változót. Az alhálózati azonosító értékének beállításához használja a következő parancsot:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Meghatározzuk a kimenő típust, hogy kövesse az alhálózaton található UDR-t, lehetővé téve az AKS számára, hogy kihagyja a terheléselosztó beállítását és IP-kiépítést, amely most már szigorúan belső.

Az [API-kiszolgáló engedélyezett IP-tartományaiak](api-server-authorized-ip-ranges.md) AKS-szolgáltatása hozzáadható, hogy az API-kiszolgálók hozzáférését csak a tűzfal nyilvános végpontjára korlátozza. Az engedélyezett IP-tartományok funkciót az ábrán NSG-ként jelölik, amelyet át kell adni a vezérlősík eléréséhez. Ha engedélyezi az engedélyezett IP-tartományszolgáltatásnak az API-kiszolgálók elérésének korlátozását, a fejlesztői eszközöknek a tűzfal virtuális hálózatáról egy ugródobozt kell használniuk, vagy hozzá kell adniuk az összes fejlesztői végpontot az engedélyezett IP-tartományhoz.

> [!TIP]
> A fürt központi telepítéséhez további szolgáltatások adhatók hozzá, például (privát fürt)[]. Engedélyezett IP-tartományok használata esetén az API-kiszolgáló eléréséhez a fürthálózaton belül egy ugródobozra lesz szükség.

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

A fürt engedélyezett IP-tartomány-beállítása miatt az API-kiszolgáló eléréséhez hozzá kell adnia a fejlesztői eszközeszközök IP-címeit a jóváhagyott IP-tartományok AKS-fürtlistájához. Egy másik lehetőség, hogy konfigurálja a jumpbox a szükséges eszközegy külön alhálózat a tűzfal virtuális hálózat.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz a következő paranccsal

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Az [az aks get-credentials][az-aks-get-credentials] `kubectl` paranccsal konfigurálhatja az újonnan létrehozott Kubernetes-fürthöz való csatlakozást. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>A belső terheléselosztó beállítása

Az AKS terheléselosztót telepített a fürttel, amely [belső terheléselosztóként](internal-lb.md)állítható be.

Belső terheléselosztó létrehozásához hozzon létre egy belső-lb.yaml nevű szolgáltatásjegyzéket a LoadBalancer szolgáltatástípussal és az azure-load-balancer-internal megjegyzéssel, ahogy az a következő példában látható:

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

Telepítse a belső terheléselosztót a kubectl alkalmazásával, és adja meg a YAML-jegyzékfájl nevét:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Kubernetes-szolgáltatás telepítése

Mivel a fürt kimenő típusa UDR-ként van beállítva, az ügynökcsomópontok nak a terheléselosztó háttérkészleteként való társítását az AKS nem fejezi be automatikusan a fürt létrehozási idején. A háttérkészlet-társítást azonban a Kubernetes Azure-felhőszolgáltató kezeli, amikor a Kubernetes szolgáltatás telepítve van.

Telepítse az Azure szavazási alkalmazás alkalmazását az alábbi yaml másolásával egy fájlba. `example.yaml`

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

A szolgáltatás üzembe helyezése a következő futtatásával:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>DNST-szabály hozzáadása az Azure tűzfalhoz

A bejövő kapcsolat konfigurálásához dnst-szabályt kell írni az Azure tűzfalra. A fürthöz való kapcsolódás teszteléséhez egy szabály van definiálva a tűzfal előtér nyilvános IP-címére a belső szolgáltatás által elérhetővé tett belső IP-címhez való átirányításhoz.

A célcím testreszabható, mivel ez a tűzfal elérhető portja. A lefordított címnek a belső terheléselosztó IP-címének kell lennie. A lefordított portnak a Kubernetes szolgáltatás elérhető portjának kell lennie.

Meg kell adnia a Kubernetes szolgáltatás által létrehozott terheléselosztóhoz rendelt belső IP-címet. A cím lekérése a következő futtatásával:

```bash
kubectl get services
```

A szükséges IP-cím az EX-IP oszlopban jelenik meg, hasonlóan a következőkhöz.

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
> A Kubernetes belső szolgáltatás törlésekor, ha a belső terheléselosztó már nem használja semmilyen szolgáltatás, az Azure-felhőszolgáltató törli a belső terheléselosztót. A következő szolgáltatás üzembe helyezésekor egy terheléselosztó lesz telepítve, ha nem található a kért konfigurációval.

Az Azure-erőforrások törléséhez törölje az AKS-erőforráscsoportot.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Keresse meg az Azure Firewall előtér-IP-címét egy böngészőben a kapcsolat érvényesítéséhez.

Az Azure szavazási alkalmazás egy képjelenik meg.

## <a name="next-steps"></a>További lépések

Tekintse meg [az Azure hálózati UDR áttekintését.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)

Útvonaltábla [létrehozása, módosítása és törlése.](https://docs.microsoft.com/azure/virtual-network/manage-route-table)
