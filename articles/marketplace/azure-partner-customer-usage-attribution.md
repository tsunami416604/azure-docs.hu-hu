---
title: Az Azure partner- és ügyfél használati megnevezése
description: Nyomon követheti az ügyfelek általi használatot, az Azure piactér-megoldások áttekintése
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: 3f3c7523bfc800a74da56b1b3241ac5756c68d14
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215506"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partneri ügyfél használati megnevezése

Partnerként szoftverek az Azure a megoldások Azure-összetevőket kell, vagy közvetlenül az Azure-infrastruktúra üzembe van szükségük. Ügyfelek, akik egy partnermegoldás üzembe helyezését, és a saját Azure-erőforrások kiépítése nehéz értékes információkhoz juthat a központi telepítés állapotát, és optika kaphat a Azure növekedési gyakorolt hatást. Amikor hozzáad egy magasabb szintű láthatóság, igazodnak a Microsoft értékesítői csapatok, és a Microsoft partner programok kreditet kaphat. 

A Microsoft kínál egy metódust elősegítik a partnerek jobban ügyfél központi telepítését a szoftver az Azure használatának nyomon követése az Azure-ban. Az új módszer, amellyel a központi telepítés az Azure-szolgáltatások Azure Resource Managert használja.

A Microsoft-partnerként bármely Azure-erőforrások, amelyek az ügyfél nevében üzembe Azure-használat is társíthat. A társítás használatával az Azure piactéren, a rövid útmutató tárház, privát GitHub-adattárak és személyes customer engagement is alkotnak. Ügyfél használati tesznek elérhetővé; ilyenek három központi telepítési beállításokat támogatja:

- Az Azure Resource Manager-sablonok: Partnerek a Resource Manager-sablonok segítségével telepítheti a partner szoftverek futtatását az Azure-szolgáltatások. Partnerek hozhat létre egy Resource Manager-sablon infrastruktúráját és konfigurációját az Azure-megoldás meghatározásához. A Resource Manager-sablon lehetővé teszi, hogy Ön és ügyfelei teljes életciklusa során a megoldás üzembe helyezéséhez. Biztos lehet benne, hogy az erőforrások telepítése konzisztens lesz. 
- Az Azure Resource Manager API-kat: Partnerek segítségével meghívhatja a Resource Manager API-k, közvetlenül a Resource Manager-sablon üzembe helyezése vagy létrehozni az API-hívások közvetlenül az Azure-szolgáltatások kiépítése. 
- A Terraform: Partnerek felhőalapú szervezőt, például a Terraform használatával a Resource Manager-sablon üzembe helyezése, vagy közvetlenül az Azure-szolgáltatások telepítését. 

Ügyfél használati tesznek elérhetővé; ilyenek az új központi telepítést, és nem támogatja a címkézést a meglévő erőforrások, már telepítve lett.

Az ügyfél használati tesznek elérhetővé; ilyenek szükséges [Azure-alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): megoldás sablon ajánlat az Azure Marketplace-en közzétett.

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata

Számos partneri megoldás telepítve vannak, egy ügyfél előfizetését Resource Manager-sablonok használatával. Ha rendelkezik egy Resource Manager-sablon, amely elérhető az Azure piactéren, a Githubon, vagy a rövid útmutatóban a folyamat engedélyezéséhez az ügyfél használati tesznek elérhetővé; ilyenek a sablon módosításához nagyon egyszerű kell lennie.

A létrehozása és közzététele Megoldássablonok további információkért lásd:

* [Létre és helyezhet üzembe az első Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Az Azure Application ajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Videó: [Létrehozásához Megoldássablonokkal, és a felügyelt alkalmazások az Azure Marketplace-en](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Adjon hozzá egy GUID Azonosítót a sablonhoz

Adjon hozzá egy globálisan egyedi azonosítóját (GUID), győződjön meg a fő sablonfájl egyetlen módosítása:

1. [Hozzon létre egy GUID Azonosítót](#create-guids) a javasolt módszer használatával és [regisztrálja a GUID](#register-guids-and-offers).

1. Nyissa meg a Resource Manager-sablon.

1. Adjon hozzá egy új erőforrást a fő sablonfájlban. Az erőforrás kell lennie a **mainTemplate.json** vagy **azuredeploy.json** fájl csak, és nem az összes beágyazott vagy kapcsolódó sablonok.

1. Adja meg a GUID értéket után a **pid -** előtagja (például pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Ellenőrizze a sablont, a hibákat.

1. Tegye közzé újra a sablont a megfelelő tárházban.

1. [Sikerességéről GUID Azonosítóját a sablon központi telepítésben lévő](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Mintául szolgáló Resource Manager-sablon kódot

Ahhoz, hogy a sablon erőforrásainak követési, hozzá kell a következő további erőforrás az erőforrás szakasz alatt. Ellenőrizze, hogy módosítani az alábbi mintakód a saját adatbevitel a fő sablonfájl hozzáadásakor.
Az erőforrás a hozzá kell adnia a **mainTemplate.json** vagy **azuredeploy.json** fájl csak, és nem az összes beágyazott vagy kapcsolódó sablonok.
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

## <a name="use-the-resource-manager-apis"></a>A Resource Manager API-k

Bizonyos esetekben előfordulhat, hogy szeretne hívásokat indítani közvetlenül a Resource Manager REST API-k üzembe helyezéséhez az Azure-szolgáltatások. [Az Azure támogatja több SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) ahhoz, hogy ezeket a hívásokat. Az SDK-k valamelyikével, vagy közvetlenül az erőforrások üzembe helyezése a REST API-kat.

Resource Manager-sablon használata, a megoldás a korábban ismertetett utasítások szerint kell címkéznie. Ha nem egy Resource Manager-sablon használatával, és közvetlen API-hívások, továbbra is megjelölheti a központi telepítés rendelni az Azure-erőforrások használatát. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>A Resource Manager API-kkal központi telepítés megjelölése

Ügyfél használati megnevezése, az API-hívások tervezésekor engedélyezéséhez vegye fel a felhasználói ügynök a kérelemben szereplő tartományfejléc egy GUID Azonosítót. Adja hozzá az egyes ajánlatok GUID azonosítója vagy Termékváltozat. A karakterláncot formázni a **pid -** előtagot, valamint tartalmazza a partner által létrehozott GUID. Íme egy példa a GUID azonosító formátumát szúr be a felhasználói ügynök: 

![GUID formátumban](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> A karakterlánc formátuma fontos. Ha a **pid -** előtag nem tartalmaz, ezért nem lehetséges az adatok lekérdezéséhez. Különböző SDK-k eltérően nyomon követése. Ez a metódus megvalósításához, tekintse át a támogatási, és nyomon követi a megközelítést az előnyben részesített Azure SDK-hoz készült. 

#### <a name="example-the-python-sdk"></a>Példa: The Python SDK

A Python, használja a **config** attribútum. Az attribútum csak egy UserAgent adhat hozzá. Például:

![Az attribútum hozzáadása a felhasználói ügynök](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Az attribútum hozzáadása minden ügyfél számára. Nem tartozik a globális statikus konfiguráció. Egy ügyfél-előállítót, és ellenőrizze, hogy minden ügyfél nyomon követéséhez előfordulhat, hogy címkékkel. További információkért lásd: Ez [ügyfél gyári minta a Githubon](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Központi telepítés címkézése az Azure PowerShell-lel

Ha helyez üzembe erőforrásokat az Azure Powershellen keresztül, fűzze hozzá a GUID használatával a következő metódust:

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Központi telepítés címkézése az Azure CLI-vel

Az Azure CLI használatával fűzze hozzá a GUID Azonosítót, ha a **AZURE_HTTP_USER_AGENT** környezeti változót. Ezt a változót egy parancsfájl hatókörén belül állíthatja be. A változó globálisan rendszerhéj hatókör számára is állíthatja be:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
## <a name="use-terraform"></a>A Terraform használata

A Terraform támogatása az Azure szolgáltatói 1.21.0 keresztül érhető el kiadási: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Ez a támogatás érvényes összes partnereknek a megoldás a Terraform telepítése és minden erőforrások üzembe helyezése és az Azure-szolgáltató által mért (1.21.0 verzió vagy újabb).

Terraform az Azure-szolgáltatója nevű új nem kötelező mezőt hozzá [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) adhatja meg a követési GUID azonosítója, amelyet használhat a megoldáshoz, azaz. Ez a mező értékét is képes eszközattribútumból származó a *ARM_PARTNER_ID* környezeti változó.

```
provider "azurerm" { 
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 
          …… 
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Szeretné, hogy az ügyfél használati tesznek elérhetővé; ilyenek által nyomon követett Terraform keresztüli üzembe helyezést partnereknek kell tegye a következőket:

* Hozzon létre egy GUID Azonosítót (GUID kell adni egyes ajánlat és a Termékváltozat)
* Az értéket a saját Azure-szolgáltató frissítése *partner_id* GUID (ne előtti javítás a GUID Azonosítót az "pid-", csak állítsa be a tényleges GUID)

## <a name="create-guids"></a>Hozzon létre GUID-azonosítói

GUID 32 hexadecimális számjegyet tartalmaz egyedi hivatkozási szám. Követési GUID azonosítókat létrehozni, egy GUID-generátort kell használnia. Az Azure Storage csapata létrehozott egy [GUID generátor űrlap](https://aka.ms/StoragePartners) , amely e-mailt küld, egy GUID Azonosítót a helyes formátumú, és a különböző nyomkövetési rendszerek felhasználhatók. 

> [!Note]
> Azt javasoljuk, hogy használja [Azure Storage GUID generátor űrlap](https://aka.ms/StoragePartners) hozhat létre a GUID Azonosítót. További információkért lásd: a [– gyakori kérdések](#faq).

Javasoljuk, hogy hozzon létre egy egyedi GUID azonosító minden egyes termékekhez tartozó ajánlat és a terjesztési csatorna. A termék több terjesztési csatornák egyetlen GUID használatára, ha nem szeretné méretüknél fogva reporting kérheti. 

Ha a sablonok segítségével adott termék központi telepítését, és az Azure Marketplace-en és githubon érhető, akkor létrehozhat és regisztrálhat 2 egyedi GUID:

*   Az Azure Marketplace-en A termék 
*   A termék a Githubon

Jelentéskészítés végzi el a partner érték (Microsoft-Partnerazonosító) és a GUID megváltozott. 

GUID azonosítókat, például a Termékváltozat, ahol a termékváltozatok a következők ajánlat változatának részletesebb szinten is követheti.

## <a name="register-guids-and-offers"></a>Regisztrálja a GUID-ok és ajánlatok

Ügyfél használati tesznek elérhetővé; ilyenek engedélyezéséhez regisztrálni kell a GUID megváltozott.

Az összes regisztrációs sablon GUID-n keresztül az Azure Marketplace-en Cloud Partner Portal (CPP) kell elvégezni. 

Miután a GUID azonosító hozzáadása a sablonhoz, vagy a felhasználói ügynök, és regisztrálja a globálisan egyedi Azonosítót a CPP, központi telepítések nyomon követi. 

1. A alkalmazni [Azure Marketplace-en](https://aka.ms/listonazuremarketplace) és a CPP eléréséhez.

   * Partnerek szükségesek [rendelkeznek profillal a CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Javasoljuk, hogy a listában az ajánlat az Azure Marketplace-en vagy az appsource-ban Ön.
   * Partnerek regisztrálhatja az több GUID-ot.
   * Partnerek regisztrálhat egy GUID Azonosítót a-Marketplace megoldássablonok és ajánlatokkal.
 
1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

1. A jobb felső sarokban válassza a fiók ikont, és válassza **közzétevő profil**.

   ![Közzétevő profil kiválasztása](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Az a **profillapján**válassza **követési GUID hozzáadása.**

   ![Válassza a nyomon követése a GUID azonosító hozzáadása](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Az a **követési GUID** mezőbe írja be a nyomon követési GUID azonosítója. Csak a GUID nélkül adja meg a **pid -** előtag. Az a **egyéni leírása** mezőbe írja be az ajánlat neve vagy leírása.

   ![Profil oldalához](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![Adja meg a GUID Azonosítót és az ajánlat leírása](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Egynél több GUID regisztrálásához válassza **követési GUID hozzáadása** újra. További mező jelenik meg az oldalon.

   ![Válassza ki újra a követési GUID hozzáadása](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![Adjon meg egy másik GUID és ajánlat leírása](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Kattintson a **Mentés** gombra.

   ![Mentés kiválasztása](media/marketplace-publishers-guide/guid-dev-center-save.png)

Miután a GUID azonosító hozzáadása a sablonhoz, vagy a felhasználói ügynök, és regisztrálja a globálisan egyedi Azonosítót a CPP, központi telepítések nyomon követi. 

## <a name="verify-the-guid-deployment"></a>A GUID-telepítés ellenőrzése 

Után módosítja a sablont, és üzembe helyezési teszt futtatása, a következő PowerShell-parancsfájl használatával telepített, és a címkézett erőforrásokat lekérni. 

A parancsfájl segítségével győződjön meg arról, hogy a globálisan egyedi Azonosítót sikerült felvenni a Resource Manager-sablon. A parancsfájl nem vonatkozik a Resource Manager API-telepítés.

Jelentkezzen be az Azure-ba. Válassza ki az előfizetést, a parancsfájl futtatása előtt ellenőrizni szeretné a központi telepítésére. Futtassa a szkriptet az üzembe helyezés előfizetés keretén belül.

A **GUID** és **resourceGroup** nevét, a központi telepítés rendszer szükséges paramétereket.

Megjelenik a [az eredeti parancsfájl](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) a Githubon.

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Jelentés

A jelentés az ügyfél használati tesznek elérhetővé; ilyenek a Partner Center elemzése irányítópultján található. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)).

A Partner társítási típust a jelentés megtekintéséhez a legördülő listában válassza ki a sablonnal követni.

![Jelentés az ügyfél használati megnevezése](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Az ügyfelek értesítése

Partnerek kapcsolatos ügyfél használati tesznek elérhetővé; ilyenek használó központi telepítések tájékoztatnia kell az ügyfeleknek. A Microsoft ezeket az üzemelő példányokat a partnert társított Azure-használatát jelenti. A következő tartalom értesítse a felhasználókat arról a történő központi telepítését, amelyekkel például. A példákban cserélje le a \<PARTNER > a vállalat nevére. Partnerek győződjön meg arról, hogy adataikat adatvédelmi és -gyűjteményt házirendek, beleértve az ügyfelek ki lesznek zárva a követési illeszkedik az értesítést. 

### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés a Resource Manager-sablon-üzembehelyezések

Ez a sablon telepítésekor lehet azonosítani a telepítés,-e a Microsoft \<PARTNER > az Azure-erőforrások, központilag telepített szoftvereket. A Microsoft is képes az Azure-erőforrások, amelyek támogatják a szoftver összekapcsolását. A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatok gyűjtése és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Értesítési SDK vagy API-t központi telepítésekhez

Üzembe helyezésekor \<PARTNER > szoftver, a Microsoft a telepítésének azonosítani tudja \<PARTNER > az Azure-erőforrások, központilag telepített szoftvereket. A Microsoft is képes az Azure-erőforrások, amelyek támogatják a szoftver összekapcsolását. A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatok gyűjtése és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Támogatás kérése

Ha segítségre van szüksége a Marketplace-en bevezetési és/vagy az ügyfél használati tesznek elérhetővé; ilyenek, kövesse az alábbi lépéseket.

1. Nyissa meg a [támogatási oldal](https://go.microsoft.com/fwlink/?linkid=844975). 

1. Alatt **Problématípus**válassza **Marketplace bevezetési**.

1. Válassza ki a **kategória** a problémára:

   - Válassza ki a társítás kapcsolatos használati **más**.
   - Hozzáférési problémák az Azure Marketplace-en CPP, válasszon **hozzáférési probléma**.
   
    ![Válassza ki a probléma kategóriát](media/marketplace-publishers-guide/lu-article-incident.png)

1. Válassza ki **indítására vonatkozó kérés**.

1. A következő oldalon adja meg a szükséges értékeket. Válassza a **Folytatás** elemet.

1. A következő oldalon adja meg a szükséges értékeket.

   > [!Important] 
   > Az a **esemény címe** mezőbe írja be **ISV használat nyomon követése**. Ismertesse a problémát, részletesen.
   
   ![Adja meg a független Szoftverszállító használat nyomon követése az incidens címe](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Töltse ki az űrlapot, és válassza ki **küldés**.

Technikai útmutató a Microsoft partneri technikai tanácsadó műszaki vásárlás előtti, üzembe helyezéséhez és alkalmazás fejlesztési forgatókönyvek megértéséhez, és építse be az ügyfél használati tesznek elérhetővé; ilyenek juthat.

### <a name="how-to-submit-a-technical-consultation-request"></a>Hogyan műszaki konzultáció kérelem küldése

1. Látogasson el [ http://aka.ms/TechnicalJourney ](http://aka.ms/TechnicalJourney).
1. Válassza ki a felhőalapú infrastruktúra és a felügyeleti és a egy új oldalt megnyílik a technikai foglalkozás megtekintéséhez.
1. A központi telepítési szolgáltatások területen kattintson a küldési kérelem gomb
1. Jelentkezzen be az MSA-(MPN-fiókjába) vagy az aad-ben (Partner irányítópult-fiók); a bejelentkezési hitelesítő adatok alapján, ekkor megnyílik az online űrlapot: 
    * Fejezze be/tekintse át a kapcsolattartási adatokat.
    * A konzultáció adatai előfordulhat, hogy előre kitöltve, vagy a legördülő listákból válassza ki.
    * Adja meg egy címet és a probléma leírása (adja meg a lehető legtöbb részletet lehető).
1. Kattintson a Küldés gombra

Képernyőképeket, részletes útmutató megtekintéséhez [ http://aka.ms/TechConsultInstructions ](http://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Mit tartogat a jövő

Akkor lesz felveszi Önnel a kapcsolatot egy Microsoft Partner technikai tanácsadó állíthat be egy hívás hatókörének beállításához az igényeinek.

## <a name="faq"></a>GYIK

**Mi az az előnye, hogy a GUID azonosító hozzáadása a sablonhoz?**

A Microsoft partnereinek ügyfél központi telepítések elemzések és megoldások nézetet biztosít, a befolyásolt felhasználási. A Microsoft, és a partner használhatja ezt az információt meghajtó közelebb engagement értékesítői csapatok között. A Microsoft, és a partner segítségével az adatok az Azure növekedési egy egyedi partner hatás egységesebb képet kaphat. 

**Miután hozzáadott egy GUID Azonosítót, akkor módosítható?**
 
Igen, egy ügyfél vagy megvalósítási partnere előfordulhat, hogy a sablon testreszabásához, és módosíthatja vagy eltávolítja a globálisan egyedi Azonosítót. Javasoljuk, hogy a partnerek proaktív módon írja le a szerepkört az erőforrás és a GUID azonosító GUID eltávolítása és a módosítások megelőzése érdekében az ügyfelek és partnerek számára. A GUID módosítása hatással van, csak a új, nem meglévő üzemelő példányok és erőforrások.

**Sablonok üzembe helyezett egy nem Microsoft-adattárból, például a Githubból tudja követni?**

Igen, mindaddig, amíg a GUID azonosító megtalálható a sablon üzembe helyezésekor, használat nyomon van. Partnereink rendelkeznek profillal a CPP regisztrálni az Azure piactéren kívül az üzembe helyezéshez használt GUID a szükségesek. 

**Az ügyfél fogad, valamint reporting?**

Ügyfelek nyomon követheti az egyes erőforrások vagy erőforráscsoportok felhasználó által meghatározott az Azure Portalon a használatuk.   

**Ez a módszer több hasonló a digitális partnert a rekord (DPOR) az?**

Ez az új módszer az üzembe helyezés és használat kapcsolódni egy partneri megoldás lehetővé teszi a partneri megoldások összekapcsolása az Azure-használat. Tanácsadási (rendszerintegrátor) vagy (felügyelt szolgáltató) felügyeleti partnertől, az ügyfél Azure-előfizetés társítása dpor használatára alkalmas.   

**Mi az az előnye az Azure Storage GUID-generátor űrlap?**

Az Azure Storage GUID-generátor űrlap garantáltan hozzon létre egy GUID Azonosítót a kötelező formátum. Ezenkívül ha bármelyik Azure Storage-adatsík nyomon követési módszereket használ, használhatja a Marketplace-en vezérlősík követési ugyanaz a GUID. Ez lehetővé teszi lehetővé külön GUID karbantartása nélkül egy Partner tesznek elérhetővé; ilyenek egységes célalhálózathoz GUID azonosító.

**Használhatok egy saját, egyéni VHD-t egy megoldás sablon ajánlat az Azure piactéren?**

Nem. A virtuálisgép-rendszerképet kell származnak, az Azure Marketplace-ről, lásd: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines). 

A Marketplace-en az egyéni virtuális merevlemez használatával hozzon létre egy Virtuálisgép-ajánlat, és jelölje meg személyes senki nem láthatják. Ez a megoldássablon lévő virtuális géphez majd hivatkozás.

**Nem sikerült frissíteni a *contentVersion* tulajdonság a fő sablon?**

Valószínűleg hiba bizonyos esetekben, amikor a sablon üzembe helyezéséhez a TemplateLink egy másik sablon használatával, amelyek hatással vannak a régebbi contentVersion valamilyen okból. A megoldás, hogy használja a metaadat-tulajdonságot:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```