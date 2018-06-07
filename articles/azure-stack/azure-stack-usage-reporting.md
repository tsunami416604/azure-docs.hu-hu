---
title: Jelentés Azure verem használati adatait az Azure-bA |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be Azure verem reporting használati adatok.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: daaaf6c574c4b169c19ebec42ad68e2d818ca1cb
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603702"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Jelentés Azure verem használati adatait az Azure-bA 

Használati adatokat, más néven az adatokkal, adja meg annak a használt erőforrások. 

Fogyasztás alapján számlázási modellt használó Azure verem többcsomópontos rendszerekhez jelenteniük kell használati adatokat az Azure-bA számlázási célra.  Azure verem operátorok kell konfigurálja az Azure-verem jelentés használati adatokat az Azure-bA.

> [!NOTE]
> Használati adatok jelentése az Azure-verem többcsomópontos-felhasználók számára a fizetési,-akkor-használható modellben licenc szükséges. Nem kötelező számára, akik a kapacitás modellben licenc (lásd a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). A felhasználók Azure verem szoftverfejlesztői készlet Azure verem operátorok használati adatait, és a szolgáltatás tesztelése. Felhasználók nem megterheljük keletkeznek bármely használatra. 


![számlázási folyamat](media/azure-stack-usage-reporting/billing-flow.png)

Használati adatok Azure verem az Azure-bA az Azure hídon keresztül küldi. Az Azure a commerce rendszer feldolgozza a használati adatokat, és állít elő, a számlázási. Miután a számlázási jön létre, az Azure-előfizetés tulajdonosa megtekinthetik, és töltse le a [Azure Account Center](https://account.windowsazure.com/Subscriptions). Hogyan történik az Azure-verem licencelése kapcsolatos információkért tekintse meg a [Azure verem csomagolására és dokumentum árképzési](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Állítsa be a használati adatok jelentése

Használati adatok jelentése beállításához kell [Azure verem példány regisztrálni Azure](azure-stack-register.md). A regisztrációs folyamat részeként az Azure-vermet, Azure verem kapcsolódik az Azure és a használati adatokat küld, amely Azure híd összetevő van konfigurálva. A következő használati adatok Azure küldeni az Azure oszlopból:

- **Azonosító mérni** – az erőforrás, a felhasznált egyedi azonosítója.
- **Mennyiség** – erőforrás-használat mennyisége.
- **Hely** – helyre az aktuális Azure verem erőforrás telepítési helyét.
- **Erőforrás URI** –, amelynek használati jelentett az erőforrás URI a teljes minősítése.
- **Előfizetés-azonosító** – Azure verem felhasználó, amely a helyi (Azure-verem) előfizetés előfizetés-azonosító.
- **Idő** – kezdő és záró idő a használati adatok. Van néhány késleltetés közötti idő amikor ezeket az erőforrásokat az Azure-készletben felhasználják, és a használati adatok commerce jelentésekor. 24 óránként Azure verem összesítések használati adatok és a jelentéskészítési használati adatokat az Azure kereskedelmi folyamat egy másik néhány órát igényel. Igen használati, amely hamarosan éjfél előtt előfordulhat, hogy jelennek meg az Azure a következő napon.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentése készítése

1. Használati adatok jelentésében teszteléséhez néhány erőforrások létrehozása Azure-készletben. Létrehozhat például egy [tárfiók](azure-stack-provision-storage-account.md), [Windows Server virtuális gép](azure-stack-provision-vm.md) és a Linux virtuális gép és a Basic és Standard termékváltozat hogyan alapvető használati jelez-e. A használati adatokat a különböző típusú erőforrások különböző mérőszámok alapján jelenti.

2. Hagyja a erőforrásaihoz, néhány órán keresztül. Használati adatok gyűjtése körülbelül óránként. Után gyűjtése, ezeket az adatokat az Azure-bA továbbított és feldolgozása az Azure kereskedelmi rendszerbe. Ez a folyamat néhány órára is eltarthat.

## <a name="view-usage---csp-subscriptions"></a>Használati adatok megtekintése – CSP előfizetések

Ha regisztrált az Azure-verem egy CSP-előfizetését használja, a használat és díjak megtekintheti a ugyanúgy, ahol megtekintheti az Azure-használatát. Az Azure verem használati szerepelni fog a számla és a fájlban egyeztetés keresztül elérhető [Partnerközpontjában](https://partnercenter.microsoft.com/partner/home). Az egyeztetés fájl havi frissült. Legutóbbi Azure verem használati adatok eléréséhez szükséges, ha a Partner Center API-kat is használhatja.

   ![partnerközpontjában](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Használati adatok megtekintése – nagyvállalati szerződés előfizetések

Ha regisztrált az Azure-verem egy nagyvállalati szerződés az előfizetést, a használat és díjak a megtekintheti a [EA Portal](https://ea.azure.com/). Azure verem használata mellett a jelentések szakaszban a EA portál Azure használati speciális letöltések fognak szerepelni. 

## <a name="view-usage--other-subscriptions"></a>Használati adatok megtekintése – más előfizetések

Ha regisztrált az Azure verem bármely más előfizetés típus használatával, például a Használatalapú előfizetés használat és díjak megtekintheti a az Azure account center. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) , az Azure rendszergazdai fiókot, és válassza ki az Azure-előfizetés, amely a regisztráció az Azure-verem. Az Azure-verem használati adatok, az alábbi ábrán látható módon a használt erőforrások mindegyikének felszámított összeg tekintheti meg:

   ![számlázási folyamat](media/azure-stack-usage-reporting/pricing-details.png)

Az Azure verem szoftverfejlesztői készlet Azure verem erőforrások nem fizetnie, az ár $0,00 jelenik meg.

## <a name="which-azure-stack-deployments-are-charged"></a>Mely Azure verem központi telepítések van szó?

Erőforrás-használat az Azure verem szoftverfejlesztői készlet szabad. Mivel Azure verem több csomópontos rendszerekhez, a munkaterhelési virtuális gépekhez, a tárolási szolgáltatások és az App Service szolgáltatások van szó.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>A virtuális gépek infrastruktúra felszámított felhasználók?

Nem. Használati adatok bizonyos Azure verem erőforrás-szolgáltató virtuális gépeket az Azure bejelentések, de nincsenek terhelések virtuális gépeken, sem a virtuális gépek telepítése során létrehozott engedélyezése az Azure-verem infrastruktúra van.  

Felhasználók csak a bérlői előfizetések alatt futó virtuális gépek van szó. A bérlői előfizetések Azure verem licencelési feltételeit. ahhoz, hogy minden munkaterhelést kell telepíteni.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Azure-veremben használni kívánt Windows Server licenccel kell, hogyan ehhez tennem?

A meglévő-licencet használ elkerüli a használati mérőszámok létrehozásakor. Meglévő Windows Server-licenceket használható Azure-készletben, a "Meglévő szoftver használata Azure verem" szakaszában leírt [az Azure verem licencelési útmutató](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Az ügyfelek kell telepítenie a Windows Server virtuális gépei, lásd: a [Windows Server-licenc hibrid juttatása](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) ahhoz, hogy a meglévő licenceiket cikk.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Mely előfizetés fel van töltve, a felhasznált erőforrásokért?
Az előfizetés, mikor biztosított [Azure verem regisztrálása az Azure-ral](azure-stack-register.md) fel van töltve.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetések használati adatok jelentése támogatottak?

Azure verem multinode nagyvállalati szerződés (EA) és a CSP előfizetések esetén támogatottak. Az Azure verem szoftverfejlesztői készlet nagyvállalati szerződés (EA), a használatalapú fizetés, a CSP és az MSDN-előfizetés támogatja a használati adatok jelentése.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Használati adatok szuverén felhőkben munkahelyi reporting használ?

A csomagban Azure verem használati adatok jelentése a globális Azure rendszerben létrehozott előfizetések van szükség. A szuverén felhők (az Azure Government, a Németországi Azure és az Azure Kína felhők) valamelyikével létrehozott előfizetések nem lehet regisztrálni az Azure-ral, így nem támogatja a használati adatok jelentése.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Hogyan azonosíthatja a felhasználók az Azure verem használati adatokat a számlázási Azure-portálon?

Felhasználók a használat részleteiről fájlban Azure verem használati adatok megtekintéséhez. Hogyan kérhet a használat részleteiről fájl tudnia, tekintse meg a [használati fájl letöltését az Azure Account Center cikk](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). A használat részleteiről fájl tartalmazza az Azure-verem mérőszámok, amelyek azonosítják a verem Azure storage és a virtuális gépek. Azure-készletben használt erőforrások jelenti az "Azure verem." nevű régió alatt

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Miért nem egyezik meg az Azure-készletben jelentett használati Azure Account Center által létrehozott jelentést?

Az az Azure-verem használati API-k által jelentett használati adatok és a használati adatokat az Azure Account Center által jelentett késleltetés mindig van. Ez a késés az használati adatokat az Azure kereskedelmi Azure veremből feltöltéséhez szükséges idő. Ez a késés miatt, amely hamarosan előtt éjfél használati előfordulhat, hogy jelenik meg az Azure-ban a következő napon. Ha használja a [Azure verem használati API-k](azure-stack-provider-resource-api.md), és hasonlítsa össze a használathoz az Azure számlázási portálon jelentett eredményt úgy is, hogy eltérés.

## <a name="next-steps"></a>További lépések

* [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
* [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
* [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
* [Felhasználás kezelésére és a Felhőbeli szolgáltató számlázási](azure-stack-add-manage-billing-as-a-csp.md)
