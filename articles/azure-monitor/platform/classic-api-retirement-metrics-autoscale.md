---
title: Az Azure Monitor használatból való kivonást egyaránt klasszikus üzemi modell API-k a metrikák és az automatikus méretezés
description: Metrikák és az automatikus skálázási klasszikus API-k, más néven Azure szolgáltatásfelügyelet (ASM) vagy kivezetjük az RDFE-alapú üzemi modellben
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: 1ea8fddf2b4e75abba38f011a271aada848a8cb5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192192"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Az Azure Monitor használatból való kivonást egyaránt klasszikus üzemi modell API-k a metrikák és az automatikus méretezés

Az Azure Monitor (korábbi nevén Azure Insights első kiadásakor) jelenleg lehetőséget nyújt a és hozhat létre és automatikus méretezési beállításainak kezelése a klasszikus virtuális gépeket és a klasszikus Cloud Services metrikák felhasználásához. A klasszikus üzemi modellen alapuló API-k lesz eredeti készletét **2019. június 30. után elavult** az összes Azure nyilvános és privát felhő minden régióban.   

Ugyanazokat a műveleteket támogatják keresztül egy csoportot az Azure Resource Manager-alapú API-k egy adott évben. Az Azure Portalon az automatikus méretezés és a mérőszámokhoz az új REST API-kat használ. Egy új SDK-t, a PowerShell és CLI Resource Manager API-k alapján is elérhetők. Figyelés partner szolgáltatásaink felhasználása az új Resource Manager-alapú Azure Monitor REST API-k.  

## <a name="who-is-not-affected"></a>Ki nem érintett

Ha az automatikus skálázás az Azure Portalon keresztül kezeli a [új Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, a parancssori felület vagy a Resource Manager-sablonok, semmilyen műveletet nem szükséges.  

Ha használ-e fel az Azure Portalon vagy a különböző metrikák [partner-szolgáltatások figyelésének](../../monitoring-and-diagnostics/monitoring-partners.md), semmilyen műveletet nem szükséges. A Microsoft monitoring migrálása az új API-k a partnerek dolgoznak.

## <a name="who-is-affected"></a>Akik érintett

Ez a cikk vonatkozik, ha a következő összetevőket használja:

- **Klasszikus Azure Insights SDK-t** – Ha használja a [klasszikus Azure Insights SDK-t](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), váltson át az új Azure Monitor SDK-t [.NET](https://github.com/azure/azure-libraries-for-net#download) vagy [Java](https://github.com/azure/azure-libraries-for-java#download). Töltse le a [Azure Monitor SDK NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasszikus automatikus skálázási** – Ha próbáljuk hívni a [klasszikus automatikus méretezési beállításokkal API-k](https://msdn.microsoft.com/library/azure/mt348562.aspx) a személyre szabott eszközök használatával, vagy a [klasszikus Azure Insights SDK-t](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), kell váltani a használatával[ Resource Manager az Azure Monitor REST API-val](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klasszikus metrikák** – Ha már használ a metrikák használata a [klasszikus REST API-k](https://msdn.microsoft.com/library/azure/dn510374.aspx) vagy [klasszikus Azure Insights SDK-t](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) az adott feladatra elől, meg kell térni a a [ Resource Manager az Azure Monitor REST API-val](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Ha biztos benne, hogy a kód vagy egyéni eszközöket a klasszikus API-k hívott, keresse meg a következőket:

- Tekintse át az URI-t a kódot, vagy az eszköz hivatkozik. A klasszikus API-k használata az URI-t https://management.core.windows.net. Meg kell használnia az újabb URI-t a Resource Manager alapú API-k kezdődik https://management.azure.com/.

- Hasonlítsa össze a szerelvény neve a gépen. A régebbi klasszikus szerelvény jelenleg https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Ha Tanúsítványalapú hitelesítés metrikák vagy az automatikus skálázási API-k eléréséhez használ, egy klasszikus végpont és a könyvtár használ. Az újabb, Resource Manager API-k a szolgáltatásnév vagy a felhasználó egyszerű Azure Active Directory-hitelesítésre van szükség.

- A következő hivatkozások bármelyikére: a dokumentációban hivatkozott hívások használja, ha a régebbi klasszikus API-kat használ.

  - [Windows.Azure.Management.Monitoring osztálytár](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Figyelés .NET (klasszikus)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations felület](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Miért kell váltani

A meglévő képességeket, az automatikus méretezés és a mérőszámokhoz továbbra is működnek az új API-kon keresztül.  

Áttelepítés során az újabb API-k kapható Resource Manager-alapú képességek, például a konzisztens szerepköralapú hozzáférés-vezérlés (RBAC) az összes figyelési szolgáltatások teljes támogatása. Ezenkívül igénybe veheti metrikákhoz további funkciók: 

- Dimenziók támogatása
- egységes 1 perces metrika granularitási összes szolgáltatásban 
- jobban lekérdezése
- magasabb szintű adatmegőrzést (93 napnyi metrikák vs. 30 nap) 

Általános, mint minden más szolgáltatás az Azure-ban, az erőforrás-kezelő alapján az Azure Monitor API-k kapható jobb teljesítményt, méretezhetőséget és megbízhatóságot. 

## <a name="what-happens-if-you-do-not-migrate"></a>Mi történik, ha nem telepíti át

### <a name="before-retirement"></a>Kivonás előtt

Nem az Azure-szolgáltatások vagy a számítási feladatok közvetlen hatással lesz.  

### <a name="after-retirement"></a>Kivonás után

Minden API-k, előzőleg felsorolt a klasszikus hívásainak meghiúsul, és vissza hibaüzenetek hasonló a következő kapcsolatok:

Az automatikus skálázás: *Ez az API elavult. Az automatikus méretezési beállítások kezelése az Azure Portalon, az Azure Monitor SDK-t, a PowerShell, a parancssori felület vagy a Resource Manager-sablonok használatával*.  

A metrikák: *Ez az API elavult. Használja az Azure Portalon, az Azure Monitor SDK-t, PowerShell, CLI metrikák lekérdezéséhez*.

## <a name="email-notifications"></a>E-mail-értesítések

E-mail-címek a következő fiók szerepköröknek küldött használatból való kivonást egyaránt értesítést: 

- Fiók és a szolgáltatás-rendszergazdák
- Társrendszergazdák  

Ha bármilyen kérdése van, írjon nekünk az MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referencia

- [Az Azure Monitor az újabb REST API-k](https://docs.microsoft.com/rest/api/monitor/) 
- [Újabb Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
