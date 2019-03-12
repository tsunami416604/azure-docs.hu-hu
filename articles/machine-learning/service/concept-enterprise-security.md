---
title: Vállalati biztonság
titleSuffix: Azure Machine Learning service
description: 'Biztonságosan használata az Azure Machine Learning szolgáltatás: hitelesítés, engedélyezés, hálózati biztonság, adatok titkosítására és figyelése.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731328"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatás nagyvállalati szintű biztonság

Ebben a cikkben ismertetik elérhető biztonsági funkciók és az Azure Machine learning-szolgáltatás.

Ha a használt felhő alapú szolgáltatás, korlátozhatja a hozzáférést a felhasználók, akiknek szükség van, ajánlott eljárás. Ekkor elindul a hitelesítési és engedélyezési modellje a szolgáltatás által használt megismerése. Előfordulhat, hogy a is szeretné korlátozni a hálózati hozzáférés, vagy biztonságosan csatlakozzon a helyszíni hálózat szervezhet marketingkampányokat, felhőalapú erőforrásokat. Adattitkosítás létfontosságú is, inaktív és az adatok szolgáltatások közötti áthelyezése közben. Végül, képeseknek kell lenniük a szolgáltatás figyelésére, és minden tevékenység egy naplók.

## <a name="authentication"></a>Authentication
Ha az Azure Active Directory (Azure AD) van konfigurálva a többtényezős hitelesítés támogatott.
* Ügyfél bejelentkezik az Azure ad-ben, és Azure Resource Manager-token beolvasása.  Felhasználók és az egyszerű szolgáltatások teljes mértékben támogatottak.
* Ügyfél bemutatja az Azure Resource Manager-token & az összes Azure Machine Learning-szolgáltatások
* Az Azure Machine Learning szolgáltatás egy Azure Machine Learning-jogkivonatot a felhasználó számítási biztosít. Ha például Machine Learning Compute. Az Azure Machine Learning jogkivonat felhasználói által használt számítási visszahívja (korlátozza hatókör munkaterülethez) Azure Machine Learning szolgáltatás a Futtatás után befejeződött.

![Képernyőfelvétel: a hitelesítés az Azure Machine Learning szolgáltatás működése](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>A webszolgáltatás üzembe a hitelesítési kulcsok

Ha engedélyezi a hitelesítést egy központi telepítés, hitelesítési kulcsok automatikus létrehozása.

* Hitelesítés alapértelmezés szerint engedélyezve van, az Azure Kubernetes Service-ben való telepítéséhez.
* Hitelesítés le van tiltva alapértelmezés szerint az Azure Container Instancesben való telepítéséhez.

Hitelesítés beállításához használja a `auth_enabled` paraméter létrehozása vagy frissítése egy központi telepítést.

Ha a hitelesítés engedélyezve van, használhatja a `get_keys` metódusának segítéségével lekérheti az egy elsődleges és másodlagos hitelesítési kulcs:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha a kulcs újragenerálása van szüksége, használja a [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Engedélyezés

Több munkaterületet is létrehozhat, és minden munkaterülethez több személy közösen használhat. Ha megoszt egy munkaterületet, szabályozhatja a hozzáférést a következő szerepkörök hozzárendelésével a felhasználók számára:
* Tulajdonos
* Közreműködő
* Olvasó
    
Az alábbi táblázat a fő Azure Machine Learning szolgáltatás műveletek és a szerepkör által végrehajtható műveleteket:

| Az Azure Machine Learning szolgáltatás művelet | Tulajdonos | Közreműködő | Olvasó |
| ---- |:----:|:----:|:----:|
| Munkaterület létrehozása | ✓ | ✓ | |
| Munkaterület megosztása | ✓ | |  |
| Számítási létrehozása | ✓ | ✓ | |
| Számítási csatolása | ✓ | ✓ | |
| Adattárolók csatolása | ✓ | ✓ | |
| Kísérlet futtatása | ✓ | ✓ | |
| Futtatások/metrikáinak megtekintése | ✓ | ✓ | ✓ |
| Modell regisztrálása | ✓ | ✓ | |
| Lemezkép létrehozása | ✓ | ✓ | |
| Webszolgáltatás üzembe helyezése | ✓ | ✓ | |
| Modellek/képek/megtekintése | ✓ | ✓ | ✓ |
| Webszolgáltatás hívása | ✓ | ✓ | ✓ |

Ha a beépített szerepkörök az igényeinek megfelelő, létrehozhat egyéni szerepköröket is. Vegye figyelembe, hogy a csak egyéni szerepkörök támogatjuk a munkaterület és a Machine Learning COMPUTE számítási műveletekhez. Az egyéni szerepkör előfordulhat, hogy rendelkezik olvasási, írási és engedélyek a munkaterülethez és a számítási erőforrás munkaterület törlése. Elérhetővé teheti a szerepkör egy adott munkaterület szintjén, egy adott erőforráscsoport szintjén vagy egy adott előfizetés szintjén. További információkért lásd: [felhasználók és szerepkörök az Azure Machine Learning-munkaterület kezelése](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Számítási és adatok védelme
Tulajdonosok és a közreműködőknek minden számítási célok és adattárakat, a munkaterülethez csatlakoztatott használhatja.  
Minden munkaterülethez társított alapértelmezett felügyelt identitás (a neve megegyezik a munkaterületen) is rendelkezik csatlakoztatott erőforrásokon a munkaterületen használja a következő engedélyekkel:

A felügyelt identitásokból további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Erőforrás | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő | 
| Tárfiók | Storage-Blobadatok Közreműködője | 
| Key Vault | Kulcsok, titkos kódok, összes tanúsítványhoz hozzá | 
| Azure Container Registry | Közreműködő | 
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő | 
| A Key Vault (ha eltérnek a munkaterületet tartalmazó) tartalmazó erőforráscsoport | Közreműködő | 

Javasoljuk, hogy a rendszergazdák nem visszavonni a hozzáférést, a fent említett erőforrások felügyelt identitásnak. Hozzáférés a kulcsok újraszinkronizálása művelettel állítható vissza.

Az Azure Machine Learning szolgáltatás egy további alkalmazás (nevének kezdő karaktere aml-) hoz létre a közreműködője szintű hozzáféréssel az előfizetésében minden munkaterület régióhoz. Az ex. Ha rendelkezik egy munkaterületet, az USA keleti régiójában és Észak-Európában ugyanazt az előfizetést egy másik munkaterületet látni fogja az ilyen alkalmazások 2. Erre azért van szükség, így a számítási erőforrásokat az Azure Machine Learning szolgáltatás segítségével kezelheti.


## <a name="network-security"></a>Hálózati biztonság

Az Azure Machine Learning szolgáltatás más Azure-szolgáltatások számítási erőforrások is támaszkodik. A számítási erőforrások (számítási céljainak) segítségével betanítása és a modellek üzembe helyezése. Ezek a számítási céljainak egy virtuális hálózaton belül hozható létre. Például használhatja a Microsoft Data Science virtuális gép betanítja a modellt, és majd a modell üzembe helyezése az Azure Kubernetes Service (AKS).  

További információkért lásd: [kísérletek vagy következtetési futtatása a virtuális hálózat](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Adattitkosítás

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
#### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Machine Learning szolgáltatás pillanatképeket, kimenetek és a naplók az Azure Blob Storage-fiók, amely vannak kötve, az Azure Machine Learning szolgáltatás munkaterület és a felhasználó előfizetése formában tárolja. Az Azure Blob Storage szolgáltatásban tárolt összes adat titkosítása Microsoft-Managed kulcsokkal.

Ahhoz, hogy a saját kulcsok az Azure Blob Storage-ban tárolt adatok hogyan további információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Betanítási adatok általában is tárolódik az Azure Blob storage-ban, hogy képzési számítási számára is elérhető legyen. Ez a tároló nem kezeli az Azure Machine Learning, de egy távoli fájlkiszolgáló-rendszer számítási csatlakoztatva van.

#### <a name="cosmos-db"></a>Cosmos DB
Az Azure Machine Learning szolgáltatás egy Microsoft Azure Machine Learning szolgáltatás által kezelt előfizetésben metrikák és a Cosmos DB-hez, amely él metaadatait tárolja. Az összes, a Cosmos DB-ben tárolt adatok titkosítása a Microsoft által felügyelt kulcsok használatával.

#### <a name="azure-container-registry-acr"></a>Az Azure Container Registry (ACR)
A beállításjegyzék (ACR) az összes tárolórendszerkép titkosítása inaktív. Az Azure automatikusan titkosítja a képet a tárolás előtt, és visszafejti a működés közbeni amikor az Azure Machine Learning szolgáltatás lekéri a rendszerképet.

#### <a name="machine-learning-compute"></a>Machine Learning Compute
Az operációsrendszer-lemez esetében az Azure Storage szolgáltatásban tárolt minden számítási csomópont kulcsokkal van titkosítva a Microsoft kezeli az Azure Machine Learning szolgáltatás tárfiókok. A számítási rövid élettartamú, és a fürtök vertikálisan leskálázni általában vannak, ha ott nem találhatók futtatások várakozási sorba állított. A mögöttes virtuális gép eltávolítjuk és operációsrendszer-lemez törlése. Az Azure disk encryption nem támogatott a rendszerlemez számára.
Minden virtuális gép is rendelkezik az operációs rendszer operations helyi ideiglenes lemezt. Ez a lemez is igény szerint használható a betanítási adatok készíthetők elő. Ez a lemez nincs titkosítva. Inaktív adatok titkosítását az Azure-ban működésével kapcsolatos további információkért lásd: [Azure adatok titkosítása inaktív](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Mindkét belső kommunikációs között különböző micro Azure Machine Learning-szolgáltatások és a pontozási végpontot hív-e a külső kommunikációs SSL használatával támogatott. Minden Azure Storage-hozzáférés egyben egy biztonságos csatornán. További információkért lásd: [SSL használatával biztonságossá tétele az Azure Machine Learning webszolgáltatások](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Az Azure Key Vault használatával
Key Vault-példány a munkaterülethez társított Azure Machine Learning szolgáltatás által használt különböző típusú hitelesítő adatok tárolásához:
* A társított tárfiók kapcsolati sztringje
* A jelszavakat az Azure tároló-beállításjegyzékbe példányok
* Kapcsolati karakterláncok adatokat tárolja. 

SSH-jelszavak és kulcsok például a HDI HDInsight és a virtuális gép cél számítási külön kulcstároló, amelyet a Microsoft-előfizetéssel társítva van tárolva. Az Azure Machine Learning szolgáltatásban tárolja a jelszavakat, vagy a kulcsok a felhasználó által megadott inkább állít elő, engedélyezi, és tárolja a saját SSH-kulcsok használata esetén csatlakozhassanak az történő futtatásához a virtuális gép vagy HDInsight. Minden munkaterülethez társított alapértelmezett felügyelt identitás (a neve megegyezik a munkaterületen), amely hozzáfér az összes kulcsok, titkos kódok és tanúsítványok a Key vaultban van.

 
## <a name="monitoring"></a>Figyelés

Felhasználók láthatják a tevékenységnaplóban a munkaterületen, és tekintse meg a munkaterület különböző műveleteket az alapvető információkat, mint például a művelet neve, esemény kezdeményezője, időbélyegző stb.

Az alábbi képernyőfelvételen a tevékenységnapló egy munkaterület:

![Képernyőkép – bemutató tevékenységnapló munkaterület](./media/enterprise-readiness/workspace-activity-log.png)


Pontozó a kérelem részletes adatainak vannak tárolva az AppInsights, amely jön létre a felhasználói előfizetést a munkaterület létrehozása során. This includes fields like HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, Duration etc.


## <a name="data-flow-diagram"></a>Adatfolyam-Diagram

### <a name="create-workspace"></a>Munkaterület létrehozása
Az alábbi ábrán látható, a létrehozás munkaterület munkafolyamat.
Felhasználó Azure ad-ben egyetlen Azure Machine Learning szolgáltatás támogatott ügyfelek (parancssori felület, a Python SDK-t, az Azure portal) és a megfelelő Azure Resource Manager-tokent kér.  Felhasználó ekkor meghívja a munkaterület létrehozásához Azure Resource Manager.  Az Azure Resource Manager-ügyfelek az Azure Machine Learning szolgáltatás erőforrás-szolgáltató a munkaterület kiépítése.  További erőforrások jönnek létre a munkaterület létrehozása során az ügyfél előfizetését:
* KeyVault (a titkos kulcsokat tárolhat)
* Azure Storage-fiókba (beleértve a Blob & fájlmegosztás)
* Az Azure Container Registry (a következtetési és kísérletezés docker-rendszerképek tárolásához)
* Az Application Insights (a telemetriai adatok tárolása)

Más számítási erőforrások (Azure Kubernetes Service-ben, virtuális gépek stb.) egy munkaterülethez csatlakoztatott is bővítheti ügyfél igény szerint. 

![Képernyőfelvétel: a munkaterület munkafolyamat létrehozása](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Mentse a forráskód (betanítási szkriptekhez)
Az alábbi ábrán látható, a kód pillanatkép munkafolyamat.
Az Azure Machine Learning társított szolgáltatás munkaterületén könyvtárak (kísérletek), a forráskód (betanítási szkriptekhez) tartalmazó.  Ezeket a rendszer az ügyfél helyi gépen, és a felhőben (az Azure Blob Storage, az ügyfél-előfizetéshez) tárolja. A kód pillanatképek végrehajtása vagy a korábbi naplózás ellenőrzés szolgálnak.

![Képernyőfelvétel: a munkaterület munkafolyamat létrehozása](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Képzés
Az alábbi ábrán látható, a képzési munkafolyamat.
* Az Azure Machine Learning szolgáltatás menti a fenti kód elkészíteni a pillanatképet a pillanatkép-Azonosítóval rendelkező neve
* Az Azure Machine Learning szolgáltatás hoz létre azonosítója (nem kötelező) és az Azure Machine Learning szolgáltatás jogkivonatot, amely később használják a számítási célokhoz, például a Machine Learning számítási/VM vissza kommunikáljon az Azure Machine Learning szolgáltatás futtatása
* Választhat, vagy egy felügyelt számítási (például. A Machine Learning Compute) vagy a nem felügyelt számítási (például. Virtuális gép) való betanítási feladatok futtatása. Az adatfolyam kifejtett mindkét az alábbi esetekben:
* (Virtuális gép vagy HDInsight/helyi – elért SSH hitelesítő adatok használata a Key Vaultban a Microsoft-előfizetés) Az Azure Machine Learning szolgáltatás felügyeleti kódot futtat a számítási célnak, amely:
    1.  A környezetet előkészíti (Megjegyzés: A docker egy virtuális gép/helyi beállítást is. Tekintse meg a lépéseket a Machine Learning Compute alábbi tudni, hogyan fut, a docker-tároló működéséről kísérlet)
    2.  A kód letöltése
    3.  Beállítja a környezeti változók/konfigurációk
    4.  (A fent említett kód pillanatkép) felhasználói parancsfájl futtatása
* (Machine Learning számítási – elérhető munkaterület felügyelt identitás használata) Vegye figyelembe, hogy mivel a Machine Learning Compute egy felügyelt számítási, a Microsoft által felügyelt, ezért futása alatt a Microsoft-előfizetést.
    1.  Távoli Docker-építési megkezdődik a szükség esetén
    2.  Felügyeleti kódot ír a felhasználó Azure-fájlmegosztás
    3.  Kezdeti kezdődik tároló parancsot, hogy felügyeleti kód az előző lépésben


#### <a name="querying-runs--metrics"></a>Futtatások és a metrikák lekérdezése
Ezt a lépést jelenik meg a folyamatban, ahol a betanítási számítási írások a *metrikák futtatása* térjen vissza az Azure Machine Learning szolgáltatás, amely lekérdezi a Cosmos DB-ben tárolja. Az ügyfelek meghívhat Azure Machine Learning szolgáltatás, amely viszont kérje le a metrikákat a Cosmos DB-ből, és térjen vissza az ügyfél.

![Képernyőfelvétel: a munkaterület munkafolyamat létrehozása](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Webes szolgáltatások létrehozása
Az alábbi ábrán látható, amelyben a modell üzembe lett helyezve web szolgáltatásként következtetési munkafolyamat.
A részleteket alább találja:
* Felhasználó regisztrálja a modellt, például az Azure Machine Learning-SDK-ügyfél használatával
* Felhasználó hoz létre a model, pontszám fájl és egyéb modell függőségek a képet
* A Docker-rendszerképet, és az ACR
* Webszolgáltatás telepítve van a fent létrehozott rendszerkép használatával számítási célnak (ACI/AKS)
* Pontozó a kérelem részletes adatainak vannak tárolva, az felhasználó előfizetése van, amely az AppInsights
* Telemetria szintén leküldéssel Microsoft/Azure-előfizetéshez

![Képernyőfelvétel: a munkaterület munkafolyamat létrehozása](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>További lépések

* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Hogyan futtathat batch-előrejelzés](how-to-run-batch-predictions.md)
* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az Azure Machine Learning szolgáltatás használata az Azure Virtual Network szolgáltatással](how-to-enable-virtual-network.md)
* [Gyakorlati tanácsok a javaslattételi rendszerek készítése](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlás API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
