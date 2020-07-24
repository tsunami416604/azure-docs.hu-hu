---
title: Vállalati biztonság
titleSuffix: Azure Machine Learning
description: 'Biztonságos használat Azure Machine Learning: hitelesítés, engedélyezés, hálózati biztonság, adattitkosítás és figyelés.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: 94724ea44b52ae885594fe55b67d74a03e339dab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012881"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Vállalati biztonsági Azure Machine Learning

Ebben a cikkben megismerheti a Azure Machine Learning számára elérhető biztonsági funkciókat.

Ha Cloud Service-t használ, az ajánlott eljárás a hozzáférés korlátozása csak azokra a felhasználókra, akiknek szükségük van rá. Először is megismerheti a szolgáltatás által használt hitelesítési és engedélyezési modellt. Előfordulhat, hogy korlátozni kívánja a hálózati hozzáférést, vagy biztonságos módon csatlakoztatja a helyszíni hálózatban lévő erőforrásokat a felhőhöz. Az adattitkosítás is létfontosságú, mind a nyugalmi állapotban, mind pedig az adatok a szolgáltatások között mozognak. Végezetül meg kell tudnia figyelni a szolgáltatást, és minden tevékenységhez létre kell hoznia egy naplót.

> [!NOTE]
> A cikkben található információk a Python SDK Azure Machine Learning 1.0.83.1 vagy újabb verziójával működnek.

## <a name="authentication"></a>Hitelesítés

A többtényezős hitelesítés akkor támogatott, ha a Azure Active Directory (Azure AD) használatára van konfigurálva. A hitelesítési folyamat a következő:

1. Az ügyfél bejelentkezik az Azure AD-be, és beolvas egy Azure Resource Manager tokent.  A felhasználók és az egyszerű szolgáltatások teljes mértékben támogatottak.
1. Az ügyfél az Azure Resource Manager és az összes Azure Machine Learning számára megadja a tokent.
1. A Machine Learning szolgáltatás Machine Learning szolgáltatási jogkivonatot biztosít a felhasználó számítási céljához (például Machine Learning Compute). Ezt a tokent a felhasználói számítási cél használja a Machine Learning szolgáltatás visszahívására a Futtatás befejezése után. A hatókör csak a munkaterületre korlátozódik.

[![Hitelesítés Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

További információ: [Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](how-to-setup-authentication.md). Ez a cikk a hitelesítéssel kapcsolatos információkat és példákat tartalmaz, beleértve az egyszerű szolgáltatások és az automatizált munkafolyamatok használatát.

### <a name="authentication-for-web-service-deployment"></a>Hitelesítés a webszolgáltatások üzembe helyezéséhez

Azure Machine Learning a következő két hitelesítési módszert támogatja a webszolgáltatások esetében: kulcs és jogkivonat. Az egyes webszolgáltatások egyszerre csak egy hitelesítési űrlapot tudnak engedélyezni.

|Hitelesítési módszer|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Kulcs|A kulcsok statikusak, és nem kell frissíteni. A kulcsok újragenerálása manuálisan végezhető el.|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezett|
|Jogkivonat|A tokenek a megadott időszak után lejárnak, és frissíteni kell őket.| Nem elérhető| Alapértelmezés szerint letiltva |

A kódokra vonatkozó példákat a [webszolgáltatások hitelesítése című szakaszban](how-to-setup-authentication.md#web-service-authentication)találja.

## <a name="authorization"></a>Engedélyezés

Több munkaterületet is létrehozhat, és az egyes munkaterületek több személy számára is megoszthatók. Munkaterületek megosztásakor a hozzáférését a következő szerepköröknek a felhasználókhoz való hozzárendelésével szabályozhatja:

* Tulajdonos
* Közreműködő
* Olvasó

A következő táblázat a főbb Azure Machine Learning-műveleteit és az azokat elvégező szerepköröket sorolja fel:

| Azure Machine Learning művelet | Tulajdonos | Közreműködő | Olvasó |
| ---- |:----:|:----:|:----:|
| Munkaterület létrehozása | ✓ | ✓ | |
| Munkaterület megosztása | ✓ | |  |
| Munkaterület frissítése nagyvállalati verzióra | ✓ | |
| Számítási cél létrehozása | ✓ | ✓ | |
| Számítási cél csatolása | ✓ | ✓ | |
| Adattárak csatolása | ✓ | ✓ | |
| Kísérlet futtatása | ✓ | ✓ | |
| Futtatások/mérőszámok megtekintése | ✓ | ✓ | ✓ |
| Modell regisztrálása | ✓ | ✓ | |
| Rendszerkép létrehozása | ✓ | ✓ | |
| Webszolgáltatás üzembe helyezése | ✓ | ✓ | |
| Modellek/lemezképek megtekintése | ✓ | ✓ | ✓ |
| Webszolgáltatás hívása | ✓ | ✓ | ✓ |

Ha a beépített szerepkörök nem felelnek meg az igényeinek, létrehozhat egyéni szerepköröket is. Az egyéni szerepkörök csak a munkaterületen lévő műveleteknél és Machine Learning Compute támogatottak. Az egyéni szerepkörök olvasási, írási és törlési engedélyekkel rendelkezhetnek a munkaterületen és a munkaterületen lévő számítási erőforráson. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti. További információ: [felhasználók és szerepkörök kezelése Azure Machine learning munkaterületen](how-to-assign-roles.md).

> [!WARNING]
> A Azure Machine Learning Azure Active Directory vállalatok közötti együttműködésben támogatott, de jelenleg nem támogatott a Azure Active Directory vállalat – felhasználó együttműködésben.

### <a name="securing-compute-targets-and-data"></a>Számítási célok és adatok biztonságossá tétele

A tulajdonosok és a közreműködők használhatják a munkaterülethez csatolt összes számítási célt és adattárat.  

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás is, amelynek a neve megegyezik a munkaterülettel. A felügyelt identitás a következő engedélyekkel rendelkezik a munkaterületen használt csatolt erőforrásokhoz.

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Erőforrás | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő |
| Tárfiók | Storage blob adatközreműködői |
| Key Vault | Hozzáférés az összes kulcshoz, titokhoz, tanúsítványhoz |
| Azure Container Registry | Közreműködő |
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő |
| A kulcstárolót tartalmazó erőforráscsoport (ha eltér a munkaterületet tartalmazótól) | Közreműködő |

Nem javasoljuk, hogy a rendszergazdák visszavonják a felügyelt identitás hozzáférését az előző táblázatban említett erőforrásokhoz. A hozzáférést a kulcsok újraszinkronizálása művelettel állíthatja vissza.

A Azure Machine Learning egy további alkalmazást hoz létre (a név `aml-` a `Microsoft-AzureML-Support-App-` következővel kezdődik: vagy), és közreműködői szintű hozzáféréssel rendelkezik az előfizetésben az egyes munkaterület-régiókban. Ha például egy, az USA keleti régiójában és egy észak-európai régióban található egy munkaterülete ugyanabban az előfizetésben, akkor két alkalmazás fog megjelenni. Ezek az alkalmazások lehetővé teszik Azure Machine Learning számára a számítási erőforrások kezelését.

## <a name="network-security"></a>Hálózati biztonság

A Azure Machine Learning a számítási erőforrások egyéb Azure-szolgáltatásaira támaszkodik. A számítási erőforrások (számítási célok) a modellek betanítására és üzembe helyezésére szolgálnak. Ezeket a számítási célokat virtuális hálózatban is létrehozhatja. Például az Azure Data Science Virtual Machine használatával betaníthatja a modelleket, majd üzembe helyezheti a modellt az AK-ra.  

További információ: Hogyan lehet [biztonságosan futtatni a kísérleteket és következtetéseket egy elkülönített virtuális hálózatban](how-to-enable-virtual-network.md).

Az Azure Private-hivatkozást is engedélyezheti a munkaterülethez. A privát hivatkozás lehetővé teszi, hogy a kommunikációt egy Azure-Virtual Network korlátozza a munkaterületre. További információt a [privát hivatkozás konfigurálása](how-to-configure-private-link.md)című témakörben talál.

## <a name="data-encryption"></a>Adattitkosítás

> [!IMPORTANT]
> Az éles környezetben való titkosításhoz __a Microsoft__Azure Machine learning számítási fürt használatát javasolja. A Microsoft az Azure Kubernetes Service használatát javasolja az éles környezetbeli adattitkosításhoz a __következtetések__során.
>
> Azure Machine Learning számítási példány fejlesztési és tesztelési környezet. A használatakor azt javasoljuk, hogy a fájlokat, például jegyzetfüzeteket és parancsfájlokat egy fájlmegosztás tárolja. Az adatait adattárban kell tárolni.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

> [!IMPORTANT]
> Ha a munkaterület bizalmas adatokat tartalmaz, javasoljuk, hogy a munkaterület létrehozásakor a [hbi_workspace jelzőt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) állítsa be. 

A `hbi_workspace` jelző szabályozza a Microsoft által diagnosztikai célokra gyűjtött adatok mennyiségét, és lehetővé teszi a további titkosítást a Microsoft által felügyelt környezetekben. Emellett a következő műveleteket is lehetővé teszi:

* Megkezdi a helyi lemez titkosítását a Azure Machine Learning számítási fürtben, ha nem hozott létre egy korábbi fürtöt az előfizetésben. Más esetben egy támogatási jegyet kell létrehoznia, amely lehetővé teszi a számítási fürtökhöz tartozó kaparós lemez titkosítását 
* Kiüríti az ideiglenes lemezt a futtatások között
* Biztonságosan továbbítja a Storage-fiók, a tároló-beállításjegyzék és az SSH-fiók hitelesítő adatait a végrehajtási rétegből a kulcstartó használatával a számítási fürtökbe
* Engedélyezi az IP-szűrést annak érdekében, hogy a mögöttes batch-készletek ne legyenek meghívva a AzureMachineLearningService-től eltérő külső szolgáltatásokkal.

> [!WARNING]
> A `hbi_workspace` jelző csak akkor állítható be, ha létrehoznak egy munkaterületet. A meglévő munkaterületek esetében nem módosítható.

További információ arról, hogyan működik a titkosítás az Azure-ban: inaktív [Azure-adattitkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

#### <a name="azure-blob-storage"></a>Azure Blob Storage

A Azure Machine Learning a pillanatképeket, a kimeneteket és a naplókat a Azure Machine Learning munkaterülethez és az előfizetéséhez kötött Azure Blob Storage-fiókban tárolja. Az Azure Blob Storage-ban tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

Az Azure Blob Storage-ban tárolt adatok saját kulcsaival kapcsolatos információkért lásd: az [Azure Storage titkosítása az ügyfél által felügyelt kulcsokkal Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

A betanítási adatok általában az Azure Blob Storage-ban is tárolódnak, hogy elérhetők legyenek a számítási célok betanításához. Ezt a tárolót nem Azure Machine Learning felügyeli, de távoli fájlrendszerként van csatlakoztatva a számítási célokhoz.

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, bármikor megteheti. A kulcsok elforgatásakor a Storage-fiók az új kulccsal (legújabb verzióval) kezdi meg az adatok titkosítását a nyugalmi állapotban. A kulcsok visszavonása (letiltása) esetén a Storage-fiók gondoskodik a sikertelen kérelmekről. Ez általában egy órát vesz igénybe a rotáció vagy a visszavonás érvénybe léptetéséhez.

A hozzáférési kulcsok újragenerálásával kapcsolatos információkért lásd: [tároló-hozzáférési kulcsok](how-to-change-storage-access-key.md)újragenerálása.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Machine Learning a metrikákat és a metaadatokat egy Azure Cosmos DB példányban tárolja. Ez a példány egy Azure Machine Learning által felügyelt Microsoft-előfizetéshez van társítva. A Azure Cosmos DB tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

Ha saját (ügyfél által felügyelt) kulcsokat kíván használni a Azure Cosmos DB-példány titkosításához, létrehozhat egy dedikált Cosmos DB-példányt a munkaterülethez való használatra. Ezt a megközelítést javasoljuk, ha a Microsoft-előfizetésben üzemeltetett több-bérlős Cosmos DB példányon kívül szeretné tárolni az adatokat, például a futtatási előzmények információit. 

Az ügyfél által felügyelt kulcsokkal rendelkező Cosmos DB példány kiépítés engedélyezéséhez hajtsa végre a következő műveleteket:

* Regisztrálja a Microsoft. MachineLearning és a Microsoft.DocumentDB erőforrás-szolgáltatót az előfizetésében, ha még nem tette meg.

* Engedélyezze az előfizetéshez tartozó közreműködői engedélyekkel rendelkező Machine Learning alkalmazást (az identitás-és hozzáférés-kezelésben).

    ![Az "Azure Machine Learning alkalmazás" engedélyezése a portálon az identitás-és hozzáférés-kezelésben](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* A Azure Machine Learning munkaterület létrehozásakor használja a következő paramétereket. Mindkét paraméter kötelező és támogatott az SDK-ban, a CLI-ben, a REST API-kon és a Resource Manager-sablonokban.

    * `resource_cmk_uri`: Ez a paraméter a Key vaultban lévő ügyfél által felügyelt kulcs teljes erőforrás-URI-ja, beleértve a [kulcs verziószámát](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)is. 

    * `cmk_keyvault`: Ez a paraméter az előfizetésében található kulcstartó erőforrás-azonosítója. Ennek a kulcstartónak ugyanabban a régióban és előfizetésben kell lennie, amelyet az Azure Machine Learning munkaterülethez fog használni. 
    
        > [!NOTE]
        > Ez a Key Vault-példány különbözhet a munkaterület kiépítésekor Azure Machine Learning által létrehozott kulcstartótól. Ha ugyanazt a Key Vault-példányt szeretné használni a munkaterülethez, a [key_vault paraméter](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)használatával adja meg ugyanazt a kulcstartót, miközben a munkaterületet kiépíti. 

Ezt a Cosmos DB példányt egy Microsoft által felügyelt erőforráscsoport hozza létre az előfizetésében. A felügyelt erőforráscsoport formátuma a neve `<AML Workspace Resource Group Name><GUID>` .

> [!IMPORTANT]
> * Ha törölnie kell ezt a Cosmos DB példányt, törölnie kell az azt használó Azure Machine Learning munkaterületet. 
> * Ehhez a Cosmos DB-fiókhoz az alapértelmezett [__kérési egység__](../cosmos-db/request-units.md) __8000__-kor van beállítva. Az érték módosítása nem támogatott. 

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, bármikor megteheti. A kulcsok elforgatásakor Cosmos DB megkezdi az új kulcs (legújabb verzió) használatát az inaktív adatok titkosításához. Kulcs visszavonása (letiltása) esetén Cosmos DB a sikertelen kérelmeket. Ez általában egy órát vesz igénybe a rotáció vagy a visszavonás érvénybe léptetéséhez.

Az ügyfél által felügyelt Cosmos DB kulcsokkal kapcsolatos további információkért lásd: az [ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos db-fiókhoz](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

A beállításjegyzékben található összes tároló lemezkép (Azure Container Registry) inaktív állapotban van. Az Azure automatikusan titkosítja a képeket a tárolás előtt, és visszafejti azt, amikor Azure Machine Learning lekéri a rendszerképet.

Ha saját (felhasználó által felügyelt) kulcsokat kíván használni a Azure Container Registry titkosításához, létre kell hoznia a saját ACR-t, és csatolnia kell azt a munkaterület kiépítés során, vagy titkosítania kell az alapértelmezett példányt, amelyet a rendszer a munkaterület üzembe helyezésének időpontjában hoz létre.

> [!IMPORTANT]
> A Azure Machine Learning megköveteli, hogy a rendszergazdai fiók engedélyezve legyen a Azure Container Registry. Alapértelmezés szerint ez a beállítás le van tiltva a tároló-beállításjegyzék létrehozásakor. A rendszergazdai fiók engedélyezésével kapcsolatos információkért lásd: [rendszergazdai fiók](/azure/container-registry/container-registry-authentication#admin-account).
>
> Ha egy munkaterülethez Azure Container Registry lett létrehozva, ne törölje. Ezzel megszakítja Azure Machine Learning munkaterületét.

A munkaterületek meglévő Azure Container Registry használatával történő létrehozásának példáját a következő cikkekben találja:

* [Hozzon létre egy munkaterületet Azure Machine learning az Azure CLI-vel](how-to-manage-workspace-cli.md).
* [Munkaterületek létrehozása Azure Machine Learninghez Azure Resource Manager sablon használatával](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

Az üzembe helyezett Azure Container instance-(ACI-) erőforrások titkosítása az ügyfél által felügyelt kulcsokkal végezhető el. Az ACI-hoz használt ügyfél által felügyelt kulcs a munkaterület Azure Key Vault tárolható. A kulcsok létrehozásával kapcsolatos információkért lásd: [adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Ahhoz, hogy a kulcsot a modell Azure Container-példányra történő telepítésekor használja, hozzon létre egy új központi telepítési konfigurációt a használatával `AciWebservice.deploy_configuration()` . Adja meg a kulcs adatait a következő paraméterekkel:

* `cmk_vault_base_url`: A kulcsot tartalmazó kulcstartó URL-címe.
* `cmk_key_name`: A kulcs neve.
* `cmk_key_version`: A kulcs verziószáma.

A központi telepítési konfiguráció létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) hivatkozás
* [Az üzembe helyezés módja és helye](how-to-deploy-and-where.md)
* [Modell üzembe helyezése Azure Container Instances](how-to-deploy-azure-container-instance.md)

Az ACI-val rendelkező, ügyfél által felügyelt kulcs használatával kapcsolatos további információkért lásd: [adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az üzembe helyezett Azure Kubernetes szolgáltatásbeli erőforrásokat bármikor titkosíthatja az ügyfél által felügyelt kulcsokkal. További információ: [saját kulcsok használata az Azure Kubernetes szolgáltatással](../aks/azure-disk-customer-managed-keys.md). 

Ez a folyamat lehetővé teszi a Kubernetes-fürtben lévő telepített virtuális gépek és az operációs rendszer lemezének titkosítását.

> [!IMPORTANT]
> Ez a folyamat csak az K8s 1,17-es vagy újabb verziójával működik. Azure Machine Learning a 1,17-es, Jan. január 13-án a 2020-os támogatást adta hozzá.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

Az Azure Storage-ban tárolt összes számítási csomópont operációsrendszer-lemeze titkosítva van a Microsoft által felügyelt kulcsokkal Azure Machine Learning Storage-fiókokban. Ez a számítási cél elmúló, és a fürtöket jellemzően akkor kell lekicsinyíteni, ha nincsenek várólistán lévő futtatások. A mögöttes virtuális gép kiépítve, az operációsrendszer-lemez pedig törlődik. Az operációsrendszer-lemez nem támogatja a Azure Disk Encryption.

Minden virtuális gép helyi ideiglenes lemezzel is rendelkezik az operációs rendszer műveleteihez. Ha szeretné, használhatja a lemezt a betanítási adatgyűjtéshez. Alapértelmezés szerint a lemez titkosítva van a (z `hbi_workspace` ) paraméterrel beállított munkaterületek esetében `TRUE` . Ez a környezet csak a Futtatás időtartamára vonatkozik, és a titkosítási támogatás csak a rendszer által felügyelt kulcsokra korlátozódik.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks használható Azure Machine Learning folyamatokban. Alapértelmezés szerint a Azure Databricks által használt Databricks fájlrendszer (DBFS) Microsoft által felügyelt kulccsal van titkosítva. Az ügyfél által felügyelt kulcsok használatára Azure Databricks konfigurálását lásd: az [ügyfél által felügyelt kulcsok konfigurálása az alapértelmezett (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

A Azure Machine Learning a TLS-t használja a különböző Azure Machine Learning-szolgáltatások közötti belső kommunikáció biztonságossá tételéhez. Az Azure Storage összes hozzáférése egy biztonságos csatornán is megtörténik.

A pontozási végponton végzett külső hívások biztonságossá tételéhez Azure Machine Learning a TLS protokollt használja. További információkért lásd: [webszolgáltatások biztonságossá tétele a TLS használatával Azure Machine Learningon keresztül](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Azure Key Vault használata

Azure Machine Learning a munkaterülethez társított Azure Key Vault-példányt használja a különböző típusú hitelesítő adatok tárolásához:

* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Az SSH-jelszavak és-kulcsok a számítási célokhoz, például az Azure HDInsight és a virtuális gépekhez a Microsoft-előfizetéshez társított külön kulcstartóban tárolódnak. Azure Machine Learning nem tárolja a felhasználók által biztosított jelszavakat vagy kulcsokat. Ehelyett saját SSH-kulcsokat hoz létre, engedélyez és tárol a virtuális gépekhez és HDInsight való kapcsolódáshoz a kísérletek futtatásához.

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás, amelynek a neve megegyezik a munkaterülettel. Ez a felügyelt identitás hozzáfér a kulcstartóban található összes kulcshoz, titokhoz és tanúsítványhoz.

## <a name="data-collection-and-handling"></a>Adatgyűjtés és-kezelés

### <a name="microsoft-collected-data"></a>Microsoft összegyűjtött adatok

A Microsoft a nem felhasználótól származó azonosító adatokat (például az adathalmaz nevét vagy a Machine learning-kísérlet nevét) vagy a munkahelyi környezeti változókat diagnosztikai célokra gyűjtheti. Az összes ilyen adatokat a Microsoft által felügyelt kulcsok tárolják a Microsoft tulajdonában lévő előfizetésekben üzemeltetett tárolókban, és a [Microsoft szabványos adatvédelmi szabályzatát és adatkezelési szabványait](https://privacy.microsoft.com/privacystatement)követi.

A Microsoft azt is javasolja, hogy ne tárolja a bizalmas adatokat (például a fiók kulcsának titkos adatait) a környezeti változókban. A környezeti változók naplózása, titkosítása és tárolása az USA-ban történik. Hasonlóképpen a [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)elnevezése esetén Kerülje a bizalmas adatokat, például a felhasználóneveket vagy a titkos projektek nevét. Ezek az információk megjelenhetnek Microsoft ügyfélszolgálata mérnökök számára elérhető telemetria-naplókban.

A gyűjtött diagnosztikai adatok közül kiválaszthatja, `hbi_workspace` hogy a paramétert úgy állítja be, hogy `TRUE` kiépítse a munkaterületet. Ez a funkció a AzureML Python SDK, a CLI, a REST API-k vagy a Azure Resource Manager-sablonok használata esetén támogatott.

### <a name="microsoft-generated-data"></a>Microsoft által generált adatszolgáltatások

Ha olyan szolgáltatásokat használ, mint például az automatizált Machine Learning, a Microsoft átmeneti, előre feldolgozott adatfeldolgozást eredményezhet több modell betanításához. Ezeket az adatait a munkaterület egy adattárában tárolja, amely lehetővé teszi a hozzáférés-vezérlés és a titkosítás megfelelő betartatását.

Érdemes lehet titkosítani az [üzembe helyezett végpontból naplózott diagnosztikai adatokat](how-to-enable-app-insights.md) az Azure Application Insights-példányba.

## <a name="monitoring"></a>Figyelés

### <a name="metrics"></a>Mérőszámok

Azure Monitor metrikák használatával megtekintheti és figyelheti a Azure Machine Learning munkaterület metrikáit. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet, majd válassza a **metrikák**elemet:

[![A munkaterület mérőszámait ábrázoló képernyőkép](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

A metrikák a futtatások, a központi telepítések és a regisztrációk információit tartalmazzák.

További információ: [Azure monitor mérőszámai](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Tevékenységnapló

A munkaterületek tevékenység naplóját megtekintve megtekintheti a munkaterületen végrehajtott különböző műveleteket. A napló alapvető adatokat tartalmaz, például a művelet nevét, az esemény kezdeményezőjét és az időbélyeget.

Ez a képernyőkép a munkaterület tevékenység naplóját jeleníti meg:

[![Munkaterület tevékenységi naplóját ábrázoló képernyőfelvétel](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

A pontozási kérelmek részleteit a Application Insights tárolja. A munkaterületek létrehozásakor Application Insights jön létre az előfizetésben. A naplózott adatok olyan mezőket tartalmaznak, mint például a következők:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* Kérelemazonosító
* Időtartam

> [!IMPORTANT]
> A Azure Machine Learning munkaterület egyes műveletei nem naplózzák az adatokat a tevékenység naplójába. Például a képzés kezdete és a modell regisztrálása nem kerül be a naplóba.
>
> A műveletek némelyike a munkaterület **tevékenységek** területén jelenik meg, de ezek az értesítések nem jelzik, hogy ki kezdeményezte a tevékenységet.

## <a name="data-flow-diagrams"></a>Adatfolyam-diagramok

### <a name="create-workspace"></a>Munkaterület létrehozása

A következő ábra a munkaterület létrehozása munkafolyamatot mutatja be.

* Jelentkezzen be az Azure AD-be az egyik támogatott Azure Machine Learning ügyfélről (Azure CLI, Python SDK, Azure Portal), és kérje meg a megfelelő Azure Resource Manager tokent.
* A munkaterületet a Azure Resource Manager meghívásával hozhatja létre. 
* Azure Resource Manager a munkaterület kiépítéséhez kapcsolatba lép a Azure Machine Learning erőforrás-szolgáltatóval.

A Munkaterületek létrehozása során további erőforrások jönnek létre a felhasználó előfizetésében:

* Key Vault (a titkok tárolására)
* Egy Azure Storage-fiók (beleértve a blobot és a fájlmegosztást)
* Azure Container Registry (a Docker-rendszerképek tárolása a következtetések/pontozás és kísérletezés érdekében)
* Application Insights (a telemetria tárolásához)

A felhasználó a munkaterülethez (például az Azure Kubernetes szolgáltatáshoz vagy virtuális gépekhez) kapcsolódó egyéb számítási célokat is kiépítheti, ha szükséges.

[![Munkaterület-munkafolyamat létrehozása](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Forráskód mentése (betanítási parancsfájlok)

Az alábbi ábrán a kód pillanatkép-munkafolyamata látható.

Az Azure Machine Learning munkaterülethez tartozó könyvtárak (kísérletek) a forráskódot tartalmazzák (betanítási parancsfájlok). Ezeket a szkripteket a helyi gépen és a felhőben (az előfizetéséhez tartozó Azure Blob Storage-ban) tárolja a rendszer. A kód pillanatképei a korábbi naplózás végrehajtásához vagy ellenőrzéséhez használatosak.

[![Kód pillanatkép-munkafolyamata](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Oktatás

Az alábbi ábra a betanítási munkafolyamatot mutatja be.

* A Azure Machine Learning a rendszer az előző szakaszban mentett kód pillanatképéhez tartozó pillanatkép-AZONOSÍTÓval hívja meg.
* Azure Machine Learning létrehoz egy futtatási azonosítót (nem kötelező) és egy Machine Learning szolgáltatási jogkivonatot, amelyet később a számítási célok, például a Machine Learning Compute/virtuális gépek használják a Machine Learning szolgáltatással való kommunikációhoz.
* Kiválaszthat egy felügyelt számítási célt (például Machine Learning Compute) vagy egy nem felügyelt számítási célt (például virtuális gépeket) a betanítási feladatok futtatásához. Mindkét forgatókönyv esetén az alábbi adatfolyamatok érhetők el:
   * Virtuális gépek/HDInsight, a Microsoft-előfizetés egyik kulcstartójában elérhető SSH hitelesítő adatokkal. Azure Machine Learning a következő számítási célra futtatja a felügyeleti kódot:

   1. Előkészíti a környezetet. (A Docker lehetőséget biztosít a virtuális gépek és a helyi számítógépek számára. A következő lépésekből megtudhatja, hogyan működik a kísérletek futtatása a Docker-tárolókban Machine Learning Compute.)
   1. Letölti a kódot.
   1. Beállítja a környezeti változókat és a konfigurációkat.
   1. Futtatja a felhasználói parancsfájlokat (az előző szakaszban említett kód-pillanatképet).

   * Machine Learning Compute, amely egy munkaterület által felügyelt identitáson keresztül érhető el.
Mivel Machine Learning Compute felügyelt számítási cél (azaz a Microsoft felügyeli), a Microsoft-előfizetése alatt fut.

   1. Ha szükséges, a távoli Docker-konstrukció indult el.
   1. A felügyeleti kód a felhasználó Azure Files megosztására íródik.
   1. A tároló egy kezdeti paranccsal indult el. Ez a felügyeleti kód az előző lépésben leírtak szerint.

#### <a name="querying-runs-and-metrics"></a>Futtatások és metrikák lekérdezése

Az alábbi folyamatábrán ez a lépés akkor következik be, amikor a betanítási cél a futtatási metrikákat a Cosmos DB-adatbázisban lévő tárterületről Azure Machine Learningra írja vissza. Az ügyfelek hívhatják Azure Machine Learning. Machine Learning a Cosmos DB-adatbázisból lekéri a metrikákat, majd visszaküldi azokat az ügyfélnek.

[![Betanítási munkafolyamat](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Webszolgáltatások létrehozása

A következő ábra a következtetési munkafolyamatot mutatja be. A következtetés vagy a modell pontozása az a fázis, amelyben a rendszer az üzembe helyezett modellt használja az előrejelzéshez, leggyakrabban az éles adatforgalomra.

A részletek a következők:

* A felhasználó egy olyan ügyfél használatával regisztrálja a modellt, mint a Azure Machine Learning SDK-t.
* A felhasználó létrehoz egy rendszerképet egy modell, egy pontszám-fájl és más modell függőségei használatával.
* A Docker-rendszerkép létrehozása és tárolása Azure Container Registryban történik.
* A webszolgáltatás üzembe helyezése a számítási célra (Container Instances/ak) történik az előző lépésben létrehozott rendszerkép használatával.
* A pontozási kérés részleteit Application Insights tárolja a rendszer, amely a felhasználó előfizetésében található.
* A telemetria a Microsoft/Azure-előfizetésre is leküldve.

[![Következtetési munkafolyamat](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Machine Learning webszolgáltatások TLS-vel](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [Azure Machine Learning használata a Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning használata az Azure-ban Virtual Network](how-to-enable-virtual-network.md)
* [Ajánlott eljárások az ajánlásokat tartalmazó rendszerek létrehozásához](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlási API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
