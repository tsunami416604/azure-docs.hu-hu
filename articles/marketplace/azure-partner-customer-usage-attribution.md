---
title: Az Azure-partnerek és az ügyfelek használati hozzárendelése | Azure Piactér
description: Az Azure Marketplace-megoldások ügyfélhasználatának nyomon követésének áttekintése
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/23/2019
ms.author: dsindona
ms.openlocfilehash: 2895944dea6417949488076186135680523e19db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874959"
---
# <a name="azure-partner-customer-usage-attribution"></a>Az Azure-partnererőforrások ügyfelek általi használatának nyomon követése

Az Azure szoftverpartnereként a megoldások Azure-összetevőket igényelnek, vagy közvetlenül az Azure-infrastruktúrára kell őket telepíteni. Azok az ügyfelek, akik egy partnermegoldást telepítenek, és saját Azure-erőforrásokat biztosítanak, nehezen érhetik el a központi telepítés állapotát, és optika-ot kaphatnak az Azure növekedésére gyakorolt hatásról. Ha magasabb szintű láthatóságot ad hozzá, a Microsoft értékesítési csapataihoz igazodik, és kreditet szerez a Microsoft partnerprogramjaiszámára.

A Microsoft most egy olyan módszert kínál, amely ekkel segít a partnereknek jobban nyomon követni az Azure-beli szoftvereik Azure-beli üzembe helyezésének azure-használatát. Az új módszer az Azure Resource Manager használatával vezényli az Azure-szolgáltatások üzembe helyezését.

Microsoft-partnerként társíthatja az Azure-használatot bármely Azure-erőforrással, amelyet az ügyfél nevében biztosít. A társítás az Azure Marketplace-en, a rövid útmutató tárházon, a privát GitHub-adattárakon és az egy-egy ügyféllel kapcsolatos elköteleződésen keresztül alakítható ki. Az ügyfélhasználat-hozzárendelés három telepítési lehetőséget támogat:

- Azure Resource Manager-sablonok: A partnerek erőforrás-kezelői sablonok használatával telepíthetik az Azure-szolgáltatásokat a partner szoftverének futtatásához. A partnerek létrehozhatnak egy Resource Manager-sablont az Azure-megoldásuk infrastruktúrájának és konfigurációjának meghatározásához. Az Erőforrás-kezelő sablon lehetővé teszi, hogy Ön és ügyfelei a megoldás teljes életciklusa során üzembe helyezhetők. Biztos lehet benne, hogy az erőforrások konzisztens állapotban vannak telepítve.
- Azure Resource Manager API-k: A partnerek közvetlenül hívhatják meg az Erőforrás-kezelő API-kat egy Resource Manager-sablon üzembe helyezéséhez, vagy létrehozhatják az API-hívásokat az Azure-szolgáltatások közvetlen kiépítéséhez.
- Terraform: A partnerek használhatják a felhővezényt, például a Terraformot egy Resource Manager-sablon üzembe helyezéséhez vagy az Azure-szolgáltatások közvetlen üzembe helyezéséhez.

Az ügyfélhasználat-hozzárendelés új telepítéshez használható, és nem támogatja a már üzembe helyezett meglévő erőforrások címkézését.

Az Azure-alkalmazáshoz szükség van [Azure Application](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)az ügyfélhasználati hozzárendelésre: az Azure Marketplace-en közzétett megoldássablon-ajánlat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata
Számos partnermegoldás van telepítve az ügyfél előfizetésén az Erőforrás-kezelő sablonok használatával. Ha rendelkezik egy Erőforrás-kezelő sablonnal, amely elérhető az Azure Marketplace-en, a GitHubon vagy rövid útmutatóként, a sablon módosításának folyamata az ügyfélhasználat-hozzárendelés engedélyezéséhez azonnal továbbításra kell, hogy kerüljön.

A megoldássablonok létrehozásával és közzétételével kapcsolatos további információkért lásd:

* [Hozza létre és telepítse az első Erőforrás-kezelő sablont.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)
* [Az Azure-alkalmazásajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Videó: [Megoldássablonok és felügyelt alkalmazások létrehozása az Azure Piactérhez](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>GUID hozzáadása a sablonhoz

Globálisan egyedi azonosító (GUID) hozzáadásához egyetlen módosítást kell végrehajtania a fő sablonfájlon:

1. [Hozzon létre egy GUID-t](#create-guids) a javasolt módszerrel, és [regisztrálja a GUID azonosítót.](#register-guids-and-offers)

1. Nyissa meg az Erőforráskezelő sablont.

1. Új erőforrás hozzáadása a fősablonfájlhoz. Az erőforrásnak csak a **mainTemplate.json** vagy **az azuredeploy.json** fájlban kell lennie, és nem beágyazott vagy csatolt sablonokban.

1. Adja meg a GUID értéket a **pid-előtag** után (pl. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Ellenőrizze a sablonban az esetleges hibákat.

1. Tegye közzé újra a sablont a megfelelő adattárakban.

1. [Ellenőrizze a GUID sikeres telepítését.](#verify-the-guid-deployment)

### <a name="sample-resource-manager-template-code"></a>Minta erőforrás-kezelő sablonkódja

A sablon nyomon követési erőforrásainak engedélyezéséhez a következő további erőforrást kell hozzáadnia az erőforrások szakaszhoz. Kérjük, győződjön meg róla, hogy módosítsa az alábbi mintakódot a saját bemeneteivel, amikor hozzáadja a fő sablonfájlhoz.
Az erőforrást csak a **mainTemplate.json** vagy **az azuredeploy.json** fájlban kell hozzáadni, és egyetlen beágyazott vagy csatolt sablonban sem.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Az Erőforrás-kezelő API-k használata

Bizonyos esetekben előfordulhat, hogy közvetlenül a Resource Manager REST API-k on az Azure-szolgáltatások üzembe helyezéséhez hívásokat kezdeményez. [Az Azure több SDK-t is támogat](https://docs.microsoft.com/azure/?pivot=sdkstools) a hívások engedélyezéséhez. Használhatja az SDK-k egyikét, vagy hívja meg a REST API-kat közvetlenül az erőforrások üzembe helyezéséhez.

Ha Erőforrás-kezelő sablont használ, a korábban ismertetett utasításokat követve címkézze meg a megoldást. Ha nem használja a Resource Manager-sablont, és közvetlen API-hívásokat hajt si, továbbra is megcímkézheti a központi telepítést az Azure-erőforrások használatának társításához.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Központi telepítés címkézése az Erőforrás-kezelő API-kkal

Az ügyfélhasználati hozzárendelés engedélyezéséhez az API-hívások tervezésekor vegyen fel egy GUID azonosítót a kérelemben a felhasználói ügynök fejlécében. Adja hozzá a GUID minden egyes ajánlat vagy termékváltozat. Formázza a **pid-** karakterláncot a pid-előtaggal, és tartalmazza a partner által létrehozott GUID azonosítót. Íme egy példa a felhasználói ügynökbe való beillesztés GUID formátumára:

![Példa GUID formátumra](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> A karakterlánc formátuma fontos. Ha a **pid-előtag** nem szerepel, nem lehet lekérdezni az adatokat. A különböző SDK-k eltérő módon követik nyomon. A módszer megvalósításához tekintse át a támogatott És nyomon követési megközelítést a preferált Azure SDK.To implement this method, review the support and tracking approach for your preferred Azure SDK.

#### <a name="example-the-python-sdk"></a>Példa: A Python SDK

Python esetén használja a **config** attribútumot. Az attribútum csak useragenthez adhat hozzá attribútumot. Például:

![Attribútum hozzáadása felhasználói ügynökhöz](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adja hozzá az attribútumot az egyes ügyfelekhez. Nincs globális statikus konfiguráció. Előfordulhat, hogy egy ügyfélgyárat jelöl meg, hogy megbizonyosodjon arról, hogy minden ügyfél nyomon követi. További információ: ez az [ügyfélgyári minta a GitHubon.](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Központi telepítés címkézése az Azure PowerShell használatával

Ha az Azure PowerShellen keresztül telepíterőforrásokat, a GUID-t a következő módszerrel fűzheti hozzá:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Központi telepítés címkézése az Azure CLI használatával

Ha az Azure CLI használatával hozzáfűzi a GUID, állítsa be a **AZURE_HTTP_USER_AGENT** környezeti változó. Ezt a változót parancsfájl hatókörén belül állíthatja be. A változót globálisan is beállíthatja a rendszerhéj hatókörére vonatkozóan:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
További információ: [Azure SDK for Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Terraform használata

A Terraform támogatása az Azure Provider 1.21.0-s kiadásán keresztül érhető el: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Ez a támogatás minden olyan partnerre vonatkozik, akik a terraformon keresztül telepítik a megoldásukat, valamint az Azure-szolgáltató által üzembe helyezett és felügyelt összes erőforrásra (1.21.0-s vagy újabb verzió).

Az Azure-szolgáltató a Terraform hozzáadott egy új opcionális mező neve [*partner_id,*](https://www.terraform.io/docs/providers/azurerm/#partner_id) amely, ahol adja meg a követési GUID, hogy a megoldáshoz használt. Ennek a mezőnek az értéke a *környezeti változó ARM_PARTNER_ID* is származhat.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Azoknak a partnereknek, akik a Terraform-on keresztül szeretnék telepíteni a telepítést, és az ügyfélhasználat-hozzárendelés alapján nyomon kívánják követni, a következőket kell tenniük:

* GUID létrehozása (a GUID-ot minden egyes ajánlathoz vagy termékváltozathoz hozzá kell adni)
* Frissítse az Azure-szolgáltatóértékét, hogy a *partner_id* értékét a GUID azonosítóra állítsa be (NE rögzítse előre a GUID-ot "pid-"-nel, csak állítsa be a tényleges GUID-ra)

## <a name="create-guids"></a>GUID azonosítók létrehozása

A GUID egy egyedi hivatkozási szám, amely 32 hexadecimális számjegyből áll. A nyomon követéshez guid azonosítók létrehozásához guid generátort kell használnia. Az Azure Storage csapata létrehozott egy [GUID-generátor űrlapot,](https://aka.ms/StoragePartners) amely e-mailben küldi el a megfelelő formátumú GUID-ot, és újra felhasználható a különböző követőrendszerekben.

> [!Note]
> Erősen ajánlott, hogy az [Azure Storage GUID generator űrlap](https://aka.ms/StoragePartners) használatával hozza létre a GUID. További információkért tekintse meg [a GYIK-et.](#faq)

Javasoljuk, hogy minden egyes termékhez hozzon létre egyedi GUID azonosítót minden egyes ajánlathoz és terjesztési csatornához. Ha nem szeretné, hogy a jelentésfelosztás a termék több terjesztési csatornájánként egyetlen GUID-ot használjon.

Ha egy sablon használatával telepít egy terméket, és az elérhető az Azure Marketplace-en és a GitHubon is, létrehozhat és regisztrálhat 2 különböző GUIDS-t:

*   "A" termék az Azure Piactéren
*   "A" termék a GitHubon

A jelentéskészítést a partnerérték (Microsoft Partner-azonosító) és a GUID-ok végzik.

A GUID-ok részletesebb szinten is nyomon követhetőek, például a termékváltozatok, ahol a termékváltozatok egy ajánlat változatai.

## <a name="register-guids-and-offers"></a>GUID-ok és ajánlatok regisztrálása

A GUID azonosítókregisztrálva kell lenniük az ügyfelek használatának engedélyezéséhez.

A sablon GUID azonosítóinak minden regisztrációja a Partnerközpontban történik.

Miután hozzáadja a GUID azonosítót a sablonhoz vagy a felhasználói ügynökhöz, és regisztrálja a GUID-ot a Partnerközpontban, a rendszer nyomon követi az összes központi telepítést.

1. Iratkozzon fel, mint a [kereskedelmi piactér kiadója](https://aka.ms/JoinMarketplace).

   * A partnereknek [profillal kell rendelkezniük a Partnerközpontban.](https://docs.microsoft.com/azure/marketplace/become-publisher) Javasoljuk, hogy sorolja fel az ajánlatot az Azure Marketplace-en vagy az AppSource-ban.
   * A partnerek több GUID-ot is regisztrálhatnak.
   * A partnerek regisztrálhatnak egy GUID-ot a nem Marketplace-alapú megoldássablonokhoz és -ajánlatokhoz.

1. Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard).

1. A jobb felső sarokban jelölje ki a beállítások fogaskerék ikonját, majd a **Fejlesztői beállítások lehetőséget**.

1. A **Fiókbeállítások lapon**válassza **a Követés GUID hozzáadása lehetőséget.**

1. A **GUID** mezőbe írja be a követési GUID azonosítót. Csak a GUID azonosítót adja meg **a pid-** előtag nélkül. A **Leírás** mezőbe írja be az ajánlat nevét vagy leírását.

1. Ha egynél több GUID azonosítót szeretne regisztrálni, válassza ismét **a Követés GUID hozzáadása** lehetőséget. További mezők jelennek meg az oldalon.

1. Kattintson a **Mentés** gombra.


## <a name="verify-the-guid-deployment"></a>A GUID központi telepítésének ellenőrzése

Miután módosította a sablont, és futtatott egy tesztközponti telepítést, használja a következő PowerShell-parancsfájlt a telepített és címkézett erőforrások lekéréséhez.

A parancsfájl segítségével ellenőrizheti, hogy a GUID sikeresen hozzá lett-e adva az Erőforrás-kezelő sablonhoz. A parancsfájl nem vonatkozik a Resource Manager API-ra vagy a Terraform-telepítésekre.

Jelentkezzen be az Azure-ba. Válassza ki az előfizetést a központi telepítés, amely ellenőrizni szeretné a parancsfájl futtatása előtt. Futtassa a parancsfájlt a központi telepítés előfizetési környezetében.

A **központi telepítés GUID** és **resourceGroup** neve szükséges paraméterek.

Az [eredeti parancsfájlt a](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) GitHubon szerezheti be.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Jelentés

A vevői használatra vonatkozó hozzárendelési jelentést a Partnerközpont[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)irányítópultján ( találja meg). A jelentés megtekintéséhez a Partnerközpont hitelesítő adatait kell használnia a bejelentkezéshez. Ha bármilyen problémát tapasztal a jelentéssel vagy a bejelentkezéssel kapcsolatban, hozzon létre egy támogatási kérelmet a Támogatás kérése szakasz utasításait követve.

A jelentés megtekintéséhez válassza a Kapcsolattípus legördülő listájában a Nyomon követett sablon lehetőséget.

![Jelentés a vevői felhasználás hozzárendeléséről](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Az ügyfelek értesítése

A partnereknek tájékoztatniuk kell ügyfeleiket az ügyfélhasználati hozzárendelést használó központi telepítésekről. A Microsoft jelenti a partnernek az azure-használat, amely ezekkel a központi telepítésekkel kapcsolatos. A következő példák olyan tartalmat tartalmaznak, amelynek segítségével értesítheti az ügyfeleket ezekről a központi telepítésekről. A példákban \<cserélje le a PARTNER> a vállalat nevére. A partnereknek gondoskodniuk kell arról, hogy az értesítés igazodjon az adatvédelmi és adatgyűjtési irányelveikhez, beleértve azügyfelek nyomon követésből való kizárásának lehetőségeit is.

### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés az Erőforrás-kezelő sablontelepítéseiről

A sablon telepítésekor a Microsoft képes azonosítani \<a PARTNER> szoftver telepítését a telepített Azure-erőforrásokkal. A Microsoft képes korrelálni a szoftver támogatásához használt Azure-erőforrásokat. A Microsoft azért gyűjti ezeket az adatokat, hogy a legjobb felhasználói élményt nyújtsa a termékeikkel kapcsolatban, és működtesse a vállalkozását. Az adatokat a Microsoft adatvédelmi irányelvei gyűjtik és https://www.microsoft.com/trustcenterszabályozzák, amelyek a következő helyen találhatók:

### <a name="notification-for-sdk-or-api-deployments"></a>Értesítés Az SDK- vagy API-telepítésekről

A PARTNER \<> szoftver telepítésekor a Microsoft \<képes azonosítani a PARTNER> szoftver telepítését a telepített Azure-erőforrásokkal. A Microsoft képes korrelálni a szoftver támogatásához használt Azure-erőforrásokat. A Microsoft azért gyűjti ezeket az adatokat, hogy a legjobb felhasználói élményt nyújtsa a termékeikkel kapcsolatban, és működtesse a vállalkozását. Az adatokat a Microsoft adatvédelmi irányelvei gyűjtik és https://www.microsoft.com/trustcenterszabályozzák, amelyek a következő helyen találhatók:

## <a name="get-support"></a>Támogatás kérése

A felmerülő problémáktól függően két támogatási csatorna létezik.

Ha bármilyen problémát tapasztal a Partnerközpontban, például látja az ügyfélhasználati hozzárendelési jelentést vagy bejelentkezik, hozzon létre támogatási kérelmet a Partnerközpont támogatási csapatával:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Ha segítségre van szüksége a Marketplace-bevezetéshez és/vagy az ügyfelek használatához általában, például az ügyfélhasználati hozzárendelés beállításához, kövesse az alábbi lépéseket:

1. Nyissa meg a [támogatási oldalt](https://go.microsoft.com/fwlink/?linkid=844975).

1. A **Probléma típusa csoportban**válassza **a Marketplace Onboarding**lehetőséget.

1. Válaszd ki a problémád **kategóriáját:**

   - Használati társítási problémák esetén válassza az **Egyéb**lehetőséget.
   - Az Azure Piactérrel kapcsolatos hozzáférési problémák esetén válassza az **Access Problem (Hozzáférési probléma) lehetőséget.**

     ![A problémakategória kiválasztása](media/marketplace-publishers-guide/lu-article-incident.png)

1. Válassza **a Kérés indítása**lehetőséget.

1. A következő lapon adja meg a szükséges értékeket. Válassza a **Folytatás** elemet.

1. A következő lapon adja meg a szükséges értékeket.

   > [!Important]
   > Az **Incidens címmezőbe** írja be az **ISV-használat követést.** Írja le részletesen a problémát.

   ![Az incidens címéhez adja meg az ISV-használat követést](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Töltse ki az űrlapot, majd válassza **a Küldés**lehetőséget.

Technikai útmutatást is kaphat a Microsoft partner műszaki tanácsadójától a technikai előértékesítési, üzembe helyezési és alkalmazásfejlesztési forgatókönyvekhez, hogy megértse és beépítse az ügyfelek használati hozzárendelését.

### <a name="how-to-submit-a-technical-consultation-request"></a>A technikai konzultációs kérelem benyújtása

1. Látogasson [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney)el.
1. Válassza a Felhőalapú infrastruktúra és -felügyelet lehetőséget, és megnyílik egy új lap a technikai út megtekintéséhez.
1. A Telepítési szolgáltatások csoportban kattintson a Kérelem küldése gombra
1. Jelentkezzen be MSA-fiókjával (MPN-fiók) vagy AAD-fiókjával (Partner irányítópult-fiók); a bejelentkezési hitelesítő adatok alapján megnyílik egy online igénylőlap:
    * Töltse ki/tekintse át a kapcsolattartási adatokat.
    * A konzultáció részletei előre kitölthetők, vagy választhatnak a legördülő menükből.
    * Adja meg a címet és a probléma leírását (adjon meg a lehető legrészletesebben).
1. Kattintson a Submit (Küldés) gombra

Tekintse meg a részletes utasításokat a [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)képernyőképek segítségével a.-on.

### <a name="whats-next"></a>A következő lépések

A Microsoft partner műszaki tanácsadója felveszi Önnel a kapcsolatot, hogy az ön igényeinek megfelelő hívást kezdeményezzen.

## <a name="faq"></a>GYIK

**Mi az előnye, hogy hozzáadja a GUID-t a sablonhoz?**

A Microsoft a partnerek számára áttekintést nyújt a megoldásaik ügyféláltali üzembe helyezéséről, és betekintést nyújt a használatukra vonatkozó információkhoz. Mind a Microsoft, mind a partner felhasználhatja ezeket az információkat az értékesítési csapatok közötti szorosabb elköteleződés érdekében. Mind a Microsoft, mind a partner használhatja az adatokat, hogy egységesebb képet kapjon az egyes partnerek Azure-növekedésre gyakorolt hatásáról.

**A GUID hozzáadása után módosítható-e?**

Igen, az ügyfél vagy a megvalósítási partner testreszabhatja a sablont, és módosíthatja vagy eltávolíthatja a GUID azonosítót. Javasoljuk, hogy a partnerek proaktív módon írják le az erőforrás és a GUID szerepét ügyfeleikés partnereik számára, hogy megakadályozzák a GUID eltávolítását vagy szerkesztését. A GUID módosítása csak az új, nem a meglévő, a központi telepítéseket és az erőforrásokat érinti.

**Nyomon követhetem a nem microsoftos tárházból, például a GitHubról telepített sablonokat?**

Igen, mindaddig, amíg a GUID jelen van a sablon telepítésekor, a használat nyomon követi. A partnereknek rendelkezniük kell egy profillal a kereskedelmi piactéren a Partnerközpontban való regisztrációban az Azure Marketplace-en kívüli üzembe helyezéshez használt GUID-ok regisztrálásához.

**Az ügyfél is kap jelentést?**

Az ügyfelek nyomon követhetik az egyes erőforrások vagy az ügyfél által definiált erőforráscsoportok használatát az Azure Portalon belül.

**Hasonló-e ez a módszer a digitális adatkapcsolati partnerhez (DPOR)?**

Ez az új módszer a központi telepítés és a használat partner megoldásához való csatlakoztatása egy olyan mechanizmust biztosít, amely egy partnermegoldást az Azure-használathoz kapcsol. A DPOR egy tanácsadó (Systems Integrator) vagy felügyeleti (felügyelt szolgáltató) partnertársának társítására szolgál az ügyfél Azure-előfizetéséhez.

**Mi az előnye az Azure Storage GUID Generator űrlapjának?**

Az Azure Storage GUID Generator űrlap garantáltan a szükséges formátumú GUID-ot hozza létre. Emellett ha az Azure Storage adatsík-követési módszerei közül bármelyiket használja, használhatja ugyanazt a GUID-ot a Piactér vezérlősík-követéshez. Ez lehetővé teszi, hogy kihasználja az egységes, egységes GUID-ot a partnerhozzárendeléshez anélkül, hogy külön GUIDS-t kellene fenntartania.

**Használhatok egy privát, egyéni virtuális merevlemezt egy megoldássablon-ajánlathoz az Azure Marketplace-en?**

Nem, nem lehet. A virtuális gép lemezképének az Azure [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)Piactérről kell származnia, lásd: .

Virtuálisgép-ajánlatot hozhat létre a piactéren az egyéni virtuális merevlemez használatával, és jelölje meg privátként, hogy senki ne láthassa. Ezután hivatkozzon erre a virtuális gépre a megoldássablonban.

**Nem sikerült frissíteni a *contentVersion* tulajdonságot a fő sablonhoz?**

Valószínűleg egy hiba bizonyos esetekben, amikor a sablon telepítése egy TemplateLink egy másik sablont, amely elvárja a régebbi contentVersion valamilyen okból. A megoldás a metaadat-tulajdonság használata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
