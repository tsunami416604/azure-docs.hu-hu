---
title: Hyperledger Fabric Consortium az Azure Kubernetes Service-ben (ak)
description: A Hyperledger Fabric Consortium Network üzembe helyezése és konfigurálása az Azure Kubernetes Service-ben
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: da4ec99f1b9d73ab67a2312094feaa1a89aee394
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980225"
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

- **Azure Key Vault**: a rendszer üzembe helyezi a Key Vault-példányt, hogy mentse a háló hitelesítésszolgáltatói hitelesítő adatait és az ügyfél által biztosított főtanúsítványokat, amelyeket a rendszer a sablon telepítésének újrapróbálkozása esetén használ a sablon mechanikaának kezeléséhez.
- **Azure Managed Disk**: az Azure Managed Disk a Főkönyv és a társ-csomópontok globális állapotú adatbázisának állandó tárolója.
- **Nyilvános IP-cím**: a fürttel való együttműködéshez üzembe helyezett AK-fürt nyilvános IP-végpontja.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Blockchain-hálózat beállítása

A kezdéshez olyan Azure-előfizetésre van szükség, amely támogatja több virtuális gép és standard Storage-fiók telepítését. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/)is.

Állítsa be a Hyperledger Fabric Blockchain-hálózatot a következő lépések végrehajtásával:

- [A megrendelés/társ szervezet üzembe helyezése](#deploy-the-ordererpeer-organization)
- [A konzorcium összeállítása](#build-the-consortium)

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
    - **Szervezet neve**: a háló szervezet neve, amely különböző adatsíkok-műveletekhez szükséges. Az üzembe helyezéshez a szervezet nevének egyedinek kell lennie.
    - **Háló hálózati összetevő**: válassza ki a beállítani kívánt Blockchain hálózati összetevő alapján a szolgáltatás vagy társ csomópontok rendezése lehetőséget.
    - **Csomópontok száma** – a következő két típusú csomópont létezik:
        - Rendezési szolgáltatás – válassza ki, hogy hány csomópontot adjon meg a hálózat hibatűrésének. A támogatott sorrendű csomópontok száma csak 3, 5 és 7.
        - Társ-csomópontok – a követelmények alapján kiválaszthatja a 1-10 csomópontot.
    - **Társ csomópont globális állapotának adatbázisa**: válasszon a LevelDB és a CoucbDB között. Ez a mező akkor jelenik meg, ha a felhasználó a háló hálózati összetevő legördülő menüjében a társ csomópontot választja.
    - **Háló felhasználóneve**: adja meg a háló hitelesítésszolgáltatói hitelesítéshez használt felhasználónevet.
    - **FABRIC CA-jelszó**: adja meg a háló hitelesítésszolgáltatói hitelesítésének jelszavát.
    - **Jelszó megerősítése**: erősítse meg a háló hitelesítésszolgáltatói jelszavát.
    - **Tanúsítványok**: Ha saját főtanúsítványokat kíván használni a háló hitelesítésszolgáltató inicializálásához, válassza a főtanúsítvány feltöltése a hálóhoz CA lehetőséget, máskülönben a Fabric CA alapértelmezés szerint önaláírt tanúsítványokat hoz létre.
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

Ha a blockchain konzorciumot a rendezési szolgáltatás és a társ-csomópontok üzembe helyezése után szeretné felépíteni, az alábbi lépéseket kell végrehajtania a sorozatban. Azure HLF script (azhlf), amely segít a konzorcium beállításában, csatorna-és chaincode-műveletek létrehozásában.

> [!NOTE]
> A szkriptben frissítés található, ez a frissítés az Azure HLF szkripttel való további funkcionalitást biztosít. Ha a régi szkriptre szeretne hivatkozni, [tekintse meg a következőt:](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Ez a szkript kompatibilis a Hyperledger Fabrictel az Azure Kubernetes Service template 2.0.0 vagy újabb verziójában. A központi telepítés verziójának vizsgálatához kövesse a [Hibaelhárítás](#troubleshoot)című szakasz lépéseit.

> [!NOTE]
> Az Azure HLF (azhlf) által biztosított parancsfájl csak a bemutató/DevTest forgatókönyvekhez nyújt segítséget. A szkript által létrehozott Channel és Consortium alapszintű HLF-házirendekkel rendelkezik a bemutató/DevTest forgatókönyv leegyszerűsítése érdekében. Éles környezetben a natív HLF API-k használatával javasolt a Channel/Consortium HLF szabályzatok frissítése a szervezet megfelelőségi igényeinek megfelelően.


Az Azure HLF-szkript futtatásához szükséges összes parancs az Azure bash parancssorán keresztül hajtható végre. Felület (CLI). Az Azure Shell web Version-be a következővel jelentkezhet be  ![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) lehetőség a Azure Portal jobb felső sarkában. A parancssorba írja be a bash parancsot, és váltson a bash parancssori felületre.

További információt az [Azure-rendszerhéjban](https://docs.microsoft.com/azure/cloud-shell/overview) talál.

![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Az alábbi képen látható, hogyan lehet konzorciumot felépíteni egy megrendelő szervezet és egy társ szervezet között. A lépések végrehajtásához szükséges részletes parancsok a következő fejezetekben vannak rögzítve.

![Hyperledger-háló az Azure Kubernetes Service sablonban](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Az ügyfélalkalmazás kezdeti beállításához kövesse az alábbi parancsokat: 

1.  [Ügyfél-alkalmazásfájlok letöltése](#download-client-application-files)
2.  [Környezeti változók beállítása](#setup-environment-variables)
3.  [Szervezeti kapcsolatprofil, rendszergazdai felhasználó és MSP importálása](#import-organization-connection-profile-admin-user-identity-and-msp)

A kezdeti beállítás befejezése után az ügyfélalkalmazás használatával elérheti az alábbi műveleteket:  

- [Csatorna-felügyeleti parancsok](#channel-management-commands)
- [Konzorcium-kezelési parancsok](#consortium-management-commands)
- [Chaincode-kezelési parancsok](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Ügyfél-alkalmazásfájlok letöltése

Az első beállítás az ügyfélalkalmazás fájljainak letöltése. Futtassa az alábbi parancsot az összes szükséges fájl és csomag letöltéséhez:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Ezek a parancsok a nyilvános GitHub-tárházból fogják betölteni az Azure HLF-ügyfélalkalmazás kódját, majd betöltik az összes függő NPM-csomagot. A parancs sikeres végrehajtása után node_modules mappát láthat az aktuális könyvtárban. Az összes szükséges csomag betöltődik a node_modules mappába.


### <a name="setup-environment-variables"></a>Környezeti változók beállítása

> [!NOTE]
> Az összes környezeti változó az Azure erőforrás-elnevezési konvenciót követi.


**Az alábbi környezeti változók beállítása a rendezési szervezet ügyfeléhez**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Az alábbi környezeti változók beállítása a társ-ügyfél számára**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> A konzorciumban található társ-szervezethez száma alapján előfordulhat, hogy meg kell ismételnie a társ-parancsokat, és ennek megfelelően kell beállítania a környezeti változót.

**Az alábbi környezeti változók beállítása az Azure Storage-fiók beállításához**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Kövesse az alábbi lépéseket az Azure Storage-fiókok létrehozásához. Ha már létrehozta az Azure Storage-fiókot, ugorja át ezeket a lépéseket

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Kövesse az alábbi lépéseket egy fájlmegosztás Azure Storage-fiókban való létrehozásához. Ha már létrehozott egy fájlmegosztást, ugorja át ezeket a lépéseket

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Kövesse az alábbi lépéseket az Azure file share-kapcsolatok karakterláncának létrehozásához

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Szervezeti kapcsolatprofil importálása, rendszergazdai felhasználói identitás és MSP

Az alábbi parancsokkal lehívhatja a szervezet profilját, a rendszergazdai felhasználói identitást és az MSP-t az Azure Kubernetes-fürtből, és ezeket az identitásokat az ügyfélalkalmazás helyi tárolójában, azaz a "azhlfTool/Stores" könyvtárban tárolja.

Rendezési szervezet:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Társ-szervezet esetén:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Csatorna-felügyeleti parancsok

> [!NOTE]
> A Channel művelet megkezdése előtt győződjön meg arról, hogy az ügyfélalkalmazás kezdeti beállítása megtörtént.  

A két csatorna-felügyeleti parancs a következő:

1. [Csatorna létrehozása parancs](#create-channel-command)
2. [A (z) főpartner (ek) parancs beállítása](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Csatorna létrehozása parancs

Az orderer szervezeti ügyfélprogramból hozzon létre egy új csatornát a kiállító paranccsal. Ezzel a paranccsal olyan csatornát hozhat létre, amely csak a rendezési szervezettel rendelkezik.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>A (z) főpartner (ek) parancs beállítása
A társ szervezeti ügyféltől a következő parancs kiadásával állíthatja be a társ-munkatársat a megadott csatornán.

>[!NOTE]
> A parancs végrehajtása előtt győződjön meg arról, hogy a társ-szervezet hozzá van adva a csatornához a konzorcium-felügyeleti parancsok használatával.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`egy szóközzel elválasztott lista, amely összeállított társ-csomópontként van beállítva. Például:

  - Állítsa `<anchorPeersList>` "peer1" értékre, ha csak peer1-csomópontot kíván beállítani.
  - Állítsa `<anchorPeersList>` "peer1" "peer3" értékre, ha a peer1 és a peer3 csomópontot is be szeretné állítani a horgony társként.

### <a name="consortium-management-commands"></a>Konzorcium-kezelési parancsok

>[!NOTE]
> A konzorciumi művelet megkezdése előtt győződjön meg arról, hogy az ügyfélalkalmazás kezdeti beállítása megtörtént.  

A megadott sorrendben hajtsa végre az alábbi parancsokat egy társ-szervezet hozzáadásához egy csatornában és konzorciumban
1.  Társ-szervezeti ügyfélről, a peer Organization MSP feltöltése az Azure Storage-ban

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  A rendezési szervezet ügyfelétől töltse le a peer Organization MSP-t az Azure Storage-ból, majd a parancs kiadásával adja hozzá a partner szervezetet a Channel/Consortium-ben.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  A rendezési szervezet ügyfelétől, az Azure Storage-beli orderer-kapcsolati profil feltöltése annak érdekében, hogy a társ-munkahelyhez a kapcsolati profil használatával lehessen csatlakozni a rendezési csomópontokhoz

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  A társ-ügyfél, az Azure Storage-ból töltse le a rendelés-összekapcsolási profilt, majd adja ki a parancsot a társ-csomópontok hozzáadásához a csatornában

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Hasonlóképpen, ha további társ-szervezeteket szeretne hozzáadni a csatornához, frissítse a társ-környezeti változókat a szükséges társ-szervezet szerint, és hajtsa végre az 1 – 4. lépést.


### <a name="chaincode-management-commands"></a>Chaincode-kezelési parancsok

>[!NOTE]
> A chaincode művelet megkezdése előtt győződjön meg arról, hogy az ügyfélalkalmazás kezdeti beállítása megtörtént.  

**Az alábbi chaincode-specifikus környezeti változók beállítása**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

Az alábbi chaincode műveletek végezhetők el:  

- [A chaincode telepítése](#install-chaincode)  
- [Chaincode példányának példányai](#instantiate-chaincode)  
- [Chaincode meghívása](#invoke-chaincode)
- [Chaincode lekérdezése](#query-chaincode)


### <a name="install-chaincode"></a>A chaincode telepítése  

Futtassa az alábbi parancsot a chaincode telepítéséhez a társ-szervezetben.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Telepíti a chaincode-t a ORGNAME környezeti változóban beállított társ-szervezeti csoport összes egyenrangú csomópontjára. Ha a csatornán kettő vagy több egyenrangú szervezet található, és mindegyikhez telepíteni szeretné a chaincode-t, akkor az összes társ-szervezetnél külön kell végrehajtania ezt a parancsot.  

Kövesse az alábbi lépéseket:  

1.  A `ORGNAME` és `USER_IDENTITY` a érték beállítása a peerOrg1 `./azhlf chaincode install` és a Issue parancs esetében.  
2.  A `ORGNAME` és `USER_IDENTITY` a érték beállítása a peerOrg2 `./azhlf chaincode install` és a Issue parancs esetében.  

### <a name="instantiate-chaincode"></a>Chaincode példányának példányai  

A társ ügyfélalkalmazás alkalmazásban futtassa az alábbi parancsot a chaincode a csatornán való létrehozásához.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
A (z) és a (z) és a ( `<instantiateFunc>` z `<instantiateFuncArgs>` ) és a (z) argumentumának átadása az Például chaincode_example02. go chaincode-ben a chaincode a következőre van állítva `<instantiateFunc>` `init` `<instantiateFuncArgs>` : "a" "2000" "b" "1000".

> [!NOTE]
> Hajtsa végre a parancsot egyszer a csatornán lévő bármelyik társ-szervezettől. Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. Ezért a chaincode a csatorna összes társ-csomópontján lévő összes társ-csomóponton példányba kerül.  


### <a name="invoke-chaincode"></a>Chaincode meghívása  

A chaincode függvény meghívásához hajtsa végre az alábbi parancsot a társ szervezeti ügyféltől:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

 `<invokeFunction>` Adja `<invokeFuncArgs>`meg a függvény nevét és az argumentumok szóközzel tagolt listáját. Folytassa a chaincode_example02. go chaincode-példával, hogy elindítsa `<invokeFunction>` a `invoke` meghívási műveletet az "a" "b" vagy `<invokeFuncArgs>` "10" értékre.  

>[!NOTE]
> Hajtsa végre a parancsot egyszer a csatornán lévő bármelyik társ-szervezettől. Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. Ezért a globális állapotot a rendszer az összes társ-csomóponton frissíti a csatornán.  


### <a name="query-chaincode"></a>Chaincode lekérdezése  

Futtassa az alábbi parancsot a chaincode lekérdezéséhez:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Adja át a lekérdezési függvény nevét és az argumentumok `<queryFunction>`  `<queryFuncArgs>` szóközzel tagolt listáját. A chaincode_example02. go chaincode-ként a "a" értéket kell megtekintenie a `<queryFunction>` globális állapotban `query`  `<queryArgs>` lévő "a" érték lekéréséhez.  

## <a name="troubleshoot"></a>Hibaelhárítás

**A futó sablon verziójának ellenőrzése**

Futtassa az alábbi parancsokat a sablon központi telepítésének megkereséséhez.

Állítsa be az alábbi környezeti változókat olyan erőforráscsoport szerint, amelyben a sablon telepítve lett.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Futtassa az alábbi parancsot a sablon verziójának kinyomtatásához
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```
