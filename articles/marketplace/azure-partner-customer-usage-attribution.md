---
title: Az Azure partner- és ügyfél használati megnevezése
description: Nyomon követheti az ügyfelek általi használatot, az Azure piactér-megoldások áttekintése
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: ce862758d97737d16ef26ca7172cad39f8d8336a
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359961"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partneri ügyfél használati megnevezése

Partnerként szoftverek az Azure-hoz, a megoldások vagy szükséges Azure-összetevőket, vagy közvetlenül az Azure-infrastruktúra üzembe helyezni.  Amikor egy ügyfél üzembe helyez egy partneri megoldás, és kiosztja a saját Azure-erőforrások, jelenleg a partner számára ezen központi telepítések állapotát a bonyolult és nehezen optika kaphat Azure fejlődés korlátja lesz hatással. Magasabb szintű láthatóság hozzáadása segíti a partnerek igazodnak a Microsoft értékesítői csapatok és a Microsoft partner programok kreditet kaphat.   

A Microsoft hoz létre egy új módszer köszönhetően elősegítik a partnerek jobban nyomon követheti az Azure-használat eredménye egy ügyfél az Azure-szoftverek üzembe helyezésére. Ez az új módszer alapján az Azure Resource Managerrel történő üzembe helyezés az Azure-szolgáltatások.

A Microsoft-partnerként társíthatja az Azure használati bármely Azure-erőforrások üzembe helyezésekor az ügyfél nevében.  Ezt a társítást az Azure piactéren, a rövid útmutató adattárat, privát github-adattárak és 1 customer engagement 1 még akkor is, megteheti.  Ahhoz, hogy nyomon követése, két módszer érhető el:

- Az Azure Resource Manager-sablonok: Az Azure Resource Manager-sablonok vagy megoldássablonok a partner szoftverek futtatását az Azure-szolgáltatások üzembe helyezéséhez. Partnerek hozhat létre, amely meghatározza az infrastruktúra és az Azure-megoldás konfigurálása Azure Resource Manager-sablon. Az Azure Resource Manager-sablon létrehozása lehetővé teszi, hogy Ön és ügyfelei üzembe helyezheti megoldását teljes életciklusa során, és biztos lehet benne, az erőforrások telepítése konzisztens lesz. 

- Az Azure Resource Manager API-k: partnerek meghívhatja az Azure Resource Manager API-k közvetlenül, vagy üzembe helyezése az Azure Resource Manager-sablon vagy létrehozni az API-hívások közvetlenül üzembe helyezése Azure-szolgáltatások. 

## <a name="method-1-azure-resource-manager-templates"></a>1. módszer: Az Azure Resource Manager-sablonok 

Ma számos partneri megoldás telepítve vannak egy ügyfél-előfizetés Azure Resource Manager-sablonok használatával.  Ha már rendelkezik egy Azure Resource Manager-sablon elérhető az Azure piactéren, a GitHub vagy a rövid útmutatóban a folyamat az új követési módszer engedélyezéséhez a sablon módosításával nagyon egyszerű kell lennie.  Ha az Azure Resource Manager-sablon jelenleg nem használ, az alábbiakban néhány hivatkozások segítenek jobban megismerheti az Azure Resource Manager-sablonok és a egy létrehozása: 

*   [Létre és helyezhet üzembe az első Azure Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Útmutató a megoldássablon létrehozásához az Azure Marketplace-en](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Utasítások: egy GUID Azonosítót a meglévő Azure Resource Manager-sablon hozzáadása

A GUID azonosító hozzáadása a fő sablonfájl egyetlen módosítását a következő:
 1. Hozzon létre egy GUID Azonosítót, tegyük fel, hogy a létrehozott értéket eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Nyissa meg az Azure Resource Manager-sablon
 3. Adjon hozzá egy új erőforrást a fő sablonfájlban. Az erőforrás csak kell lennie a mainTemplate.json vagy azuredeploy.json, nem az összes beágyazott vagy kapcsolódó sablonok.
 4. Adja meg a GUID után a "pid-" jelennek meg.

   Valami példához hasonlóan kell kinéznie: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Ellenőrizze a hibákat a sablont
 6. Tegye közzé újra a sablont a megfelelő tárházban

## <a name="sample-template-code"></a>Sablon mintakód

```

{ // add this resource to the mainTemplate.json (do not add the entire file)
      "apiVersion": "2018-02-01",
      "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your GUID here
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": []
        }
      }
    } // remove all comments from the file when done

```

## <a name="method-2-azure-resource-manager-apis"></a>2. módszer: Az Azure Resource Manager API-k

Bizonyos esetekben előfordulhat, hogy szeretne hívásokat indítani közvetlenül az Azure Resource Manager REST API-k üzembe helyezéséhez az Azure-szolgáltatások. [Az Azure támogatja több SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) ennek engedélyezéséhez.  Az SDK-k valamelyikével, vagy közvetlenül az erőforrások üzembe helyezése a REST API-kat.

Ha egy Azure Resource Manager-sablont használ, a megoldás a fenti utasítások kell címkéznie.  Ha nem egy Azure Resource Manager-sablonnal, és közvetlen API-hívások továbbra is megjelölheti a központi telepítés rendelni az Azure-erőforrások használatát. 

**Az Azure Resource Manager API-kat használó telepítés címkézése:** az ebben a megközelítésben az API-hívások, egy GUID Azonosítót tartalmazza a felhasználói ügynök a kérelemben szereplő tartományfejléc tervezésekor. A globálisan egyedi Azonosítót minden ajánlat vagy Termékváltozat hozzá kell adni.  A karakterlánc a előtaggal kell formázni kell pid - majd illessze be a partner létrehozott GUID.   

>[!Note] 
>GUID formátumú szúr be a felhasználói ügynök: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 / / a GUID megadása után a "pid-"

A karakterlánc formátuma fontos. Az előtag "pid-" lehetőség nem része, ha nem lehetséges az adatok lekérdezéséhez. Különböző SDK-k ehhez eltérően.  Ez a metódus megvalósításához, szüksége lesz tekintse át a támogatási és megközelítés az előnyben részesített Azure SDK-hoz készült. 

**A példában a Python SDK-val:** a Python, kell használnia a "konfiguráció" attribútum. Csak akkor adhat hozzá egy UserAgent. Például:

```python

client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
        client.config.add_user_agent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

>Ez az egyes ügyfelek elvégezni, akkor nem tartozik globális statikus konfiguráció (Előfordulhat, hogy választja egy ügyfél-előállítót, és ellenőrizze, hogy minden ügyfél állapotát. 
>[További információk](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Az Azure PowerShell vagy az Azure CLI használatával központi telepítés címkézése: AzurePowerShell erőforrásai telepít, a GUID hozzáfűzheti a következő módon:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Fűzze hozzá a GUID Azonosítót, ha az Azure CLI használatával, állítsa a AZURE_HTTP_USER_AGENT környezeti változót.  Egy parancsfájl vagy globálisan, shell hatókör használatra a hatókörön belüli ezzel a változóval adhatja meg:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>GUID-ok/ajánlatok regisztrálása

Ahhoz, hogy a globálisan egyedi Azonosítót a nyomon követési szerepeltetni regisztrálnia kell azt.  

Az összes regisztrációs sablon GUID-n keresztül az Azure Marketplace-en Cloud Partner Portal (CPP) történik. 

A alkalmazni [Azure Marketplace-en](http://aka.ms/listonazuremarketplace) ma és a get-hozzáférés a Cloud Partner portálra.

*   Partnerek lesz szükség [rendelkeznek profillal a CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) és javasolt, hogy a listában az ajánlat az Azure Marketplace-en vagy az appsource-ban 
*   Partnerek tudják regisztrálni az több GUID-azonosítói 
*   Partnerek is tudnak regisztrálni egy GUID Azonosítót a-Marketplace megoldás sablonok/ajánlatok

Miután hozzáadta a GUID a sablonhoz, vagy a felhasználói ügynök, és a globálisan egyedi Azonosítót a központi telepítések követi CPP regisztrálva. 

## <a name="verification-of-guid-deployment"></a>A GUID-telepítés ellenőrzése 

Miután módosította a sablont, és egy tesztelési üzembe helyezés végrehajtott, használhatja a következő PowerShell-parancsfájlt az üzembe helyezett és címkézett erőforrásokat lekérni. 

Ellenőrizze, hogy ha a GUID már hozzáadtuk az Azure Resource Manager-sablon sikeresen használható. Nem vonatkozik Azure Resource Manager API-telepítéshez.

Jelentkezzen be az Azure-ba, és válassza ki az előfizetést, amely tartalmazza az üzembe helyezés, a parancsfájl futtatása előtt ellenőrizni szeretné. Az üzembe helyezés előfizetés környezetében kell futtatni.

Az üzembe helyezés GUID Azonosítót és az erőforráscsoport nevét is kötelező paraméterei.

Az eredeti parancsfájlt annak [Itt](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Útmutatás a GUID azonosítók létrehozása

Egy globálisan egyedi azonosító (GUID) egy olyan 32 hexadecimális számjegy egyedi hivatkozási szám. Szeretne létrehozni egy GUID Azonosítót, egy GUID-generátort hozhat létre saját követési GUID kell használnia.  Egyszerre több [online GUID generátorok](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) is használhatja.

Javasoljuk, hogy hozzon létre egy egyedi GUID azonosító minden ajánlat és a terjesztési csatornák áll.  Például ha két megoldást, és mindkét sablon segítségével telepítve vannak és az Azure piactéren, és a Githubon érhető el.  Hozzon létre négy GUID-AZONOSÍTÓI:

*   Az Azure Marketplace-en A ajánlat 
*   Ajánlat A githubon
*   B ajánlat az Azure Marketplace-en 
*   Az ajánlat B a Githubon

Jelentéskészítés történik (Microsoft-Partnerazonosító) partner és a GUID Azonosítót. 

Azt is beállíthatja GUID azonosítókat, azaz Termékváltozat (ahol termékváltozatok a következők ajánlat változatának) még finomabban szabályozható szinten nyomon követéséhez.

## <a name="guidance-on-privacy-and-data-collection"></a>Útmutatás az adatvédelem és az adatok gyűjtése

Partnerek üzenetkezelés, amely az Azure Resource Manager GUID követési tartalmazó üzemelő példányok lehetővé teszi a Microsoft ezen központi telepítések számára a partner társított Azure-használati jelentésekhez ügyfelek tájékoztatnia kell biztosítania.  Néhány példa nyelv nem éri el. Amennyiben azt jelzi, hogy "PARTNER", akkor töltse ki a saját cég nevét. Emellett partnereink biztosítania kell a nyelvi igazodik a saját adataik adatvédelmi és -gyűjteményt házirendek, beleértve az ügyfelek ki lesznek zárva a nyomon követése: 

**Az Azure Resource Manager-sablon-üzembehelyezések**

Ez a sablon üzembe helyezésekor, a Microsoft fogja tudni azonosíthatja a PARTNER szoftver telepítése az Azure-erőforrások üzembe helyezve.  A Microsoft tudnia kell korrelálni a szoftvert támogató Azure-erőforrások lesz.  A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatokat, és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter. 

**Az SDK vagy API-t központi telepítéséhez**

Ha szoftverek központi telepítése PARTNER, a Microsoft képes lesz azonosítani a PARTNER szoftver telepítése az üzembe helyezett Azure-erőforrások.  A Microsoft tudnia kell korrelálni a szoftvert támogató Azure-erőforrások lesz.  A Microsoft ezeket az információkat a lehető legjobb élményt biztosíthat a termékeiket és üzleti működésre gyűjt. Az adatokat, és található a Microsoft adatvédelmi irányelvei szabályozzák https://www.microsoft.com/trustcenter.

## <a name="support"></a>Támogatás

Segítségnyújtás, kövesse az alábbi lépéseket:
 1. Látogasson el a támogatási oldalon található [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Használati társítása – problémáinak válassza a Problem type: **Marketplace bevezetési** és kategória: **más** majd **kérelem indítása.** 
>[!Note]
>Problémák az Azure Marketplace-en Cloud Partner Portalon – válassza a Problem type elérése: **Marketplace bevezetési** és kategória: **hozzáférési probléma** majd **kérelem indítása.**
 3. Töltse ki a kötelező mezőket a következő oldalon, és kattintson a **tovább.**
 4. Fejezze be a következő oldalon a szabad szöveges mezők.  
 

 
>[!Important] 
>Adja meg az esemény címe **"ISV használat követése"** és ismertesse a problémát a nagy méretű szabad szöveges mező után részletesen ismertetjük.  Fejezze be az űrlapot, és kattintson a **küldés**.

## <a name="faqs"></a>Gyakori kérdések

**Mi az az előnye, hogy a GUID azonosító hozzáadása a sablonhoz?**

A Microsoft partnerek ügyfél központi telepítései insights, valamint a sablonok nézetet biztosít a befolyásolt felhasználási.  A Microsoft, és a partner is használhatja ezt az információt meghajtó közelebb engagement értékesítői csapatok között. A Microsoft, és a partner segítségével is, az Azure növekedési egy egyedi partner hatás egységesebb képet kaphat. 

**Akik GUID adhat hozzá egy sablont?**

A nyomon követési erőforrás az célja, hogy a partner megoldás csatlakoztatása az ügyfeleket az Azure használati.  A használati adatok egy partner Microsoft Partner Network-azonosító (MPN-azonosító) van kötve, és jelentéskészítés lesz elérhető, a partnerek a Cloud Partner Portal (CPP).  

**Miután az hozzá lett adva egy GUID Azonosítót, módosítható?**
 
Igen, egy ügyfél vagy megvalósítási partnere előfordulhat, hogy a sablon testreszabásához és sikerült módosítása vagy eltávolítása a GUID Azonosítót. Javasoljuk, hogy a partnerek proaktív módon írja le a szerepkört az erőforrás és GUID GUID követési eltávolítása és a módosítások megelőzése érdekében az ügyfelek és partnerek számára.  A GUID módosítása csak érinti új, nem meglévő üzemelő példányok és erőforrások.

**Amikor jelentéskészítési rendelkezésre áll?**

Jelentéskészítés béta verzióját kell hamarosan közzétesszük.  Jelentéskészítés integrálva lesz az a Cloud Partner Portal (CPP).

**Sablonok üzembe helyezett egy nem Microsoft-adattárból, például a Githubból tudja követni?**

Igen, mindaddig, amíg a GUID azonosító megtalálható a sablon üzembe helyezésekor, használati követi.  
Partnereink rendelkeznek profillal a Cloud Partner Portalon közzé az Azure piactéren kívül a kapcsolódó sablonok regisztrálásához szükséges. 

**Van különbség a Ha a sablon üzembe Azure Marketplace-ről és más tárházakban, amilyen a GitHub?**

Igen, partnereknek, akik ajánlatok közzététele az Azure Marketplace-en, részletes adatok üzemelő példányok esetében az Azure Marketplace-ről kaphat.  A partnerek megnövelt is közzéteheti az ajánlatot az ügyfelek számára az Azure Marketplace-en portál és az Azure felügyeleti portálján. Az Azure piactér lehetővé teszi az érdeklődők is létrehozhat a partner.

**Mi történik, ha egy egyéni sablont, és egy egyéni ügyféligények engagement hozhatok létre?**

Ön továbbra is üdvözli a GUID azonosító hozzáadása a sablonhoz.  Egy létező GUID-Azonosítót, amely regisztrálva lett használatakor, szerepelni fog a jelentéskészítés.  Létrehoz egy új GUID Azonosítót, ha letöltés követési tartalmazza az új GUID regisztrálni kell.

**Az ügyfél fogad, valamint reporting?**

Ügyfelek képesek jelenleg az egyes erőforrások vagy felhasználó által definiált erőforrás-csoportok az Azure felügyeleti portálján használatának nyomon követése.   

**Ez a módszer követési több hasonló a digitális partnert a rekord (DPOR) az?**

Ez az új módszer az üzembe helyezés és használat kapcsolódni egy partneri megoldás célja egy olyan mechanizmust, egy partneri megoldás összekapcsolása az Azure-használatát.  Tanácsadási (rendszerintegrátor) vagy (felügyelt szolgáltató) felügyeleti partnertől, az ügyfél Azure-előfizetés társítása dpor használatára alkalmas.   
