---
title: Együttműködés az Azure DDoS Protection Standardtal
description: Ismerje meg az Azure DDoS Protection Standard által engedélyezett partneri lehetőségeket.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849681"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Együttműködés az Azure DDoS Protection Standardtal
Ez a cikk ismerteti az Azure DDoS Protection Standard által engedélyezett partneri lehetőségeket. Ez a cikk célja, hogy segítse a termékmenedzserek és az üzletfejlesztési szerepek megértsék a befektetési útvonalakat, és betekintést nyújtsanak a partneri értékajánlatokba.

## <a name="background"></a>Háttér
Elosztott szolgáltatásmegtagadási (DDoS) támadások az egyik legjobb rendelkezésre állási és biztonsági aggályok at az ügyfelek áthelyezése az alkalmazások a felhőbe. A zsarolás és a hacktivizmus, hogy a közös motivációk mögött DDoS támadások, ők már folyamatosan növekszik a típus, a skála, és az előfordulás gyakorisága, mivel viszonylag könnyű és olcsó indítani.

Az Azure DDoS Protection ellenintézkedéseket biztosít a legkifinomultabb DDoS-fenyegetések ellen, kihasználva az Azure-hálózatok globális skáláját. A szolgáltatás továbbfejlesztett DDoS-kockázatcsökkentési lehetőségeket biztosít a virtuális hálózatokban telepített alkalmazások és erőforrások számára.

A technológiai partnerek az Azure DDoS Protection Standard segítségével natív módon védhetik meg ügyfeleik erőforrásait a DDoS-támadások miatti rendelkezésre állási és megbízhatósági problémák kezelése érdekében.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Bevezetés az Azure DDoS protection standardba
Az Azure DDoS Protection Standard továbbfejlesztett DDoS-kockázatcsökkentési képességekkel rendelkezik a Layer 3 és a Layer 4 DDoS-támadások ellen. A DDoS Protection Standard szolgáltatás főbb funkciói az alábbiakban találhatók.

### <a name="adaptive-real-time-tuning"></a>Adaptív, valós idejű hangolás
Minden védett alkalmazás, az Azure DDoS Protection Standard automatikusan beállítja a DDoS-kockázatcsökkentési szabályzat küszöbértékek alapján az alkalmazás forgalmi profil minták. A szolgáltatás ezt a testreszabást két elemzési információ használatával valósítja meg:

- Az ügyfélenkénti (IP-alapú) forgalmi minták automatikus tanulása a 3.
- A hamis pozitív értékek minimalizálása, figyelembe véve, hogy az Azure-skála lehetővé teszi, hogy jelentős mennyiségű forgalmat.

![Adaptív, valós idejű hangolás](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Támadáselemzés, telemetria, figyelés és riasztás
Az Azure DDoS-védelem azonosítja és csökkenti a DDoS-támadások felhasználói beavatkozás nélkül.

- Ha a védett erőforrás az Azure Security Center hatálya alá tartozó előfizetésben található, a DDoS Protection Standard automatikusan riasztást küld a Security Centernek, ha DDoS-támadást észlel és mérsékel a védett alkalmazással szemben.
- Azt is megteheti, ha értesítést kap, ha van egy aktív kockázatcsökkentés egy védett nyilvános IP-cím, konfigurálhatja a [riasztást](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) a metrika ddos támadás alatt, vagy sem.
- Emellett dönthet úgy, hogy riasztásokat hoz létre a többi DDoS-metrikákhoz, és konfigurálja a [támadási elemzést](manage-ddos-protection.md#configure-ddos-attack-analytics) a támadás méretének, a forgalom eldobásának, a támadási vektoroknak, a fő közreműködőknek és egyéb részleteknek a megértéséhez.

![DDoS-mérőszámok](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS gyorsválasz (DRR)
A DDoS Protection Standard ügyfelei aktív támadás során hozzáférhetnek a [gyorsreagálású csapathoz.](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) A DRR segíthet a támadások kivizsgálásában, a támadás során az egyéni megoldásokban és a támadás utáni elemzésben.

### <a name="sla-guarantee-and-cost-protection"></a>SLA garancia és költségvédelem
A DDoS Protection Standard szolgáltatást 99,99%-os SLA fedezi, és a költségvédelem erőforrás-jóváírásokat biztosít a dokumentált támadások során a horizontális felskálázáshoz. További információ: [SLA for Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Kiemelt partneri forgatókönyvek
Az Azure DDoS Protection Standard integrálásával az alábbi kulcsfontosságú előnyöket élvezheti:

- A partnerek által kínált szolgáltatásokat (terheléselosztó, webalkalmazás tűzfal, tűzfal stb.) az Azure DDoS Protection Standard automatikusan védi (fehér címkével ellátott) a háttérben.
- A partnerek hozzáférhetnek az Azure DDoS Protection Standard támadáselemzéshez és telemetriai adatokhoz, amelyeket integrálhatnak a saját termékeikkel, és egységes felhasználói élményt nyújtanak.  
- A partnerek még az Azure gyorsválaszának hiányában is hozzáférhetnek a DDoS gyorsreagáláshoz a DDoS-sal kapcsolatos problémák esetén.
- A partnerek védett alkalmazásait DDoS SLA garancia és költségvédelem támogatja DDoS-támadások esetén.

## <a name="technical-integration-overview"></a>Műszaki integráció – áttekintés
Az Azure DDoS Protection Standard partneri lehetőségek az Azure Portalon, az API-kon és a CLI/PS-en keresztül érhetők el.

### <a name="integrate-with-ddos-protection-standard"></a>Integrálás a DDoS védelmi szabványba
A következő lépések szükségesek a partnerek számára az Azure DDoS Protection Standard dal való integráció konfigurálásához:
1. Hozzon létre egy DDoS-védelmi tervet a kívánt (partneri) előfizetésben. A részletes útmutatásról a [DDoS szabványos védelmi terv létrehozása](manage-ddos-protection.md#create-a-ddos-protection-plan)című témakörben talál.
   > [!NOTE]
   > Csak 1 DDoS védelmi tervet kell létrehozni egy adott bérlőhöz. 
2. Nyilvános végponttal rendelkező szolgáltatást telepíthet a (partneri) előfizetésekben, például a terheléselosztóban, a tűzfalakban és a webalkalmazás tűzfalában. 
3. Engedélyezze az Azure DDoS Protection Standard a szolgáltatás virtuális hálózatán, amely nyilvános végpontok használatával DDoS Protection Plan az első lépésben létrehozott. A stpe-by-step utasításokat lásd: [DDoS standard védelmi terv engedélyezése](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Miután az Azure DDoS Protection Standard engedélyezve van egy virtuális hálózaton, a virtuális hálózaton belüli összes nyilvános IP-szolgáltató automatikusan védett lesz. Ezeknek a nyilvános IP-knek az eredete lehet az Azure-on (ügyfél-előfizetésen) belül vagy az Azure-on kívül. 
4. Szükség esetén integrálhatja az Azure DDoS Protection Standard telemetriai adatait és a támadáselemzéseket az alkalmazásspecifikus ügyfélközpontú irányítópultba. A telemetriai adatok használatáról a [DDoS-védelem telemetriája használata című](manage-ddos-protection.md#use-ddos-protection-telemetry)témakörben talál további információt. A támadáselemzés konfigurálásáról a [DDoS-támadáselemzés konfigurálása című](manage-ddos-protection.md#configure-ddos-attack-analytics) témakörben talál további információt.

### <a name="onboarding-guides-and-technical-documentation"></a>Bevezetési útmutatók és műszaki dokumentáció

- [Az Azure DDoS Protection termékoldala](https://azure.microsoft.com/services/ddos-protection/)
- [Az Azure DDoS Protection dokumentációja](ddos-protection-overview.md)
- [Az Azure DDoS Protection API hivatkozása](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Az Azure virtuális hálózati API-hivatkozása](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Segítségkérés

- Ha kérdése van az Azure DDoS Protection Standard alkalmazás-, szolgáltatás- vagy termékintegrációival kapcsolatban, forduljon az [Azure biztonsági közösségéhez.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Kövesse a [Veremtúlcsordulás](https://stackoverflow.com/tags/azure-ddos/)ról szóló beszélgetéseket.

### <a name="get-to-market"></a>Piacra jutás

- A Microsofttal való partneri kapcsolat elsődleges programja a [Microsoft Partner Network](https://partner.microsoft.com/). – A Microsoft Graph Security integrációi az [MPN független szoftverszállító (ISV)](https://partner.microsoft.com/saas-solution-guide) sávba esnek.
- [A Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) egy kifejezetten a Microsoft biztonsági partnerek számára elérhető program, amely nek köszönhetően gazdagíthatja a biztonsági termékeket, és javíthatja az ügyfelek számára a Microsoft Security termékekbe való integrációk felderíthetőségét.

## <a name="next-steps"></a>További lépések
Meglévő partnerintegrációk megtekintése:

- [Barracuda WAF szolgáltatásként](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF a Radware-től](https://www.radware.com/resources/microsoft-azure/)
