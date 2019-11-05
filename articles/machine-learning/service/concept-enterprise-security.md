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
ms.date: 11/04/2019
ms.openlocfilehash: e834c55ec35195ff627176603c7611abbf6adf1c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497501"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Vállalati biztonsági Azure Machine Learning

Ebben a cikkben megismerheti a Azure Machine Learning számára elérhető biztonsági funkciókat.

Ha Cloud Service-t használ, az ajánlott eljárás a hozzáférés korlátozása csak azokra a felhasználókra, akiknek szükségük van rá. Először is megismerheti a szolgáltatás által használt hitelesítési és engedélyezési modellt. Előfordulhat, hogy korlátozni kívánja a hálózati hozzáférést, vagy biztonságos módon csatlakoztatja a helyszíni hálózatban lévő erőforrásokat a felhőhöz. Az adattitkosítás is létfontosságú, mind a nyugalmi állapotban, mind pedig az adatok a szolgáltatások között mozognak. Végezetül meg kell tudnia figyelni a szolgáltatást, és minden tevékenységhez létre kell hoznia egy naplót.

## <a name="authentication"></a>Authentication

A többtényezős hitelesítés akkor támogatott, ha a Azure Active Directory (Azure AD) használatára van konfigurálva. A hitelesítési folyamat a következő:

1. Az ügyfél bejelentkezik az Azure AD-be, és beolvas egy Azure Resource Manager tokent.  A felhasználók és az egyszerű szolgáltatások teljes mértékben támogatottak.
1. Az ügyfél az Azure Resource Manager és az összes Azure Machine Learning számára megadja a tokent.
1. A Machine Learning szolgáltatás Machine Learning szolgáltatási jogkivonatot biztosít a felhasználó számítási céljához (például Machine Learning Compute). Ezt a tokent a felhasználói számítási cél használja a Machine Learning szolgáltatás visszahívására a Futtatás befejezése után. A hatókör csak a munkaterületre korlátozódik.

[![hitelesítés Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Hitelesítés a webszolgáltatások üzembe helyezéséhez

Azure Machine Learning a következő két hitelesítési módszert támogatja a webszolgáltatások esetében: kulcs és jogkivonat. Az egyes webszolgáltatások egyszerre csak egy hitelesítési űrlapot tudnak engedélyezni.

|Hitelesítési módszer|Azure Container Instances|AKS|
|---|---|---|
|Jelmagyarázat|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezve|
|Jogkivonat| Nem érhető el| Alapértelmezés szerint letiltva |

#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha engedélyezi a kulcsos hitelesítést a központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van az Azure Kubernetes szolgáltatásban (ak) való üzembe helyezéskor.
* A hitelesítés alapértelmezés szerint le van tiltva a Azure Container Instances telepítésekor.

A kulcsos hitelesítés engedélyezéséhez használja a `auth_enabled` paramétert egy központi telepítés létrehozásakor vagy frissítésekor.

Ha a kulcsos hitelesítés engedélyezve van, a `get_keys` metódussal kérhet le elsődleges és másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználóknak egy Azure Machine Learning JSON Web Token kell bemutatnia a webszolgáltatásnak az eléréséhez.

* Alapértelmezés szerint a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés nem támogatott, ha Azure Container Instances telepíti.

A jogkivonat-hitelesítés vezérléséhez használja a `token_auth_enabled` paramétert egy központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a jogkivonat-hitelesítés, használhatja a `get_token` metódust egy JSON Web Token (JWT) és a jogkivonat lejárati idejének lekéréséhez:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Új jogkivonatot kell kérnie a jogkivonat `refresh_by` idő után.
>
> Javasoljuk, hogy az Azure Kubernetes Service-fürttel azonos régióban hozza létre Azure Machine Learning munkaterületét. 
>
> A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, nem tud beolvasni egy jogkivonatot a webszolgáltatáshoz, még akkor sem, ha a fürt egy másik régióban található a munkaterületen. Ennek eredményeképpen az Azure AD-hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. 
>
> Továbbá minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

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

A Azure Machine Learning egy további alkalmazást hoz létre (a név `aml-` vagy `Microsoft-AzureML-Support-App-`), és közreműködői szintű hozzáféréssel rendelkezik az előfizetésben az egyes munkaterület-régiókban. Ha például egy, az USA keleti régiójában és egy másik, észak-európai munkaterületen lévő munkaterülettel rendelkezik ugyanabban az előfizetésben, két ilyen alkalmazást fog látni. Ezek az alkalmazások lehetővé teszik Azure Machine Learning számára a számítási erőforrások kezelését.

## <a name="network-security"></a>Hálózati biztonság

A Azure Machine Learning a számítási erőforrások egyéb Azure-szolgáltatásaira támaszkodik. A számítási erőforrások (számítási célok) a modellek betanítására és üzembe helyezésére szolgálnak. Ezeket a számítási célokat virtuális hálózatban is létrehozhatja. Például az Azure Data Science Virtual Machine használatával betaníthatja a modelleket, majd üzembe helyezheti a modellt az AK-ra.  

További információ: [kísérletek futtatása egy virtuális hálózatban](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Adattitkosítás

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

#### <a name="azure-blob-storage"></a>Azure Blob Storage

A Azure Machine Learning a pillanatképeket, a kimeneteket és a naplókat a Azure Machine Learning munkaterülethez és az előfizetéséhez kötött Azure Blob Storage-fiókban tárolja. Az Azure Blob Storage-ban tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

Az Azure Blob Storage-ban tárolt adatok saját kulcsaival kapcsolatos információkért lásd: az [Azure Storage titkosítása az ügyfél által felügyelt kulcsokkal Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

A betanítási adatok általában az Azure Blob Storage-ban is tárolódnak, hogy elérhetők legyenek a számítási célok betanításához. Ezt a tárolót nem Azure Machine Learning felügyeli, de távoli fájlrendszerként van csatlakoztatva a számítási célokhoz.

A munkaterülethez használt Azure Storage-fiókok hozzáférési kulcsainak újragenerálásával kapcsolatos információkért lásd: [tároló-hozzáférési kulcsok újragenerálása](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning az Azure Machine Learning által kezelt Microsoft-előfizetéshez társított Azure Cosmos DB-példány metrikáit és metaadatait tárolja. A Azure Cosmos DB tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

#### <a name="azure-container-registry"></a>Azure Container Registry

A beállításjegyzékben található összes tároló lemezkép (Azure Container Registry) inaktív állapotban van. Az Azure automatikusan titkosítja a képeket a tárolás előtt, és visszafejti azt a menet közben, amikor Azure Machine Learning lekéri a rendszerképet.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

Az Azure Storage-ban tárolt összes számítási csomópont operációsrendszer-lemeze titkosítva van a Microsoft által felügyelt kulcsokkal Azure Machine Learning Storage-fiókokban. Ez a számítási cél elmúló, és a fürtöket jellemzően akkor kell lekicsinyíteni, ha nincsenek várólistán lévő futtatások. A mögöttes virtuális gép kiépítve, az operációsrendszer-lemez pedig törlődik. Az operációsrendszer-lemez nem támogatja a Azure Disk Encryption.

Minden virtuális gép helyi ideiglenes lemezzel is rendelkezik az operációs rendszer műveleteihez. Ha szeretné, használhatja a lemezt a betanítási adatgyűjtéshez. A lemez nincs titkosítva.
További információ arról, hogyan működik a titkosítás az Azure-ban: inaktív [Azure-adattitkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben

Az SSL használatával biztonságossá teheti a Azure Machine Learning-szolgáltatások közötti belső kommunikációt, és a pontozási végpont felé irányuló külső hívásokat is biztonságossá teheti. Az Azure Storage összes hozzáférése egy biztonságos csatornán is megtörténik.

További információ: [SSL használata webszolgáltatások biztonságossá tételéhez Azure Machine Learningon keresztül](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Azure Key Vault használata

Azure Machine Learning a munkaterülethez társított Azure Key Vault-példányt használja a különböző típusú hitelesítő adatok tárolásához:

* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Az SSH-jelszavak és-kulcsok a számítási célokhoz, például az Azure HDInsight és a virtuális gépekhez a Microsoft-előfizetéshez társított külön kulcstartóban tárolódnak. Azure Machine Learning nem tárolja a felhasználók által biztosított jelszavakat vagy kulcsokat. Ehelyett saját SSH-kulcsokat hoz létre, engedélyez és tárol a virtuális gépekhez és HDInsight való kapcsolódáshoz a kísérletek futtatásához.

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás, amelynek a neve megegyezik a munkaterülettel. Ez a felügyelt identitás hozzáfér a kulcstartóban található összes kulcshoz, titokhoz és tanúsítványhoz.

## <a name="monitoring"></a>Figyelés

### <a name="metrics"></a>Mérőszámok

Azure Monitor metrikák használatával megtekintheti és figyelheti a Azure Machine Learning munkaterület metrikáit. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet, majd válassza a **metrikák**elemet:

[![képernyőkép a munkaterület mérőszámait mutatja](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

A metrikák a futtatások, a központi telepítések és a regisztrációk információit tartalmazzák.

További információ: [Azure monitor mérőszámai](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Tevékenységnapló

A munkaterületek tevékenység naplóját megtekintve megtekintheti a munkaterületen végrehajtott különböző műveleteket. A napló alapvető adatokat tartalmaz, például a művelet nevét, az esemény kezdeményezőjét és az időbélyeget.

Ez a képernyőkép a munkaterület tevékenység naplóját jeleníti meg:

[a munkaterület tevékenységi naplóját ábrázoló ![képernyőkép](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

A pontozási kérelmek részleteit a Application Insights tárolja. A munkaterületek létrehozásakor Application Insights jön létre az előfizetésben. A naplózott adatok olyan mezőket tartalmaznak, mint például a HTTPMethod, a UserAgent, a ComputeType, a RequestUrl, a StatusCode, a Kérelemazonosító és az időtartam.

> [!IMPORTANT]
> A Azure Machine Learning munkaterület egyes műveletei nem naplózzák az adatokat a tevékenység naplójába. Például a képzés kezdete és a modell regisztrálása nem kerül be a naplóba.
>
> A műveletek némelyike a munkaterület **tevékenységek** területén jelenik meg, de ezek az értesítések nem jelzik, hogy ki kezdeményezte a tevékenységet.

## <a name="data-flow-diagrams"></a>Adatfolyam-diagramok

### <a name="create-workspace"></a>Munkaterület létrehozása

A következő ábra a munkaterület létrehozása munkafolyamatot mutatja be.

* A felhasználó bejelentkezik az Azure AD-be az egyik támogatott Azure Machine Learning ügyfélről (Azure CLI, Python SDK, Azure Portal), és a megfelelő Azure Resource Manager tokent kéri le.
* A felhasználó meghívja a Azure Resource Manager a munkaterület létrehozásához. 
* Azure Resource Manager a munkaterület kiépítéséhez kapcsolatba lép a Azure Machine Learning erőforrás-szolgáltatóval.

A Munkaterületek létrehozása során további erőforrások jönnek létre a felhasználó előfizetésében:

* Key Vault (a titkok tárolására)
* Egy Azure Storage-fiók (beleértve a blobot és a fájlmegosztást)
* Azure Container Registry (a Docker-rendszerképek tárolása a következtetések/pontozás és kísérletezés érdekében)
* Application Insights (a telemetria tárolásához)

A felhasználó a munkaterülethez (például az Azure Kubernetes szolgáltatáshoz vagy virtuális gépekhez) kapcsolódó egyéb számítási célokat is kiépítheti, ha szükséges.

[munkaterület-munkafolyamatok létrehozása ![](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Forráskód mentése (betanítási parancsfájlok)

Az alábbi ábrán a kód pillanatkép-munkafolyamata látható.

Az Azure Machine Learning munkaterülethez tartozó könyvtárak (kísérletek) a forráskódot tartalmazzák (betanítási parancsfájlok). Ezeket a szkripteket a helyi gépen és a felhőben (az előfizetéséhez tartozó Azure Blob Storage-ban) tárolja a rendszer. A kód pillanatképei a korábbi naplózás végrehajtásához vagy ellenőrzéséhez használatosak.

[![kód pillanatképének munkafolyamata](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Képzés

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

[![betanítási munkafolyamat](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Webszolgáltatások létrehozása

A következő ábra a következtetési munkafolyamatot mutatja be. A következtetés vagy a modell pontozása az a fázis, amelyben a rendszer az üzembe helyezett modellt használja az előrejelzéshez, leggyakrabban az éles adatforgalomra.

A részletek a következők:

* A felhasználó egy olyan ügyfél használatával regisztrálja a modellt, mint a Azure Machine Learning SDK-t.
* A felhasználó létrehoz egy rendszerképet egy modell, egy pontszám-fájl és más modell függőségei használatával.
* A Docker-rendszerkép létrehozása és tárolása Azure Container Registryban történik.
* A webszolgáltatás üzembe helyezése a számítási célra (Container Instances/ak) történik az előző lépésben létrehozott rendszerkép használatával.
* A pontozási kérés részleteit Application Insights tárolja a rendszer, amely a felhasználó előfizetésében található.
* A telemetria a Microsoft/Azure-előfizetésre is leküldve.

[![következtetési munkafolyamat](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Machine Learning webszolgáltatások SSL használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [A Batch-előrejelzések futtatása](how-to-run-batch-predictions.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning használata az Azure-ban Virtual Network](how-to-enable-virtual-network.md)
* [Ajánlott eljárások az ajánlásokat tartalmazó rendszerek létrehozásához](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlási API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
