---
title: Azure-fájlmegosztás létrehozása
titleSuffix: Azure Files
description: Azure-fájlmegosztás létrehozása az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596896"
---
# <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Azure-fájlmegosztás létrehozásához három kérdésre kell válaszolnia a használatával kapcsolatban:

- **Milyen teljesítménykövetelmények vonatkoznak az Azure-fájlmegosztásra?**  
    Az Azure Files szabványos fájlmegosztásokat kínál, amelyek merevlemez-alapú (HDD-alapú) hardveren és prémium szintű fájlmegosztásokon találhatók, amelyek ssd-alapú (SSD-alapú) hardveren találhatók.

- **Milyen méretű fájlmegosztásra van szüksége?**  
    A normál fájlmegosztások akár 100 TiB-re is kiterjedhetnek, de ez a funkció alapértelmezés szerint nincs engedélyezve; ha 5 TiB-nél nagyobb fájlmegosztásra van szüksége, engedélyeznie kell a tárfiók nagy fájlmegosztási funkcióját. Prémium fájlmegosztások span akár 100 TiB nélkül különleges beállítást, de prémium fájlmegosztások vannak kiépítve, ahelyett, hogy fizetni, ahogy megy, mint a standard fájlmegosztások. Ez azt jelenti, hogy a szükségesnél sokkal nagyobb fájlmegosztás kiépítése növeli a teljes tárolási költséget.

- **Milyen redundanciakövetelmények vonatkoznak az Azure-fájlmegosztásra?**  
    A szabványos fájlmegosztások helyileg redundáns (LRS), zónaredundáns (ZRS), georedundáns (GRS) vagy geozónaredundáns (GZRS) tárolást kínálnak, azonban a nagy fájlmegosztási szolgáltatás csak helyiredundáns és zónaredundáns fájlmegosztásokesetén támogatott. A prémium fájlmegosztások nem támogatják a georedundancia semmilyen formáját.

    Prémium szintű fájlmegosztások érhetők el a helyi redundancia a legtöbb régióban, amelyek tárfiókokat kínálnak, és zónaredundancia a régiók kisebb részein. Ha meg szeretné tudni, hogy a prémium fájlmegosztások jelenleg elérhetők-e az Ön régiójában, tekintse meg az Azure [régiónként elérhető termékeit.](https://azure.microsoft.com/global-infrastructure/services/?products=storage) A ZRS-t támogató régiókról az [Azure Storage redundanciája](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)című témakörben talál további információt.

A három választási lehetőségről az [Azure-fájlok telepítésének megtervezése című témakörben](storage-files-planning.md)talál további információt.

## <a name="prerequisites"></a>Előfeltételek
- Ez a cikk feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, akkor a kezdés előtt hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ha az Azure PowerShellt kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Ha az Azure CLI-t kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-storage-account"></a>Create a storage account
Az Azure-fájlmegosztások *a tárfiókokban*vannak telepítve, amelyek legfelső szintű objektumok, amelyek megosztott tárolókészletet képviselnek. Ez a tárolókészlet több fájlmegosztás telepítésére használható. 

Az Azure támogatja a különböző típusú tárfiókok különböző tárolási forgatókönyvek ügyfelek lehetnek, de két fő típusa az Azure Files tárfiókok. A létrehozandó tárfiók típusától függ, hogy szabványos fájlmegosztást vagy prémium szintű fájlmegosztást kíván-e létrehozni: 

- **Általános célú 2-es verziójú (GPv2) tárfiókok:** A GPv2-tárfiókok lehetővé teszik az Azure-fájlmegosztások szabványos/merevlemezalapú (HDD-alapú) hardverre történő üzembe helyezését. Az Azure-fájlmegosztások tárolása mellett a GPv2-tárfiókok más tárolási erőforrásokat is tárolhatnak, például blobtárolókat, várólistákat vagy táblákat. 

- **FileStorage storage-fiókok:** FileStorage storage-fiókok lehetővé teszik az Azure-fájlmegosztások üzembe helyezését prémium/ssd-alapú (SSD-alapú) hardveren. A FileStorage-fiókok csak Azure-fájlmegosztások tárolására használhatók; más tárolási erőforrások (blobtárolók, várólisták, táblák stb.) nem telepíthetők egy FileStorage-fiókban.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha az Azure Portalon keresztül szeretne tárfiókot létrehozni, válassza a + Erőforrás létrehozása az irányítópultról **lehetőséget.** Az eredményül kapott Azure Marketplace keresési ablakban keressen **tárfiókot,** és válassza ki az eredményül kapott keresési eredményt. Ez a tárfiókok áttekintő lapjához vezet; A tárfiók-létrehozási varázsló val való kapcsolat folytatásához válassza a **Létrehozás** lehetőséget.

![Képernyőkép a tárfiók gyors létrehozási lehetőségéről a böngészőben](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Az alapok szakasz
A tárfiók létrehozásához szükséges első szakasz **alapjai**címkével van ellátva. Ez tartalmazza a tárfiók létrehozásához szükséges összes mezőt. GPv2-tárfiók létrehozásához győződjön meg arról, hogy a **Teljesítmény** választógomb *Normál* értékre van állítva, és a **Fiók-típusú** legördülő lista a *StorageV2 (általános célú v2)* értékre van kiválasztva.

![Képernyőkép a Teljesítmény választógombról, amelyen a Standard és a Account kind is a StorageV2 van kiválasztva](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage tárfiók létrehozásához győződjön meg arról, hogy a **Teljesítmény** választógomb *Prémium* lesz, és a **Fiók-típusú** legördülő lista a *FileStorage*elemre van kiválasztva.

![Képernyőkép a Teljesítmény választógombról, amelyen a Premium és a Account kind is ki van jelölve a FileStorage](media/storage-how-to-create-file-share/create-storage-account-2.png)

A többi alapmező független a tárfiók választásától:
- **Előfizetés**: A tárfiók üzembe helyezéséhez szükséges előfizetés. 
- **Erőforráscsoport**: A tárfiók üzembe helyezéséhez szánt erőforráscsoport. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévő erőforráscsoportot. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot.
- **Tárfiók neve**: A létrehozandó tárfiók-erőforrás neve. Ennek a névnek globálisan egyedinek kell lennie, de egyébként bármilyen nevet megkaphat. A tárfiók neve lesz a kiszolgáló neve, amikor egy Azure-fájlmegosztást csatlakoztat az SMB-n keresztül.
- **Hely**: Az a régió, ahol a tárfiók üzembe helyezhető. Ez lehet az erőforráscsoporthoz társított régió vagy bármely más elérhető régió.
- **Replikáció**: Bár ez replikációcímkével van ellátva, ez a mező valójában **redundanciát**jelent ; ez a kívánt redundanciaszint: helyi redundancia (LRS), zónaredundancia (ZRS), georedundancia (GRS) és geozóna-redundancia. Ez a legördülő lista tartalmazza az olvasási hozzáférésű georedundanciát (RA-GRS) és az olvasási hozzáférésű geozóna-redundanciát (RA-GZRS), amelyek nem vonatkoznak az Azure fájlmegosztásokra; a kiválasztott tárfiókban létrehozott fájlmegosztások ténylegesen georedundánsak, illetve geozóna-redundánsak lesznek. A régiótól vagy a kiválasztott tárfiók típusától függően előfordulhat, hogy egyes redundanciabeállítások nem engedélyezettek.
- **Hozzáférési szint**: Ez a mező nem vonatkozik az Azure Files, így kiválaszthatja a választógombok egyikét.

#### <a name="the-networking-blade"></a>A Hálózati panel
A hálózati szakasz lehetővé teszi a hálózati beállítások konfigurálását. Ezek a beállítások nem kötelező a tárfiók létrehozásához, és később konfigurálhatók, ha szükséges. Ezekről a lehetőségekről az Azure Files hálózati szempontjai című [témakörben talál](storage-files-networking-overview.md)további információt.

#### <a name="the-advanced-blade"></a>Az Advanced penge
A speciális szakasz az Azure fájlmegosztások számos fontos beállítását tartalmazza:

- **Biztonságos átvitel szükséges:** Ez a mező azt jelzi, hogy a tárfiók titkosítást igényel-e a tárfiókba való kommunikációhoz. Azt javasoljuk, hogy ez engedélyezve marad, azonban ha SMB 2.1 támogatásra van szüksége, ezt le kell tiltania. Javasoljuk, hogy ha letiltja a titkosítást, hogy korlátozza a tárfiók hozzáférését egy virtuális hálózathoz szolgáltatásvégpontokkal és/vagy privát végpontokkal.
- **Nagy fájlmegosztások:** Ez a mező lehetővé teszi, hogy a tárfiók legfeljebb 100 TiB fájlmegosztásokon terjedjen ki. A szolgáltatás engedélyezése csak helyileg redundáns és zónaredundáns tárolási lehetőségekre korlátozza a tárfiókot. Ha egy GPv2 tárfiók engedélyezve van a nagy fájlmegosztásokhoz, nem tilthatja le a nagy fájlmegosztási képességet. FileStorage storage-fiókok (tárfiókok prémium fájlmegosztások) nem rendelkeznek ezzel a beállítással, mivel az összes prémium szintű fájlmegosztások skálázható akár 100 TiB. 

![Képernyőkép az Azure Files ra vonatkozó fontos speciális beállításokról](media/storage-how-to-create-file-share/create-storage-account-3.png)

A speciális lapon elérhető egyéb beállítások (blob soft-delete, hierarchikus névtér az Azure Data Lake storage gen 2 és NFSv3 blob storage) nem vonatkoznak az Azure Files.

#### <a name="tags"></a>Címkék
A címkék olyan név-/értékpárok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoportra alkalmazza. Ezek nem kötelező, és a tárfiók létrehozása után alkalmazható.

#### <a name="review--create"></a>Véleményezés + létrehozás
A tárfiók létrehozásának utolsó lépése a Véleményezés **+ létrehozás** lap **Létrehozás** gombjának kiválasztása. Ez a gomb nem érhető el, ha a tárfiókhoz szükséges összes mező nincs kitöltve.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
A PowerShell használatával tárfiókot hoz `New-AzStorageAccount` létre, a parancsmag használatát fogjuk használni. Ez a parancsmag számos lehetőségközül választhat; csak a szükséges beállítások jelennek meg. A speciális beállításokról a [ `New-AzStorageAccount` parancsmag dokumentációjában](/powershell/module/az.storage/new-azstorageaccount)olvashat bővebben.

A tárfiók és az azt követő fájlmegosztás létrehozásának egyszerűsítése érdekében számos paramétert tárolunk változókban. A változó tartalmát lecserélheti bármilyen kívánt értékre, azonban vegye figyelembe, hogy a tárfiók nevének globálisan egyedinek kell lennie.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Szabványos Azure-fájlmegosztások tárolására képes tárfiók létrehozásához a következő parancsot fogjuk használni. A `-SkuName` paraméter a kívánt redundancia típusára vonatkozik; ha georedundáns vagy geozónaredundáns tárfiókot szeretne, `-EnableLargeFileShare` el kell távolítania a paramétert is.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Prémium szintű Azure-fájlmegosztások tárolására képes tárfiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a `-SkuName` paraméter `Premium` megváltozott, hogy tartalmazza mind a`LRS`mind a kívánt redundancia szintje a helyileg redundáns ( ). A `-Kind` paraméter `FileStorage` helyett `StorageV2` azért, mert prémium fájlmegosztások kell létrehozni egy FileStorage tárfiók helyett a GPv2 tárfiók.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az Azure CLI használatával tárfiókot hoz létre, az az storage-fiók create parancsot fogjuk használni. Ez a parancs számos lehetőségközül választhat; csak a szükséges beállítások jelennek meg. A speciális beállításokról a [ `az storage account create` parancs dokumentációjában olvashat bővebben.](/cli/azure/storage/account)

A tárfiók és az azt követő fájlmegosztás létrehozásának egyszerűsítése érdekében számos paramétert tárolunk változókban. A változó tartalmát lecserélheti bármilyen kívánt értékre, azonban vegye figyelembe, hogy a tárfiók nevének globálisan egyedinek kell lennie.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Szabványos Azure-fájlmegosztások tárolására képes tárfiók létrehozásához a következő parancsot fogjuk használni. A `--sku` paraméter a kívánt redundancia típusára vonatkozik; ha georedundáns vagy geozónaredundáns tárfiókot szeretne, `--enable-large-file-share` el kell távolítania a paramétert is.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Prémium szintű Azure-fájlmegosztások tárolására képes tárfiók létrehozásához a következő parancsot fogjuk használni. Vegye figyelembe, hogy a `--sku` paraméter `Premium` megváltozott, hogy tartalmazza mind a`LRS`mind a kívánt redundancia szintje a helyileg redundáns ( ). A `--kind` paraméter `FileStorage` helyett `StorageV2` azért, mert prémium fájlmegosztások kell létrehozni egy FileStorage tárfiók helyett a GPv2 tárfiók.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Fájlmegosztás létrehozása
Miután létrehozta a tárfiókot, már csak a fájlmegosztás létrehozása maradt. Ez a folyamat többnyire ugyanaz, függetlenül attól, hogy prémium fájlmegosztást vagy szabványos fájlmegosztást használ. Az elsődleges különbség a **kvóta,** és mit képvisel.

A szabványos fájlmegosztások esetében ez az Azure-fájlmegosztás felső határa, amelyen túl a végfelhasználók nem léphetnek. A szabványos fájlmegosztási kvóta elsődleges célja a költségvetés: "Nem akarom, hogy ez a fájlmegosztás ezen a ponton túl nőjön". Ha nincs megadva kvóta, a szabványos fájlmegosztás legfeljebb 100 TiB -re (vagy 5 TiB-re terjedhet ki, ha a nagy fájlmegosztások tulajdonsága nincs beállítva tárfiókhoz).

Prémium fájlmegosztások esetén a kvóta túlterhelt, és **a kiépített méretet**jelenti. A kiosztott méret az az összeg, amelyet a tényleges felhasználástól függetlenül ki kell fizetnie. Prémium szintű fájlmegosztás kiépítésekor két tényezőt kell figyelembe venni: 1) a megosztás jövőbeni növekedését a helykihasználási szempontból, és 2) a munkaterheléshez szükséges IOPS.When provision a premium file share, you want to consider two factors: 1) the future growth of the share from a space kihasználtsági terv és 2) the IOPS required for your workload. Minden kiosztott GiB feljogosítja Önt további fenntartott és burst IOPS. A prémium szintű fájlmegosztások megtervezéséről a [prémium fájlmegosztások kiépítésécímű](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)témakörben talál további információt.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha most hozta létre a tárfiókot, a központi telepítés képernyőről az Ugrás az **erőforrásra**lehetőséget választva navigálhat rá. Ha korábban már létrehozta a tárfiókot, az azt tartalmazó erőforráscsoporton keresztül navigálhat hozzá. Miután a tárfiókban, válassza ki a csempe feliratú **Fájlmegosztások** (akkor is navigálhat **a fájlmegosztások** a tárfiók tartalomjegyzékén keresztül).

![Képernyőkép a Fájlmegosztáscsempe-csempéről](media/storage-how-to-create-file-share/create-file-share-1.png)

A fájlmegosztási listában a tárfiókban korábban létrehozott fájlmegosztásokat kell látnia; üres tábla, ha még nem jött létre fájlmegosztás. Új fájlmegosztás létrehozásához válassza **a + Fájlmegosztás** lehetőséget.

Az új fájlmegosztási panelnek meg kell jelennie a képernyőn. Fájlmegosztás létrehozásához töltse ki az új fájlmegosztási panel mezőit:

- **Név**: a létrehozandó fájlmegosztás neve.
- **Kvóta**: A szabványos fájlmegosztások fájlmegosztásának kvótája; a prémium fájlmegosztások fájlmegosztásának kiosztott mérete.

Válassza a **Létrehozás** lehetőséget az új megosztás létrehozásának befejezéséhez. Vegye figyelembe, hogy ha a tárfiók egy virtuális hálózatban van, nem lesz képes sikeresen létrehozni egy Azure-fájlmegosztást, kivéve, ha az ügyfél is a virtuális hálózatban. Ezt a pont-az-időpontkorlátozást is megoldhatja `New-AzRmStorageShare` az Azure PowerShell-parancsmag használatával.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Az Azure-fájlmegosztást a [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) parancsmaggal hozhatja létre. A következő PowerShell-parancsok feltételezik, `$resourceGroupName` `$storageAccountName` hogy beállította a változókat, és a fent meghatározott a tárfiók létrehozása az Azure PowerShell-szakaszban. 

> [!Important]  
> Prémium fájlmegosztások `-QuotaGiB` esetén a paraméter a fájlmegosztás kiépített méretére hivatkozik. A fájlmegosztás kiosztott mérete az az összeg, amelyért a használattól függetlenül fizetnie kell. A szabványos fájlmegosztások számlázása a használat, nem pedig a kiépített méret alapján van számlázva.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és -fájlok elnevezésével kapcsolatos részletes információt a [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Mielőtt létrehozhatunk egy Azure-fájlmegosztást az Azure CLI-vel, be kell szereznie egy tárfiók kulcsot a fájlmegosztás-létrehozási művelet engedélyezéséhez. Ez a [`az storage account keys list`](/cli/azure/storage/account/keys) következő paranccsal végezhető el:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Miután rendelkezik a tárfiók kulcsával, létrehozhatja [`az storage share create`](/cli/azure/storage/share) az Azure-fájlmegosztást a paranccsal. 

> [!Important]  
> Prémium fájlmegosztások `--quota` esetén a paraméter a fájlmegosztás kiépített méretére hivatkozik. A fájlmegosztás kiosztott mérete az az összeg, amelyért a használattól függetlenül fizetnie kell. A szabványos fájlmegosztások számlázása a használat, nem pedig a kiépített méret alapján van számlázva.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Ez a parancs sikertelen lesz, ha a tárfiók egy virtuális hálózatban található, és az a számítógép, amelyről ezt a parancsot megidézi, nem része a virtuális hálózatnak. Ezt a pont-az-időpontban korlátozást megkerülheti `New-AzRmStorageShare` az Azure PowerShell-parancsmag használatával a fent leírtak szerint, vagy az Azure CLI-t a virtuális hálózat részét tartalmazó számítógépről, többek között VPN-kapcsolaton keresztül.

---

> [!Note]  
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és -fájlok elnevezésével kapcsolatos részletes információt a [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>További lépések
- [Tervezze meg az Azure Files telepítését,](storage-files-planning.md) vagy [tervezze meg az Azure File Sync telepítését.](storage-sync-files-planning.md) 
- [A hálózat – áttekintés .](storage-files-networking-overview.md)
- Fájlmegosztás csatlakoztatása és csatlakoztatása [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)és [Linux rendszeren.](storage-how-to-use-files-linux.md)