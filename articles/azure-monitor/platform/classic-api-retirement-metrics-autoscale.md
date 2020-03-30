---
title: Az Azure Monitor klasszikus üzembe helyezési modell API-k kivonása metrikákhoz és automatikus skálázáshoz
description: Metrikák és automatikus skálázású klasszikus API-k, más néven Azure Service Management (ASM) vagy RDFE telepítési modell kivonása
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 9dfa6b278587f4ed79b1c3cd9eff1defd09ec0bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294661"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Az Azure Monitor klasszikus üzembe helyezési modell API-k kivonása metrikákhoz és automatikus skálázáshoz

Az Azure Monitor (korábban Azure Insights első kiadáskor) jelenleg lehetővé teszi az automatikus skálázási beállítások létrehozását és kezelését a klasszikus virtuális gépek és a klasszikus felhőszolgáltatások metrikáihoz. **2019. június 30-a után a** klasszikus üzembe helyezési modell alapú API-k eredeti készlete minden azure-beli nyilvános és privát felhőben minden régióban megszűnik.   

Ugyanazokat a műveleteket több mint egy éve támogatja az Azure Resource Manager alapú API-k készlete. Az Azure Portal az új REST API-kat használja az automatikus skálázáshoz és a metrikákhoz. Ezeken az Erőforrás-kezelő API-kon alapuló új SDK, PowerShell és CLI is elérhetők. A figyelésre szolgáló partnerszolgáltatásaink az Azure Monitor új Resource Manager-alapú REST API-jait használják fel.  

## <a name="who-is-not-affected"></a>Aki nem érintett

Ha az automatikus skálázás t az Azure Portalon, az [új Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, CLI vagy Resource Manager-sablonokon keresztül kezeli, nincs szükség műveletre.  

Ha metrikákat fogyaszt az Azure Portalon keresztül vagy különböző [figyelési partnerszolgáltatásokon](../../azure-monitor/platform/partners.md)keresztül, nincs szükség műveletre. A Microsoft felügyeleti partnerekkel együttműködve áttér az új API-kra.

## <a name="who-is-affected"></a>Az érintett

Ez a cikk a következő összetevők használata esetén vonatkozik:

- **Klasszikus Azure Insights SDK** – Ha a [klasszikus Azure Insights SDK-t](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)használja, váltson [Java](https://github.com/azure/azure-libraries-for-java#download)az új Azure Monitor SDK-ra [.](https://github.com/azure/azure-libraries-for-net#download) Töltse le az [Azure Monitor SDK NuGet csomagot.](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

- **Klasszikus automatikus skálázás** – Ha a [klasszikus automatikus skálázási beállítások API-kat](https://msdn.microsoft.com/library/azure/mt348562.aspx) hívja meg az egyéni tervezésű eszközökről, vagy a [klasszikus Azure Insights SDK-t](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)használja, át kell váltania a [Resource Manager Azure Monitor REST API használatával.](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)

- **Klasszikus metrikák** – Ha a metrikák a [klasszikus REST API-k](https://msdn.microsoft.com/library/azure/dn510374.aspx) vagy a [klasszikus Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) egyéni eszközök, át kell váltania a [Resource Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)használatával. 

Ha nem biztos abban, hogy a kód vagy az egyéni eszközök a klasszikus API-kat hívják-e, tekintse meg az alábbiakat:

- Tekintse át a kódban vagy eszközben hivatkozott URI-t. A klasszikus API-k https://management.core.windows.netaz URI-t használják. Az erőforrás-kezelő alapú API-k újabb URI-ját `https://management.azure.com/`kell használnia.

- Hasonlítsa össze a szerelvény nevét a gépen. A régebbi klasszikus https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/szerelvény a .

- Ha tanúsítvány-hitelesítést használ a metrikák vagy az automatikus skálázási API-k eléréséhez, klasszikus végpontot és könyvtárat használ. Az újabb Erőforrás-kezelő API-k megkövetelik az Azure Active Directory-hitelesítést egy egyszerű vagy felhasználó egyszerű szolgáltatáson keresztül.

- Ha az alábbi hivatkozások valamelyikén hivatkozott hívásokat használ, akkor a régebbi klasszikus API-kat használja.

  - [Windows.Azure.Management.Monitoring osztálykönyvtár](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Figyelés (klasszikus) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations felület](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Miért érdemes váltani

Az automatikus skálázás és a metrikák összes meglévő funkciója továbbra is működni fog az új API-kon keresztül.  

Az újabb API-kba való áttérés erőforrás-kezelő alapú képességekkel érhető el, például a figyelőszolgáltatások egységes szerepköralapú hozzáférés-vezérlésének (RBAC) támogatása. A mérőszámokhoz további funkciókat is szerezhet: 

- dimenziók támogatása
- konzisztens, 1 perces metrikus részletesség az összes szolgáltatásban 
- jobb lekérdezés
- nagyobb adatmegőrzés (93 nap metrikák vs. 30 nap) 

Összességében, mint az Azure összes többi szolgáltatása, az Erőforrás-kezelő alapú Azure Monitor API-k jobb teljesítményt, méretezhetőséget és megbízhatóságot eredményeznek. 

## <a name="what-happens-if-you-do-not-migrate"></a>Mi történik, ha nem telepíti át

### <a name="before-retirement"></a>Nyugdíjba vonulás előtt

Nem lesz közvetlen hatása az Azure-szolgáltatások vagy a munkaterhelések.  

### <a name="after-retirement"></a>Nyugdíjba vonulás után

A korábban felsorolt klasszikus API-k hívása sikertelen lesz, és a következőhöz hasonló hibaüzeneteket fog visszaadni:

Automatikus skálázás esetén: *Ez az API elavult. Az Automatikus skálázási beállítások kezeléséhez használja az Azure Portal, az Azure Monitor SDK, a PowerShell, a CLI vagy az Erőforrás-kezelő sablonjait.*  

Metrikák: *Ez az API elavult. Használja az Azure Portalon, az Azure Monitor SDK, PowerShell, CLI metrikák lekérdezéséhez.*

## <a name="email-notifications"></a>E-mail-értesítések

A rendszer a következő fiókszerepkörökhöz küldött e-mail-címekre megszüntetési értesítést: 

- Fiók- és szolgáltatásrendszergazdák
- Társadminisztrátorok  

Ha bármilyen kérdése van, MonitorClassicAPIhelp@microsoft.comlépjen kapcsolatba velünk a .  

## <a name="references"></a>Referencia

- [Újabb REST API-k az Azure Monitorhoz](https://docs.microsoft.com/rest/api/monitor/) 
- [Újabb Azure-figyelő SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
