---
title: Az Azure DevTest Labs – gyakori kérdések |} A Microsoft Docs
description: Válaszok az Azure DevTest Labs kapcsolatos gyakori kérdésekre.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: d8fc929b21bedcb3e7e2bd3f5ed1d6c867bca3c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58803374"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs GYIK
Az Azure DevTest Labs kapcsolatos leggyakoribb kérdésekre adott válaszok.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Általános**

## <a name="blog-post"></a>Blogbejegyzés
A DevTest Labs-csapat blogja kezdődően 2019. március 20. így visszavontuk. 

### <a name="where-can-i-track-feature-updates-going-forward"></a>Amennyiben tudja követni a későbbiekben funkciófrissítések?
Módosítástól, azt fogja kell üzenetküldés funkciófrissítések és/vagy informatív blogbejegyzések az Azure-blogban, és az Azure frissíti. Ezek a blogbejegyzések is összekapcsolja a dokumentáció tartalmaz, bárhol is szükséges.

Fizessen elő a [DevTest Labs Azure blogon](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) és [frissíti az Azure DevTest Labs](https://azure.microsoft.com/updates/?product=devtest-lab) licenchasználati DevTest Labs szolgáltatásban létrehozott új funkciókról.

### <a name="what-happens-to-the-existing-blog-posts"></a>Mi történik, a meglévő blogbejegyzések?
Jelenleg folyamatban van (kivéve a szolgáltatáskimaradás frissítések) áttelepítése meglévő blogbejegyzések a [DevTest Labs-dokumentáció](devtest-lab-overview.md). Ha az MSDN-blog elavult, azt a rendszer átirányítja a dokumentáció – áttekintés a DevTest Labs szolgáltatásban. Miután átirányítva, kereshet a cikk a "Szűrő által" cím keres. Vegye figyelembe, hogy még nincs áttelepítve minden bejegyzés, de a hónap végén kell elvégezni. 


### <a name="where-do-i-see-outage-updates"></a>Hol láthatók szolgáltatáskimaradás frissítések?
Fogja azt könyvelési szolgáltatáskimaradás frissítések használatával továbbítja a Twitter-leírót. Kövessen minket a Twitteren a legújabb frissítéseket a leállásokat és az ismert hibák.

### <a name="twitter"></a>Twitter 
A Twitter-leírót: [@azlabservices](https://twitter.com/azlabservices)

## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha kérdésem itt nem választ?
A kérdés nem szerepel itt, tudassa velünk, és segítünk választ találjanak.

* Ez a GYIK végén kérdését. Az Azure Cache csapata és a Közösség többi tagjával, ez a cikk kapcsolatos érhet el.
* Szélesebb elérni, új kérdést tenne fel a a [Azure DevTest Labs MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Az Azure DevTest Labs-csapat és a Közösség más tagjai érhet el.
* Szolgáltatással kapcsolatos kéréseit, küldje el a kérelmek és ötlet segíthet megtenni [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Miért érdemes használni az Azure DevTest Labs?
Az Azure DevTest Labs a csapat időt és pénzt takaríthat meg. A fejlesztők több különböző adatbázisok használatával hozhat létre saját környezeteket. Is használhatják összetevők a gyors üzembe helyezése, és konfigurálhatja az alkalmazásokat. Egyéni rendszerképek és képletek használatával sablonként menteni a virtuális gépek (VM), és könnyedén között a csapat reprodukálnia. DevTest Labs is kínál számos konfigurálható szabályzatok a labor rendszergazdák segítségével csökkentheti a veszteséget és a egy csapat-környezetek felügyeletét. Ezek a házirendek automatikus leállítási, költség küszöbértéket, felhasználónként, és maximális Virtuálisgép-méret maximális virtuális gépek közé tartozik. Részletesebb magyarázatra van szüksége a DevTest Labs szolgáltatásban, lásd: a [áttekintése](devtest-lab-overview.md) vagy a [bevezető videót](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Mit jelent "megbízható önkiszolgáló"?
Önkiszolgáló megbízható, az azt jelenti, hogy a fejlesztők és tesztelők hozzon létre saját környezeteket igény szerint. Biztonsága érdekében, hogy DevTest Labs csökkentheti a veszteséget és a vezérlési költségeket, hogy a rendszergazdák rendelkeznek. A rendszergazdák is adja meg, melyik Virtuálisgép-méretek engedélyezett, a virtuális gépek maximális számát, és ha virtuális gép elindult, és állítsa le. DevTest Labs is megkönnyíti a költségek figyelése, és segítenek, hogy vegye figyelembe, hogy a labor-erőforrásokat vannak használatban, a riasztásokat állíthat be.

## <a name="how-can-i-use-devtest-labs"></a>Hogyan használható a DevTest Labs?
DevTest Labs akkor hasznos, bármikor szükséges fejlesztési vagy tesztelési környezetek, és azokat gyorsan reprodukálhatja vagy költségtakarékos házirendek segítségével kezelheti azokat.

Az alábbiakban néhány olyan forgatókönyvet használó ügyfeleink számára a DevTest Labs szolgáltatásban:

* Kezelheti a fejlesztési és tesztelési környezetek egy helyen. Házirendek használata csökkentheti a költségeket, és hozzon létre egyéni rendszerképek megosztani a csapat közötti épít fel.
* Egy alkalmazás fejlesztése egyéni rendszerképek használatával való mentése során a fejlesztési szakaszban a lemez állapotát.
* Nyomon követheti a folyamat viszonyítva költség.
* Minőségi assurance tesztelési tömeges tesztelési környezeteket hozhat létre.
* Összetevők és a képletek használatával egyszerűen konfigurálhatja, és Reprodukálja egy alkalmazás különböző környezetekben.
* Virtuális gépek elosztása a ötletbörzékhez (együttműködési fejlesztési és tesztelési munka), és ezután könnyedén megszüntetése őket, amikor véget ért az esemény.

## <a name="how-am-i-billed-for-devtest-labs"></a>Hogyan történik a számlázás a DevTest Labs?
DevTest Labs szolgáltatás ingyenes. Tesztkörnyezetek létrehozása és konfigurálása a házirendek, sablonok és összetevők a DevTest Labs szolgáltatásban díjmentes. Csak az Azure-erőforrások használják a Labs-környezetben, például a virtuális gépek, tárfiókok és virtuális hálózatok fizet. A labor-erőforrásokat kapcsolatos további információkért lásd: [Azure DevTest Labs díjszabás](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Biztonság**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Mik azok a különböző biztonsági szintek a DevTest Labs?
Határozza meg biztonsági hozzáférési [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/built-in-roles.md). Ha szeretné megtudni, hogyan működik a hozzáférés, segít a engedélyt, a szerepkör és egy hatókör közötti különbségekről további RBAC szerint.

* **Engedély**: Engedély egy meghatározott hozzáférést egy adott művelet. Például egy engedély az olvasási hozzáférést minden virtuális gép lehet.
* **Szerepkör**: A szerepkör az vannak csoportosítva, és a felhasználóhoz rendelt engedélyek egy készletét. Ha például egy előfizetés-tulajdonosi szerepkörrel rendelkező felhasználó hozzáfér egy előfizetésen belüli összes erőforrás.
* **Hatókör**: A hatókör egy Azure-erőforrás a hierarchiában egy szint. A hatókör lehet például egy erőforráscsoport, egy egyetlen labor vagy a teljes előfizetés.

DevTest Labs hatókörén belül van, amelyek meghatározzák a felhasználói engedélyek szerepkörök két típusa:

* **Labor tulajdonosa**: A lab tulajdonosa erőforrásokhoz való teljes hozzáférés a tesztkörnyezetben. Egy tesztlabor tulajdonosa is szabályzatok módosításához, olvasni és írni az olyan virtuális gépek, módosítsa a virtuális hálózat, és így tovább.
* **Lab-felhasználó**: A lab felhasználói megtekinthetik minden labor-erőforrásokat, például a virtuális gépek, a házirendek és a virtuális hálózatok. De a lab-felhasználó nem módosítható, szabályzatok, vagy bármely más felhasználók által létrehozott virtuális gépek. 

Egyéni szerepkörök a DevTest Labs szolgáltatásban hozhat létre. Egyéni szerepkörök létrehozása a DevTest Labs szolgáltatásban létrehozott kapcsolatban lásd: [felhasználó engedélyeket adott laborszabályzatok](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel hatókörök hierarchikus, amikor a felhasználók számára egy bizonyos hatókörben, a felhasználó számára automatikusan biztosított ezeket az engedélyeket, minden egyes alacsonyabb szintű hatókörhöz. a hatókör. Például ha egy felhasználó az előfizetés tulajdonosa a szerepkör van hozzárendelve, a felhasználónak hozzáférése van az összes erőforrást az előfizetéshez. Ilyen erőforrások többek között az összes virtuális gép összes virtuális hálózatot és az összes labs. Előfizetés tulajdonosaként automatikusan örökli a labor tulajdonosi szerepkörhöz. Azonban nem igaz ennek az ellenkezője. A lab tulajdonosa hozzáférés a laborokhoz, amely kisebb, mint az előfizetés szintjén hatókör. Ezért a labor tulajdonosa nem látható, virtuális gépek, virtuális hálózatok vagy bármely más erőforrások, amelyek túlmutatnak a labor létrehozása.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Hogyan hozhatok létre egy szerepkört, hogy a felhasználók egy adott feladat végrehajtásához?
Egyéni szerepkörök létrehozása és az engedélyek hozzárendelése szerepkörhöz kapcsolatos átfogó című cikket, lásd: [felhasználó engedélyeket adott laborszabályzatok](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Íme egy példa a parancsfájl, amely létrehozza a szerepkör *DevTest Labs-speciális felhasználó*, amely jogosult arra, hogy a labor virtuális gépeinek kezdődik és:

    $policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  


**CI/CD-integráció és automatizálás**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs a CI/CD eszközlánc integrálása?
Ha az Azure DevOps használ, használhatja a [DevTest Labs-feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) a DevTest Labs szolgáltatásban létrehozott kibocsátási folyamatok automatizálásához. Ez a bővítmény elvégezhető feladatok a következők:

* Hozzon létre, és automatikusan üzembe helyezése egy virtuális Gépet. Is konfigurálhatja a virtuális Gépet a legújabb buildben az Azure File Copy vagy a PowerShell az Azure DevOps-szolgáltatásokkal feladatok használatával.
* Tesztelés reprodukálnia a hibát ugyanarról a virtuális gépről további vizsgálat után automatikusan rögzítheti a virtuális gép állapotát.
* Ha már nincs rá szükség, törölje a kibocsátási folyamat végén a virtuális gép.

Az alábbi blogbejegyzések ajánlat útmutató és az Azure DevOps-szolgáltatásokkal bővítmény használatával kapcsolatos információk:

* [DevTest Labs és az Azure DevOps-bővítmény](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Az Azure DevOps-szolgáltatások egy meglévő DevTest Labs labor egy új virtuális gép üzembe helyezése](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Az Azure DevOps Services kiadáskezelés használatával DevTest Labs folyamatos központi telepítésére](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Egyéb folyamatos integrációs (CI) / folyamatos készregyártás (CD) fordíthatók, akkor érhető el, ugyanezeket a forgatókönyveket a telepítése [Azure Resource Manager-sablonok](https://aka.ms/dtlquickstarttemplate) használatával [Azure PowerShell-parancsmagok](../azure-resource-manager/resource-group-template-deploy.md) és [.NET SDK-k](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Használhatja még [REST API-k a DevTest Labs](https://aka.ms/dtlrestapis) az eszközlánc integrálását.  


**Virtuális gépek**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Miért nem látom virtuális gépeket a Virtual Machines lapon láthatók a DevTest Labs?
DevTest Labs szolgáltatásban létrehozott virtuális gép létrehozásakor, a virtuális gép hozzáférési engedélyt kapnak. Megtekintheti a virtuális gép, mind a labs lapon, majd a a **virtuális gépek** lapot. A DevTest Labs labor felhasználói szerepkörhöz tartozó felhasználók tekintheti meg a labor létrehozása a lab-ben létrehozott összes virtuális gép **összes virtuális gép** lapot. Azonban a DevTest Labs labor felhasználói szerepkörrel rendelkező felhasználók vannak nem automatikusan kapott olvasási hozzáférést az, hogy más felhasználók létrehozott virtuális gép erőforrásait. Ezért ezeken a virtuális gépeken nem jelennek meg a **virtuális gépek** lapot.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Mi a különbség egy egyéni rendszerkép és a egy képlet?
Egyéni rendszerkép egy virtuális merevlemezt (VHD). A képlet egy képet további beállításokat is konfigurálhatja, majd mentse és Reprodukálja. Egyéni rendszerkép Ha több környezetek gyors létrehozását az ugyanazon alapvető, nem módosítható a lemezkép használatával szeretné használata előnyösebb lehet. Előfordulhat, hogy egy képlettel jobb, ha azt szeretné, a legújabb elemeket és a virtuális gép konfigurációjának állítja elő a virtuális hálózat vagy alhálózat részeként, vagy egy meghatározott méretű virtuális gépként. Részletesebb magyarázatra van szüksége, lásd: [egyéni rendszerképek és képletek DevTest Labs szolgáltatásban létrehozott](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hogyan hozhatok létre több virtuális gép ugyanazt a sablont a egyszerre?
Egyszerre több virtuális gép ugyanazt a sablont hoz létre a két lehetősége van:
* Használhatja a [Azure fejlesztési és üzemeltetési feladatokat bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Is [Resource Manager-sablon létrehozása](devtest-lab-add-vm.md#save-azure-resource-manager-template) egy virtuális gép létrehozása közben, és [üzembe helyezése a Resource Manager-sablon a Windows Powershellből](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hogyan helyezhetek át meglévő Azure virtuális gépek a DevTest Labs labor be?
A meglévő virtuális gépek DevTest Labs másolása:

1. A meglévő virtuális gép VHD-fájl másolása egy PowerShell-parancsprogram használatával:
   * Erőforrás-kezelő: [CopyRmVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyRmVHDFromVMToLab.ps1)
   * Klasszikus: [CopyClassicVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyClassicVHDFromVMToLab.ps1)
2. [Az egyéni lemezkép készítése](devtest-lab-create-template.md) a DevTest Labs labor belül.
3. Hozzon létre egy virtuális Gépet a labor létrehozása az egyéni rendszerképpel.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Tudok több lemez is csatlakoztatható a virtuális gépek?
Igen, több lemez is csatlakoztatható a virtuális gépekhez.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Ha szeretne egy Windows operációs rendszer lemezképe használjam tesztelésre, kell vásárolni az MSDN-előfizetéssel?
A fejlesztés vagy a tesztelés az Azure-ban Windows ügyfél operációsrendszer-lemezképek (Windows 7 vagy újabb verzió) használatához tegye a következők egyikét:

- [Az MSDN-előfizetés vásárlása](https://www.visualstudio.com/products/how-to-buy-vs).
- Ha nagyvállalati szerződéssel rendelkezik, hozzon létre egy Azure-előfizetést, a [Enterprise Dev/Test ajánlat](https://azure.microsoft.com/offers/ms-azr-0148p).

Az Azure-kreditek minden MSDN-ajánlat a kapcsolatos további információkért lásd: [havi Azure-kredit a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hogyan automatizálható az egyéni lemezképek létrehozása VHD-fájlok feltöltése a folyamat?
Automatizálás létrehozása egyéni rendszerképek VHD fájlokat tölthet fel, két lehetősége van:

* Használat [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) másolása vagy VHD-fájlok feltöltése a storage-fiók a tesztkörnyezetben társított.
* Használat [az Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer egy önálló alkalmazás, amely Windows, OS X és Linux rendszereken.   

A cél tárfiók a labor társított megkeresése:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. A bal oldali menüben válassza ki a **erőforráscsoportok**.
3. Keresse meg és válassza ki az erőforráscsoportot, amely a labor van társítva.
4. A **áttekintése**, válasszon ki egy tárfiókot.
5. Válassza ki a **Blobok** lehetőséget.
6. Keresse meg a listában lévő feltöltések. Ha még nem létezik, térjen vissza a 4. lépés, és próbálja meg egy másik tárfiókba.
7. Használja a **URL-cím** az AzCopy-parancsban céljaként.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hogyan automatizálható a laborkörnyezetben található összes virtuális gép törlésének?
Törölheti a virtuális gépek az Azure Portalon tesztkörnyezetben. Is törölheti a virtuális gépek a tesztkörnyezetben egy PowerShell-parancsprogram használatával. A következő példa alapján a **értékek módosítása** megjegyzés, módosítsa a paraméterek értékeit. Lekérheti a `subscriptionId`, `labResourceGroup`, és `labName` értékeket a labor panelről az Azure Portalon.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.Name -like "$($lab.Name)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzResource -ResourceId $labVM.ResourceId -Force
    }

**Összetevők**
## <a name="what-are-artifacts"></a>Mik az összetevők?
Összetevők olyan testreszabható elemek, amelyek segítségével telepítheti a legújabb elemeket vagy a fejlesztői eszközöket egy virtuális géphez. Összetevők csatlakoztassa a virtuális Géphez a virtuális gép létrehozásakor. Után a virtuális gép ki van építve, az összetevők üzembe helyezése, és konfigurálja a virtuális Gépet. Különböző már meglévő összetevőkkel érhető el a [nyilvános GitHub-adattárból](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Emellett [hozhat létre saját összetevők](devtest-lab-artifact-author.md).


**Tesztlabor-konfiguráció**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hogyan hozhatok létre labor a Resource Manager-sablonnal?
Biztosítunk egy [lab Azure Resource Manager-sablonok GitHub-adattára](https://aka.ms/dtlquickstarttemplate) üzembe helyezhető-, vagy módosítsa a laborral kapcsolatos egyéni sablonokat létrehozni. Mindegyik sablon található hivatkozás segítségével üzembe a labort,-van a saját Azure-előfizetésében. Vagy testre szabhatja a sablont, és [üzembe helyezése PowerShell vagy az Azure CLI használatával](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Miért jönnek létre a virtuális gépek eltérő erőforráscsoportokban tetszőleges nevekkel? Nevezze át vagy módosítsa ezeket az erőforráscsoportokat?
Erőforráscsoportok DevTest Labs szolgáltatásban kezelhető a felhasználói engedélyek és hozzáférés a virtuális gépek jönnek létre ezzel a módszerrel. Bár a virtuális gép áthelyezése másik erőforráscsoportba, és a kívánt nevet használja, azt javasoljuk, hogy nem módosítja az erőforráscsoportokat. Dolgozunk, hogy rugalmasabb javítására.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hány labs hozhat létre egy előfizetésen belül?
A megadott határértéket előfizetésenként létrehozható labs száma nem áll rendelkezésre. Azonban előfizetésenként használt erőforrások mennyisége korlátozva. Itt olvashat a [korlátok és kvóták az Azure-előfizetések](../azure-subscription-service-limits.md) és [ezek a korlátok növelése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Hány virtuális gépet hozhat létre egy tesztlabor?
Nincs konkrét tesztlabor hozható létre virtuális gépek száma korlátlan. Azonban az erőforrásokat (virtuális gép magjainak, nyilvános IP-címeket, és így tovább) használt előfizetésenként korlátozott is. Itt olvashat a [korlátok és kvóták az Azure-előfizetések](../azure-subscription-service-limits.md) és [ezek a korlátok növelése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hogyan oszthatom meg egy közvetlen hivatkozást a laborhoz?

1. Az Azure Portalon nyissa meg a labor létrehozása.
2. Másolja a labor URL-címet a böngészőben, és megoszthatja a labor-felhasználók számára.

> [!NOTE]
> A lab-felhasználó egy külső felhasználó, aki rendelkezik-e egy [Microsoft-fiók](#what-is-a-microsoft-account), de aki nem tagja a szervezet Active Directory-példányból áll, a felhasználó hibaüzenetet során tapasztalhatja próbál hozzáférni a megosztott hivatkozás. Ha egy külső felhasználót a hibaüzenetet látja, kérje meg a felhasználót, hogy először válassza az Azure portal jobb felső sarkában a nevét. Ezt követően a a **Directory** szakaszában a menüben, a felhasználó kiválaszthatja a könyvtárban, ahol a labor létezik.
>
>

## <a name="what-is-a-microsoft-account"></a>Mi a Microsoft-fiók?
Microsoft-fiókot egy olyan fiók, szinte mindenben a Microsoft eszközeit és szolgáltatásait használja. Egy e-mail címet és jelszót, amelyekkel bejelentkezhet a Skype, az Outlook.com, onedrive vállalati verzió, Windows phone és az Xbox Live. Egy olyan fiók, az azt jelenti, hogy a fájlokat, fényképeket, névjegyek és beállítások követheti, bármilyen eszközön.

> [!NOTE]
> Microsoft-fiók segítségével kell meghívni egy *Windows Live ID*.
>
>


**hibaelhárítással**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Az összetevő nem sikerült a virtuális gépek létrehozása során. Hogyan háríthatom azt?
Megtudhatja, hogyan szerezhet be naplófájlokat a sikertelen összetevő, lásd: [DevTest Labs-környezetben az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Miért nem a meglévő virtuális hálózat mentése megfelelően?
Több lehetősége, hogy a virtuális hálózat neve pontokat tartalmaz. Ha igen, próbálkozzon az időszakok eltávolítja vagy lecseréli őket a kötőjelet tartalmazhat. Ezt követően próbálja meg újra a virtuális hálózat mentéséhez.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Miért kapok egy "Szülőerőforrás nem található" hiba történt, amikor egy virtuális Gépet a PowerShell üzembe helyezhető?
Ha egy erőforrás egy másik erőforrás szülő, a szülő erőforrás léteznie kell a gyermek-erőforrás létrehozása előtt. Ha a szülő erőforrás nem létezik, akkor tekintse meg a **ParentResourceNotFound** üzenet. Ha nem ad meg egy függőség az erőforráson, a gyermek-erőforrás előfordulhat, hogy a szülő előtt telepíteni.

Virtuális gépek gyermek erőforrásokat egy erőforráscsoportba tartozó labor csoportban. Virtuális gépek üzembe helyezése a PowerShell használatával a Resource Manager-sablonok használatával, ha az a PowerShell-parancsfájl a megadott erőforráscsoport-név a labor létrehozása az erőforráscsoport nevének kell lennie. További információkért lásd: [gyakori Azure üzembehelyezési hibák elhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Hol találhatok további hibainformációk, ha egy virtuális gép telepítése sikertelen?
Virtuális gép üzembe helyezési hibák tevékenységeket tartalmazó naplók rögzíti a rendszer. Lab VM tevékenységeket tartalmazó naplók alapján is megtalálhatja **Auditnaplók** vagy **virtuális gép diagnosztikai** a a labor virtuális gép lapon erőforrás menüben (a lap jelenik meg, miután kiválasztotta a virtuális gépről a **saját virtuális gépek** lista).

Egyes esetekben a központi telepítési hiba lép fel, virtuális gép üzembe helyezésének megkezdése előtt. Például akkor, ha a virtuális Géppel együtt létrehozott erőforrás az előfizetésre vonatkozó korlát túllépése. Ebben az esetben a hiba részletes adatait rögzíti a rendszer a labor-szintű Tevékenységnaplók. A Tevékenységnaplók alsó részén találhatók az **Konfigurace a zásady** beállításait. Az Azure-ban naplókat tevékenység használatával kapcsolatos további információk: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
