---
title: Vállalati biztonság
titleSuffix: Azure Machine Learning service
description: 'Az Azure Machine Learning szolgáltatás biztonságos használata: hitelesítés, engedélyezés, hálózati biztonság, adattitkosítás és figyelés.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.openlocfilehash: f0fb6f0d2b2579679ee8a6ec43b3241377701d48
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780904"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Vállalati biztonság Azure Machine Learning szolgáltatáshoz

Ebben a cikkben megismerheti az Azure Machine learning szolgáltatással elérhető biztonsági funkciókat.

A felhőalapú szolgáltatás használatakor ajánlott a hozzáférés korlátozása csak azokra a felhasználókra, akiknek szükségük van rá. Ez a szolgáltatás által használt hitelesítési és engedélyezési modell megismerésével kezdődik. Előfordulhat, hogy korlátozni kívánja a hálózati hozzáférést, vagy biztonságos módon csatlakoztatja a helyszíni hálózatban lévő erőforrásokat a felhőben. Az adatok titkosítása is létfontosságú, mind a nyugalmi állapotban, mind pedig az adatok a szolgáltatások között mozognak. Végezetül meg kell tudnia figyelni a szolgáltatást, és minden tevékenységhez létre kell hoznia egy naplót.

## <a name="authentication"></a>Authentication

A többtényezős hitelesítés akkor támogatott, ha a Azure Active Directory (Azure AD) ugyanahhoz a szolgáltatáshoz van konfigurálva.

* Az ügyfél bejelentkezik az Azure AD-be, és Azure Resource Manager tokent kap.  A felhasználók és az egyszerű szolgáltatások teljes mértékben támogatottak.
* Az ügyfél jogkivonatot jelenít meg Azure Resource Manager & az összes Azure Machine Learning szolgáltatást
* Azure Machine Learning szolgáltatás Azure Machine Learning tokent biztosít a felhasználó számítási feladatokhoz. Például Machine Learning Compute. Ezt a Azure Machine Learning tokent a felhasználói számítás használja, hogy a Futtatás befejezése után visszahívja Azure Machine Learning szolgáltatásba (a hatókört a munkaterületre korlátozza).

![Képernyőfelvétel a hitelesítés működéséről Azure Machine Learning szolgáltatásban](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Hitelesítés a webszolgáltatások üzembe helyezéséhez

Azure Machine Learning két hitelesítési módszert támogat a webszolgáltatásokhoz, a kulcsokhoz és a jogkivonatokhoz. Az egyes webszolgáltatások egyszerre csak egy hitelesítési űrlapot tudnak engedélyezni.

|Hitelesítési módszer|ACI|AKS|
|---|---|---|
|Kulcs|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezve|
|Jogkivonat| Nem állnak rendelkezésre adatok| Alapértelmezés szerint letiltva |

#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha engedélyezi a kulcsos hitelesítést a központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A hitelesítés alapértelmezés szerint le van tiltva, amikor Azure Container Instances telepíti.

A kulcsos hitelesítés engedélyezéséhez használja `auth_enabled` a paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha a kulcsos hitelesítés engedélyezve van, a metódussal kérheti le az `get_keys` elsődleges és a másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a következőt[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználónak meg kell adnia egy Azure Machine Learning JSON Web Token a webszolgáltatásnak, hogy hozzáférjen.

* Alapértelmezés szerint a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés nem támogatott a Azure Container Instances való telepítésekor.

A jogkivonat-hitelesítés vezérléséhez használja `token_auth_enabled` a paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a jogkivonat-hitelesítés, a `get_token` metódus használatával kérhet le egy JWT-tokent és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat `refresh_by` időpontját követően új jogkivonatot kell kérnie.
>
> Javasoljuk, hogy a Azure Machine Learning munkaterületet ugyanabban a régióban hozza létre, mint az Azure Kubernetes Service-fürtöt. A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, akkor sem fogja tudni lehívni a webszolgáltatáshoz tartozó jogkivonatot, ha a fürt a munkaterülettől eltérő régióban található. Ez az Azure AD-hitelesítés gyakorlatilag nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. Emellett minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.

## <a name="authorization"></a>Authorization

Több munkaterületet is létrehozhat, és minden munkaterülethez több személy közösen használhat. Munkaterületek megosztásakor a hozzáférését a következő szerepköröknek a felhasználókhoz való hozzárendelésével szabályozhatja:

* Tulajdonos
* Közreműködő
* Olvasó

A következő táblázat felsorolja a főbb Azure Machine Learning szolgáltatási műveleteit és a velük végrehajtható szerepköröket:

| Azure Machine Learning szolgáltatási művelet | Tulajdonos | Közreműködő | Olvasó |
| ---- |:----:|:----:|:----:|
| Munkaterület létrehozása | ✓ | ✓ | |
| Munkaterület megosztása | ✓ | |  |
| Számítás létrehozása | ✓ | ✓ | |
| Számítás csatolása | ✓ | ✓ | |
| Adattárolók csatolása | ✓ | ✓ | |
| Kísérlet futtatása | ✓ | ✓ | |
| Futtatások/mérőszámok megtekintése | ✓ | ✓ | ✓ |
| Modell regisztrálása | ✓ | ✓ | |
| Lemezkép létrehozása | ✓ | ✓ | |
| Webszolgáltatás üzembe helyezése | ✓ | ✓ | |
| Modellek/lemezképek megtekintése | ✓ | ✓ | ✓ |
| Webszolgáltatás hívása | ✓ | ✓ | ✓ |

Ha a beépített szerepkörök nem elégségesek az igényeinek kielégítéséhez, létrehozhat egyéni szerepköröket is. Az egyetlen támogatott egyéni szerepkör a munkaterületen és a Machine Learning Computeon végzett műveletekhez használható. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási vagy törlési engedélyekkel rendelkeznek a munkaterületen, illetve a számítási erőforrásban az adott munkaterületen. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti. További információ: [felhasználók és szerepkörök kezelése Azure Machine learning](how-to-assign-roles.md) munkaterületen

### <a name="securing-compute-and-data"></a>A számítás és az adatok biztonságossá tétele

A tulajdonosok és a közreműködők használhatják a munkaterülethez csatolt összes számítási célt és adattárat.  
Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás is (a munkaterülettel megegyező névvel), a következő engedélyekkel a munkaterületen használt csatolt erőforrásokhoz:

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) -erőforrásokhoz

| Resource | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő |
| Tárfiók | Storage-blobadatok közreműködője |
| Key Vault | Hozzáférés az összes kulcshoz, titokhoz, tanúsítványhoz |
| Azure Container Registry | Közreműködő |
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő |
| Az Key Vaultt tartalmazó erőforráscsoport (ha eltér a munkaterületet tartalmazó csoporttól) | Közreműködő |

Javasoljuk, hogy a rendszergazdák ne vonja vissza a felügyelt identitás hozzáférését a fent említett erőforrásokhoz. A hozzáférés visszaállítható a kulcsok újraszinkronizálása művelettel.

A Azure Machine learning szolgáltatás létrehoz egy további alkalmazást (a neve `aml-`a következővel kezdődik), és az előfizetéshez tartozó közreműködői szintű hozzáféréssel rendelkezik minden munkaterület-régióhoz. Pl.: Ha az USA keleti régiójában és egy másik, észak-európai munkaterületen található munkaterülettel azonos előfizetésben található, két ilyen alkalmazást fog látni. Erre azért van szükség, hogy Azure Machine Learning szolgáltatás segítséget nyújtson a számítási erőforrások kezelésében.

## <a name="network-security"></a>Hálózati biztonság

A Azure Machine Learning szolgáltatás más Azure-szolgáltatásokra támaszkodik számítási erőforrásokhoz. A számítási erőforrások (számítási célok) a modellek betanítására és üzembe helyezésére szolgálnak. Ezeket a számítási célokat egy virtuális hálózaton belül lehet létrehozni. Például a Microsoft Data Science Virtual Machine használatával betaníthatja a modelleket, majd üzembe helyezheti a modellt az Azure Kubernetes szolgáltatásban (ak).  

További információ: [kísérletek futtatása egy virtuális hálózatban](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Adattitkosítás

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning a szolgáltatás a Azure Machine Learning szolgáltatás munkaterületéhez kötődő Azure Blob Storage-fiókban tárolja a pillanatképeket, kimeneteket és naplókat, és a felhasználó előfizetésében él. Az Azure Blob Storage tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsok használatával.

Az Azure Blob Storageban tárolt adatok saját kulcsaival kapcsolatos további információkért lásd: [Storage Service encryption az ügyfél által felügyelt kulcsok használatával Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

A betanítási adatok általában az Azure Blob Storage-ban is tárolódnak, hogy elérhetők legyenek a kiképzésben. Ezt a tárolót nem Azure Machine Learning felügyeli, de távoli fájlrendszerként van csatlakoztatva a számításhoz.

A munkaterülethez használt Azure Storage-fiókok hozzáférési kulcsainak újragenerálásával kapcsolatos információkért lásd a [Storage-hozzáférési kulcsok](how-to-change-storage-access-key.md) újragenerálása című cikket.

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning a szolgáltatás metrikákat és metaadatokat tárol a Cosmos DB, amely Azure Machine Learning szolgáltatás által kezelt Microsoft-előfizetésben él. A Cosmos DB tárolt összes adatok titkosítva maradnak a Microsoft által felügyelt kulcsok használatával.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

A beállításjegyzékben található összes tároló lemezkép (ACR) inaktív állapotban van. Az Azure automatikusan titkosítja a rendszerképet a tárolás előtt, és visszafejti azt, amikor Azure Machine Learning szolgáltatás lekéri a rendszerképet.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

Az egyes számítási csomópontok operációsrendszer-lemezét az Azure Storage tárolja a Microsoft által felügyelt kulcsok használatával Azure Machine Learning Service Storage-fiókokban. Ez a számítás elmúló, és a fürtöket általában lefelé kell méretezni, ha nincsenek várólistán lévő futtatások. A mögöttes virtuális gép kiépítés alatt áll, és az operációsrendszer-lemez törölve lett. Az operációsrendszer-lemez nem támogatja az Azure Disk Encryption használatát.
Minden virtuális gép helyi ideiglenes lemezzel is rendelkezik az operációs rendszer műveleteihez. Ezt a lemezt igény szerint is felhasználhatja a betanítási adatgyűjtéshez. Ez a lemez nincs titkosítva.
További információ arról, hogyan működik a titkosítás az Azure-ban: [Azure-beli adattitkosítás – Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben

Az SSL használatával mind a belső kommunikáció, mind a különböző Azure Machine Learning Micro Services, mind a pontozási végpont meghívásával kapcsolatos külső kommunikáció is támogatott. Az Azure Storage összes hozzáférése egy biztonságos csatornán is elérhető.
További információ: [biztonságos Azure Machine learning webszolgáltatások SSL használatával](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Azure Key Vault használata

A munkaterülethez társított Key Vault-példányt a Azure Machine Learning szolgáltatás a különböző típusú hitelesítő adatok tárolására használja:

* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Az adattárakhoz való kapcsolódási karakterláncok.

A számítási célokat, például a HDI HDInsight és a virtuális gépet az SSH-jelszavak és-kulcsok egy külön Key Vault tárolják, amely a Microsoft-előfizetéshez van társítva. Azure Machine Learning a szolgáltatás a felhasználó által megadott jelszavakat vagy kulcsokat tárolja, ehelyett a saját SSH-kulcsait hozza létre, engedélyezi és tárolja, hogy a kísérleteket a virtuális gépekhez vagy HDInsight kapcsolódjon.
Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás (a munkaterülettel megegyező névvel), amely hozzáféréssel rendelkezik a Key Vault összes kulcsához, titkához és tanúsítványához.

## <a name="monitoring"></a>Figyelés

A felhasználók a munkaterület alatt láthatják a tevékenység naplóját, és megtekinthetik a munkaterületen végrehajtott különféle műveleteket, például a művelet nevét, a által kezdeményezett eseményt, az időbélyeget stb.

A következő képernyőfelvételen a munkaterület tevékenységi naplója látható:

![A munkaterület alatti tevékenység naplóját ábrázoló képernyőfelvétel](./media/enterprise-readiness/workspace-activity-log.png)

A pontozási kérés részleteit a rendszer a AppInsights tárolja, amely a felhasználó előfizetésében jön létre a munkaterület létrehozásakor. Ide tartoznak például a HTTPMethod, a UserAgent, a ComputeType, a RequestUrl, a StatusCode, a Kérelemazonosító, az időtartam stb.

## <a name="data-flow-diagram"></a>Adatfolyam-diagram

### <a name="create-workspace"></a>Munkaterület létrehozása

A következő ábra a munkaterület létrehozása munkafolyamatot mutatja be.
A felhasználó bejelentkezik az Azure AD-be bármely támogatott Azure Machine Learning Service-ügyfélről (CLI, Python SDK, Azure Portal), és a megfelelő Azure Resource Manager tokent kéri le. A felhasználó ezután meghívja a Azure Resource Manager a munkaterület létrehozásához.  Azure Resource Manager a munkaterület kiépítéséhez kapcsolatba lép a Azure Machine Learning szolgáltatás erőforrás-szolgáltatóval.  A Munkaterületek létrehozása során további erőforrások jönnek létre az ügyfél előfizetésében:

* Kulcstartó (a titkok tárolására)
* Egy Azure Storage-fiók (beleértve a blob & fájlmegosztás)
* Azure Container Registry (a Docker-rendszerképek tárolása a következtetések/pontozás és kísérletezés érdekében)
* Application Insights (a telemetria tárolásához)

A munkaterülethez (Azure Kubernetes szolgáltatás, virtuális gép stb.) kapcsolódó egyéb számítások igény szerint az ügyfelek által is kiállíthatók.

![A munkaterület-munkafolyamatot bemutató képernyőkép](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Forráskód mentése (betanítási parancsfájlok)

Az alábbi ábrán a kód pillanatkép-munkafolyamata látható.
Egy Azure Machine Learning szolgáltatási munkaterülethez társított könyvtárak (kísérletek), amely tartalmazza a forráskódot (betanítási parancsfájlok).  Ezeket a rendszer az ügyfél helyi számítógépén és a felhőben tárolja (az Azure Blob Storage az ügyfél előfizetése alatt). Ezek a kód-Pillanatképek a korábbi naplózás végrehajtásához vagy ellenőrzéséhez használatosak.

![A munkaterület-munkafolyamatot bemutató képernyőkép](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Képzés

Az alábbi ábra a betanítási munkafolyamatot mutatja be.

* Azure Machine Learning szolgáltatást a rendszer a fentiekben mentett kód-pillanatképhez tartozó pillanatkép-AZONOSÍTÓval hívja meg
* Azure Machine Learning szolgáltatás létrehoz egy futtatási azonosítót (nem kötelező) & Azure Machine Learning Service tokent, amelyet később a számítási célok, például a Machine Learning Compute/virtuális gép használ a Azure Machine Learning szolgáltatáshoz való visszatéréshez.
* Kiválaszthat egy felügyelt számítást (pl.: Machine Learning Compute) vagy nem felügyelt számítás (pl. Virtuális gép) a betanítási feladatok futtatásához. Az adatfolyamot az alábbi forgatókönyvek esetében is meg kell magyarázni:
* (VM/HDInsight – a Microsoft-előfizetésben Key Vault SSH-alapú creds használatával érhető el) Azure Machine Learning szolgáltatás a következő számítási célra futtatja a felügyeleti kódot:

   1. Előkészíti a környezetet. (Vegye figyelembe, hogy a Docker a virtuális gép és a helyi lehetőség is. A következő lépésekből megtudhatja, hogyan működik a kísérlet a Docker-tárolóban Machine Learning Compute.)
   1. Letölti a kódot.
   1. Beállítja a környezeti változókat és a konfigurációkat.
   1. Futtatja a felhasználói parancsfájlt (a fent említett kód pillanatképét).

* (Machine Learning Compute – a munkaterület által felügyelt identitás használatával érhető el) Vegye figyelembe, hogy mivel Machine Learning Compute felügyelt számítási megoldás, amelyet a Microsoft felügyel, ennek eredményeképpen a Microsoft-előfizetésben fut.

   1. Ha szükséges, a távoli Docker-konstrukció indult el.
   1. Felügyeleti kód írása a felhasználói Azure-fájlmegosztás.
   1. Elindítja a tárolót egy kezdeti paranccsal, azaz a felügyeleti kóddal az előző lépésben leírtak szerint.

#### <a name="querying-runs-and-metrics"></a>Futtatások és metrikák lekérdezése

Ez a lépés jelenik meg a folyamatban, ahol a betanítási számítás a futtatási metrikákat a Azure Machine learning szolgáltatásba írja vissza, ahonnan a Cosmos db tárolja. Az ügyfelek meghívhatják Azure Machine Learning szolgáltatást, amely lekéri a metrikákat a Cosmos DBról, majd visszaküldi az ügyfélnek.

![A munkaterület-munkafolyamatot bemutató képernyőkép](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Webszolgáltatások létrehozása

A következő ábra a következtetési munkafolyamatot mutatja be. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban.
Tekintse meg az alábbi részleteket:

* A felhasználó egy olyan ügyféllel regisztrál egy modellt, mint az Azure ML SDK
* A felhasználó létrehoz egy rendszerképet a Model, a score file és más modellek függőségeivel
* A Docker-rendszerkép létrehozása és tárolása ACR-ben történik
* A webszolgáltatás üzembe helyezése a számítási célra (ACI/ak) történik a fent létrehozott rendszerkép használatával
* A pontozási kérés részleteit a felhasználó előfizetésében található AppInsights tárolja.
* A telemetria a Microsoft/Azure-előfizetésre is leküldve

![A munkaterület-munkafolyamatot bemutató képernyőkép](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>További lépések

* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Hogyan futtathat batch-előrejelzés](how-to-run-batch-predictions.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning szolgáltatás használata Azure-beli virtuális hálózatokkal](how-to-enable-virtual-network.md)
* [Ajánlott eljárások az ajánlásokat tartalmazó rendszerek létrehozásához](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlási API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
