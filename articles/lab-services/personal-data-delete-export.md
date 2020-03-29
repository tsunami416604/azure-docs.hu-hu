---
title: Személyes adatok törlése és exportálása az Azure DevTest Labs programból
description: Ismerje meg, hogyan törölheti és exportálhatja a személyes adatokat az Azure DevLast Labs szolgáltatásból az általános adatvédelmi rendelet (GDPR) szerinti kötelezettségeinek támogatása érdekében.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169687"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Személyes adatok exportálása vagy törlése az Azure DevTest Labs programból
Ez a cikk a személyes adatok azure DevTest Labs szolgáltatásból történő törlésének és exportálásának lépéseit ismerteti. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Milyen személyes adatokat gyűjt a DevTest Labs?
A DevTest Labs két fő személyes adatot gyűjt a felhasználótól. Ezek a következők: felhasználói e-mail cím és felhasználói objektum azonosítója. Ez az információ kritikus fontosságú a szolgáltatás számára, hogy a labor-rendszergazdák és a tesztkörnyezet-felhasználók szolgáltatáson ként szolgálnak.

### <a name="user-email-address"></a>Felhasználói e-mail cím
DevTest Labs a felhasználói e-mail cím segítségével küldautomatikus leállítási e-mail értesítéseket a labor felhasználóknak. Az e-mail értesíti a felhasználókat a számítógép leállításáról. A felhasználók elhalaszthatják vagy kihagyhatják a leállítást, ha ezt kívánják. Konfigurálja az e-mail címet a labor vagy a virtuális gép szintjén.

**E-mail beállítása a laborban:**

![E-mail beállítása a labor szintjén](./media/personal-data-delete-export/lab-user-email.png)

**E-mail beállítása a virtuális gépen:**

![E-mailek beállítása a virtuális gép szintjén](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Felhasználói objektum azonosítója
DevTest Labs a felhasználói objektum azonosítója segítségével jeleníti meg a hónapon kénti költségtrendek és a költségek erőforrás-információk a labor adminisztrátorok. Lehetővé teszi számukra a költségek nyomon követését és a tesztkörnyezetük küszöbértékének kezelését. 

**Az aktuális naptári hónap becsült költségtendenciája:**
![Az aktuális naptári hónap becsült költségtendenciája](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Becsült hónap-dátum költség erőforrás szerint:**
![Becsült hónap-dátum költség erőforrásonként](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Miért van szükségünk ezekre a személyes adatokra?
A DevTest Labs szolgáltatás a személyes adatokat használja fel működési célokra. Ezek az adatok kritikus fontosságúak a szolgáltatás számára a kulcsfontosságú funkciók biztosításához. Ha adatmegőrzési szabályt állít be a felhasználói e-mail címhez, a laborfelhasználók nem kapnak időben automatikus leállítási e-mail értesítéseket, miután az e-mail címüket törölték a rendszerünkből. Hasonlóképpen a labor rendszergazdája nem tekintheti meg a havi költségtrendek és a költségek erőforrás on-erőforrás a laborokban, ha a felhasználói objektum azonosítók adatmegőrzési házirend alapján törlődnek. Ezért ezeket az adatokat meg kell őrizni mindaddig, amíg a felhasználó erőforrás aktív a laborban.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hogyan lehet a rendszer, hogy felejtse el a személyes adatokat?
Labor felhasználóként, ha szeretné, hogy ezeket a személyes adatokat törölni, megteheti a megfelelő erőforrás törlésével a laborban. A DevTest Labs szolgáltatás anonimizálja a törölt személyes adatokat 30 nappal azután, hogy a felhasználó törölte őket.

Ha például törli a virtuális gép, vagy eltávolította az e-mail címét, a DevTest Labs szolgáltatás anonimizálja ezeket az adatokat 30 nappal az erőforrás törlése után. A 30 napos adatmegőrzési szabályzat törlés után annak biztosítása, hogy pontos havi költségelőrejelzést biztosítsunk a labor adminisztrátorának.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hogyan kérhetek exportot a személyes adataimból?
Személyes és laborhasználati adatok at exportálhat az Azure Portalon vagy a PowerShellhasználatával. Az adatok két különböző CSV-fájlként lesznek exportálva:

- **disks.csv** - információkat tartalmaz a különböző virtuális gépek által használt lemezekről
- **virtualmachines.csv** - a laborban lévő virtuális gépekről tartalmaz információkat.

### <a name="azure-portal"></a>Azure portál
Laborfelhasználóként kérheti a DevTest Labs szolgáltatás által tárolott személyes adatok exportálását. Exportálás igényléséhez keresse meg a **Személyes adatok** lehetőséget a **tesztkörnyezet Áttekintés lapján.** Válassza ki az **exportálás kérése** gombot, amely elindítja egy letölthető excel fájl létrehozását a Lab-rendszergazda tárfiókjában. Ezután kapcsolatba léphet a labor rendszergazdájával az adatok megtekintéséhez.

1. Válassza a bal oldali menü **Személyes adatai lehetőséget.** 

    ![Személyes adatok oldal](./media/personal-data-delete-export/personal-data-page.png)
2. Válassza ki a labort tartalmazó **erőforráscsoportot.**

    ![Erőforráscsoport kiválasztása](./media/personal-data-delete-export/select-resource-group.png)
3. Válassza ki a **tárfiókot** az erőforráscsoportban.
4. A **Tárfiók** lapon válassza **a Blobok**lehetőséget.

    ![Blobok csempe kiválasztása](./media/personal-data-delete-export/select-blobs-tile.png)
5. Válassza ki a **labresourceusage** nevű tárolót a tárolók listájában.

    ![Blob-tároló kijelölése](./media/personal-data-delete-export/select-blob-container.png)
6. Jelölje ki a tesztkörnyezetről elnevezett **mappát.** Ebben a mappában található **a lemez-** és **virtuálisgépekhez** való **csv-fájlok.** Letöltheti ezeket a csv fájlokat, szűrheti a hozzáférést kérő laborfelhasználó tartalmát, és megoszthatja velük.

    ![CSV-fájl letöltése](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

A fenti minta fő összetevői a következők:

- Az Invoke-AzureRmResourceAction parancs.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Két beavatkozási paraméter
    - **blobStorageAbsoluteSasUri** – A tárfiók URI-ja a megosztott hozzáférésű aláírás (SAS) jogkivonattal. A PowerShell-parancsfájlban ez az érték a tárolókulcs helyett átadható.
    - **usageStartDate** - Az adatok lekérése kezdő dátuma, a záró dátum pedig az aktuális dátum, amelyen a művelet végrehajtásra kerül. A részletesség a nap szintjén van, így még akkor is, ha időadatokat ad hozzá, a rendszer figyelmen kívül hagyja.

### <a name="exported-data---a-closer-look"></a>Exportált adatok - közelebbről
Most vessünk egy közelebbi pillantást az exportált adatokra. Mint korábban említettük, miután az adatok sikeresen exportált, nem lesz két CSV fájlokat. 

A **virtualmachines.csv** a következő adatoszlopokat tartalmazza:

| Oszlop neve | Leírás |
| ----------- | ----------- | 
| SubscriptionId | Az előfizetés-azonosító, amelyben a tesztkörnyezet létezik. |
| LabUId között | A tesztkörnyezet egyedi GUID-azonosítója |
| Laborneve | A labor neve. |
| LabResourceId | Teljesen minősített laborerőforrás-azonosító. |
| ResourceGroupName | A virtuális gép által tartalmazó erőforráscsoport neve | 
| ResourceId | A virtuális gép teljesen minősített erőforrás-azonosítója. |
| Erőforrás-azonosító | GuiD a virtuális gép |
| Név | Virtuális gép neve. |
| CreatedTime | A virtuális gép létrehozásának dátuma és időpontja. |
| DeletedDate (Törlésdátum) | A virtuális gép törlésének dátuma.The date-date, which the VM was deleted. Ha üres, a törlés még nem történt meg. |
| Erőforrástulajdonos | A virtuális gép tulajdonosa. Ha az érték üres, akkor vagy egy jogcímzésre jogosult virtuális gép, vagy egy egyszerű szolgáltatás által létrehozott. |
| Árak | A virtuális gép díjszabási szintje |
| Erőforrás állapota | A virtuális gép rendelkezésre állási állapota. Aktív, ha még létezik, vagy inaktív, ha a virtuális gép törölve lett. |
| Számítási erőforrásazonosító | Teljesen minősített virtuálisgép-számítási erőforrás-azonosító. |
| Követelésre méltó | Állítsa igaz, ha a virtuális gép egy követelhető virtuális gép | 
| Környezetazonosító | Az a környezeti erőforrás-azonosító, amelyben a virtuális gép létrejött. Üres, ha a virtuális gép nem jött létre egy környezeti erőforrás részeként. |
| Lejárati dátum | A virtuális gép lejárati dátuma. Üres, ha nincs beállítva lejárati dátum.
| GalleryImageReferenceVersion |  A virtuális gép alaplemezképének verziója. |
| GalleryImageReferenceOffer | A virtuális gép alaplemezképének ajánlata. |
| GalleryImageReferencePublisher | A virtuális gép alaplemezképének közzétevője. |
| GalériaImageReferenceSku | A virtuális gép alaplemezképének skuja |
| GalériaKépHivatkozásostípus | A virtuális gép alaplemezképének operációs rendszer típusa |
| CustomImageId azonosító | A virtuális gép alapegyéni lemezképének teljesen minősített azonosítója. |

A **disks.csv** e-mezőben található adatoszlopok a következők:

| Oszlop neve | Leírás | 
| ----------- | ----------- | 
| SubscriptionId | A tesztkörnyezetet tartalmazó előfizetés azonosítója |
| LabUId között | GuiD a labor |
| Laborneve | A labor neve | 
| LabResourceId | Teljesen minősített erőforrás-azonosító a laborhoz | 
| ResourceGroupName | A labort tartalmazó erőforráscsoport neve | 
| ResourceId | A virtuális gép teljesen minősített erőforrás-azonosítója. |
| Erőforrás-azonosító | GuiD a virtuális gép |
 |Név | A csatlakoztatott lemez neve |
| CreatedTime |Az adatlemez létrehozásának dátuma és időpontja. |
| DeletedDate (Törlésdátum) | Az adatlemez törlésének dátuma és időpontja. |
| Erőforrás állapota | Az erőforrás állapota. Aktív, ha az erőforrás létezik. Inaktív, ha törlik. |
| DiskBlobName | Az adatlemez blobneve. |
| DiskSizeGB | Az adatlemez mérete. |
| DiskType | Az adatlemez típusa. 0 a standard, 1 a prémium. |
| LeasedByVmId | Annak a virtuális gépnek az erőforrásazonosítója, amelyhez az adatlemez csatolva van. |


> [!NOTE]
> Ha több laborral foglalkozik, és átfogó információkat szeretne kapni, a két fő oszlop a **LabUID** és a **ResourceUId**, amelyek az előfizetések közötti egyedi azonosítók.

Az exportált adatok manipulálhatók és láthatóvá tehetők olyan eszközökkel, mint az SQL Server, a Power BI stb. Ez a funkció különösen akkor hasznos, ha a tesztkörnyezet használatát olyan felügyeleti csapatnak szeretné jelenteni, amely nem ugyanazt az Azure-előfizetést használja, mint Ön.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Szabályzatok beállítása egy tesztkörnyezethez](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
