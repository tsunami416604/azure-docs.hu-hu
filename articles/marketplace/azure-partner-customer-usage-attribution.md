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
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: 604eb528ef33a95993aa5b6d3ff6eebb77936aa2
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157938"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partneri ügyfél használati megnevezése

Partnerként szoftverek az Azure a megoldások Azure-összetevőket kell, vagy közvetlenül az Azure-infrastruktúra üzembe van szükségük. Ügyfelek, akik egy partnermegoldás üzembe helyezését, és a saját Azure-erőforrások kiépítése nehéz értékes információkhoz juthat a központi telepítés állapotát, és optika kaphat a Azure növekedési gyakorolt hatást. Amikor hozzáad egy magasabb szintű láthatóság, igazodnak a Microsoft értékesítői csapatok, és a Microsoft partner programok kreditet kaphat.   

A Microsoft kínál egy metódust elősegítik a partnerek jobban ügyfél központi telepítését a szoftver az Azure használatának nyomon követése az Azure-ban. Az új módszer, amellyel a központi telepítés az Azure-szolgáltatások Azure Resource Managert használja.

A Microsoft-partnerként bármely Azure-erőforrások, amelyek az ügyfél nevében üzembe Azure-használat is társíthat. A társítás használatával az Azure piactéren, a rövid útmutató tárház, privát GitHub-adattárak és személyes customer engagement is alkotnak. Ahhoz, hogy nyomon követése, két megközelítés érhetők el:

- Az Azure Resource Manager-sablonok: Resource Manager-sablonok vagy megoldássablonok a partner szoftverek futtatását az Azure-szolgáltatások üzembe helyezéséhez. Partnerek hozhat létre egy Resource Manager-sablon infrastruktúráját és konfigurációját az Azure-megoldás meghatározásához. A Resource Manager-sablon lehetővé teszi, hogy Ön és ügyfelei teljes életciklusa során a megoldás üzembe helyezéséhez. Biztos lehet benne, hogy az erőforrások telepítése konzisztens lesz. 

- Az Azure Resource Manager API-k: Partnerek segítségével meghívhatja a Resource Manager API-k, közvetlenül a Resource Manager-sablon üzembe helyezése vagy az API-hívások közvetlen kiosztása az Azure-szolgáltatások létrehozásához. 

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata

Számos partneri megoldás telepítve vannak, egy ügyfél előfizetését Resource Manager-sablonok használatával. Ha rendelkezik egy Resource Manager-sablon, amely elérhető a Githubon, vagy a rövid útmutatóban az Azure piactéren, nagyon egyszerű a folyamat módosításához a sablonhoz, amelyekkel engedélyezheti az új követési módszert kell lennie. Ha nem használja az Azure Resource Manager-sablonok, az alábbiakban néhány hivatkozások segítségével jobban megismerheti a Resource Manager-sablonok, és hogyan hozhat létre egyet: 

*   [Létre és helyezhet üzembe az első Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Megoldássablon létrehozásához az Azure Marketplace-en](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

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
Ellenőrizze, hogy módosítani az alábbi mintakód a saját adatbevitel a fő sablonfájl hozzáadásakor.
Az erőforrás a hozzá kell adnia a **mainTemplate.json** vagy **azuredeploy.json** fájl csak, és nem az összes beágyazott vagy kapcsolódó sablonok.
```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" // use your generated GUID here
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

Amikor az API-hívások, a nyomon követési módszer a felhasználói ügynök a kérelemben szereplő tartományfejléc közé tartozik a egy GUID Azonosítót. Adja hozzá az egyes ajánlatok GUID azonosítója vagy Termékváltozat. A karakterláncot formázni a **pid -** előtagot, valamint tartalmazza a partner által létrehozott GUID. Íme egy példa a GUID azonosító formátumát szúr be a felhasználói ügynök: 

![GUID formátumban](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> A karakterlánc formátuma fontos. Ha a **pid -** előtag nem tartalmaz, ezért nem lehetséges az adatok lekérdezéséhez. Különböző SDK-k eltérően nyomon követése. Ez a metódus megvalósításához, tekintse át a támogatási, és nyomon követi a megközelítést az előnyben részesített Azure SDK-hoz készült. 

#### <a name="example-the-python-sdk"></a>Példa: A Python SDK-t

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

## <a name="create-guids"></a>Hozzon létre GUID-azonosítói

GUID 32 hexadecimális számjegyet tartalmaz egyedi hivatkozási szám. Követési GUID azonosítókat létrehozni, egy GUID-generátort kell használnia. Az Azure Storage csapata létrehozott egy [GUID generátor űrlap](https://aka.ms/StoragePartners) , amely e-mailt küld, egy GUID Azonosítót a helyes formátumú, és a különböző nyomkövetési rendszerek felhasználhatók. 

> [!Note]
> Erősen ajánlott a használata [Azure Storage GUID generátor űrlap](https://aka.ms/StoragePartners) hozhat létre a GUID Azonosítót. További információkért lásd: a [– gyakori kérdések](#faq).

Hozzon létre egy egyedi GUID azonosító minden ajánlat és a terjesztési csatornák. Ha két megoldás üzembe helyezése sablon használatával, és mindegyikhez az Azure Marketplace-en és githubon érhető el, hozzon létre négy GUID szeretné:

*   Az Azure Marketplace-en A ajánlat 
*   Ajánlat A githubon
*   B ajánlat az Azure Marketplace-en 
*   Az ajánlat B a Githubon

Jelentéskészítés végzi el a partner érték (Microsoft-Partnerazonosító) és a globálisan egyedi Azonosítót. 

GUID azonosítókat, például a Termékváltozat, ahol a termékváltozatok a következők ajánlat változatának részletesebb szinten is követheti.

## <a name="register-guids-and-offers"></a>Regisztrálja a GUID-ok és ajánlatok

Szeretne adni egy GUID Azonosítót a nyomon követés, regisztrálni kell a GUID Azonosítót.  

Az összes regisztrációs sablon GUID-n keresztül az Azure Marketplace-en Cloud Partner Portal (CPP) kell elvégezni. 

Miután a GUID azonosító hozzáadása a sablonhoz, vagy a felhasználói ügynök, és regisztrálja a globálisan egyedi Azonosítót a CPP, központi telepítések nyomon követi. 

1. A alkalmazni [Azure Marketplace-en](http://aka.ms/listonazuremarketplace) és a CPP eléréséhez.

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

## <a name="notify-your-customers"></a>Az ügyfelek értesítése

Partnerek kapcsolatos követési Resource Manager GUID használó központi telepítések tájékoztatnia kell az ügyfeleknek. A Microsoft ezeket az üzemelő példányokat a partnert társított Azure-használatát jelenti. A következő tartalom értesítse a felhasználókat arról a történő központi telepítését, amelyekkel például. A példákban cserélje le a \<PARTNER > a vállalat nevére. Partnerek győződjön meg arról, hogy adataikat adatvédelmi és -gyűjteményt házirendek, beleértve az ügyfelek ki lesznek zárva a követési illeszkedik az értesítést. 

### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés a Resource Manager-sablon-üzembehelyezések

Ez a sablon telepítésekor lehet azonosítani a telepítés,-e a Microsoft \<PARTNER > az Azure-erőforrások, központilag telepített szoftvereket. A Microsoft is képes az Azure-erőforrások, amelyek támogatják a szoftver összekapcsolását. A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatok gyűjtése és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Értesítési SDK vagy API-t központi telepítésekhez

Üzembe helyezésekor \<PARTNER > szoftver, a Microsoft a telepítésének azonosítani tudja \<PARTNER > az Azure-erőforrások, központilag telepített szoftvereket. A Microsoft is képes az Azure-erőforrások, amelyek támogatják a szoftver összekapcsolását. A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatok gyűjtése és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Támogatás kérése

Ha segítségre van szüksége, kövesse az alábbi lépéseket.

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

## <a name="faq"></a>GYIK

**Mi az az előnye, hogy a GUID azonosító hozzáadása a sablonhoz?**

A Microsoft partnereinek ügyfél központi telepítései insights, valamint a sablonok nézetet biztosít, a befolyásolt felhasználási. A Microsoft, és a partner használhatja ezt az információt meghajtó közelebb engagement értékesítői csapatok között. A Microsoft, és a partner segítségével az adatok az Azure növekedési egy egyedi partner hatás egységesebb képet kaphat. 

**Akik GUID adhat hozzá egy sablont?**

A nyomon követési erőforrás célja az ügyfél Azure-használat a partnermegoldás csatlakozni. A használati adatok egy partner Microsoft Partner Network-azonosító (MPN-azonosító) van kötve. Jelentéskészítés a CPP partnerei számára érhető el.

**Miután hozzáadott egy GUID Azonosítót, akkor módosítható?**
 
Igen, egy ügyfél vagy megvalósítási partnere előfordulhat, hogy a sablon testreszabásához, és módosíthatja vagy eltávolítja a globálisan egyedi Azonosítót. Javasoljuk, hogy a partnerek proaktív módon írja le a szerepkört az erőforrás és GUID GUID követési eltávolítása és a módosítások megelőzése érdekében az ügyfelek és partnerek számára. A GUID módosítása hatással van, csak a új, nem meglévő üzemelő példányok és erőforrások.

**Amikor jelentéskészítési rendelkezésre áll?**

Jelentéskészítés béta verzióját kell hamarosan közzétesszük. Jelentéskészítés integráljuk a CPP.

**Sablonok üzembe helyezett egy nem Microsoft-adattárból, például a Githubból tudja követni?**

Igen, mindaddig, amíg a GUID azonosító megtalálható a sablon üzembe helyezésekor, használat nyomon van. Partnereink rendelkeznek profillal a kapcsolódó sablonok az Azure piactéren kívül közzétett regisztrálni a CPP van szükség. 

**Van különbség a Ha a sablon üzembe Azure Marketplace-ről és más tárházakban, amilyen a GitHub?**

Igen, partnereknek, akik ajánlatok közzététele az Azure Marketplace-en kaphat részletes adatok üzemelő példányok esetében az Azure Marketplace-ről. Partnerek előnyt is közzéteheti az ajánlatot az ügyfelek számára az Azure Marketplace-en portál és az Azure Portalon. Az Azure piactér lehetővé teszi az érdeklődők is létrehozhat a partner.

**Mi történik, ha egy egyéni sablont, és egy egyéni ügyféligények engagement hozhatok létre?**

Ön továbbra is üdvözli a GUID azonosító hozzáadása a sablonhoz. Ha egy meglévő regisztrált GUID-Azonosítót használ, a a jelentés tartalmazni fogja. Létrehoz egy új GUID Azonosítót, ha szeretne regisztrálni, hogy a követési tartalmazza az új GUID.

**Az ügyfél fogad, valamint reporting?**

Ügyfelek nyomon követheti az egyes erőforrások vagy erőforráscsoportok felhasználó által meghatározott az Azure Portalon a használatuk.   

**Ez a módszer követési több hasonló a digitális partnert a rekord (DPOR) az?**

Ez az új módszer az üzembe helyezés és használat kapcsolódni egy partneri megoldás lehetővé teszi a partneri megoldások összekapcsolása az Azure-használat. Tanácsadási (rendszerintegrátor) vagy (felügyelt szolgáltató) felügyeleti partnertől, az ügyfél Azure-előfizetés társítása dpor használatára alkalmas.   

**Mi az az előnye az Azure Storage GUID-generátor űrlap?**

Az Azure Storage GUID-generátor űrlap garantáltan hozzon létre egy GUID Azonosítót a kötelező formátum. Ezenkívül ha bármelyik Azure Storage-adatsík nyomon követési módszereket használ, használhatja a Marketplace-en vezérlősík követési ugyanaz a GUID. Ez lehetővé teszi lehetővé külön GUID karbantartása nélkül egy Partner tesznek elérhetővé; ilyenek egységes célalhálózathoz GUID azonosító.
