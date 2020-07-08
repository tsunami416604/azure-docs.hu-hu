---
title: Azure Security Center és az Azure Kubernetes szolgáltatás
description: Ismerkedjen meg Azure Security Center Azure Kubernetes-szolgáltatásokkal való integrálásával
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800177"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Az Azure Kubernetes Services és a Security Center integrációja

Az Azure Kubernetes Service (ak) a Microsoft által felügyelt szolgáltatás a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. 

Ha Azure Security Center standard szintű csomaggal rendelkezik, hozzáadhatja az AK-csomagot (lásd a [díjszabást](security-center-pricing.md)), így mélyebb láthatóságot kaphat az AK-csomópontok, a felhő-forgalom és a biztonsági vezérlők számára.

A Security Center és az AK együttese együtt a legjobb Felhőbeli natív Kubernetes biztonsági ajánlatot alkotja.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Mik a Security Center Kubernetes-védelmének összetevői?

A Kubernetes Security Center védelmét két elem kombinációjával biztosítjuk:

- **Azure Security Center a veszélyforrások elleni védelem a virtuális gépek számára** – ugyanazzal a log Analytics ügynökkel, amelyet más virtuális gépeken is Security Center használni, Security Center megmutathatja az AK-csomópontokon előforduló biztonsági problémákat. Az ügynök a Container-specifikus elemzéseket is figyeli.

- **Azure Security Center opcionális Kubernetes** -csomagja – a Kubernetes-csomag az AK szolgáltatáson keresztül fogadja a Kubernetes alrendszer naplóit és információit. Ezek a naplók már elérhetők az Azure-ban az AK szolgáltatáson keresztül. Security Center Kubernetes-kötegének engedélyezésekor Security Center hozzáférést biztosít a naplókhoz. Így Security Center biztonsági előnyöket biztosít az AK-fürtök számára az AK főcsomópontja által már összegyűjtött adatok használatával. A Kubernetes-környezet Azure Security Center által beolvasott adatok némelyike bizalmas adatokat is tartalmazhat.

    ![A Azure Security Center és az Azure Kubernetes Service (ak) magas szintű áttekintése](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Milyen védelmet biztosítanak?

A két szolgáltatás együttes használata a következőket biztosítja:

* **Biztonsági javaslatok** – Security Center AZONOSÍTJA az AK-erőforrásokat, és kategorizálja őket: a fürtöktől az egyes virtuális gépekig. Ezután megtekintheti az egyes erőforrásokra vonatkozó biztonsági javaslatokat. További információkért tekintse meg a tárolók javaslatait a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-containers). 

* **Környezet megerősítése** – Security Center folyamatosan figyeli a Kubernetes-fürtök és a Docker-konfigurációk konfigurációját. Ezután biztonsági javaslatokat hoz létre az iparági szabványoknak megfelelően.

* **Futásidejű védelem** – a következő AK-források folyamatos elemzése révén Security Center riasztásokat küld, és a gazdagépen és az AK *-* beli fürt szintjén észlelt kártékony tevékenységeket észleli. [További információ a tárolók veszélyforrások elleni védelméről](threat-protection.md#azure-containers).


     

![Azure Security Center és az Azure Kubernetes szolgáltatás (ak) részletesebben](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AK Security Center GYIK

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Továbbra is kaphatok AK-védelmet a Log Analytics ügynök nélkül?

A fentiekben leírtaknak megfelelően a választható Kubernetes-csomagok a fürt szintjén biztosítanak védelmet, a Azure Security Center standard szintű Log Analytics ügynök gondoskodik a csomópontok védelméről. 

Javasoljuk, hogy mindkét esetben a lehető legteljesebb védelem érdekében végezze el a telepítést.

Ha úgy dönt, hogy nem telepíti az ügynököt a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Container Registryvel](azure-container-registry-integration.md)

* [Adatkezelés a Microsoftnál](https://www.microsoft.com/trust-center/privacy/data-management) – ismerteti a Microsoft-szolgáltatások (beleértve az Azure-t, az Intune-t és a Microsoft 365) adatszabályzatait, a Microsoft adatkezelésének részleteit és az adatokat érintő adatmegőrzési szabályzatokat
