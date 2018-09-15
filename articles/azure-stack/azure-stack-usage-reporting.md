---
title: Az Azure-bA az Azure Stack-használati adatok jelentése |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Stackben jelentéskészítés a használati adatokról.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 54a81e6c5c6e1fe5c37b985e40174dc369edfe6d
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630409"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Az Azure-bA az Azure Stack-használati adatok jelentése 

Használati adatok, más néven a használati adatokat, a használt erőforrások mennyisége jelöli. 

Fogyasztásalapú számlázási modellt használó Azure Stack többcsomópontos rendszerekhez jelentse használati adatokat az Azure számlázási célból.  Az Azure Stack-operátorok konfigurálnia kell az Azure Stack-példány jelentés használati adatokat az Azure-bA.

> [!NOTE]
> Használati adatok jelentése szükség a több csomópontos Azure Stack-felhasználókat, akik a Használatalapú mint-akkor-használható modellben licenc. Nem kötelező ügyfelek esetében, akik a kapacitás modellben licenc (lásd a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). Az Azure Stack Development Kit a felhasználóknak az Azure Stack-operátorok használati adatokat, és tesztelheti a szolgáltatást. Azonban felhasználók nem kell fizetni keletkeznek használatot. 


![számlázási folyamat](media/azure-stack-usage-reporting/billing-flow.png)

Használati adatok az Azure Stack az Azure-bA az Azure hídon keresztül zajlik. Az Azure-ban a kereskedelmi rendszer dolgozza fel a használati adatok, és állít elő, a számlán. Miután a számla jön létre, az Azure-előfizetés tulajdonosa megtekintheti és töltse le a [Azure Account Center](https://account.windowsazure.com/Subscriptions). Hogyan licencelve van-e az Azure Stack kapcsolatos további információkért tekintse meg a [csomagolása és a díjszabás a dokumentum az Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Állítsa be a használati adatok jelentése

Használati adatok jelentése beállításához kell [regisztrálása az Azure Stack-példány az Azure-ral](azure-stack-register.md). A regisztrációs folyamat részeként az Azure Stack, amely kapcsolódik az Azure Stack az Azure-ba, és a használati adatokat küld, Azure híd összetevője van konfigurálva. A következő használati adatok Azure Stackben is küld az Azure-bA:

- **Mérőszám azonosítója** – az a felhasznált erőforrás egyedi azonosítója.
- **Mennyiség** – erőforrás-használat mennyisége.
- **Hely** – helyre, ahol a jelenlegi Azure Stack-erőforrás üzembe van helyezve.
- **Erőforrás-URI** – teljes URI-t az erőforrás, amelynek használati jelentett.
- **Előfizetés-azonosító** – az Azure Stack felhasználónak, amely a helyi (az Azure Stack) előfizetés előfizetés-azonosítója.
- **Idő** – a használati adatok kezdő és befejező időpontja. Bizonyos időbeli késedelem van az időpont közötti amikor ezeket az erőforrásokat az Azure Stackben felhasználják, és a használati adatok kereskedelmi jelentésekor. 24 óránként Azure Stack összesítések használati adatait és a jelentéskészítési használati adatokat az Azure-ban kereskedelmi folyamat egy másik néhány órát vesz igénybe. Tehát használata hamarosan éjfél előtt következik be, előfordulhat, hogy jelenik meg az Azure-ban a következő napon.

## <a name="generate-usage-data-reporting"></a>Használati adatok jelentések készítése

1. Jelentéskészítés a használati adatokról teszteléséhez hozzon létre néhány erőforrásokat az Azure Stackben. Létrehozhat például egy [tárfiók](azure-stack-provision-storage-account.md), [Windows Server rendszerű virtuális gép](azure-stack-provision-vm.md) és a egy Linuxos virtuális Gépre, az alapszintű és Standard alapvető használati jelentett hogyan megtekintéséhez. A használati adatok a különböző típusú erőforrások különböző mérőszámok alapján jelenti.

2. Az erőforrások néhány órán keresztül fut, hagyja. Körülbelül óránként gyűjt használati adatokat. Ezeket az adatokat, begyűjtését követően az Azure-bA továbbított adatok köre és dolgozzák fel az Azure kereskedelmi rendszerben. Ez a folyamat több óráig is eltarthat.

## <a name="view-usage---csp-subscriptions"></a>Használati adatok megtekintése – CSP-előfizetésekben

Ha regisztrált az Azure Stack használatával a CSP-előfizetésekben, ahol megtekintheti az Azure-használati ugyanúgy a használat és költségek megtekintheti. Az Azure Stack használatának fognak szerepelni a számlákon, és az egyeztetési fájlban, keresztül elérhető [Partnerközpont](https://partnercenter.microsoft.com/partner/home). Az egyeztetési fájlt a havonta frissül. Ha a legutóbbi Azure Stack-használati adatok elérésére van szüksége, használhatja a Partner Center API-k.

   ![partnerközpont](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Használati adatok megtekintése – nagyvállalati szerződéses előfizetések

Ha regisztrált az Azure Stack használatával a nagyvállalati szerződéses előfizetésébe, megtekintheti a használattal és a díjak a [a nagyvállalati szerződések portáljának](https://ea.azure.com/). A speciális letöltéseket és a jelentések szakaszban a nagyvállalati szerződések portálján az Azure-használat az Azure Stack használatának fog szerepelni. 

## <a name="view-usage--other-subscriptions"></a>Használati adatok megtekintése – más előfizetések

Ha regisztrált az Azure Stack használatával bármilyen más előfizetés típusa, például egy Használatalapú előfizetés használat és költségek megtekintheti az Azure account Center webhelyen. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) az Azure-rendszergazdai fiók, és válassza ki az Azure-előfizetést, amellyel az Azure Stack regisztrálni. Az Azure Stack használati adatok, az összeg, az egyes, a használt erőforrások díjat számítunk fel, a következő képen látható módon tekintheti meg:

   ![számlázási folyamat](media/azure-stack-usage-reporting/pricing-details.png)

Az Azure Stack Development Kit for Azure Stack-erőforrások nem terheli, így a díj $0,00 jelenik meg.

## <a name="which-azure-stack-deployments-are-charged"></a>Melyik Azure Stack üzemelő példányok díja?

Erőforrás-használat az Azure Stack Development Kit díjmentes. Mivel az Azure Stack több csomópontos rendszerekhez, a munkaterhelési virtuális gépekhez, a tárolási szolgáltatások és az App Services számítjuk fel.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Felhasználók a virtuális gépek infrastruktúra díjkötelesek?

Nem. Jelentett használati adatok az egyes Azure Stack erőforrás-szolgáltató virtuális gépeket az Azure-ba, de nem számítunk fel díjat ezeknek a virtuális gépeknek, sem a virtuális gépek üzembe helyezésének részeként létrehozott engedélyezése az Azure Stack-infrastruktúra.  

Felhasználók csak a bérlő az előfizetések alatt futó virtuális gépek fizetnie. Minden számítási feladatok Azure Stack licencelési feltételeit ahhoz, hogy a bérlő előfizetéseknél kell telepíteni.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Az Azure Stacken használni kívánt Windows Server-licence van, hogyan kell ehhez tennem?

A meglévő licencek használatával elkerülhető a használati mérőszámok létrehozása. Windows Server-licenceit az Azure Stackben, használható, a "Meglévő szoftverek használata az Azure Stack használatával" szakaszban leírtak szerint [az Azure Stack-licencelési útmutató](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). A Windows Server virtuális gépek üzembe helyezése, leírtak szerint kell az [Hybrid Benefit értékelem a Windows Server-licenc](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) cikk annak érdekében, hogy a meglévő licenceit használja.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Melyik előfizetést a felhasznált erőforrásokért fizet?
Az előfizetés, mikor biztosított [regisztrálása az Azure Stack az Azure-ral](azure-stack-register.md) díjat számítunk fel.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Milyen típusú előfizetések használati adatok jelentése támogatottak?

Az Azure Stack multinode a nagyvállalati szerződés (EA) és a CSP-előfizetések használata támogatott. Az Azure Stack Development Kit nagyvállalati szerződés (EA), a használatalapú fizetés, a CSP és az MSDN-előfizetés támogatja a használati adatok jelentése.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>A független felhőkben munkahelyi jelentéskészítés a használati adatokról nem?

Az Azure Stack Development Kit szükséges használati adatok jelentése a globális Azure rendszerben létrehozott előfizetések. A független felhőkben (Azure Government, Azure Germany és Azure China felhők) egyikében létrehozott előfizetések nem lehet regisztrálni az Azure-ban, így azok nem támogatják a használati adatok jelentése.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Hogyan azonosíthatja a felhasználók az Azure Stack használati adatokat az Azure számlázási portálján?

Felhasználói láthatják az Azure Stack használati adatok a használat részletei fájlban. Tudnivalók a használati részletek fájl beolvasásával kapcsolatos, tekintse meg a [használati fájlok letöltéséről a cikk az Azure Account Center](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). A használati részletei az Azure Stack mérőszámok, amelyek azonosítják az Azure Stack-tárolási és virtuális gépeket tartalmaz. Az Azure Stackben használt összes erőforrást a "Az Azure Stack." nevű régió alatt jelentett

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Miért nem egyezik meg a használati jelentés készül az Azure Stack az Azure Account Center által létrehozott jelentést?

Az Azure Stack-használati API-k által jelentett használati adatok és az Azure Account Center által jelentett használati adatok késleltetés mindig van. Ez a késleltetés a használati adatokat az Azure Stack az Azure kereskedelmi feltöltéséhez szükséges idő. Miatt ez a késleltetés használata hamarosan éjfél előtt következik be, előfordulhat, hogy jelenik meg az Azure-ban a következő napon. Ha használja a [Azure Stack-használati API-k](azure-stack-provider-resource-api.md), és hasonlítsa össze az eredményeket a használati jelentés készül az Azure számlázási portálján, akkor megtekintheti, számít.

## <a name="next-steps"></a>További lépések

* [Szolgáltatói használati API](azure-stack-provider-resource-api.md)  
* [Bérlői használati API](azure-stack-tenant-resource-usage-api.md)
* [Használat – gyakori kérdések](azure-stack-usage-related-faq.md)
* [Kezelése a használati és számlázási információkkal Felhőszolgáltató](azure-stack-add-manage-billing-as-a-csp.md)
