---
title: Hyperledger Fabric Consortium az Azure Kubernetes Service-ben (ak)
description: A Hyperledger Fabric Consortium Network üzembe helyezése és konfigurálása az Azure Kubernetes Service-ben
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 5aed420295fd17cf4e7b26c86e8b84c4687e6545
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029911"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hyperledger Fabric Consortium az Azure Kubernetes Service-ben (ak)

A Hyperledger Fabric (HLF) segítségével Azure Kubernetes Service (ak) sablonnal üzembe helyezheti és konfigurálhatja az Azure-beli Hyperledger Fabric Consortium-hálózatot.

A cikk elolvasása után:

- Szerezze be a Hyperledger-háló és a Hyperledger Fabric blockchain-hálózat építőelemeit alkotó különböző összetevőket.
- Megtudhatja, hogyan helyezhet üzembe és konfigurálhat Hyperledger Fabric consortiumt az Azure Kubernetes Service-ben az éles környezetekben.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric Consortium-architektúra

Ahhoz, hogy az Azure-beli Hyperledger-hálót hozzon létre, üzembe kell helyeznie a szolgáltatást és a szervezetet a társ-csomópontokkal. A sablon központi telepítésének részeként létrehozott különböző alapvető összetevők a következők:

- **Rendezési csomópontok**: a főkönyvben a tranzakciók rendezéséhez felelős csomópont. A többi csomóponttal együtt a megrendelt csomópontok képezik a Hyperledger Fabric-hálózat rendezési szolgáltatását.

- **Társ-csomópontok**: olyan csomópont, amely elsődlegesen a Főkönyv és az intelligens szerződések, a hálózat ezen alapvető elemei.

- **FABRIC CA**: a Fabric CA a hitelesítésszolgáltató (CA) a Hyperledger-hálóhoz. A Fabric CA segítségével inicializálhatja és elindíthatja a hitelesítésszolgáltatót futtató kiszolgálói folyamatot. Lehetővé teszi az identitások és a tanúsítványok kezelését. A sablon részeként üzembe helyezett minden AK-fürthöz alapértelmezés szerint a Fabric CA Pod lesz.

- **CouchDB vagy LevelDB**: a társ-csomópontok globális állapotú adatbázisa a LevelDB-ben vagy a CouchDB-ben is tárolható. A LevelDB a társ-csomópontban beágyazott alapértelmezett állapot-adatbázis, amely egyszerű kulcs-érték párokként tárolja a chaincode, és csak a kulcs, a kulcs tartomány és az összetett kulcsok lekérdezéseit támogatja. A CouchDB egy opcionális, alternatív állapotú adatbázis, amely támogatja a gazdag lekérdezéseket, ha a chaincode az adatértékek JSON-ként vannak modellezve.

Az üzembe helyezési sablon különböző Azure-erőforrásokat indít el az előfizetésében. Az üzembe helyezett különböző Azure-erőforrások a következők:

- **AK-fürt**: az Azure Kubernetes-fürt, amely az ügyfél által megadott bemeneti paraméterek szerint van konfigurálva. Az AK-fürthöz különböző hüvelyek vannak konfigurálva a Hyperledger háló hálózati összetevőinek futtatásához. A létrehozott különböző hüvelyek a következők:

  - **Háló eszközei**: a Fabric eszköz felelős a Hyperledger-háló összetevőinek konfigurálásához.
  - **Megrendelő/társ hüvelyek**: a HLF hálózat csomópontjai.
  - **Proxy**: egy ngnix proxy Pod, amelyen keresztül az ÜGYFÉLALKALMAZÁSOK az AK-fürthöz is csatlakozhatnak.
  - **FABRIC CA**: a Fabric CA-t futtató Pod.
- **PostgreSQL**: a rendszer üzembe helyezi a PostgreSQL egy példányát a háló hitelesítésszolgáltatói identitások fenntartásához.

- **Azure Key Vault**: a rendszer üzembe helyezi a Key Vault-példányt, hogy mentse a háló hitelesítésszolgáltatói hitelesítő adatait és az ügyfél által biztosított főtanúsítványokat, amelyeket a rendszer a sablon központi telepítésének újrapróbálkozása esetén használ. Ez a sablon mechanikaának kezelésére szolgál.
- **Azure Managed Disk**: az Azure Managed Disk a Főkönyv és a társ-csomópontok globális állapotú adatbázisának állandó tárolója.
- **Nyilvános IP-cím**: a fürttel való együttműködéshez üzembe helyezett AK-fürt nyilvános IP-végpontja.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Blockchain-hálózat beállítása

A kezdéshez olyan Azure-előfizetésre van szükség, amely támogatja több virtuális gép és standard Storage-fiók telepítését. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/)is.

Állítsa be a Hyperledger Fabric Blockchain-hálózatot a következő lépések végrehajtásával:

- [A megrendelés/társ szervezet üzembe helyezése](#deploy-the-ordererpeer-organization)
- [A konzorcium összeállítása](#build-the-consortium)
- [Natív HLF-műveletek futtatása](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>A megrendelés/társ szervezet üzembe helyezése

Az HLF hálózati összetevők üzembe helyezésének megkezdéséhez navigáljon a [Azure Portal](https://portal.azure.com). Válassza az **erőforrás létrehozása > Blockchain** > Hyperledger- **háló keresése az Azure Kubernetes Service**-ben lehetőséget.

1. Válassza a **Létrehozás** lehetőséget a sablon telepítésének elindításához. Megjelenik a **Hyperledger-háló létrehozása az Azure Kubernetes szolgáltatásban** .

    ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Adja meg a projekt részleteit az **alapok** oldalon.

    ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Adja meg a következő részleteket:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyre telepíteni szeretné a HLF hálózati összetevőit.
    - **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő üres erőforráscsoportot, az erőforráscsoport a sablon részeként telepített összes erőforrást fogja tárolni.
    - **Régió**: válassza ki azt az Azure-régiót, ahol az Azure Kubernetes-fürtöt telepíteni szeretné a HLF-összetevőkhöz. A sablon minden olyan régióban elérhető, ahol az AK elérhető legyen, és válassza ki azt a régiót, ahol az előfizetése nem éri el a virtuális gép (VM) kvótájának korlátját.
    - **Erőforrás-előtag**: előtag a központilag telepített erőforrások elnevezéséhez. Az erőforrás-előtagnak kevesebb, mint hat karakterből kell állnia, és a karakterek kombinációjának tartalmaznia kell a számokat és a betűket is.
4. A **háló beállításainak** kiválasztása lapon adhatja meg a telepítendő HLF hálózati összetevőket.

    ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Adja meg a következő részleteket:
    - **Szervezet neve**: a háló szervezet neve, amely különböző adatsíkok-műveletekhez szükséges.
    - **Háló hálózati összetevő**: válassza ki a beállítani kívánt Blockchain hálózati összetevő alapján a szolgáltatás vagy társ csomópontok rendezése lehetőséget.
    - **Csomópontok száma** – a következő két típusú csomópont létezik:
        - Rendezési szolgáltatás – válassza ki, hogy hány csomópontot adjon meg a hálózat hibatűrésének. A támogatott sorrendű csomópontok száma csak 3, 5 és 7.
        - Társ-csomópontok – a követelmények alapján kiválaszthatja a 1-10 csomópontot.
    - **Társ csomópont globális állapotának adatbázisa**: válasszon a LevelDB és a CoucbDB között. Ez a mező akkor jelenik meg, ha a felhasználó a háló hálózati összetevő legördülő menüjében a társ csomópontot választja.
    - **Háló felhasználóneve**: adja meg a háló hitelesítésszolgáltatói hitelesítéshez használt felhasználónevet.
    - **FABRIC CA-jelszó**: adja meg a háló hitelesítésszolgáltatói hitelesítésének jelszavát.
    - **Jelszó megerősítése**: erősítse meg a háló hitelesítésszolgáltatói jelszavát.
    - **Tanúsítványok**: Ha saját főtanúsítványokat kíván használni a háló hitelesítésszolgáltató inicializálásához, válassza a főtanúsítvány feltöltése a hálóhoz CA lehetőséget, különben az alapértelmezett Fabric CA önaláírt tanúsítványokat hoz létre.
    - **Főtanúsítvány**: töltse fel a főtanúsítványt (nyilvános kulcs), amelynek a hálós CA-nak inicializálva kell lennie. A. PEM formátumú tanúsítványok támogatottak, a tanúsítványoknak érvényesnek kell lenniük az UTC időzónában.
    - **Főtanúsítvány titkos kulcsa**: töltse fel a főtanúsítvány titkos kulcsát. Ha olyan. PEM tanúsítvánnyal rendelkezik, amelynek nyilvános és titkos kulcsa is van, töltse fel ide.


6. Válassza az **AK-fürt beállításai** fület az Azure Kubernetes-fürt konfigurációjának meghatározásához, amely az alapul szolgáló infrastruktúra, amelyre a háló hálózati összetevői lesznek beállítva.

    ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Adja meg a következő részleteket:
    - **Kubernetes-fürt neve**: a létrehozott AK-fürt neve. Ez a mező előre fel van töltve a megadott erőforrás-előtag alapján, szükség esetén módosítható.
    - **Kubernetes-verzió**: a fürtön telepítendő Kubernetes verziója. Az **alapok** lapon kiválasztott régió alapján az elérhető támogatott verziók változhatnak.
    - **DNS-előtag**: tartománynévrendszer (DNS) neve ELŐTAG az AK-fürthöz. A DNS használatával csatlakozhat a Kubernetes API-hoz, amikor a fürt létrehozása után kezeli a tárolókat.
    - **Csomópont mérete**: a Kubernetes csomópont mérete az Azure-ban elérhető virtuálisgép-készletezési egység (SKU) listájából választható ki. Az optimális teljesítmény érdekében javasoljuk a standard DS3 v2 használatát.
    - **Csomópontok**száma: a fürtben telepítendő Kubernetes-csomópontok számának száma. Azt javasoljuk, hogy a csomópont megtartása legalább egyenlő vagy annál nagyobb legyen, mint a háló beállításaiban megadott HLF-csomópontok száma.
    - **Egyszerű szolgáltatásnév ügyfél-azonosítója**: adja meg egy meglévő egyszerű szolgáltatásnév ügyfél-azonosítóját, vagy hozzon létre egy újat, amely az AK-hitelesítéshez szükséges. Lásd: az [egyszerű szolgáltatásnév létrehozásának](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)lépései.
    - **Egyszerű szolgáltatásnév ügyfél titka**: adja meg az egyszerű szolgáltatás ügyfél-azonosítójában megadott szolgáltatásnév ügyfél-titkát.
    - **Ügyfél titkos kulcsának megerősítése**: erősítse meg az ügyfél titkos kulcsában megadott titkos kulcsot.
    - **Tárolók figyelésének engedélyezése**: válassza az AK-figyelés engedélyezése lehetőséget, amely lehetővé teszi az AK-naplók számára a megadott log Analytics munkaterület leküldését.
    - **Log Analytics munkaterület**: a log Analytics-munkaterület fel lesz töltve az alapértelmezett munkaterülettel, amely akkor jön létre, ha a figyelés engedélyezve van.

8. A fenti részletek megadása után válassza a **felülvizsgálat és létrehozás** lapot. A felülvizsgálat és létrehozás elindítja a megadott értékek érvényesítését.
9. Az ellenőrzés után kiválaszthatja a **Létrehozás**lehetőséget.
Az üzembe helyezés általában 10-12 percet vesz igénybe, a megadott AK-csomópontok méretétől és számától függően változhat.
10. A sikeres üzembe helyezést követően értesítést kap az Azure-értesítéseken a jobb felső sarokban.
11. Válassza az **Ugrás az erőforráscsoporthoz** lehetőséget a sablon központi telepítésének részeként létrehozott összes erőforrás megtekintéséhez. Az összes erőforrás neve az **alapvető** beállítások beállításban megadott előtaggal fog kezdődni.

## <a name="build-the-consortium"></a>A konzorcium összeállítása

Ha a blockchain konzorciumot a rendezési szolgáltatás és a társ-csomópontok üzembe helyezése után szeretné felépíteni, az alábbi lépéseket kell végrehajtania a sorozatban. Hozza **létre a hálózati** parancsfájlt (Byn.sh), amely segít a konzorcium beállításában, a csatorna létrehozásában és a chaincode telepítésében.

> [!NOTE]
> A megadott hálózati (Byn) szkriptet kizárólag bemutató/devtest forgatókönyvek esetén kell használni. Az éles környezetben történő telepítéshez a natív HLF API-k használatát javasoljuk.

A Byn parancsfájl futtatásához szükséges összes parancs az Azure bash parancssori felületén (CLI) keresztül hajtható végre. Az Azure Shell web Version-be a következővel jelentkezhet be ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) lehetőség a Azure Portal jobb felső sarkában. A parancssorba írja be a bash parancsot, és váltson a bash parancssori felületre.

További információt az [Azure-rendszerhéjban](https://docs.microsoft.com/azure/cloud-shell/overview) talál.

![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Töltse le a byn.sh és a Fabric-admin. YAML fájlt.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Állítsa be az alábbi környezeti változókat az Azure CLI bash rendszerhéjon**:

Csatorna információinak és a megrendelési szervezet adatainak megadása

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Társ-szervezeti adatok megadása

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Hozzon létre egy Azure-fájlmegosztást a különböző nyilvános tanúsítványok megosztásához a társ-és a megrendelő szervezetek között.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Csatorna-felügyeleti parancsok**

Ugrás a rendezési szervezet AK-fürthöz és a probléma parancs új csatorna létrehozásához

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Konzorcium-kezelési parancsok**

A megadott sorrendben hajtsa végre az alábbi parancsokat, és adjon hozzá egy partneri szervezetet egy csatornához és konzorciumhoz.

1. Nyissa meg a társ-szervezeti AK-fürtöt, és töltse fel a tagsági szolgáltatását (MSP) egy Azure-File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Nyissa meg a rendezési szervezeti AK-fürtöt, és adja hozzá a társ-szervezetet a Channel és a Consortium szolgáltatásban.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Lépjen vissza a társ-szervezeti felületre, és adja meg a parancsot, hogy csatlakozzon a csatorna egyenrangú csomópontjaihoz.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Hasonlóképpen, ha további társközi szervezeteket szeretne hozzáadni a csatornához, frissítse a peer AK környezeti változóit a szükséges társ-munkahelyként, és hajtsa végre az 1 – 3. lépést.

**Chaincode-kezelési parancsok**

Hajtsa végre az alábbi parancsot a chaincode-hez kapcsolódó művelet végrehajtásához. Ezek a parancsok minden műveletet végrehajtanak a bemutató chaincode. Ez a bemutató chaincode két változóval rendelkezik: "a" és "b". A chaincode példányának létrehozásakor az "a" inicializálása 1000, a "b" pedig a 2000-es értékkel van inicializálva. A chaincode minden meghívásakor 10 egység kerül át az "a" és "b" értékre. A chaincode lekérdezési művelete az "a" változó globális állapotát jeleníti meg.

Hajtsa végre a következő parancsokat a társ-szervezeti AK-fürtön.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Chaincode művelet parancsai**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Natív HLF-műveletek futtatása

Annak érdekében, hogy az ügyfelek megismerkedjenek a Hyperledger natív parancsainak futtatásával a HLF-hálózaton az AK-on. A minta alkalmazást a Fabric NodeJS SDK használatával végezheti el a HLF műveletek végrehajtásához. A parancsokkal új felhasználói identitást hozhat létre, és telepítheti saját chaincode.

### <a name="before-you-begin"></a>Előkészületek

Az alkalmazás kezdeti beállításához kövesse az alábbi parancsokat:

- Alkalmazásfájlok letöltése
- Kapcsolatprofil és rendszergazdai profil létrehozása
- Rendszergazdai felhasználói identitás importálása

A kezdeti beállítás befejezése után az SDK használatával elérheti az alábbi műveleteket:

- Felhasználói identitás létrehozása
- Chaincode-műveletek

A fent említett parancsokat Azure Cloud Shell lehet végrehajtani.

### <a name="download-application-files"></a>Alkalmazásfájlok letöltése

Az alkalmazás futtatásának első beállítása az összes alkalmazás fájljának letöltése egy mappában.

**Hozzon létre egy app mappát, és írja be a mappába**:

```bash
mkdir app
cd app
```
Futtassa az alábbi parancsot az összes szükséges fájl és csomag letöltéséhez:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Ez a parancs időt vesz igénybe az összes csomag betöltéséhez. A parancs sikeres végrehajtása után `node_modules` mappát láthat az aktuális könyvtárban. Az összes szükséges csomag betöltődik a `node_modules` mappába.

### <a name="generate-connection-profile-and-admin-profile"></a>Kapcsolatprofil és rendszergazdai profil létrehozása

`profile` könyvtár létrehozása a `app` mappában

```bash
cd app
mkdir ./profile
```
A környezeti változók beállítása az Azure Cloud shellben

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Az alábbi parancs végrehajtásával létrehozza a szervezet kapcsolódási profilját és rendszergazdai profilját

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

A `profile` a (z) `<orgname>-ccp.json` és `<orgname>-admin.json` nevű profil mappájában hozza létre a szervezethez tartozó kapcsolatprofil és felügyeleti profilt.

Hasonlóképpen, az egyes megrendelésekhez és a társ-munkaszervezetekhez kapcsolódó profilt és rendszergazdai profilt is elő kell készíteni.


### <a name="import-admin-user-identity"></a>Rendszergazdai felhasználói identitás importálása

Az utolsó lépés a szervezet rendszergazdai felhasználói identitásának importálása a pénztárcába.

```bash
npm run importAdmin -- -o <orgName>

```
A fenti parancs végrehajtja a importAdmin. js fájlt, hogy importálja a rendszergazdai felhasználói identitást a pénztárcába. A parancsfájl beolvassa a rendszergazdai identitást a felügyeleti profilból `<orgname>-admin.json` és importálja a mobiltárca-ben a HLF műveletek végrehajtásához.

A parancsfájlok fájlrendszer-mobiltárca használatával tárolják az identitásokat. Létrehoz egy adatforrást a kapcsolatprofil ". Wallet" mezőjében megadott elérési út alapján. Alapértelmezés szerint a ". Wallet" mező a `<orgname>`vel van inicializálva, ami azt jelenti, hogy egy `<orgname>` nevű mappa jön létre az aktuális könyvtárban az identitások tárolásához. Ha más elérési úton szeretné létrehozni a mobiltárca szolgáltatást, módosítsa a kapcsolódási profil ". Wallet" mezőjét a regisztrálási rendszergazda felhasználó és bármely más HLF művelet futtatása előtt.

Hasonlóképpen importálja az egyes szervezetekhez tartozó rendszergazdai felhasználói identitást is.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a Command súgóját.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Felhasználói identitás létrehozása

A megadott sorrendben hajtsa végre az alábbi parancsokat a HLF-szervezet új felhasználói identitásának létrehozásához.

> [!NOTE]
> A felhasználói azonosító létrehozási lépéseinek megkezdése előtt győződjön meg arról, hogy az alkalmazás kezdeti beállítása megtörtént.

Az alábbi környezeti változók beállítása az Azure Cloud shellben

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Új felhasználó regisztrálása és regisztrálása

Új felhasználó regisztrálásához és regisztrálásához hajtsa végre az alábbi parancsot, amely végrehajtja a registerUser. js fájlt. A létrehozott felhasználói identitást a mobiltárca tárolja.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> A rendszergazdai felhasználói identitás az új felhasználó regisztrálási parancsának kiadására szolgál. Ezért a parancs végrehajtása előtt kötelező megadni a rendszergazdai felhasználói identitást a tárcában. Ellenkező esetben a parancs sikertelen lesz.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a Command súgóját.

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode-műveletek


> [!NOTE]
> A chaincode művelet megkezdése előtt győződjön meg arról, hogy az alkalmazás kezdeti beállítása megtörtént.

Állítsa be az alábbi chaincode-specifikus környezeti változókat az Azure Cloud shellben:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Az alábbi chaincode műveletek végezhetők el:

- A chaincode telepítése
- Chaincode példányának példányai
- Chaincode meghívása
- Chaincode lekérdezése

### <a name="install-chaincode"></a>A chaincode telepítése

Futtassa az alábbi parancsot a chaincode telepítéséhez a társ-szervezetben.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Telepíti a chaincode-t a `ORGNAME` környezeti változóban beállított szervezet összes egyenrangú csomópontjára. Ha a csatornán kettő vagy több egyenrangú szervezet található, és mindegyikhez telepíteni szeretné a chaincode-t, hajtsa végre a parancsokat külön-külön az egyes társ-szervezeteknél.

Kövesse az alábbi lépéseket:

- `ORGNAME` beállítása `<peerOrg1Name>` és a `installCC` parancs kiadása.
- `ORGNAME` beállítása `<peerOrg2Name>` és a `installCC` parancs kiadása.

  Hajtsa végre az összes társ-szervezet számára.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a parancs súgóját.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Chaincode példányának példányai

Futtassa az alábbi parancsot a chaincode a társon való létrehozásához.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Adja meg az argumentumok példányának nevét és vesszővel tagolt listáját `<instantiateFunc>` és `<instantiateFuncArgs>`. Például a [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)-ben a chaincode-készlet létrehozásához a `<instantiateFunc>` `"Init"` és a `<instantiateFuncArgs>` üres karakterláncot `""`.

> [!NOTE]
> Hajtsa végre a parancsot egyszer a csatornán lévő bármelyik társ-szervezettől.
> Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. Ezért a chaincode a csatorna összes társ-csomópontján lévő összes társ-csomóponton példányba kerül.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a Command súgóját.

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Chaincode meghívása

A chaincode függvény meghívásához hajtsa végre az alábbi parancsot:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Adja meg a függvény nevét, valamint az argumentumok vesszővel tagolt listáját `<invokeFunction>` és `<invokeFuncArgs>`. Folytassa a fabcar chaincode példával, hogy meghívja a initLedger Function set `<invokeFunction>` `"initLedger"` és `<invokeFuncArgs>` `""`.

> [!NOTE]
> Hajtsa végre a parancsot egyszer a csatornán lévő bármelyik társ-szervezettől.
> Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. Ezért a globális állapotot a rendszer az összes társ-csomóponton frissíti a csatornán.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a Command súgóját.

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode lekérdezése

Futtassa az alábbi parancsot a chaincode lekérdezéséhez:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Adja át a lekérdezési függvény nevét és az argumentumok vesszővel tagolt listáját `<queryFunction>` és `<queryFuncArgs>`. Ismét `fabcar` chaincode, hogy az összes, a globális állapotban lévő összes autót le lehessen kérdezni, `<queryFunction>` `"queryAllCars"`, és `<queryArgs>` `""`.

A parancsban átadott argumentumokkal kapcsolatos további részletekért tekintse meg a Command súgóját.

```bash
npm run queryCC -- -h

```
