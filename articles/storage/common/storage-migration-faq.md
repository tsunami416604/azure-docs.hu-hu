---
title: Azure Storage-áttelepítés – GYIK | Microsoft Docs
description: Válaszok az Azure Storage áttelepítésével kapcsolatos gyakori kérdésekre
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 39f1137638f9cd4926b712bdd18e681d90adcdc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668551"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Gyakori kérdések az Azure Storage migrálásával kapcsolatban

Ez a cikk az Azure Storage áttelepítésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="copy-upload-or-download"></a>Másolás, feltöltés vagy Letöltés

**Hogyan létrehozhat egy parancsfájlt, amely egy tárolóból egy másikba másolja a fájlokat?**

A fájlok tárolók közötti másolásához használhatja a AzCopy. Lásd a következő példát:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

A AzCopy a [blob másolása API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) -t használja a tárolóban lévő egyes fájlok másolásához.  

Bármely olyan virtuális gépet vagy helyi gépet használhat, amely internet-hozzáféréssel rendelkezik a AzCopy futtatásához. Ezt a Azure Batch-ütemtervet is használhatja az automatikus elvégzéséhez, de bonyolultabb lehet.  

Az Automation-szkript a tárolási tartalom kezelése helyett Azure Resource Manager üzembe helyezéshez lett tervezve. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../../azure-resource-manager/templates/deploy-powershell.md).

**Van olyan díj, amely az adatok két fájlmegosztás közötti másolását ugyanazon a tárolási fiókon belül ugyanazon a régióban történik?**

Nem. A folyamat díjmentes.

**Hogyan tölthetők le 1-2 TB adat a Azure Portalról?**

Az AzCopy használatával töltheti le az adatfájlokat. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan tölthetek le egy virtuális merevlemezt egy helyi gépre, kivéve a portálon a letöltés lehetőséggel?**

A virtuális merevlemez letöltéséhez [Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) használhat.

**Adatok letöltése egy Linux-alapú számítógépre Azure Storage-fiókból vagy egy Linux rendszerű gépről származó adatok feltöltése Hogyan?**

Használhatja az Azure CLI-t.

- Egyetlen blob letöltése:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Egyetlen blob feltöltése:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hogyan a blobokat az egyik Storage-fiókból a másikba?**

 Ezt a [blob áttelepítési parancsfájl](../scripts/storage-common-transfer-between-storage-accounts.md)használatával teheti meg.
 
## <a name="migration-or-backup"></a>Áttelepítés vagy biztonsági mentés

**Hogyan az adatok áthelyezését az egyik tárolóból a másikba?**

Kövesse az alábbi lépéseket:

1.  Hozza létre a tárolót (mappát) a cél blobban.

2.  A [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) használatával másolja át az eredeti blob-tároló tartalmát egy másik blob-tárolóba.

**Hogyan PowerShell-szkriptet létrehozni egy Azure-fájlmegosztás adatainak egy másikba való áthelyezéséhez az Azure Storage-ban?**

A AzCopy használatával áthelyezheti az Azure-fájlmegosztás adatait egy másikba az Azure Storage-ban. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan nagyméretű. csv-fájlokat tölthet fel az Azure Storage-ba?**

Nagyméretű. CSV fájlok feltöltése az Azure Storage-ba a AzCopy használatával. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Naponta át kell helyezni a naplókat a D meghajtóról az Azure Storage-fiókjába. Hogyan automatizálja ezt?**

Használhatja a AzCopy, és létrehozhat egy feladatot a Feladatütemezőben. Fájlokat tölthet fel egy Azure Storage-fiókba egy AzCopy Batch-parancsfájl használatával. További információ: a [felhőalapú szolgáltatás indítási feladatainak konfigurálása és futtatása](../../cloud-services/cloud-services-startup-tasks.md).

**Hogyan helyezze át a Storage-fiókomat az előfizetések között?**

A AzCopy használatával áthelyezheti a Storage-fiókot az előfizetések között. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetek át körülbelül 10 TB-nyi adat egy másik régióban található tárterületre?**

Az AzCopy használatával helyezze át az adatátvitelt. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan másolhatok az adatok a helyszínről az Azure Storage szolgáltatásba?**

Az adatmásoláshoz használja a AzCopy. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetők át a helyszíni adatok a Azure Filesba?**

Az AzCopy használatával helyezze át az adatátvitelt. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan felügyelt lemezeket áthelyezni egy másik Storage-fiókba?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kövesse az alábbi lépéseket:

1.  Állítsa le azt a virtuális gépet, amelyhez a felügyelt lemez csatlakoztatva van.

2.  Másolja a felügyelt lemez virtuális merevlemezét az egyik helyről a másikra a következő Azure PowerShell parancsfájl futtatásával:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Hozzon létre egy felügyelt lemezt a VHD-fájl használatával egy másik régióban, ahová a VHD-t másolta. Ehhez futtassa a következő Azure PowerShell parancsfájlt:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

A virtuális gépek felügyelt lemezről történő központi telepítésével kapcsolatos további információkért lásd: [CreateVmFromManagedOsDisk. ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hogyan az adatok áthelyezését vagy letöltését egy Storage-fiókból?**

Az AzCopy használatával töltheti le az adatfájlokat. További információkért lásd: [adatok átvitele Windows AzCopy](storage-use-azcopy.md) és [adatok átvitele a AzCopy-mel Linuxon](storage-use-azcopy-linux.md).

**Hogyan a Premium Storage-fiókból a standard Storage-fiókba?**

Kövesse az alábbi lépéseket:

1.  Hozzon létre egy szabványos Storage-fiókot. (Vagy használjon egy meglévő standard Storage-fiókot az előfizetésében.)

2.  Töltse le a AzCopy. Futtassa az alábbi AzCopy-parancsok egyikét.

    Teljes lemezek másolása a Storage-fiókba:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Csak egy lemez másolásához adja meg a lemez nevét a következő **mintában**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

A művelet több órát is igénybe vehet.

Győződjön meg arról, hogy az átvitel sikeresen befejeződött, vizsgálja meg a cél Storage-fiók tárolóját a Azure Portal. Miután a lemezeket átmásolta a standard Storage-fiókba, meglévő lemezként csatolhatja őket a virtuális géphez. További információ: [felügyelt adatlemez csatolása Windows rendszerű virtuális géphez a Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Hogyan egy klasszikus Storage-fiókból egy Azure Resource Manager Storage-fiókba?**

Használhatja a **Move-AzureStorageAccount** parancsmagot. Ez a parancsmag több lépésből áll (érvényesítés, előkészítés, véglegesítés). A mozgatás előtt ellenőrizheti az áthelyezést.

Ha virtuális gépekkel rendelkezik, további lépéseket kell végrehajtania a Storage-fiókra vonatkozó adatáttelepítés előtt. További információ: [IaaS-erőforrások migrálása klasszikusról Azure Resource Managerra Azure PowerShell használatával](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hogyan a teljes Storage-fiókomat egy másik Storage-fiókra?**

Nincs lehetőség a teljes Storage-fiók biztonsági mentésére közvetlenül. A tárolót manuálisan is áthelyezheti egy másik fiókba a AzCopy vagy a Storage Explorer használatával. A következő lépések bemutatják, hogyan helyezheti át a tárolót a AzCopy használatával:  

1.  Telepítse a [AzCopy](storage-use-azcopy.md) parancssori eszközt. Az eszköz segítségével áthelyezheti a VHD-fájlt a Storage-fiókok között.

2.  Miután telepítette a AzCopy a Windows rendszerre a telepítővel, nyisson meg egy parancssori ablakot, és keresse meg a AzCopy telepítési mappáját a számítógépen. Alapértelmezés szerint a AzCopy a következőre van telepítve: **% ProgramFiles (x86)% \ Microsoft SDKs\Azure\AzCopy** vagy **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Futtassa a következő parancsot a tároló áthelyezéséhez. A szöveget a tényleges értékekkel kell helyettesítenie.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Adja meg a forrásként szolgáló Storage-fiók URI-JÁT (a tárolóig).  
    - `/Dest`: Adja meg a célként megadott Storage-fiók URI-JÁT (a tárolóig).  
    - `/SourceKey`: Adja meg a forrásként szolgáló Storage-fiók elsődleges kulcsát. Ezt a kulcsot a Azure Portal a Storage-fiók kiválasztásával másolhatja.  
    - `/DestKey`: Adja meg a cél Storage-fiók elsődleges kulcsát. A kulcsot a portálról másolhatja, ha kiválasztja a Storage-fiókot.

A parancs futtatása után a rendszer áthelyezi a tároló fájljait a célként megadott Storage-fiókba.

> [!NOTE]
> Az AzCopy parancssori felület nem működik együtt a **minta** kapcsolóval, amikor egy Azure-blobból egy másikba másol.
>
> Közvetlenül is másolhatja és szerkesztheti a AzCopy parancsot, és megvizsgálhatja, hogy a **minta** megfelel-e a forrásnak. Győződjön meg arról is, hogy a **/s** helyettesítő karakterek vannak érvényben. További információ: [AzCopy Parameters](storage-use-azcopy.md).

**Hogyan az Azure file Storage biztonsági mentését?**

Nincs biztonsági mentési megoldás. A Azure Files azonban támogatja az aszinkron másolást is. A fájlok másolásához a következőket teheti:

- Egy megosztásból egy másik megosztásba egy Storage-fiókon belül vagy egy másik Storage-fiókban.

- Egy megosztásból egy blob-tárolóba egy Storage-fiókban vagy egy másik Storage-fiókban.

További információ: [adatok átvitele a AzCopy Windows rendszeren](storage-use-azcopy.md).
## <a name="configuration"></a>Configuration

**Hogyan módosítja a másodlagos helyet a Storage-fiókhoz tartozó Európa régióba?**

A Storage-fiók létrehozásakor ki kell választania a fiók elsődleges régióját. A másodlagos régió kiválasztása az elsődleges régión alapul, és nem módosítható. További információ [: Geo-redundáns tárolás (GRS): régiók közötti replikáció az Azure Storage-](storage-redundancy.md)hoz.

**Hol kaphatok további információt az Azure Storage Service Encryption (SSE) szolgáltatásról?**  

Lásd az alábbi cikkeket:

-  [Biztonsági útmutató az Azure Storage-hoz](../blobs/security-recommendations.md)

-  [Azure-Storage Service Encryption a nyugalmi állapotban lévő adatokhoz](storage-service-encryption.md)

**Hogyan egy Storage-fiókban tárolt adattitkosítást?**

Miután engedélyezte a titkosítást egy Storage-fiókban, a meglévő adatforgalom nincs titkosítva. A meglévő adatok titkosításához újra fel kell töltenie a Storage-fiókba.

Az AzCopy használatával másolja át az adatfájlokat egy másik Storage-fiókba, majd helyezze vissza az adatátvitelt. Használhatja [a titkosítást is a REST](storage-service-encryption.md)-ben.

**Van-e előfeltétele a Storage-fiókoknak a Geo-redundáns tárterületről a helyileg redundáns tárterületre való replikálásának módosításához?**

Nem.

**Hogyan konvertálni az Azure Premium Storage-ra egy fájlmegosztást?**

Premium Storage Azure-fájlmegosztás esetében nem engedélyezett.

**Hogyan a standard Storage-fiókból a Premium Storage-fiókra való frissítést? Hogyan a Premium Storage-fiókból a standard Storage-fiókra való visszalépést?**

Létre kell hoznia a cél Storage-fiókot, át kell másolnia az adatait a forrás fiókból a célkiszolgálóra, majd törölnie kell a fiókot. Az adatmásoláshoz olyan eszközt is használhat, mint például a AzCopy.

Ha virtuális gépekkel rendelkezik, további lépéseket kell végrehajtania a Storage-fiókra vonatkozó adatáttelepítés előtt. További információ: [áttelepítés az Azure Premium Storageba (nem felügyelt lemezek)](storage-migration-to-premium-storage.md).

**Hogyan biztosíthatok más személyeket a tárhely erőforrásaihoz?**

Más személyek számára a tárolási erőforrásokhoz való hozzáférés biztosítása:

-   Egy megosztott hozzáférés-aláírási (SAS-) token használatával biztosíthatja az erőforrásokhoz való hozzáférést.

-   Adjon meg egy felhasználót a Storage-fiók elsődleges vagy másodlagos kulcsával. További információ: a [Storage-fiók elérési kulcsainak kezelése](storage-account-keys-manage.md).

-   Módosítsa a hozzáférési házirendet a névtelen hozzáférés engedélyezéséhez. További információ: [névtelen felhasználók engedélyeinek engedélyezése tárolók és Blobok](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)számára.

**Hol van telepítve a AzCopy?**

-   Ha a Microsoft Azure Storage parancssorból fér hozzá a AzCopy-hoz, írja be a következőt: **AzCopy**. A parancssor a AzCopy együtt települ.

-   Ha telepítette a 32 bites verziót, a következő helyen található: **% ProgramFiles (x86\\)% Microsoft SDK\\-k\\Azure AzCopy**.

-   Ha telepítette a 64 bites verziót, a következő helyen található: **% ProgramFiles%\\Microsoft\\SDK-k\\Azure AzCopy**.

**Hogyan használhat egy HTTPS-alapú egyéni tartományt a saját Storage-fiókommal? Például Hogyan tehetem meg a "https:\//mystorageaccountname.blob.Core.Windows.net/images/image.gif" kifejezést "https:\//www.contoso.com/images/image.gif" néven?**

A TLS/SSL jelenleg nem támogatott az egyéni tartományokkal rendelkező Storage-fiókok esetében.
A nem HTTPS-alapú egyéni tartományokat azonban használhatja. További információ: [Egyéni tartománynév beállítása a blob Storage-végponthoz](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Hozzáférés a tárolóhoz

**Hogyan leképez egy tároló mappát egy virtuális gépen?**

Azure-fájlmegosztás használata.

**Hogyan fér hozzá Azure Files redundáns tárolóhoz?**

A redundáns tárterület eléréséhez olvasási hozzáférésű geo-redundáns tárterület szükséges. A Azure Files azonban csak a helyileg redundáns tárolást és a szabványos, csak olvasási hozzáférést nem engedélyező földrajzi redundáns tárolást támogatja.

**A replikált Storage-fiókhoz (például a zóna-redundáns tároláshoz, a Geo-redundáns tároláshoz vagy az olvasási hozzáférésű geo-redundáns tároláshoz), hogyan férhetnek hozzá a másodlagos régióban tárolt adatszolgáltatásokhoz?**

-   Ha zóna-redundáns tárolást vagy földrajzilag redundáns tárolást használ, nem férhet hozzá az adatokhoz a másodlagos régióból, kivéve, ha feladatátvételt kezdeményez az adott régióban. A feladatátvételi folyamattal kapcsolatos további információkért lásd: vész- [helyreállítási és Storage-fiók feladatátvétele (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

-   Ha olvasási hozzáférésű geo-redundáns tárolót használ, bármikor elérheti a másodlagos régió adatait. Használja az alábbi módszerek egyikét:  

    - **AzCopy**: Hozzáfűzés **– másodlagos** a Storage-fiók neve az URL-címben a másodlagos végpont eléréséhez. Például:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Sas-jogkivonat**: a végpontról származó adatok eléréséhez használjon sas-tokent. További információ: [közös hozzáférésű aláírások használata](storage-sas-overview.md).

**A Hogyan FTP használatával fér hozzá a Storage-fiókban lévő adatszolgáltatásokhoz?**

Nem lehet közvetlenül hozzáférni egy Storage-fiókhoz az FTP használatával. Beállíthat azonban egy Azure-beli virtuális gépet, majd telepíthet egy FTP-kiszolgálót a virtuális gépre. Az FTP-kiszolgáló a fájlokat egy Azure Files megosztáson vagy a virtuális gép számára elérhető adatlemezen is tárolhatja.

Ha azt szeretné, hogy csak Storage Explorer vagy hasonló alkalmazás használata nélkül töltse le az adatletöltést, lehetséges, hogy SAS-tokent használ. További információ: [közös hozzáférésű aláírások használata](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
