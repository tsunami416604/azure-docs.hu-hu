---
title: Vállalati biztonság
titleSuffix: Azure Machine Learning
description: 'Biztonságosan használhatja az Azure Machine Learninget: hitelesítés, engedélyezés, hálózati biztonság, adattitkosítás és figyelés.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 4fbb3e83692ec058c03b22654e82d4093fe3541d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756566"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Nagyvállalati biztonság az Azure Machine Learning számára

Ebben a cikkben az Azure Machine Learning hez elérhető biztonsági funkciókról olvashat.

Felhőszolgáltatás használata esetén ajánlott korlátozni a hozzáférést csak a felhasználók, akiknek szükségük van rá. Kezdje a szolgáltatás által használt hitelesítési és engedélyezési modell ismertetésével. Előfordulhat, hogy szeretné korlátozni a hálózati hozzáférést, vagy biztonságosan csatlakozni erőforrásokat a helyszíni hálózaton a felhővel. Az adatok titkosítása is létfontosságú, mind nyugalmi, mind a szolgáltatások között mozog. Végül képesnek kell lennie a szolgáltatás figyelésére, és az összes tevékenység naplójának naplózását.

> [!NOTE]
> A cikkben szereplő információk az Azure Machine Learning Python SDK 1.0.83.1-es vagy újabb verziójával működnek.

## <a name="authentication"></a>Hitelesítés

Többtényezős hitelesítés támogatott, ha az Azure Active Directory (Azure AD) van konfigurálva, hogy használja. Itt van a hitelesítési folyamat:

1. Az ügyfél bejelentkezik az Azure AD-be, és egy Azure Resource Manager-jogkivonatot kap.  A felhasználók és a szolgáltatásnévi tagok teljes mértékben támogatottak.
1. Az ügyfél bemutatja a jogkivonatot az Azure Resource Manager és az összes Azure Machine Learning.
1. A Machine Learning szolgáltatás egy Machine Learning szolgáltatás jogkivonatot biztosít a felhasználói számítási cél (például machine learning számítási). Ezt a jogkivonatot a felhasználói számítási cél használja a Machine Learning szolgáltatásba való visszahíváshoz a futtatás befejezése után. A hatókör a munkaterületre korlátozódik.

[![Hitelesítés az Azure Machine Learningben](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

További információ: [Hitelesítés beállítása az Azure Machine Learning-erőforrásokhoz és -munkafolyamatokhoz.](how-to-setup-authentication.md) Ez a cikk információkat és példákat tartalmaz a hitelesítésről, beleértve a egyszerű szolgáltatásés az automatizált munkafolyamatok használatát.

### <a name="authentication-for-web-service-deployment"></a>Hitelesítés a webszolgáltatás telepítéséhez

Az Azure Machine Learning a webszolgáltatások hitelesítésének két formáját támogatja: a kulcsot és a jogkivonatot. Minden webszolgáltatás egyszerre csak egy hitelesítési formát engedélyezhet.

|Hitelesítési módszer|Leírás|Azure Container Instances|AKS|
|---|---|---|---|
|Kulcs|A kulcsok statikusak, és nem kell frissíteni őket. A kulcsok manuálisan is újragenerálhatók.|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezett|
|Jogkivonat|A jogkivonatok egy megadott időszak után lejárnak, és frissíteni kell őket.| Nem érhető el| Alapértelmezés szerint letiltva |

A kódpéldákért tekintse meg a [webszolgáltatás hitelesítési szakaszát.](how-to-setup-authentication.md#web-service-authentication)

## <a name="authorization"></a>Engedélyezés

Több munkaterületet is létrehozhat, és minden munkaterületet több ember is megoszthat. Ha megoszt egy munkaterületet, szabályozhatja a hozzáférést, ha ezeket a szerepköröket hozzárendeli a felhasználókhoz:

* Tulajdonos
* Közreműködő
* Olvasó

Az alábbi táblázat az Azure Machine Learning főbb műveleteit és az azokat elvégezhető szerepköröket sorolja fel:

| Azure Machine Learning-művelet | Tulajdonos | Közreműködő | Olvasó |
| ---- |:----:|:----:|:----:|
| Munkaterület létrehozása | ✓ | ✓ | |
| Munkaterület megosztása | ✓ | |  |
| Munkaterület frissítése Enterprise Edition kiadásra | ✓ | |
| Számítási cél létrehozása | ✓ | ✓ | |
| Számítási cél csatolása | ✓ | ✓ | |
| Adattárak csatolása | ✓ | ✓ | |
| Kísérlet futtatása | ✓ | ✓ | |
| Futtatások/mérőszámok megtekintése | ✓ | ✓ | ✓ |
| Modell regisztrálása | ✓ | ✓ | |
| Kép létrehozása | ✓ | ✓ | |
| Webszolgáltatás telepítése | ✓ | ✓ | |
| Modellek/képek megtekintése | ✓ | ✓ | ✓ |
| Webszolgáltatás hívása | ✓ | ✓ | ✓ |

Ha a beépített szerepkörök nem felelnek meg az igényeinek, egyéni szerepköröket hozhat létre. Az egyéni szerepkörök csak a munkaterületen és a Machine Learning Compute-on végzett műveletek esetén támogatottak. Az egyéni szerepkörök rendelkezhetnek olvasási, írási vagy törlési engedélyekkel a munkaterületre és a munkaterület számítási erőforrására vonatkozóan. A szerepkört elérhetővé teheti egy adott munkaterületi szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten. További információ: [Felhasználók és szerepkörök kezelése az Azure Machine Learning-munkaterületen című témakörben.](how-to-assign-roles.md)

> [!WARNING]
> Az Azure Machine Learning jelenleg nem támogatott az Azure Active Directory üzleti együttműködés.

### <a name="securing-compute-targets-and-data"></a>Számítási célok és adatok biztonságossá tétele

A tulajdonosok és a közreműködők használhatják a munkaterülethez csatolt összes számítási célt és adattárakat.  

Minden munkaterület hez tartozik egy rendszeráltal hozzárendelt felügyelt identitás is, amelynek neve megegyezik a munkaterület nevével. A felügyelt identitás a következő engedélyekkel rendelkezik a munkaterületen használt csatolt erőforrásokhoz.

A felügyelt identitásokkal kapcsolatos további információkért lásd: [Felügyelt identitások az Azure-erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Erőforrás | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő |
| Tárfiók | Storage Blob-adatközreműködő |
| Key Vault | Hozzáférés az összes kulcshoz, titkos kulcshoz, tanúsítványhoz |
| Azure Container Registry | Közreműködő |
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő |
| A key vaultot tartalmazó erőforráscsoport (ha eltér a munkaterületet tartalmazótól) | Közreműködő |

Nem javasoljuk, hogy a rendszergazdák vonják vissza a felügyelt identitás hozzáférését az előző táblázatban említett erőforrásokhoz. A hozzáférést az újraszinkronizálási kulcsok művelettel állíthatja vissza.

Az Azure Machine Learning létrehoz egy `aml-` további `Microsoft-AzureML-Support-App-`alkalmazást (a név kezdődik, vagy ) a közreműködőszintű hozzáférés az előfizetésminden munkaterületi régióban. Ha például az USA keleti részén egy munkaterülettel, az észak-európai pedig ugyanabban az előfizetésben rendelkezik, ezek közül kettő jelenik meg. Ezek az alkalmazások lehetővé teszik az Azure Machine Learning számára a számítási erőforrások kezelését.

## <a name="network-security"></a>Hálózati biztonság

Az Azure Machine Learning más Azure-szolgáltatásokra támaszkodik a számítási erőforrásokhoz. Számítási erőforrások (számítási célok) modellek betanításához és üzembe helyezéséhez használt. Ezeket a számítási célokat virtuális hálózatban hozhatja létre. Például az Azure Data Science virtuális gép segítségével betaníthat egy modellt, majd üzembe helyezheti a modellt az AKS-re.  

További információ: [Kísérletek futtatása és következtetés a virtuális hálózatban.](how-to-enable-virtual-network.md)

Engedélyezheti az Azure Private Linket a munkaterületéhez is. A Privát hivatkozás lehetővé teszi, hogy korlátozza a kommunikációt a munkaterületre egy Azure virtuális hálózatról. További információ: [A Privát hivatkozás konfigurálása.](how-to-configure-private-link.md)

> [!TIP]
> A virtuális hálózat és a privát kapcsolat kombinálásával megvédheti a munkaterület és más Azure-erőforrások közötti kommunikációt. Egyes kombinációk azonban enterprise edition munkaterületet igényelnek. Az alábbi táblázat segítségével megismerheti, hogy milyen forgatókönyveket igényel az Enterprise Edition:
>
> | Forgatókönyv | Enterprise</br>Kiadás | Basic</br>Kiadás |
> | ----- |:-----:|:-----:| 
> | Nincs virtuális hálózat vagy privát kapcsolat | ✔ | ✔ |
> | Munkaterület privát hivatkozás nélkül. Egyéb erőforrások (az Azure Container Registry kivételével) egy virtuális hálózatban | ✔ | ✔ |
> | Munkaterület privát hivatkozás nélkül. Egyéb források a Privát linkkel | ✔ | |
> | Munkaterület a Privát hivatkozással. Egyéb erőforrások (az Azure Container Registry kivételével) egy virtuális hálózatban | ✔ | ✔ |
> | Munkaterület és bármely más erőforrás a Privát hivatkozással | ✔ | |
> | Munkaterület a Privát hivatkozással. Egyéb erőforrások privát kapcsolat vagy virtuális hálózat nélkül | ✔ | ✔ |
> | Azure Container Registry virtuális hálózatban | ✔ | |
> | Ügyfél által kezelt kulcsok a munkaterülethez | ✔ | |
> 

> [!WARNING]
> Az Azure Machine Learning számítási példányok előzetes verziója nem támogatott egy olyan munkaterületen, ahol a Privát kapcsolat engedélyezve van.
> 
> Az Azure Machine Learning nem támogatja az Azure Kubernetes-szolgáltatás használatát, amelynek privát kapcsolata engedélyezve van. Ehelyett használhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. További információ: [Secure Azure ML kísérletezés és következtetési feladatok egy Azure virtuális hálózaton belül.](how-to-enable-virtual-network.md)

## <a name="data-encryption"></a>Adattitkosítás

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

> [!IMPORTANT]
> Ha a munkaterület bizalmas adatokat tartalmaz, javasoljuk, hogy a munkaterület létrehozásakor a [hbi_workspace jelzőt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 

A `hbi_workspace` jelző szabályozza a Microsoft által diagnosztikai célokra gyűjtött adatok mennyiségét, és további titkosítást tesz lehetővé a Microsoft által felügyelt környezetekben. Ezen kívül lehetővé teszi a következőket:

* Elindítja a helyi munkalemez titkosítását az Amlcompute-fürtben, feltéve, hogy nem hozott létre korábbi fürtöket az adott előfizetésben. A számítási fürtök munkalemezének titkosításához támogatási jegyet kell emelnie 
* Megtisztítja a helyi munkalemezt a futtatások között
* Biztonságosan továbbítja a tárfiók, a tároló-beállításjegyzék és az SSH-fiók hitelesítő adatait a végrehajtási rétegből a számítási fürtökbe a key vault használatával
* Engedélyezi az IP-szűrést annak biztosítására, hogy az alapul szolgáló kötegkészleteket az AzureMachineLearningService-en kívül más külső szolgáltatások nem tudják meghívni.


Ha többet szeretne tudni arról, hogyan működik az inaktív titkosítás az Azure-ban, olvassa [el az Azure-adatok titkosítását.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Az Azure Machine Learning az Azure Machine Learning munkaterületéhez és előfizetéséhez kötött Azure Blob-tárfiókban tárolja a pillanatképeket, a kimeneteket és a naplókat. Az Azure Blob storage-ban tárolt összes adat inkultatra titkosítva van a Microsoft által felügyelt kulcsokkal.

Ha tudni szeretné, hogyan használhatja saját kulcsait az Azure Blob storage-ban tárolt adatokhoz, olvassa el az [Azure Storage titkosítása az ügyfelek által felügyelt kulcsokkal az Azure Key Vaultban című témakörben.](../storage/common/storage-encryption-keys-portal.md)

A betanítási adatok általában az Azure Blob storage-ban is tárolódnak, így a betanítási számítási célok számára is elérhetők. Ezt a tárolót nem az Azure Machine Learning kezeli, hanem távoli fájlrendszerként a célok kiszámításához van csatlakoztatva.

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, ezt bármikor megteheti. Kulcs elforgatásakor a tárfiók az új kulcs (legújabb verzió) használatával kezdi meg az inaktív adatok titkosítását. Egy kulcs visszavonásakor (letiltása) a tárfiók gondoskodik a sikertelen kérelmek. Általában egy órát vesz igénybe, amíg a rotáció vagy a visszavonás hatékony lesz.

A hozzáférési kulcsok újragenerálásáról a [Tároló-hozzáférési kulcsok újragenerálása](how-to-change-storage-access-key.md)című témakörben talál további információt.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Az Azure Machine Learning metrikákat és metaadatokat tárol egy Azure Cosmos DB-példányban. Ez a példány az Azure Machine Learning által felügyelt Microsoft-előfizetéshez van társítva. Az Azure Cosmos DB-ben tárolt összes adat inkulálva a Microsoft által felügyelt kulcsokkal van titkosítva.

Saját (ügyfél által felügyelt) kulcsok használatával titkosítja az Azure Cosmos DB-példányt, létrehozhat egy dedikált Cosmos DB-példányt a munkaterülettel való használatra. Ezt a módszert javasoljuk, ha az adatokat, például az előzmények futtatásához szükséges adatokat szeretné tárolni a Microsoft-előfizetésünkben üzemeltetett több-bérlős Cosmos DB-példányon kívül. 

A Cosmos DB-példány üzembe létesítésének engedélyezéséhez az előfizetésben az ügyfél által felügyelt kulcsokkal hajtsa végre a következő műveleteket:

* Engedélyezze a Cosmos DB ügyfél által felügyelt kulcsképességeit. Ebben az időben hozzáférést kell kérnie a funkció használatához. Ehhez vegye fel [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)a kapcsolatot.

* Regisztrálja az Azure Machine Learning és az Azure Cosmos DB erőforrás-szolgáltatókat az előfizetésében, ha még nem történt meg.

* Engedélyezze a Machine Learning-alkalmazást (az Identitás- és hozzáférés-kezelésben) az előfizetéshez kapcsolódó közreműködői engedélyekkel.

    ![Engedélyezze az "Azure Machine Learning-alkalmazást" az Identitás- és hozzáférés-kezelés ben a portálon](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Az Azure Machine Learning-munkaterület létrehozásakor használja a következő paramétereket. Mindkét paraméter kötelező és támogatja az SDK, CLI, REST API-k és Erőforrás-kezelő sablonok.

    * `resource_cmk_uri`: Ez a paraméter a kulcstartóban lévő ügyfél által kezelt kulcs teljes erőforrás-URI-ja, beleértve [a kulcs verzióadatait](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)is. 

    * `cmk_keyvault`: Ez a paraméter az előfizetés ben lévő key vault erőforrás-azonosítója. Ez a key vault kell ugyanabban a régióban és az előfizetés, amely az Azure Machine Learning-munkaterületet fogja használni. 
    
        > [!NOTE]
        > Ez a key vault-példány eltérhet az Azure Machine Learning által a munkaterület kiépítésekor létrehozott key vaulttól. Ha ugyanazt a key vaultpéldányt szeretné használni a munkaterülethez, adja át ugyanazt a key vaultot a munkaterület kiépítése közben a [key_vault paraméter](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)használatával. 

Ez a Cosmos DB-példány egy Microsoft által felügyelt erőforráscsoportban jön létre az előfizetésben. 

> [!IMPORTANT]
> * Ha törölnie kell ezt a Cosmos DB-példányt, törölnie kell az Azure Machine Learning-munkaterületet, amely azt használja. 
> * A Cosmos DB-fiók alapértelmezett [__kérelemegysége__](../cosmos-db/request-units.md) __8000.The__default Request Units for this Cosmos DB account is set at 8000 . Az érték módosítása nem támogatott. 

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, ezt bármikor megteheti. Kulcs elforgatásakor a Cosmos DB az új kulcs (legújabb verzió) használatával kezdi meg az inaktív adatok titkosítását. Egy kulcs visszavonásakor (letiltásakor) a Cosmos DB gondoskodik a sikertelen kérelmekről. Általában egy órát vesz igénybe, amíg a rotáció vagy a visszavonás hatékony lesz.

Az ügyfél által felügyelt kulcsokról a Cosmos DB-vel kapcsolatos további információkért [lásd: Ügyfél által felügyelt kulcsok konfigurálása az Azure Cosmos DB-fiókjához.](../cosmos-db/how-to-setup-cmk.md)

#### <a name="azure-container-registry"></a>Azure Container Registry

A beállításjegyzékben lévő összes tárolórendszerkép (Azure Container Registry) inkamvanálva titkosítva van. Az Azure automatikusan titkosítja a lemezképet, mielőtt tárolna, és visszafejti azt, amikor az Azure Machine Learning lekéri a lemezképet.

A saját (ügyfél által felügyelt) kulcsok titkosításához az Azure Container Registry, létre kell hoznia a saját ACR és csatolja a munkaterület kiépítése közben, vagy titkosítja az alapértelmezett példány, amely jön létre a munkaterület kiépítése kor.

Egy példa egy munkaterület létrehozásához egy meglévő Azure Container Registry használatával, lásd a következő cikkeket:

* [Hozzon létre egy munkaterületet az Azure Machine Learning számára az Azure CLI segítségével.](how-to-manage-workspace-cli.md)
* [Az Azure Resource Manager-sablon használata munkaterület létrehozásához az Azure Machine Learning számára](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

Titkosíthatja az üzembe helyezett Azure Container Instance (ACI) erőforrást az ügyfél által felügyelt kulcsok használatával. Az ACI-hoz használt ügyfél által felügyelt kulcs tárolható az Azure Key Vaultban a munkaterülethez. A kulcs létrehozásáról az [Adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)című témakörben talál további információt.

Ha használni szeretné a kulcsot egy modell Azure Container Instance `AciWebservice.deploy_configuration()`üzembe helyezésekor, hozzon létre egy új központi telepítési konfigurációt a használatával. Adja meg a legfontosabb információkat a következő paraméterek használatával:

* `cmk_vault_base_url`: A kulcsot tartalmazó kulcstartó URL-címe.
* `cmk_key_name`: A kulcs neve.
* `cmk_key_version`: A kulcs verziója.

A központi telepítési konfiguráció kondilétrehozásáról és használatáról az alábbi cikkekben talál további információt:

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) – hivatkozás
* [Az üzembe helyezés módja és helye](how-to-deploy-and-where.md)
* [Modell üzembe helyezése az Azure Container-példányok ba](how-to-deploy-azure-container-instance.md)

Az ügyfél által felügyelt kulcs ACI-val való használatáról az [Adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)című témakörben talál további információt.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Bármikor titkosíthat egy üzembe helyezett Azure Kubernetes-szolgáltatás erőforrást az ügyfél által felügyelt kulcsok használatával. További információ: [Saját kulcsok beszállítása az Azure Kubernetes szolgáltatással](../aks/azure-disk-customer-managed-keys.md)című témakörben talál. 

Ez a folyamat lehetővé teszi a Kubernetes-fürtben üzembe helyezett virtuális gépek data és operációs rendszerlemezének titkosítását.

> [!IMPORTANT]
> Ez a folyamat csak az AKS K8s 1.17-es vagy újabb verziójával működik. 2020. január 13-án az Azure Machine Learning támogatást nyújtott az AKS 1.17-hez.

#### <a name="machine-learning-compute"></a>Gépi tanulási számítás

Az Azure Storage-ban tárolt minden egyes számítási csomópont operációs rendszerlemeze microsoft által felügyelt kulcsokkal van titkosítva az Azure Machine Learning storage-fiókokban. Ez a számítási cél rövid élettartamú, és a fürtök általában kicsinyített, ha nincs fut várólistára. Az alapul szolgáló virtuális gép kiépített, és az operációs rendszer lemeztörlődik. Az Azure Disk Encryption nem támogatott az operációs rendszer lemeze.

Minden virtuális gép rendelkezik egy helyi ideiglenes lemez operációs rendszer műveleteket. Ha szeretné, a lemez segítségével a betanítási adatok at. A lemez alapértelmezés szerint titkosítva `hbi_workspace` van a `TRUE`munkaterületek számára, ahol a paraméter beállítása . Ez a környezet csak a futtatás időtartama alatt rövid életű, és a titkosítástámogatása csak a rendszer által felügyelt kulcsokra korlátozódik.

#### <a name="azure-databricks"></a>Azure Databricks

Az Azure Databricks azure Machine Learning-folyamatokban is használható. Alapértelmezés szerint az Azure Databricks által használt Databricks fájlrendszer (DBFS) microsoft által kezelt kulccsal van titkosítva. Ha az Azure Databricks-t ügyfél által kezelt kulcsok használatára szeretné konfigurálni, olvassa el [az Ügyfél által felügyelt kulcsok konfigurálása alapértelmezett (gyökér) DBFS szolgáltatásban című témakört.](/azure/databricks/security/customer-managed-keys-dbfs)

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

A TLS használatával biztonságos belső kommunikáció azure Machine Learning mikroszolgáltatások és a pontozási végpont külső hívások biztonságossá tétele. Minden Azure Storage-hozzáférés is történik egy biztonságos csatornán keresztül.

További információ: [A TLS használata webszolgáltatás védelméhez az Azure Machine Learningen keresztül.](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)

### <a name="using-azure-key-vault"></a>Az Azure Key Vault használata

Az Azure Machine Learning a munkaterülethez társított Azure Key Vault-példányt használja a különböző típusú hitelesítő adatok tárolására:

* A társított tárfiók kapcsolati karakterlánca
* Jelszavak az Azure Container Repository példányaihoz
* Kapcsolati karakterláncok adattárakkal

SSH jelszavak és kulcsok számítási célok, például az Azure HDInsight és a virtuális gépek egy külön kulcstartóban, amely a Microsoft-előfizetéshez társított. Az Azure Machine Learning nem tárolja a felhasználók által biztosított jelszavakat és kulcsokat. Ehelyett saját SSH-kulcsokat hoz létre, engedélyez és tárol a virtuális gépekhez és a HDInsighthoz a kísérletek futtatásához.

Minden munkaterület rendelkezik egy rendszeráltal hozzárendelt felügyelt identitással, amelynek neve megegyezik a munkaterület nevével. Ez a felügyelt identitás hozzáférést biztosít a kulcstartóban lévő összes kulcshoz, titkos kulcshoz és tanúsítványhoz.

## <a name="data-collection-and-handling"></a>Adatgyűjtés és -kezelés

### <a name="microsoft-collected-data"></a>A Microsoft adatokat gyűjtött

A Microsoft diagnosztikai célokra nem felhasználói azonosító adatokat gyűjthet, például az adatkészlet nevét vagy a gépi tanulási kísérlet nevét, illetve a feladatkörnyezeti változókat. Minden ilyen adatot a Microsoft által felügyelt kulcsok használatával tárolunk a Microsoft tulajdonában lévő előfizetésekben tárolt tárolókban, és a [Microsoft általános adatvédelmi irányelveit és adatkezelési szabványait](https://privacy.microsoft.com/privacystatement)követik.

A Microsoft azt is javasolja, hogy ne tárolja a bizalmas adatokat (például a fiók kulcstitkos kulcsait) a környezeti változókban. A környezeti változókat naplózjuk, titkosítjuk és tároljuk. Hasonlóképpen, ha a [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)nevet, ne tartalmazza a bizalmas információkat, például a felhasználóneveket vagy a titkos projektneveket. Ezek az információk a Microsoft támogatási szakemberei számára elérhető telemetriai naplókban jelenhetnek meg.

Leiratkozhat a diagnosztikai adatok gyűjtéséről, ha a paramétert a `hbi_workspace` munkaterület kiépítése `TRUE` során állítja be. Ez a funkció az AzureML Python SDK, CLI, REST API-k vagy az Azure Resource Manager-sablonok használata esetén támogatott.

### <a name="microsoft-generated-data"></a>Microsoft által létrehozott adatok

Szolgáltatások, például az automatikus gépi tanulás használata során a Microsoft átmeneti, előre feldolgozott adatokat hozhat létre több modell betanításához. Ezeket az adatokat a munkaterület egy adattárban tárolja, amely lehetővé teszi a hozzáférés-vezérlés és a titkosítás megfelelő kényszerítését.

Érdemes lehet az [üzembe helyezett végpontról az](how-to-enable-app-insights.md) Azure Application Insights-példányba naplózott diagnosztikai adatok titkosítását is.

## <a name="monitoring"></a>Figyelés

### <a name="metrics"></a>Mérőszámok

Az Azure Monitor metrikák segítségével megtekintheti és figyelheti az Azure Machine Learning-munkaterület metrikákat. Az [Azure Portalon](https://portal.azure.com)válassza ki a munkaterületet, majd válassza **a Metrikák**lehetőséget:

[![Egy munkaterület példamutatóit ábrázoló képernyőkép](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

A metrikák a futtatásokra, az üzemelő telepítésekre és a regisztrációkra vonatkozó információkat tartalmaznak.

További információ: [Metrics in Azure Monitor.](/azure/azure-monitor/platform/data-platform-metrics)

### <a name="activity-log"></a>Tevékenységnapló

A munkaterület tevékenységnaplójában megtekintheti a munkaterületen végrehajtott különböző műveleteket. A napló olyan alapvető információkat tartalmaz, mint a művelet neve, az esemény kezdeményezője és az időbélyeg.

Ez a képernyőkép egy munkaterület tevékenységnaplóját mutatja:

[![Munkaterület tevékenységnaplóját bemutató képernyőkép](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

A pontozási kérelmek részletei az Application Insightsban tárolódnak. Az Application Insights az előfizetésben jön létre, amikor létrehoz egy munkaterületet. A naplózott adatok olyan mezőket tartalmaznak, mint például:

* HTTPMódszer
* Useragent
* Számítási típus
* RequestUrl
* StatusCode (Állapotkód)
* Kérelemazonosító
* Időtartam

> [!IMPORTANT]
> Az Azure Machine Learning-munkaterületen bizonyos műveletek nem naplózzák az adatokat a tevékenységnaplóba. Például egy betanítási futtatás kezdete és a modell regisztrációja nem kerül naplózásra.
>
> A műveletek némelyike a munkaterület **Tevékenységek** területén jelenik meg, de ezek az értesítések nem jelzik, hogy ki kezdeményezte a tevékenységet.

## <a name="data-flow-diagrams"></a>Adatfolyam-diagramok

### <a name="create-workspace"></a>Munkaterület létrehozása

Az alábbi ábrán a munkaterületi munkafolyamat létrehozása látható.

* Az Azure AD-be a támogatott Azure Machine Learning-ügyfelek (Azure CLI, Python SDK, Azure Portal) egyikéről jelentkezik be, és kéri a megfelelő Azure Resource Manager-jogkivonatot.
* A munkaterület létrehozásához hívja meg az Azure Resource Managert. 
* Az Azure Resource Manager kapcsolatba lép az Azure Machine Learning erőforrás-szolgáltatóval a munkaterület kiépítése érdekében.

További erőforrások jönnek létre a felhasználó előfizetésében a munkaterület létrehozása során:

* Key Vault (titkok tárolásához)
* Azure-tárfiók (beleértve a blobot és a fájlmegosztást)
* Azure Container Registry (docker-rendszerképek tárolása következtetés/pontozás és kísérletezés érdekében)
* Application Insights (telemetriai adatok tárolására)

A felhasználó is kiépítheti a munkaterülethez csatlakoztatott egyéb számítási célokat (például az Azure Kubernetes-szolgáltatáshoz vagy a virtuális gépekhez) szükség szerint.

[![Munkaterületi munkafolyamat létrehozása](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Forráskód mentése (betanítási parancsfájlok)

Az alábbi ábra a kód pillanatfelvételi munkafolyamatát mutatja be.

Az Azure Machine Learning-munkaterülethez olyan könyvtárak (kísérletek), amelyek tartalmazzák a forráskódot (betanítási parancsfájlokat). Ezek a parancsfájlok a helyi gépen és a felhőben vannak tárolva (az Azure Blob storage-ban az előfizetéshez). A kód pillanatképek végrehajtásához vagy ellenőrzéshez szolgálnak a korábbi naplózáshoz.

[![Kódpillanatkép-munkafolyamat](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Képzés

Az alábbi ábrán a betanítási munkafolyamat látható.

* Az Azure Machine Learning neve az előző szakaszban mentett kódpillanatkép pillanatkép-azonosítójával történik.
* Az Azure Machine Learning létrehoz egy futtatási azonosítót (nem kötelező) és egy Machine Learning szolgáltatás jogkivonatot, amelyet később számítási célok, például a Machine Learning számítási/virtuális gépek a Machine Learning szolgáltatással való kommunikációhoz használnak.
* Választhat egy felügyelt számítási cél (például a Machine Learning Compute) vagy egy nem felügyelt számítási cél (például virtuális gépek) a betanítási feladatok futtatásához. Az adatfolyamok mindkét esetben a következők:
   * Virtuális gépek/HDInsight, ssh hitelesítő adatokkal érhető el a Microsoft-előfizetés egyik kulcstartójában. Az Azure Machine Learning felügyeleti kódot futtat a számítási célon, amely:

   1. Előkészíti a környezetet. (A Docker a virtuális gépek és a helyi számítógépek számára is használható. Tekintse meg az alábbi lépéseket a Machine Learning Compute a Docker-tárolókon futó kísérletek működésének megismeréséhez.)
   1. Letölti a kódot.
   1. Környezeti változók és konfigurációk beállítása.
   1. Futtatja a felhasználói parancsfájlokat (az előző szakaszban említett kódpillanatkép).

   * Machine Learning Compute, egy munkaterület által felügyelt identitáson keresztül érhető el.
Mivel a Machine Learning Compute egy felügyelt számítási cél (azaz a Microsoft kezeli), a Microsoft-előfizetés alatt fut.

   1. Távoli Docker építési elindult, ha szükséges.
   1. A felügyeleti kód a felhasználó Azure Files-megosztására íródott.
   1. A tároló egy kezdeti paranccsal indul. Ez azt, hogy az előző lépésben leírtak szerint a felügyeleti kód.

#### <a name="querying-runs-and-metrics"></a>Futások és mérőszámok lekérdezése

Az alábbi folyamatábrán ez a lépés akkor következik be, amikor a betanítási számítási cél írja a futtatási metrikák at az Azure Machine Learning a Cosmos DB-adatbázis tárolójából. Az ügyfelek hívhatják az Azure Machine Learninget. A Machine Learning a metrikákat a Cosmos DB adatbázisból lekéri, és visszaküldi azokat az ügyfélnek.

[![Képzési munkafolyamat](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Webszolgáltatások létrehozása

Az alábbi ábrán a következtetési munkafolyamat látható. A következtetés vagy a modell pontozása az a fázis, amelyben az üzembe helyezett modell előrejelzési, leggyakrabban a termelési adatok.

Itt vannak a részletek:

* A felhasználó regisztrál egy modellt egy ügyfél, például az Azure Machine Learning SDK használatával.
* A felhasználó egy modell, egy pontszámfájl és más modellfüggőségek használatával hoz létre egy lemezképet.
* A Docker-rendszerkép jön létre, és az Azure Container Registry tárolja.
* A webszolgáltatás a számítási cél (container instances/AKS) az előző lépésben létrehozott lemezkép használatával van telepítve.
* A pontozási kérelem részletei az Application Insightsban tárolódnak, amely a felhasználó előfizetésében található.
* Telemetriai adatok is leküldéses a Microsoft/Azure-előfizetés.

[![Következtetési munkafolyamat](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Machine Learning-webszolgáltatások a TLS-sel](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [Kötegelt előrejelzések futtatása](how-to-use-parallel-run-step.md)
* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az Azure Machine Learning használata az Azure virtuális hálózattal](how-to-enable-virtual-network.md)
* [Gyakorlati tanácsok az épületajánlási rendszerekhez](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlási API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
