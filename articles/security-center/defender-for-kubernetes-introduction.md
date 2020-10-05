---
title: Azure Defender for Kubernetes – az előnyök és funkciók
description: Ismerje meg a Kubernetes készült Azure Defender előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3308a72421b851402642f12daf56359c7e3c9216
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449065"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Bevezetés az Azure Defender for Kubernetes használatába

Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

A Azure Security Center és az AK a legjobb felhőalapú Kubernetes biztonsági ajánlatot alkotja, és együttesen biztosítja a környezet megkeményedését, a munkaterhelések védelmét és a futásidejű védelmet az alább leírtak szerint.

A Kubernetes-fürtök fenyegetés-észleléséhez engedélyezze **Az Azure Defendert a Kubernetes**számára.

Ha engedélyezi az [Azure Defender for Servers](defender-for-servers-introduction.md)-t, a gazdagép szintű fenyegetések észlelése a linuxos AK-csomópontok esetében elérhető.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A **Kubernetes készült Azure Defender** számlázása [a díjszabási oldalon](security-center-pricing.md) látható.|
|Szükséges szerepkörök és engedélyek:|A **biztonsági rendszergazda** figyelmen kívül hagyhatja a riasztásokat.<br>A **biztonsági olvasó** megtekintheti az eredményeket.|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Nem](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Milyen előnyökkel jár az Azure Defender for Kubernetes?

### <a name="run-time-protection"></a>Futásidejű védelem

A következő AK-források folyamatos elemzése révén Security Center valós idejű veszélyforrások elleni védelmet biztosít a tároló környezetek számára, és riasztásokat hoz létre a gazdagépen és az AK *-* fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységekkel kapcsolatban. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A Security Center különböző szinteken biztosítja a veszélyforrások elleni védelmet: 

- **Gazdagép szintje (az Azure Defender for Servers által biztosított)** – ugyanazzal a log Analytics ügynökkel, amelyet más virtuális gépeken Security Center használ, az Azure Defender figyeli a linuxos AK-csomópontokat a gyanús tevékenységekhez, például a webrendszerhéj észleléséhez és az ismert gyanús IP-címekkel való kapcsolathoz. Az ügynök a tároló-specifikus elemzéseket is figyeli, például a privilegizált tárolók létrehozását, az API-kiszolgálókhoz való gyanús hozzáférést, valamint a Docker-tárolón belül futó Secure Shell-(SSH-) kiszolgálókat.

    Az AK-gazdagép szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-containerhost)tartalmazza.

    >[!IMPORTANT]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

- **AK-fürt szintje (az Azure Defender for Kubernetes által biztosított)** – a fürt szintjén a fenyegetések elleni védelem a Kubernetes-naplók elemzésén alapul. Az **ügynök** nélküli figyelés engedélyezéséhez engedélyezze az Azure Defendert. A riasztások ezen a szinten történő létrehozásához Security Center figyeli az AK által felügyelt szolgáltatásokat az AK által beolvasott naplók használatával. Az ezen a szinten található események közé tartoznak például az elérhető Kubernetes-irányítópultok, a magas jogosultsági szintű szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása.

    Az AK-beli fürt szintű riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-akscluster)tartalmazza.

    >[!NOTE]
    > A Security Center biztonsági riasztásokat hoz létre az Azure Kubernetes szolgáltatás műveleteihez, illetve a Kubernetes beállítás engedélyezése után előforduló központi telepítések esetén az előfizetési beállításokban. 

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.

> [!TIP]
> A tárolói riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)utasításait követve végezhető el.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Hogyan működik Security Center Kubernetes-védelme?

Az alábbiakban a Azure Security Center, az Azure Kubernetes Service és a Azure Policy közötti interakcióról szóló ábrán talál.

Láthatja, hogy a Security Center kapott és elemzett elemek a következők:

- naplók naplózása az API-kiszolgálóról
- nyers biztonsági események a Log Analytics-ügynökből
- a fürt konfigurációs adatai az AK-fürtből
- munkaterhelés-konfiguráció Azure Policy (a **Kubernetes Azure Policy-bővítményének**használatával). [További információ a számítási feladatok védelmének legjobb gyakorlatáról a Kubernetes belépésvezérlés használatával](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Az Azure Security Center, az Azure Kubernetes szolgáltatás és a Azure Policy közötti interakció magas szintű architektúrája" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes – gyakori kérdések

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Továbbra is kaphatok AK-védelmet a Log Analytics ügynök nélkül?

A fentiekben leírtaknak megfelelően a választható **Azure Defender for Kubernetes** -csomag a fürt szintjén biztosítja a védelmet, a **kiszolgálókhoz készült Azure Defender** log Analytics-ügynöke védi a csomópontokat. 

Javasoljuk, hogy mindkét esetben a lehető legteljesebb védelem érdekében végezze el a telepítést.

Ha úgy dönt, hogy nem telepíti az ügynököt a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát kapja meg. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta Security Center Kubernetes-védelmét, beleértve a Kubernetes készült Azure Defendert is. 

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- [Az Azure Defender engedélyezése](security-center-pricing.md)
- [Riasztások exportálása egy Azure Sentinelbe vagy egy harmadik féltől származó SIEM-be](continuous-export.md)
- [Riasztások hivatkozási táblázata](alerts-reference.md)