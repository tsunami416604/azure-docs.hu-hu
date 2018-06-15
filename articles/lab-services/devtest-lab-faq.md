---
title: Az Azure DevTest Labs – gyakori kérdések |} Microsoft Docs
description: Azure DevTest Labs kapcsolatos gyakori kérdésekre adott válaszok.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: a295cad2bf1cafce4dc64909174e9417daa7918e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787674"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs GYIK
Azure DevTest Labs kapcsolatos általános kérdésekre adott válaszok.

**Általános**
## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha a fentiekben itt nem választ?
Ha a kérdését itt nem látható, ossza meg velünk, így tudunk segíteni, hogy választ találjanak.

* Ez a GYIK végén elküldésekor. Az Azure Cache csoportját, és a Közösség más tagjaival kapcsolatos cikkben bevonásához.
* Egy szélesebb körű célközönség eléréséhez fel kérdést az [Azure DevTest Labs MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Az Azure DevTest Labs csoportját, és a Közösség más tagjai bevonásához.
* Funkciókérések, nyújt a kérelmek és ötleteket a [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Miért érdemes használni Azure DevTest Labs szolgáltatásban?
Az Azure DevTest Labs szolgáltatásban a csapat időt és pénzt takaríthat meg. A fejlesztők számos különböző kiindulási segítségével létrehozható a saját környezetben. Is használhatnak összetevők gyorsan üzembe helyezését, és konfigurálhatja az alkalmazásokat. Egyéni lemezképek és a formulák használatával virtuális gépek (VM) Mentés másként sablonok, és könnyen előhívja azokat a csoport között. DevTest Labs szolgáltatásban is biztosít, több konfigurálható házirendek adott labor rendszergazdák segítségével csökkentheti a pazarlás és kezelhetik a csapat környezeteket. Ezek a házirendek közé tartoznak az automatikus rendszerleállítást, költség küszöbérték maximális virtuális gépek minden felhasználó és a maximális Virtuálisgép-méretet. DevTest Labs részletesebb leírását, tekintse meg a [áttekintése](devtest-lab-overview.md) vagy a [bevezető videó](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Mit jelent "megbízható önkiszolgáló"?
Önkiszolgáló megbízható, az azt jelenti, hogy a fejlesztők és a tesztelést létrehozása a saját környezetben szükség szerint. A rendszergazdák rendelkeznek ismerete, hogy a DevTest Labs segítségével, minimálisra csökkenthető a pazarlás és ellenőrzési költségek biztonságát. A rendszergazdák adhat meg, melyik Virtuálisgép-méretek engedélyezett virtuális gépek maximális számát, és ha a virtuális gépek indítása és leállítása. DevTest Labs szolgáltatásban is egyszerűen figyelheti a költségek, és állítson be riasztásokat a segítenek, hogy tisztában legyen hogyan labor erőforrások felhasználását.

## <a name="how-can-i-use-devtest-labs"></a>Hogyan használhatók a DevTest Labs szolgáltatásban?
DevTest Labs akkor hasznos, bármikor Ön szükséges fejlesztői vagy tesztelési környezetben, és gyorsan reprodukálásához szükséges, vagy kezelheti azokat költségkímélő házirendek segítségével.

Az alábbiakban néhány forgatókönyv használó ügyfeleink DevTest Labs:

* Kezelése fejlesztési és tesztelési környezetben egy helyen. Házirendek használata csökkentheti a költségeket, és hozzon létre egyéni lemezképek megosztásához épít, a csoport között.
* Kifejleszthet egy alkalmazást a fejlesztési műveletben lemez állapotmentése egyéni lemezképek használatával.
* Nyomon költség viszonyítva folyamatban van.
* Hozzon létre minőségi megbízhatósági tesztelési tömeges tesztkörülmények között.
* Az összetevők és a formulák segítségével könnyen konfigurálása, és Reprodukálja az alkalmazás különböző környezetekben.
* Virtuális gépek terjesztése a hackathons (fejlesztési és tesztelési együttműködés), és majd könnyen kiosztásának megszüntetése azokat az esemény végén.

## <a name="how-am-i-billed-for-devtest-labs"></a>Hogyan vagyok számlázása a DevTest Labs szolgáltatásban?
DevTest Labs egy ingyenes szolgáltatás. Tesztkörnyezetek létrehozása és konfigurálása a házirendek, sablonok és összetevők a DevTest Labs szolgáltatásban felszabadul. Csak a labs, például a virtuális hálózatok, virtuális gépek és tárfiókok használt Azure-erőforrások fizetnie. A tesztkörnyezet erőforrások költsége kapcsolatos további információkért lásd: [Azure DevTest Labs árképzési](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Biztonság**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Mik a különböző védelmi szintek a DevTest Labs szolgáltatásban?
Biztonsági hozzáférést határozza meg [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/built-in-roles.md). Megtudhatja, hogyan működik a hozzáférést, segít engedélyt, a szerepkör és a hatókör, közötti különbségekről további RBAC által definiált konfigurációjának kialakításához.

* **Engedély**: engedély meghatározott érhető el egy bizonyos művelet. Például egy engedély lehet olvasási hozzáférés az összes virtuális gépen.
* **Szerepkör**: A szerepkör az olyan készlete, amely csoportosítja, és a felhasználóhoz rendelt engedélyek. Például egy előfizetés tulajdonosának szerepkörrel rendelkező felhasználó hozzáfér egy előfizetésen belüli összes erőforrást.
* **Hatókör**: egy hatókör egy szint, egy Azure-erőforrás a hierarchiában. A hatókör lehet például egy erőforráscsoportot, egy laboratóriumi vagy az egész előfizetésre.

DevTest Labs hatókörén belül két típusa van a felhasználói engedélyek megadásához szerepkörei:

* **A tesztkörnyezet tulajdonosa**: A tesztkörnyezet tulajdonosa számára elérhető összes erőforrás-a labor. A tesztkörnyezet tulajdonosa is házirendek módosíthatók, olvasása és írása az összes virtuális gépen, módosítsa a virtuális hálózatot, és így tovább.
* **Lab-felhasználó**: lab-felhasználó megtekintheti a tesztlabor összes erőforrás, például a virtuális gépek, a házirendek és a virtuális hálózatok. De lab-felhasználó nem módosíthatja a házirendeket vagy bármely más felhasználók által létrehozott virtuális gépek. 

Egyéni szerepkörök a DevTest Labs szolgáltatásban hozhat létre. Egyéni szerepkörök létrehozása a DevTest Labs szolgáltatásban, lásd: [felhasználói engedélyeket adott labor házirendek](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel a hatókörök hierarchikus, amikor egy felhasználó jogosult bizonyos hatókörre, a felhasználó automatikusan megadja ezeket az engedélyeket a hatókörében minden alacsonyabb szintű hatókörben. Például ha egy felhasználó a szerepkört az előfizetés tulajdonosa van hozzárendelve, a felhasználó rendelkezik minden erőforrásokhoz való hozzáférést egy előfizetésben. Ilyen erőforrások többek között az összes virtuális gépet, az összes virtuális hálózatot és minden labs. Egy előfizetés tulajdonosa automatikusan örökli a tesztkörnyezet tulajdonosa szerepe. Azonban a ennek fordítottja nem igaz. A tesztkörnyezet tulajdonosa hozzáfér egy tesztkörnyezetet, amelyhez a hatókör alacsonyabb, mint az előfizetés szintjéről. Ezért a tesztkörnyezet tulajdonosa nem látható, virtuális gépek, virtuális hálózatok vagy bármely más erőforrások, amelyek túlmutatnak a labor.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Hogyan hozható létre a felhasználók egy adott feladat végrehajtásához szerepkör?
Átfogó cikk egyedi szerepkörök létrehozását és engedélyek hozzárendelése a szerepkörhöz, lásd: [felhasználói engedélyeket adott labor házirendek](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Íme egy példa egy parancsfájl, amely a szerepkör hoz *DevTest Labs speciális felhasználói*, amely jogosult indítása és leállítása a tesztlabor virtuális gépeinek:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**CI/CD-integráció és automatizálás**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs szolgáltatásban a CI/CD toolchain integrálása?
Ha a Visual Studio Team Services használ, egy [DevTest Labs feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) a DevTest Labs szolgáltatásban a kiadási folyamat automatizálására. Ez a bővítmény elvégezhető feladatokat tartalmazza:

* Hozzon létre, és automatikusan telepítse a virtuális Gépet. Is konfigurálható a virtuális Gépet a legújabb buildjével Azure fájl másolása vagy a PowerShell Team Services feladatok használatával.
* Automatikusan rögzíteni a virtuális gépek állapotát állítja elő a további vizsgálatok esetén az azonos virtuális gépen programhiba tesztelés után.
* Már nincs szükség a kiadási folyamat végén a virtuális gép törlése

A következő blogbejegyzéseket ajánlat útmutató és a Team Services bővítmény használatával kapcsolatos információk:

* [DevTest Labs és a Visual Studio Team Services bővítmény](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Olyan új virtuális gép egy meglévő DevTest Labs, amikor a Team Services telepítéséhez](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Team Services kiadáskezelés folyamatos központi telepítésére DevTest Labs segítségével](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Az egyéb folyamatos integrációhoz (CI) / folyamatos kézbesítési (CD) toolchains érhet el az azonos forgatókönyvek szerint telepítése [Azure Resource Manager-sablonok](https://aka.ms/dtlquickstarttemplate) használatával [Azure PowerShell-parancsmagok](../azure-resource-manager/resource-group-template-deploy.md) és [.NET SDK-k](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Is használhat, [REST API-k a DevTest Labs](http://aka.ms/dtlrestapis) a toolchain integrálható.  


**Virtuális gépek**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Miért nem látható a virtuális gépek panel, amelyen a DevTest Labs szolgáltatásban látható a virtuális gépek?
Ha a virtuális gép létrehozása a DevTest Labs szolgáltatásban, lehetősége van ezt a virtuális Gépet hozzáférési engedélyt. A virtuális gép, mind a a labs panelen, majd a tekintheti meg a **virtuális gépek** panelen. A DevTest Labs labor felhasználói szerepkörhöz rendelt felhasználók láthatják az összes virtuális gépet, a laborban a tesztkörnyezet a létrehozott **az összes virtuális gép** panelen. Azonban a DevTest Labs labor felhasználói szerepkörrel rendelkező felhasználók nem automatikusan olvasási hozzáférést kapnak a más felhasználók által létrehozott Virtuálisgép-erőforrások. Ezért, virtuális gépek nem jelenik meg a **virtuális gépek** panelen.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Mi az a különbség egy egyéni lemezképet és egy képletet?
Egyéni lemezkép egy virtuális merevlemez (VHD). A képlet egy olyanra, amely a további beállítások konfigurálása, majd mentse és Reprodukálja. Előfordulhat, hogy egyéni lemezkép hatékonyabb, ha azt szeretné, hogy az ugyanazon alapvető, nem módosítható a lemezkép használatával több környezetben gyors létrehozásához. Előfordulhat, hogy egy képletet jobb, ha azt szeretné, hogy a virtuális Gépet a legújabb bitként konfigurációját reprodukálásához szükséges, egy virtuális hálózathoz vagy alhálózathoz részeként, vagy egy adott méretű virtuális gépként. A részletesebb magyarázatáért lásd: [az egyéni lemezképek és a DevTest Labs szolgáltatásban képletek összehasonlítása](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hogyan hozható létre több virtuális gép ugyanazon sablonból egyszerre?
Egyszerre több virtuális gép létrehozása ugyanazt a sablont a két lehetőség közül választhat:
* Használhatja a [Visual Studio Team Services feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Is [a Resource Manager-sablonok készítése](devtest-lab-add-vm.md#save-azure-resource-manager-template) egy virtuális gép létrehozása közben és [a Resource Manager-sablon a Windows PowerShell telepítése](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Milyen a meglévő Azure virtuális gépek áthelyezi a DevTest Labs labor?
Másolja a meglévő virtuális gépek DevTest Labs szolgáltatásban:

1. A VHD-fájlt a meglévő virtuális gép másolása segítségével egy [Windows PowerShell-parancsfájl](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Az egyéni lemezképet](devtest-lab-create-template.md) a DevTest Labs labor belül.
3. Virtuális gép létrehozása az egyéni lemezképet a tesztkörnyezetben.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Hozzácsatlakoztathatok több lemezt a virtuális gépek?
Igen, több olyan lemezt lehet kapcsolódni a virtuális gépek.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>A Windows operációs rendszer lemezképét a a teszteléshez használni kívánt, ha rendelkezik az MSDN előfizetői?
Használó Windows ügyfél operációs rendszer lemezképeit (Windows 7 vagy újabb) a fejlesztés és tesztelés az Azure-ban, tegye a következők egyikét:

- [Az MSDN-előfizetés vásárlása](https://www.visualstudio.com/products/how-to-buy-vs).
- Ha egy nagyvállalati szerződés, az Azure-előfizetés létrehozása a [vállalati fejlesztési és tesztelési célú ajánlat](https://azure.microsoft.com/offers/ms-azr-0148p).

Az Azure-krediteket az egyes MSDN ajánlat kapcsolatos további információkért lásd: [havi Azure-kredit a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hogyan automatizálhatók az egyéni lemezképek létrehozásához VHD-fájlok feltöltése a folyamatot?
Automatikus feltöltése VHD-fájlok egyéni lemezképek létrehozásához, két lehetőség közül választhat:

* Használjon [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) másolása vagy VHD-fájlok feltöltése a tárfiókba, a labor társított.
* Használjon [Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md). A Tártallózó egy különálló alkalmazás, amely futtatja a Windows, OS X, Linux.   

A cél tárfiókkal a labor társított megkeresése:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. A bal oldali menüben válassza **erőforráscsoportok**.
3. Keresse meg és jelölje ki az erőforráscsoportot, amelyhez a labor van társítva.
4. A **áttekintése**, válasszon egyet a storage-fiókok.
5. Válassza ki **Blobok**.
6. Keresse meg a listában feltöltések. Ha még nem létezik ilyen, térjen vissza a 4. lépés, és próbálja meg egy másik tárfiókhoz.
7. Használja a **URL-cím** az AzCopy parancs céljaként.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hogyan automatizálhatók a tesztlabor összes virtuális gép törlésének?
Törölheti az Azure-portálon a labor virtuális gépeken. Is törölheti a virtuális gépeket a tesztkörnyezetben egy PowerShell-parancsfájl használatával. A következő példa alapján a **értékek módosítása** megjegyzés, módosítsa a paraméterértékek. Kérheti le a `subscriptionId`, `labResourceGroup`, és `labName` értékek a labor panelről az Azure portálon.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Az összetevők**
## <a name="what-are-artifacts"></a>Mik azok az összetevők?
Az összetevők olyan testreszabható elemei, amelyek segítségével telepítheti a legújabb bits vagy a fejlesztői eszközök egy virtuális géphez. Az összetevők csatolása a virtuális Gépet a virtuális gép létrehozásakor. A virtuális gép üzembe helyezése után az összetevők telepítése és konfigurálása a virtuális gép. Különböző elérésű, korábban létező összetevők érhetők el a [nyilvános GitHub-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Emellett [hozhatnak létre a saját összetevők](devtest-lab-artifact-author.md).


**Tesztlabor-konfiguráció**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hogyan hozható létre egy tesztkörnyezetet a Resource Manager sablon alapján?
Fel egy [labor Azure Resource Manager-sablonok a GitHub-tárházban](https://aka.ms/dtlquickstarttemplate) , telepítheti-, vagy módosítsa a labs egyéni sablonok létrehozásához. Minden sablon tartalmaz egy hivatkozást a labor, mint a telepítendő-van a saját Azure-előfizetésben. Vagy testre szabhatja a sablon és [telepítését PowerShell vagy Azure CLI](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Miért létrejönnek a virtuális gépek különböző erőforráscsoportokra tetszőleges nevű? Nevezze át vagy módosítsa ezeket az erőforráscsoportokat?
Erőforráscsoportok ily módon jönnek létre, így DevTest Labs kezelhető a felhasználói engedélyek és a hozzáférés a virtuális gépekhez. Bár a virtuális gép áthelyezése egy másik erőforráscsoportban, és a kívánt nevet használja, azt javasoljuk, hogy nem módosítja az erőforráscsoportokat. Ez a felület további rugalmas javítása dolgozunk.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hány labs hozhat létre a ugyanazt az előfizetést?
Nem áll rendelkezésre egy adott labs előfizetésenként hozható létre a számára vonatkozó korlátozást. Azonban előfizetésenként használt erőforrások mennyisége korlátozva. Emellett tájékozódhat a [korlátozásai és az Azure-előfizetések kvótái](../azure-subscription-service-limits.md) és [működés felső korlátjának növelésére](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Hány virtuális gépeket hozhat létre száma tesztkörnyezetenként?
A virtuális gépek száma tesztkörnyezetenként létrehozható számát az adott korlátozva van. Azonban az erőforrások (VM mag, nyilvános IP-címeket, és így tovább) használt korlátozottak előfizetésenként. Emellett tájékozódhat a [korlátozásai és az Azure-előfizetések kvótái](../azure-subscription-service-limits.md) és [működés felső korlátjának növelésére](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hogyan megosztani az egy közvetlen hivatkozást a laborkörnyezetben?

1. Az Azure-portálon lépjen a laborkörnyezetben.
2. A tesztkörnyezet URL-Címének másolása a böngészőből, és megosztása a labor felhasználók.

> [!NOTE]
> Ha a labor felhasználó-e egy külső felhasználó, aki rendelkezik egy [Microsoft-fiók](#what-is-a-microsoft-account), aki tagja nem a szervezet Active Directory-példányban, a felhasználó hibaüzenetet során tapasztalhatja próbálnak hozzáférni a megosztott hivatkozás, de. Ha egy külső felhasználó meglátja ezt a hibaüzenetet, kérje meg, hogy először válassza ki azt a nevet az Azure-portál jobb felső sarkában. Ezt követően a a **Directory** szakasza a menüben, a felhasználó kiválaszthatja a könyvtárban, ahol a labor van.
>
>

## <a name="what-is-a-microsoft-account"></a>Mi a Microsoft-fiók?
A Microsoft-fiók egy csaknem minden Microsoft-eszközöket és szolgáltatásokat a használt fiók. Egy e-mail címet és jelszót, amely jelentkezhet Skype, Outlook.com, OneDrive, Xbox Live, és a Windows phone. Egy olyan fiók, az azt jelenti, hogy a fájlokat, fényképek, névjegyek és beállítások követve, bármilyen eszközről.

> [!NOTE]
> A Microsoft-fiók használatával hívható meg egy *Windows Live ID*.
>
>


**hibaelhárítással**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Az összetevő virtuális gép létrehozása sikertelen volt. Hogyan hibaelhárítása azt?
Naplók lekérése az sikertelen összetevő, lásd: [a DevTest Labs szolgáltatásban összetevő hibák diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Miért nem a meglévő virtuális hálózat mentése megfelelően?
Több lehetősége, hogy a virtuális hálózati pontokat tartalmazza. Ha igen, próbálja meg a időszakok eltávolítása vagy lecserélése kötőjeleket. Ezután próbálja meg újra a virtuális hálózat mentéséhez.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Miért jelenik meg a "Szülő erőforrás nem található" hiba történt, amikor egy virtuális Gépet a PowerShell kiépíteni I?
Amikor egy erőforrást egy másik erőforrásra való szülője, a szülő erőforrás léteznie kell, a gyermek-erőforrás létrehozása előtt. Ha a szülő erőforrás nem létezik, akkor tekintse meg a **ParentResourceNotFound** üzenet. Ha a szülő erőforrás függősége nem adja meg, a gyermek-erőforrás a szülő előtt lehet, hogy telepíteni.

Virtuális gépek gyermekerőforrásait egy erőforráscsoportban, amikor a rendszer. Resource Manager-sablonok használatával történő telepítése a virtuális gépek PowerShell használatával, a PowerShell-parancsfájl a megadott erőforráscsoport-név – a labor az erőforráscsoport nevének kell lennie. További információkért lásd: [az Azure-telepítés gyakori hibáinak az elhárítását](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Hol találok további hibainformációk, ha egy virtuális gép telepítése sikertelen?
Virtuális gép telepítési hibák tevékenység naplózva lesznek rögzítve. Labor VM tevékenységi naplóit alapján is megtalálhatja **a naplók** vagy **virtuálisgép-diagnosztika** a erőforrás menü a labor virtuális gép panelen (a panelen megjelenik a virtuális gép kiválasztása után a **saját virtuális gépek** lista).

Egyes esetekben a központi telepítési hiba lép fel, a Virtuálisgép-telepítéshez megkezdése előtt. Például akkor, ha a előfizetés hoztak létre a VM erőforrás meghaladja a korlátozást. Ebben az esetben a hiba részletes adatait a labor szintű tevékenység naplózva lesznek rögzítve. Tevékenység naplók találhatók alján a **konfigurációs és házirendek** beállításait. Tevékenység használatáról további információkat naplózza az Azure-ban, a következő témakörben: [tevékenységi naplóit rendszervizsgálati műveleteket az egyes erőforrások megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
