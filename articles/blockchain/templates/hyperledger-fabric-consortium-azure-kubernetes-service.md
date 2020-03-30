---
title: Hyperledger Fabric konzorcium az Azure Kubernetes szolgáltatáson (AKS)
description: A Hyperledger Fabric konzorciumi hálózatának üzembe helyezése és konfigurálása az Azure Kubernetes szolgáltatásban
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476440"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hyperledger Fabric konzorcium az Azure Kubernetes szolgáltatáson (AKS)

A Hyperledger Fabric (HLF) segítségével az Azure Kubernetes Service (AKS) sablon üzembe helyezéséhez és konfigurálásához hyperledger Fabric konzorcium hálózat az Azure-ban.

A cikk elolvasása után:

- Szerezzen munkaismereteket a Hyperledger Fabric és a Hyperledger Fabric blokkjait alkotó különböző összetevőkről.
- Ismerje meg, hogyan telepíthet és konfigurálhat egy Hyperledger Fabric-konzorciumot az Azure Kubernetes-szolgáltatásban az éles forgatókönyvekhez.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric Consortium architektúra

Hyperledger Fabric-hálózat létrehozása az Azure-ban, üzembe kell helyeznie rendelési szolgáltatás és a szervezet társcsomópontok. A sablon központi telepítése során létrehozott különböző alapvető összetevők a következők:

- **Orderer csomópontok**: Olyan csomópont, amely felelős a főkönyvben a tranzakciók rendezéséért. Más csomópontokkal együtt a megrendelt csomópontok alkotják a Hyperledger Fabric hálózat rendelési szolgáltatását.

- **Társcsomópontok**: Olyan csomópont, amely elsősorban a gazdafőkönyveket és az intelligens szerződéseket, a hálózat alapvető elemeit tartalmazza.

- **Fabric CA**: Fabric CA a Hyperledger Fabric hitelesítésszolgáltatója (CA). A Fabric hitelesítésszolgáltató lehetővé teszi a hitelesítésszolgáltatót tartalmazó kiszolgálói folyamat inicializálását és elindítását. Lehetővé teszi az identitások és tanúsítványok kezelését. A sablon részeként üzembe helyezett minden Egyes AKS-fürt alapértelmezés szerint egy Fabric CA-podtal rendelkezik.

- **CouchDB vagy LevelDB**: A társcsomópontok világállapot-adatbázisa a LevelDB vagy a CouchDB fájlban tárolható. A LevelDB a társcsomópontba ágyazott alapértelmezett állapotadatbázis, amely a chaincode adatokat egyszerű kulcsérték-párokként tárolja, és csak a kulcs-, kulcstartomány- és összetettkulcs-lekérdezéseket támogatja. A CouchDB egy opcionális alternatív állapotadatbázis, amely támogatja a bővített lekérdezéseket, ha a chaincode adatértékek JSON-ként vannak modellezve.

A központi telepítéssablon különböző Azure-erőforrásokat hoz létre az előfizetésben. A különböző üzembe helyezett Azure-erőforrások a következők:

- **AKS-fürt:** Az Azure Kubernetes-fürt, amely az ügyfél által biztosított bemeneti paraméterek szerint van konfigurálva. Az AKS-fürt különböző podok konfigurálva a Hyperledger Fabric hálózati összetevők futtatásához. A különböző létrehozott podok a következők:

  - **Fabric eszközök**: A szövet eszköz felelős a Hyperledger Fabric összetevőinek konfigurálásáért.
  - **Orderer/peer podok**: A HLF-hálózat csomópontjai.
  - **Proxy**: NGNIX proxypod, amelyen keresztül az ügyfélalkalmazások kapcsolatba léphetnek az AKS-fürttel.
  - **Fabric CA**: A fabric ca-t futtató pod.
- **PostgreSQL**: A PostgreSQL egy példánya telepítve van a Fabric CA identitások karbantartásához.

- **Azure Key Vault:** A key vault-példány telepítve van a Fabric hitelesítésszolgáltató hitelesítő adatainak és az ügyfél által biztosított főtanúsítványainak mentéséhez, amely sablonüzembe helyezése esetén újrapróbálkozik, ez a sablon mechanikájának kezelése.
- **Azure Felügyelt lemez:** Az Azure Felügyelt lemez a főkönyvi és társcsomópont-állapot-adatbázis állandó tárolására.
- **Nyilvános IP:** Az AKS-fürt nyilvános IP-végpontja, amelyet a fürttel való kapcsolódásra telepítettek.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Blockchain hálózati beállítás

Először is olyan Azure-előfizetésre van szüksége, amely több virtuális gép és normál tárfiók üzembe helyezését is támogatja. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

A Hyperledger Fabric Blockchain hálózat beállítása a következő lépésekkel:

- [Az orderer/peer szervezet telepítése](#deploy-the-ordererpeer-organization)
- [A konzorcium építése](#build-the-consortium)
- [Natív HLF-műveletek futtatása](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Az orderer/peer szervezet telepítése

A HLF hálózati összetevők üzembe helyezésének megkezdéséhez keresse meg az [Azure Portalt.](https://portal.azure.com) Válassza **az Erőforrás létrehozása > Blockchain** > keressen **hyperledger Fabric az Azure Kubernetes szolgáltatás.**

1. A sablon központi telepítésének elindításához válassza a **Létrehozás** lehetőséget. A **Hyperledger Fabric létrehozása az Azure Kubernetes szolgáltatáson** jelenik meg.

    ![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Adja meg a projekt részleteit az **Alapok** lapon.

    ![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Adja meg a következő részleteket:
    - **Előfizetés**: Válassza ki azt az előfizetésnevet, amelyen telepíteni szeretné a HLF hálózati összetevőit.
    - **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő üres erőforráscsoportot, az erőforráscsoport a sablon részeként üzembe helyezett összes erőforrást megtartja.
    - **Régió:** Válassza ki azt az Azure-régiót, ahol telepíteni szeretné az Azure Kubernetes-fürtöt a HLF-összetevőkhöz. A sablon minden olyan régióban elérhető, ahol Az AKS elérhető, győződjön meg arról, hogy válasszon egy régiót, ahol az előfizetés nem éri el a virtuális gép (VM) kvótakorlátot.
    - **Erőforrás-előtag:** Az üzembe helyezett erőforrások elnevezésének előtagja. Az erőforrás-előtag nak hat karakternél rövidebbnek kell lennie, és a karakterek kombinációjának számokat és betűket is tartalmaznia kell.
4. Válassza **a Fabric Beállítások** fülre a HLF hálózati összetevők, amelyek telepítve lesz.

    ![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Adja meg a következő részleteket:
    - **Szervezet neve**: A Háló szervezet neve, amely különböző adatsík-műveletekhez szükséges. A szervezet nevének telepítésenként egyedinek kell lennie. 
    - **Fabric hálózati összetevő:** Válassza a Rendelési szolgáltatás vagy a Társcsomópontok alapján Blockchain hálózati összetevő be szeretne állítani.
    - **Csomópontok száma** – A csomópontok két típusa a következő:
        - Rendelési szolgáltatás - válassza ki a hálózati hibatűréshez megadott csomópontok számát. Csak 3,5 és 7 a támogatott rendelési csomópontok száma.
        - Társcsomópontok – 1-10 csomópontot választhat a követelmény alapján.
    - **Társcsomópont világállapot-adatbázisa**: Válasszon a LevelDB és a CoucbDB között. Ez a mező akkor jelenik meg, amikor a felhasználó a Fabric hálózati összetevőjének legördülő menüben választja a társcsomópontot.
    - **Fabric felhasználónév:** Adja meg a felhasználónevet, amely a Fabric hitelesítési használt.
    - **Fabric CA-jelszó**: Adja meg a fabric hitelesítési jelszó.
    - **Jelszó megerősítése**: Erősítse meg a Fabric hitelesítési jelszót.
    - **Tanúsítványok:** Ha saját főtanúsítványokkal szeretné inicializálni a fabric hitelesítésszolgáltatót, majd válassza a Legfelső szintű tanúsítvány feltöltése a Fabric CA beállításhoz lehetőséget, másnéven alapértelmezés szerint a Fabric hitelesítésszolgáltató önaláírt tanúsítványokat hoz létre.
    - **Főtanúsítvány:** Töltse fel a legfelső szintű tanúsítványt (nyilvános kulcsot), amellyel a Fabric hitelesítésszolgáltatót inicializálni kell. A .pem formátumú tanúsítványok támogatottak, a tanúsítványoknak UTC időzónában kell érvényeseknek lenniük.
    - **Főtanúsítvány titkos kulcsa**: Töltse fel a főtanúsítvány titkos kulcsát. Ha rendelkezik .pem tanúsítvánnyal, amely nyilvános és személyes kulccsal is rendelkezik, töltse fel ide is.


6. Válassza **az AKS-fürt beállításai** fülre az Azure Kubernetes fürtkonfiguráció, amely az alapul szolgáló infrastruktúra, amelyen a Fabric hálózati összetevők lesz beállítva.

    ![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Adja meg a következő részleteket:
    - **Kubernetes-fürt neve**: A létrehozott AKS-fürt neve. Ez a mező előre kivan töltve a megadott erőforráselőtag alapján, szükség esetén módosítható.
    - **Kubernetes-verzió:** A Kubernetes fürtön telepítendő verziója. Az **Alapok** lapon kiválasztott régió alapján a rendelkezésre álló támogatott verziók változhatnak.
    - **DNS-előtag**: Az AKS-fürt DNS-névelőtagja. A DNS használatával csatlakozhat a Kubernetes API-hoz a tárolók kezelése a fürt létrehozása után.
    - **Csomópont mérete:** A Kubernetes-csomópont mérete, az Azure-ban elérhető virtuálisgép-készletezési egység (SK) listájából választhat. Az optimális teljesítmény érdekében standard DS3 v2 ajánlott.
    - **Csomópontszám**: A fürtben üzembe helyezendő Kubernetes-csomópontok száma. Azt javasoljuk, hogy ez a csomópont száma legalább egyenlő vagy nagyobb, mint a HlF-csomópontok a Fabric-beállításokban megadott száma.
    - **Egyszerű szolgáltatásügyfél-azonosító:** Adja meg egy meglévő egyszerű szolgáltatásügyfél-azonosítóját, vagy hozzon létre egy újat, amely az AKS-hitelesítéshez szükséges. Lásd: egyszerű [szolgáltatás létrehozásának lépései.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Szolgáltatás elsődleges ügyféltitkos:** Adja meg az egyszerű szolgáltatásnévben megadott szolgáltatásnév ügyféltkatkatit.
    - **Ügyféltitok megerősítése:** Erősítse meg a szolgáltatás elsődleges ügyféltitkosjában megadott ügyféltitkot.
    - **Tárolófigyelés engedélyezése:** Válassza az AKS-figyelés engedélyezését, amely lehetővé teszi, hogy az AKS-naplók a megadott Log Analytics-munkaterületre kerüljön.
    - **Log Analytics-munkaterület:** A naplóelemzési munkaterület feltöltésre kerül az alapértelmezett munkaterület, amely akkor jön létre, ha a figyelés engedélyezve van.

8. Miután megadta a fenti részleteket, válassza a **Véleményezés és létrehozás** lapot. Az ellenőrzés és a létrehozás elindítja a megadott értékek érvényesítését.
9. Miután az ellenőrzés sikeres volt, kiválaszthatja a **create lehetőséget.**
A központi telepítés általában 10–12 percet vesz igénybe, a megadott AKS-csomópontok méretétől és számától függően változhat.
10. A sikeres üzembe helyezés után értesítést kap az Azure-értesítéseken keresztül a jobb felső sarokban.
11. Válassza **az Ugrás erőforráscsoportba** lehetőséget a sablon központi telepítésének részeként létrehozott összes erőforrás ellenőrzéséhez. Az összes erőforrásnév az **Alapok** beállításban megadott előtaggal kezdődik.

## <a name="build-the-consortium"></a>A konzorcium építése

A blokklánc-konzorcium létrehozása a rendelési szolgáltatás és a társcsomópontok üzembe helyezése után, az alábbi lépéseket sorrendben kell végrehajtania. A Network parancsfájl (byn.sh) **létrehozása,** amely segít a konzorcium létrehozásában, a csatorna létrehozásában és a chaincode telepítésében.

> [!NOTE]
> Build Your Network (byn) script biztosított szigorúan kell használni a demo/devtest forgatókönyvek. A gyártási fokozatú beállításhoz a natív HLF API-k használatát javasoljuk.

A byn parancsfájl futtatásához szükséges összes parancs végrehajtható az Azure Bash parancssori felületén (CLI) keresztül. Az Azure rendszerhéj webes verziójába is bejelentkezhet a ![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) lehetőség az Azure Portal jobb felső sarkában. A parancssorba írja be a bash parancsot, és írja be a bash CLI-re való váltáshoz.

További információkért lásd az [Azure rendszerhéjat.](https://docs.microsoft.com/azure/cloud-shell/overview)

![Hyperledger Fabric az Azure Kubernetes szolgáltatássablonján](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Töltse le byn.sh és a fabric-admin.yaml fájlt.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Állítsa be az alábbi környezeti változókat az Azure CLI Bash rendszerhéjban:**

Csatornaadatok és rendelési szervezeti adatok beállítása

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Társszervezeti adatok beállítása

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Hozzon létre egy Azure-fájlmegosztást a különböző nyilvános tanúsítványok társ- és rendezett szervezetek közötti megosztásához.

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
**Csatornakezelési parancsok**

Ugrás az aKS-fürtszervező szervezetre, és új csatorna létrehozása parancs

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Konzorciumkezelési parancsok**

Az alábbi parancsok végrehajtása a megadott sorrendben egy társszervezet hozzáadása a csatornában és a konzorciumban.

1. Nyissa meg a társszervezet AKS-fürtjét, és töltse fel a tagszolgáltatás-szolgáltatást (MSP) egy Azure-fájltárolón.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Nyissa meg az orderer Organization AKS-fürtöt, és adja hozzá a társszervezetet a csatornához és a konzorciumhoz.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Lépjen vissza a társszervezethez, és adja ki a parancsot a társcsomópontok csatlakoztatásához a csatornában.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Hasonlóképpen, további társszervezetek hozzáadása a csatornában, frissítse a társ AKS környezeti változók, mint egy a szükséges társ-szervezet, és hajtsa végre az 1-3 lépéseket.

**Lánckódkezelési parancsok**

Az alábbi parancs végrehajtása a chaincode-hoz kapcsolódó művelet végrehajtásához. Ezek a parancsok minden műveletet végrehajtanak egy demo chaincode-on. Ez a demo chaincode két változó "a" és "b". A lánckód példányosításakor az "a" 1000, a "b" pedig 2000-rel inicializálódik. A lánckód minden egyes meghívásakor 10 egység kerül át "a"-ról "b"-re. A chaincode lekérdezési művelete az "a" változó világállapotát mutatja.

Hajtsa végre a társszervezet AKS-fürtén végrehajtott következő parancsokat.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Lánckód műveletparancsok**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Natív HLF-műveletek futtatása

Annak érdekében, hogy az ügyfelek könnyebben végrehajthassák a Hyperledger natív parancsait a HLF-hálózaton az AKS-en. A mintaalkalmazás biztosított, amely a NodeJS SDK hálót használja a HLF-műveletek végrehajtásához. A parancsok új felhasználói identitás létrehozása és a saját chaincode telepítése.

### <a name="before-you-begin"></a>Előkészületek

Kövesse az alábbi parancsokat az alkalmazás kezdeti beállításához:

- Alkalmazásfájlok letöltése
- Kapcsolatprofil és rendszergazdai profil létrehozása
- Rendszergazdai felhasználói identitás importálása

A kezdeti beállítás befejezése után az SDK segítségével elérheti az alábbi műveleteket:

- Felhasználói identitás létrehozása
- Lánckódos műveletek

A fent említett parancsok az Azure Cloud Shell ből hajthatók végre.

### <a name="download-application-files"></a>Alkalmazásfájlok letöltése

Az alkalmazás futtatásának első beállítása a mappában lévő összes alkalmazásfájl letöltése.

**Alkalmazásmappa létrehozása és a mappa beírása:**

```bash
mkdir app
cd app
```
Az alábbi parancs végrehajtása az összes szükséges fájl és csomag letöltéséhez:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Ez a parancs időt vesz igénybe az összes csomag betöltéséhez. A parancs sikeres végrehajtása után `node_modules` egy mappa látható az aktuális könyvtárban. Az összes szükséges csomag betöltődik a `node_modules` mappába.

### <a name="generate-connection-profile-and-admin-profile"></a>Kapcsolatprofil és rendszergazdai profil létrehozása

Könyvtár `profile` létrehozása `app` a mappán belül

```bash
cd app
mkdir ./profile
```
Állítsa be ezeket a környezeti változókat az Azure felhőalapú rendszerhéján

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Az alábbi parancs végrehajtása a szervezet kapcsolati profiljának és felügyeleti profiljának létrehozásához

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Ez létrehoz kapcsolat profilés admin `profile` a szervezet a `<orgname>-ccp.json` `<orgname>-admin.json` profil mappában a nevét, illetve.

Hasonlóképpen hozzon létre kapcsolati profilt és rendszergazdai profilt minden egyes orderer és társszervezethez.


### <a name="import-admin-user-identity"></a>Rendszergazdai felhasználói identitás importálása

Az utolsó lépés a szervezet rendszergazdai felhasználói identitásának importálása a pénztárcában.

```bash
npm run importAdmin -- -o <orgName>

```
A fenti parancs végrehajtja az importAdmin.js parancsot a rendszergazdai felhasználói identitás importálásához a pénztárcába. A parancsfájl beolvassa a rendszergazdai identitást a rendszergazdai profilból, `<orgname>-admin.json` és importálja azt a pénztárcában a HLF-műveletek végrehajtásához.

A parancsfájlok fájlrendszer-pénztárcát használnak az identitások tárolására. Létrehoz egy pénztárcát a kapcsolatprofil ".wallet" mezőjében megadott elérési út nak. Alapértelmezés szerint a ".wallet" mező `<orgname>`inicializálásra kerül a program, ami azt jelenti, hogy egy megnevezett `<orgname>` mappa jön létre az aktuális könyvtárban az identitások tárolásához. Ha egy másik útvonalon szeretne pénztárcát létrehozni, módosítsa a ".wallet" mezőt a kapcsolatprofilban, mielőtt rendszergazdai felhasználót és bármely más HLF-műveletet futtatna.

Hasonlóképpen importálja az egyes szervezetek felügyeleti felhasználói identitását.

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgóját.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Felhasználói identitás létrehozása

Hajtsa végre az alábbi parancsokat a megadott sorrendben, hogy új felhasználói identitásokat hozzon létre a HLF szervezet számára.

> [!NOTE]
> A felhasználói identitás generálásának lépései előtt győződjön meg arról, hogy az alkalmazás kezdeti beállítása megtörtént.

Állítsa be az alábbi környezeti változókat az azure felhőalapú rendszerhéjon

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Új felhasználó regisztrálása és regisztrálása

Új felhasználó regisztrálásához és igényléséhez hajtsa végre a registerUser.js fájlon végrehajtó alábbi parancsot. Menti a generált felhasználói identitása a pénztárca.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> A rendszergazdai felhasználói identitás az új felhasználó regiszterparancsának kiadására szolgál. Ezért a parancs végrehajtása előtt kötelező a rendszergazdai felhasználói identitás a pénztárcában lennie. Ellenkező esetben ez a parancs sikertelen lesz.

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgójának hivatkozását

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Lánckódos műveletek


> [!NOTE]
> Mielőtt bármilyen chaincode művelet, győződjön meg arról, hogy a kezdeti beállítás az alkalmazás megtörtént.

Az azure cloud-rendszerhéjban az alábbi chaincode-specifikus környezeti változókat állíthatja be:

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

Az alábbi chaincode műveleteket lehet végezni:

- A lánckód telepítése
- A lánckód példányos példányosítása
- Lánckód meghívása
- Lánckód lekérdezése

### <a name="install-chaincode"></a>A lánckód telepítése

Az alábbi parancs végrehajtása a lánckód telepítéséhez a társszervezeten.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
A szervezet összes környezeti változóban `ORGNAME` beállított társcsomópontjára chaincode-kódot telepít. Ha két vagy több társszervezet van a csatornában, és mindegyikre chaincode-ot szeretne telepíteni, a parancsokat minden társszervezethez külön-külön hajtsa végre.

Kövesse a következő lépéseket:

- Beállítás `ORGNAME` `<peerOrg1Name>` és `installCC` problémaparancs.
- Beállítás `ORGNAME` `<peerOrg2Name>` és `installCC` problémaparancs.

  Hajtsa végre az egyes társszervezeteknél.

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgóját.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>A lánckód példányos példányosítása

Az alábbi parancs végrehajtása a lánckód példányosítására a társon.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Adja át az példányosítási függvény nevét és `<instantiateFunc>` vesszővel elválasztott argumentumlistáját. `<instantiateFuncArgs>` Például a [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go),, hogy példányosítani `"Init"` `<instantiateFuncArgs>` a chaincode meg, `""` `<instantiateFunc>` és üres string .

> [!NOTE]
> A parancsot egyszer hajtsa végre a csatorna bármely társszervezetéből.
> Miután a tranzakció sikeresen elküldésre került a rendelésre, az orderer szétosztja ezt a tranzakciót a csatorna összes társszervezetének. Ezért a chaincode példányosítja az összes társcsomópontok a társszervezetek a csatornában.

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgójának hivatkozását

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Lánckód meghívása

Az alábbi parancs végrehajtása a chaincode függvény meghívásához:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Pass invoke függvény neve és vesszővel elválasztott `<invokeFunction>` `<invokeFuncArgs>` argumentumok listája, illetve. Folytatva a fabcar chaincode például, hogy `<invokeFunction>` `"initLedger"` meghívja initLedger függvény beállítása, és `<invokeFuncArgs>` `""`.

> [!NOTE]
> A parancsot egyszer hajtsa végre a csatorna bármely társszervezetéből.
> Miután a tranzakció sikeresen elküldésre került a rendelésre, az orderer szétosztja ezt a tranzakciót a csatorna összes társszervezetének. Ezért a világállapot frissül a csatorna összes társszervezetének összes társcsomópontján.

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgójának hivatkozását

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Lánckód lekérdezése

Az alábbi parancs végrehajtása a chaincode lekérdezéséhez:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Adja át a lekérdezési függvény nevét és `<queryFunction>` vesszővel elválasztott argumentumlistáját. `<queryFuncArgs>` Ismét figyelembe `fabcar` chaincode referenciaként, hogy a lekérdezés az `<queryFunction>` `"queryAllCars"` összes `<queryArgs>` `""`autó a világ állam meg, és .

A parancsban átadott argumentumokkal kapcsolatos további részletekért olvassa el a parancs súgójának hivatkozását

```bash
npm run queryCC -- -h

```
