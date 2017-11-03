---
title: "Ügyfél számlázási és az Azure-készletben jóváírási |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Használati és számlázási Azure verem

Használati jelenti. a felhasználó által használt erőforrások mennyisége. Azure verem minden felhasználóhoz a használati adatokat gyűjt, és számlázási azokat használja. Ez a cikk ismerteti, hogyan Azure verem felhasználók számlázása az erőforrás-használatát, és hogyan érhető el a számlázási adatokat elemzés, jóváírási, stb.

Az Azure verem az infrastruktúra begyűjtése és összesítése a használati adatokat az összes erőforrásra, és ezen adatok elküldhetők az Azure kereskedelmi tartalmazza. Elérje ezeket az adatokat és számlázási adapter használatával exportálja a számlázási rendszerhez, vagy egy üzleti intelligencia eszköz, például a Microsoft Power BI exportálni onnan. Exportálása, után a számlázási adatokat analytics használt, vagy a jóváírási rendszerbe kerül.

![Fogalmi modell Azure verem csatlakozik a számlázás elszámolási adapter alkalmazás](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Használati folyamat

Mindegyik erőforrás-szolgáltató Azure verem használati adatok erőforrás-használat szerinti bocsát ki. Rendszeres időközönként a Usage webszolgáltatás (óránként vagy naponta) összesíti a használati adatait, és a használati adatbázisban tárolja. A tárolt adatok használati API-k használatával helyileg Azure verem operátor és felhasználó férhet hozzá. 

Ha rendelkezik [Azure verem példány regisztrálva van az Azure](azure-stack-register.md), használati híd a használati adatok küldése az Azure kereskedelmi van konfigurálva. Miután az adatok az Azure-ban elérhető, hozzá tud férni a számlázási portálon keresztül vagy Azure használati API használatával. Tekintse meg a [használati adatok jelentése](azure-stack-usage-reporting.md) témakörét, ahol több milyen használatával kapcsolatos adatok bejelentések Azure. 

A következő kép bemutatja a legfontosabb összetevők, a használati feldolgozási:

![Használati folyamat](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati információk találhatók, és hogyan?

Az Azure verem erőforrás-szolgáltatók, köztük a számítási, tárolási és hálózati, az egyes előfizetésekhez óránként hozhat létre használati adatokat. A használati adatok vonatkozó információkat tartalmaz az erőforrás, például az erőforrás nevét, a használt előfizetés, a használt mennyiség használt, stb. Mérőszámok azonosító erőforrásokkal kapcsolatos további tudnivalókért tekintse meg a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md) cikk. 

Miután a használati adatokat összegyűjtötte-e, [Azure jelentett](azure-stack-usage-reporting.md) létrehozni egy számlázási, amely az Azure számlázási portálján keresztül lehet megtekinteni. 

> [!NOTE]
> Használati adatok jelentése nincs szükség Azure verem szoftverfejlesztői készlet és integrált Azure verem rendszer felhasználók számára, akik a kapacitás modellben licenc. Azure-készletben licenceléssel kapcsolatos további tudnivalókért tekintse meg a [csomagolás és árképzési](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) Adatlap.

Az Azure számlázási portálon kizárólag a használt terhelhető erőforrások használati adatainak megjelenítése. Azure verem terhelhető erőforrásokat, rögzíti a használati adatok összefoglalásához szélesebb körben állítja be a erőforrásokat, amelyek a verem Azure környezetben a REST API-k vagy a Powershellen keresztül érheti el. Azure verem operátorok lekérhetik a használati adatok előfizetéseket felhasználói, mivel a felhasználó csak használati adatait kérheti le.

## <a name="retrieve-usage-information"></a>Használati adatok beolvasása

A használati adatok létrehozásához kell fut, használja a rendszer, például aktívan erőforrások, egy aktív virtuális géphez, vagy egy tárfiókot, úgy, hogy bizonyos adatok stb. Ha nem tudja, hogy rendelkezik olyan erőforrásokkal Azure verem Marketplace-beli, telepítse a virtuális gépet (VM) és ellenőrizze a virtuális gép panelt, és győződjön meg arról, hogy figyelési fut. A következő PowerShell-parancsmagok segítségével a használati adatok megtekintése:

1. [Telepítse a PowerShell Azure verem.](azure-stack-powershell-install.md)
2. [Konfigurálja az Azure-verem felhasználói](user/azure-stack-powershell-configure-user.md) vagy a [Azure verem operátor](azure-stack-powershell-configure-admin.md) PowerShell-környezet 

3. A használati adatok lekéréséhez használja a [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell-parancsmagot:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Következő lépések

[Jelentés Azure verem használati adatait az Azure-bA](azure-stack-usage-reporting.md)

[Szolgáltató Erőforrás kihasználtsága API](azure-stack-provider-resource-api.md)

[A bérlői API-erőforrás-használat](azure-stack-tenant-resource-usage-api.md)

[Használati kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)

