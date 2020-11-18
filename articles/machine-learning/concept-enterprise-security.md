---
title: Vállalati biztonság és irányítás
titleSuffix: Azure Machine Learning
description: 'Biztonságos használat Azure Machine Learning: hitelesítés, engedélyezés, hálózati biztonság, adattitkosítás és figyelés.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: a9ad018980784a1f809ad28a77dacf9f0328fffa
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873896"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Nagyvállalati biztonság és irányítási Azure Machine Learning

Ebben a cikkben megismerheti a Azure Machine Learning számára elérhető biztonsági funkciókat.

Ha Cloud Service-t használ, az ajánlott eljárás a hozzáférés korlátozása csak azokra a felhasználókra, akiknek szükségük van rá. Először is megismerheti a szolgáltatás által használt hitelesítési és engedélyezési modellt. Előfordulhat, hogy korlátozni kívánja a hálózati hozzáférést, vagy biztonságos módon csatlakoztatja a helyszíni hálózatban lévő erőforrásokat a felhőhöz. Az adattitkosítás is létfontosságú, mind a nyugalmi állapotban, mind pedig az adatok a szolgáltatások között mozognak. Előfordulhat, hogy olyan házirendeket is létre szeretne hozni, amelyek bizonyos konfigurációkat vagy naplókat kényszerítenek, ha nem megfelelő konfigurációk jönnek létre. Végezetül meg kell tudnia figyelni a szolgáltatást, és minden tevékenységhez létre kell hoznia egy naplót.

> [!NOTE]
> A cikkben található információk a Python SDK Azure Machine Learning 1.0.83.1 vagy újabb verziójával működnek.

## <a name="authentication--authorization"></a>Hitelesítés & engedélyezése

A Azure Machine Learning erőforrásokhoz való legtöbb hitelesítés a hitelesítéshez Azure Active Directory (Azure AD), az engedélyezéshez pedig szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ. A kivételek a következők:

* __SSH__: engedélyezheti az SSH-hozzáférést bizonyos számítási erőforrásokhoz, például Azure Machine learning számítási példányhoz. Az SSH-hozzáférés kulcs alapú hitelesítést használ. Az SSH-kulcsok létrehozásával kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása és kezelése](../virtual-machines/linux/create-ssh-keys-detailed.md). További információ az SSH-hozzáférés engedélyezéséről: [Azure Machine learning számítási példány létrehozása és kezelése](how-to-create-manage-compute-instance.md).
* __Webszolgáltatásként üzembe helyezett modellek: a__ webszolgáltatás központi telepítései a __kulcs__ -vagy __jogkivonat__-alapú hozzáférés-vezérlést is használhatják. A kulcsok statikus karakterláncok. A tokeneket egy Azure AD-fiók használatával kéri le a rendszer. További információkért lásd: [webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása](how-to-authenticate-web-service.md).

Azok a szolgáltatások, amelyek Azure Machine Learning, például az Azure adattárolási szolgáltatásaira támaszkodnak, saját hitelesítési és engedélyezési módszerekkel rendelkeznek. A Storage Services-hitelesítéssel kapcsolatos további információkért lásd: [Kapcsolódás a tárolási szolgáltatásokhoz](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

A többtényezős hitelesítés akkor támogatott, ha a Azure Active Directory (Azure AD) használatára van konfigurálva. A hitelesítési folyamat a következő:

1. Az ügyfél bejelentkezik az Azure AD-be, és beolvas egy Azure Resource Manager tokent.  A felhasználók és az egyszerű szolgáltatások teljes mértékben támogatottak.
1. Az ügyfél az Azure Resource Manager és az összes Azure Machine Learning számára megadja a tokent.
1. A Machine Learning szolgáltatás Machine Learning szolgáltatási jogkivonatot biztosít a felhasználó számítási céljához (például Machine Learning Compute). Ezt a tokent a felhasználói számítási cél használja a Machine Learning szolgáltatás visszahívására a Futtatás befejezése után. A hatókör csak a munkaterületre korlátozódik.

[![Hitelesítés Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

További információ: Azure Machine Learning- [munkaterület hitelesítése](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Azure RBAC-vel

Több munkaterületet is létrehozhat, és az egyes munkaterületek több személy számára is megoszthatók. Az Azure AD-fiók Azure RBAC-szerepkörökhöz való hozzárendelésével szabályozhatja, hogy a munkaterület felhasználói milyen szolgáltatásokat és műveleteket érhetik el. A beépített szerepkörök a következők:

* Tulajdonos
* Közreműködő
* Olvasó

A tulajdonosok és a közreműködők használhatják a munkaterülethez csatolt összes számítási célt és adattárat.  

A következő táblázat a főbb Azure Machine Learning-műveleteit és az azokat elvégező szerepköröket sorolja fel:

| Azure Machine Learning művelet | Tulajdonos | Közreműködő | Olvasó |
| ---- |:----:|:----:|:----:|
| Munkaterület létrehozása | ✓ | ✓ | |
| Munkaterület megosztása | ✓ | |  |
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

Ha a beépített szerepkörök nem felelnek meg az igényeinek, létrehozhat egyéni szerepköröket is. Az egyéni szerepkörök egy adott munkaterületen belüli összes műveletet vezérlik, például a számítási feladatok létrehozását, a Futtatás elküldését, az adattár regisztrálását vagy a modell üzembe helyezését. Az egyéni szerepkörök olvasási, írási és törlési engedélyekkel rendelkezhetnek a munkaterület különböző erőforrásaihoz, például a fürtökhöz, az adattárolóhoz, a modellekhez és a végpontokhoz. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti. További információ: [felhasználók és szerepkörök kezelése Azure Machine learning munkaterületen](how-to-assign-roles.md).

A RBAC és a Kubernetes használatával kapcsolatos további információkért lásd: [Azure Role-Based Access Control Kubernetes engedélyezéséhez](../aks/manage-azure-rbac.md).

> [!IMPORTANT]
> Azure Machine Learning a többi Azure-szolgáltatástól, például az Azure Blob Storagetól és az Azure Kubernetes servicestől függ. Minden Azure-szolgáltatás saját Azure RBAC-konfigurációval rendelkezik. A hozzáférés-vezérlés kívánt szintjének elérése érdekében előfordulhat, hogy mindkét Azure RBAC-konfigurációt alkalmaznia kell Azure Machine Learning és a Azure Machine Learning használt szolgáltatásokhoz.

> [!WARNING]
> A Azure Machine Learning Azure Active Directory vállalatok közötti együttműködésben támogatott, de jelenleg nem támogatott a Azure Active Directory vállalat – felhasználó együttműködésben.

### <a name="managed-identities"></a>Felügyelt identitások

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) is, amelynek a neve megegyezik a munkaterülettel. A felügyelt identitás a munkaterület által használt erőforrások biztonságos elérésére szolgál. A következő engedélyekkel rendelkezik a csatolt erőforrásokhoz:

| Erőforrás | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő |
| Tárfiók | Storage-blobadatok közreműködője |
| Key Vault | Hozzáférés az összes kulcshoz, titokhoz, tanúsítványhoz |
| Azure Container Registry | Közreműködő |
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő |
| A kulcstárolót tartalmazó erőforráscsoport (ha eltér a munkaterületet tartalmazótól) | Közreműködő |

Nem javasoljuk, hogy a rendszergazdák visszavonják a felügyelt identitás hozzáférését az előző táblázatban említett erőforrásokhoz. A hozzáférést a kulcsok újraszinkronizálása művelettel állíthatja vissza.

A Azure Machine Learning egy további alkalmazást hoz létre (a név `aml-` a `Microsoft-AzureML-Support-App-` következővel kezdődik: vagy), és közreműködői szintű hozzáféréssel rendelkezik az előfizetésben az egyes munkaterület-régiókban. Ha például egy, az USA keleti régiójában és egy észak-európai régióban található egy munkaterülete ugyanabban az előfizetésben, akkor két alkalmazás fog megjelenni. Ezek az alkalmazások lehetővé teszik Azure Machine Learning számára a számítási erőforrások kezelését.

Igény szerint saját felügyelt identitásokat is konfigurálhat az Azure Virtual Machines és Azure Machine Learning számítási fürttel való használatra. A virtuális géppel a felügyelt identitás használható a munkaterület az SDK-ból való elérésére az egyéni felhasználó Azure AD-fiókja helyett. A számítási fürttel a felügyelt identitás az olyan erőforrások elérésére szolgál, mint például a betanítási feladatot futtató felhasználó által nem férhet hozzá. További információ: Azure Machine Learning- [munkaterület hitelesítése](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Hálózati biztonság és elkülönítés

A Azure Machine Learning erőforrásokhoz való fizikai hozzáférés korlátozásához használhatja az Azure Virtual Networkt (VNet). A virtuális hálózatok lehetővé teszi olyan hálózati környezetek létrehozását, amelyek részben vagy teljesen el vannak különítve a nyilvános internetről. Ez csökkenti a megoldás támadási felületét, valamint az adatkiszűrése esélyét.

További információ a következő dokumentumokban található:

* [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)
* [Biztonságos munkaterületi erőforrások](how-to-secure-workspace-vnet.md)
* [Biztonságos betanítási környezet](how-to-secure-training-vnet.md)
* [Biztonságos következtetési környezet](how-to-secure-inferencing-vnet.md)
* [A Studio használata biztonságos virtuális hálózaton](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Adattitkosítás

Azure Machine Learning különböző számítási erőforrásokat és adattárakat használ. Ha többet szeretne megtudni arról, hogy ezek hogyan támogatják az adattitkosítást a REST és az átvitel során, tekintse meg az [adattitkosítás Azure Machine learning](concept-data-encryption.md)használatával című témakört.

### <a name="microsoft-generated-data"></a>Microsoft által generált adatszolgáltatások

Ha olyan szolgáltatásokat használ, mint például az automatizált Machine Learning, a Microsoft átmeneti, előre feldolgozott adatfeldolgozást eredményezhet több modell betanításához. Ezeket az adatait a munkaterület egy adattárában tárolja, amely lehetővé teszi a hozzáférés-vezérlés és a titkosítás megfelelő betartatását.

Érdemes lehet titkosítani az [üzembe helyezett végpontból naplózott diagnosztikai adatokat](how-to-enable-app-insights.md) az Azure Application Insights-példányba.

## <a name="monitoring"></a>Figyelés

A szerepkörtől és a figyeléstől függően számos figyelési forgatókönyv Azure Machine Learning.

| Szerepkör | Használat figyelése | Leírás |
| ---- | ----- | ----- |
| Adminisztrátor, DevOps, MLOps | [Azure monitor mérőszámok](#azure-monitor), [műveletnapló](#activity-log), [sebezhetőségi vizsgálat](#vulnerability-scanning) | Szolgáltatási szint adatai |
| Adattudós, MLOps | [Figyelő futtatása](#monitor-runs) | A betanítási futtatások során naplózott információk |
| MLOps | [Modell adatainak összegyűjtése](how-to-enable-data-collection.md), [figyelés Application Insights](how-to-enable-app-insights.md) | Webszolgáltatásként vagy IoT Edge-modulként telepített modellek által naplózott információk|

### <a name="monitor-runs"></a>Figyelő futtatása

Figyelheti Azure Machine Learning a kísérleteket, beleértve a betanítási parancsfájlokból való naplózási adatokat is. Ezek az információk az SDK-val, az Azure CLI-vel és a Studióval is megtekinthetők. További információért tekintse át a következő cikkeket:

* [Betanítási futtatások indítása, figyelése és megszakítása](how-to-manage-runs.md)
* [Naplók engedélyezése](how-to-track-experiments.md)
* [Naplók megtekintése](how-to-monitor-view-training-logs.md)
* [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor metrikák használatával megtekintheti és figyelheti a Azure Machine Learning munkaterület metrikáit. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet, majd válassza a **metrikák** elemet:

[![A munkaterület mérőszámait ábrázoló képernyőkép](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

A metrikák a futtatások, a központi telepítések és a regisztrációk információit tartalmazzák.

További információ: [Azure monitor mérőszámai](../azure-monitor/platform/data-platform-metrics.md).

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

### <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az Azure Machine learning esetében engedélyeznie kell a Azure Container Registry erőforrás és az Azure Kubernetes szolgáltatás-erőforrások vizsgálatát. Lásd: [Azure Container Registry rendszerképek vizsgálata Security Center](../security-center/defender-for-container-registries-introduction.md) és [Az Azure Kubernetes Services integrációja Security Center](../security-center/defender-for-kubernetes-introduction.md)használatával.

## <a name="audit-and-manage-compliance"></a>Naplózás és megfelelőség kezelése

A [Azure Policy](../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi, hogy az Azure-erőforrások megfeleljenek a szabályzatoknak. A Azure Machine Learning használatával a következő házirendeket rendelheti hozzá:

* **Ügyfél által felügyelt kulcs**: naplózás vagy betartatás, hogy a munkaterületeknek ügyfél által felügyelt kulcsot kell-e használniuk.
* **Privát hivatkozás**: annak ellenőrzése, hogy a munkaterületek használnak-e privát végpontot a virtuális hálózattal való kommunikációhoz.

A Azure Policyről a [Azure Policy dokumentációjában](../governance/policy/overview.md)talál további információt.

További információ a Azure Machine Learningra vonatkozó házirendekről: a [megfelelőség naplózása és kezelése Azure Policyokkal](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Erőforrás-zárolások

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Következő lépések

* [Biztonságos Azure Machine Learning webszolgáltatások TLS-vel](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [Azure Machine Learning használata a Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning használata az Azure-ban Virtual Network](how-to-network-security-overview.md)
* [Adatok titkosítása nyugalmi és átviteli állapotban](concept-data-encryption.md)
* [Valós idejű ajánlási API létrehozása az Azure-ban](/azure/architecture/reference-architectures/ai/real-time-recommendation)
