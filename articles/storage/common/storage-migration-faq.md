---
title: Azure Storage áttelepítésének gyakori kérdések | Microsoft dokumentumok
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
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668551"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Gyakori kérdések az Azure Storage migrálásával kapcsolatban

Ez a cikk az Azure Storage áttelepítésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="copy-upload-or-download"></a>Másolás, feltöltés vagy letöltés

**Hogyan hozhatok létre parancsfájlt a fájlok egyik tárolóból a másikba történő másolásához?**

Fájlok tárolók közötti másolásához használhatja az AzCopy programot. Lásd a következő példát:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzAzCopy a [Blob másolása API-t](https://docs.microsoft.com/rest/api/storageservices/copy-blob) használja a tárolóban lévő egyes fájlok másolásához.  

Az AzCopy futtatásához bármilyen virtuális gép vagy helyi számítógép használható, amely rendelkezik internet-hozzáféréssel. Ehhez automatikusan használhatja az Azure Batch-ütemezést is, de ez bonyolultabb.  

Az automation-parancsfájl az Azure Resource Manager üzembe helyezéséhez készült a tárolási tartalom kezelése helyett. További információ: [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával](../../azure-resource-manager/templates/deploy-powershell.md)című témakörben olvashat.

**Van-e díj az adatok másolása két fájlmegosztások ugyanazon a tárfiókon belül ugyanabban a régióban?**

Nem. A folyamatért nem számítunk fel díjat.

**Hogyan tölthetek le 1-2 TB adatot az Azure Portalról?**

Az adatok letöltéséhez használja az AzCopy programot. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan tölthetek le virtuális merevlemezt egy helyi számítógépre, kivéve a portál letöltési lehetőségének használatával?**

A [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) segítségével virtuális merevlemezt tölthet le.

**Hogyan tölthetek le adatokat Egy Linux-alapú számítógépre egy Azure-tárfiókból, vagy hogyan tölthetek fel adatokat egy Linux-gépről?**

Használhatja az Azure CLI.You can use the Azure CLI.

- Egyetlen blob letöltése:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Egyetlen blob feltöltése:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hogyan telepíthetem át a blobokat egyik tárfiókból a másikba?**

 Ezt a Blob [áttelepítési parancsfájlunk](../scripts/storage-common-transfer-between-storage-accounts.md)használatával teheti meg.
 
## <a name="migration-or-backup"></a>Áttelepítés vagy biztonsági mentés

**Hogyan helyezhetem át az adatokat az egyik tárolóból a másikba?**

Kövesse az alábbi lépéseket:

1.  Hozza létre a tárolót (mappát) a célblobban.

2.  Az [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) segítségével másolja a tartalmat az eredeti blob tárolóból egy másik blob tárolóba.

**Hogyan hozhatok létre egy PowerShell-parancsfájlt az adatok egyik Azure-fájlmegosztásból a másikba való áthelyezéséhez az Azure Storage-ban?**

Az AzCopy használatával áthelyezheti az adatokat az egyik Azure-fájlmegosztásról egy másikra az Azure Storage-ban. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan tölthetek fel nagy .csv fájlokat az Azure Storage-ba?**

Az AzCopy használatával nagy .csv fájlokat tölthet fel az Azure Storage-ba. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**A naplókat a D meghajtóról minden nap át kell helyeznem az Azure-tárfiókomba. Hogyan automatizálhatom ezt?**

Az AzCopy segítségével a Feladatütemezőben létrehozhat egy feladatot. Fájlok feltöltése egy Azure-tárfiókba egy AzCopy kötegelt parancsfájl használatával. További információt a [Felhőszolgáltatások indítási feladatainak konfigurálása és futtatása](../../cloud-services/cloud-services-startup-tasks.md)című témakörben talál.

**Hogyan helyezhetem át a tárfiókot az előfizetések között?**

Az AzCopy használatával áthelyezheti a tárfiókot az előfizetések között. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetek át körülbelül 10 TB adatot egy másik régióban tárolt tárhelyre?**

Az adatok áthelyezéséhez használja az AzCopy programot. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan másolhatok adatokat a helyszíni Azure Storage-ba?**

Az adatok másolásához az AzCopy segítségével másolja a programot. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetem át az adatokat a helyszíni Azure Files?**

Az Adatok áthelyezéséhez használja az AzCopy programot. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetem át a felügyelt lemezeket egy másik tárfiókba?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kövesse az alábbi lépéseket:

1.  Állítsa le azt a virtuális gépet, amelyhez a felügyelt lemez csatlakozik.

2.  Másolja a felügyelt lemez virtuális merevlemezét egyik területről a másikra a következő Azure PowerShell-parancsfájl futtatásával:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Felügyelt lemez létrehozása a vhd fájl használatával egy másik régióban, amelyre a virtuális merevlemezt másolta. Ehhez futtassa a következő Azure PowerShell-parancsfájlt:  

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

A virtuális gépek felügyelt lemezről történő központi telepítéséről a [CreateVmFromManagedOsDisk.ps1 című témakörben talál](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1)további információt.

**Hogyan helyezhetek át vagy tölthetek le adatokat egy tárfiókból?**

Az adatok letöltéséhez használja az AzCopy programot. További információ: [Adatok átvitele az AzCopy segítségével Windows rendszeren](storage-use-azcopy.md) és [Adatátvitel az AzCopy segítségével Linuxon](storage-use-azcopy-linux.md).

**Hogyan helyezhetek át prémium szintű tárfiókról normál tárfiókra?**

Kövesse az alábbi lépéseket:

1.  Hozzon létre egy szabványos tárfiókot. (Vagy használjon egy meglévő normál tárfiókot az előfizetésben.)

2.  Letöltés AzCopy. Futtassa az alábbi AzCopy parancsok egyikét.

    Teljes lemezek másolása a tárfiókba:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Ha csak egy lemezt szeretne másolni, adja meg a lemez nevét a **Minta alkalmazásban:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

A művelet befejezése több órát is igénybe vehet.

Győződjön meg arról, hogy az átvitel sikeresen befejeződött, vizsgálja meg a céltárfiók tárolóját az Azure Portalon. Miután a lemezeket a szabványos tárfiókba másolta, meglévő lemezként csatolhatja őket a virtuális géphez. További információ: [Felügyelt adatlemez csatolása Windows-alapú virtuális géphez az Azure Portalon.](../../virtual-machines/windows/attach-managed-disk-portal.md)  

**Hogyan helyezhetek át egy klasszikus tárfiókról egy Azure Resource Manager-tárfiókra?**

Használhatja a **Move-AzureStorageAccount** parancsmag. Ez a parancsmag több lépésből áll (érvényesítés, előkészítés, véglegesítés). Mielőtt megcsinálna, érvényesítheti az áthelyezést.

Ha virtuális gépekkel rendelkezik, további lépéseket kell tennie a tárfiók adatainak áttelepítése előtt. További információ: [IaaS-erőforrások áttelepítése klasszikusról az Azure Resource Manager az Azure PowerShell használatával.](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)

**Hogyan tudok biztonsági másolatot tenni a teljes tárfiókomról egy másik tárfiókba?**

Nincs lehetőség közvetlenül egy teljes tárfiók biztonsági másolatot. De manuálisan áthelyezheti a tárolót a tárfiókban egy másik fiókba az AzCopy vagy a Storage Explorer használatával. A következő lépések bemutatják, hogyan helyezheti át az AzCopy programot a tároló:  

1.  Telepítse az [AzCopy](storage-use-azcopy.md) parancssori eszközt. Ez az eszköz segít a VHD-fájl tárfiókok közötti áthelyezésében.

2.  Miután telepítette az AzCopy alkalmazást a Windows rendszeren a telepítő vel, nyisson meg egy parancssori ablakot, majd keresse meg a számítógépen található AzCopy telepítési mappát. Alapértelmezés szerint az AzCopy a **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** vagy **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**mappába van telepítve.

3.  A tároló áthelyezéséhez futtassa a következő parancsot. A szöveget a tényleges értékekre kell cserélnie.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Adja meg a forrástárfiók URI-ját (a tárolóig).  
    - `/Dest`: Adja meg az URI-t a céltárfiókhoz (a tárolóig).  
    - `/SourceKey`: Adja meg a forrástárfiók elsődleges kulcsát. Ezt a kulcsot az Azure Portalról másolhatja a tárfiók kiválasztásával.  
    - `/DestKey`: Adja meg az elsődleges kulcsot a céltárfiókhoz. Ezt a kulcsot a portálról a tárfiók kiválasztásával másolhatja.

A parancs futtatása után a tárolófájlok átkerülnek a céltárfiókba.

> [!NOTE]
> Az AzCopy CLI nem működik együtt a **minta** kapcsolóval, amikor egyik Azure-blobból a másikba másol.
>
> Közvetlenül másolhatja és szerkesztheti az AzCopy parancsot, és átveheti a ellenőrzést, hogy a **Minta** megegyezik-e a forrással. Győződjön meg arról is, hogy **a /S** helyettesítő karakterek vannak érvényben. További információ: [AzCopy parameters](storage-use-azcopy.md).

**Hogyan tudok biztonsági másolatot tenni az Azure-fájltárolásról?**

Nincs biztonsági mentési megoldás. Azonban az Azure Files is támogatja az aszinkron másolás. Így a fájlok másolása:

- Egy megosztásról egy tárfiókon belüli másik megosztásra vagy egy másik tárfiókba.

- Egy megosztásról egy blob tároló egy tárfiókon belül, vagy egy másik tárfiókba.

További információt az [Adatok átvitele az AzCopy programmal Windows rendszeren című témakörben talál.](storage-use-azcopy.md)
## <a name="configuration"></a>Konfiguráció

**Hogyan tudom megváltoztatni a másodlagos helyet az Európa régióban egy tárfiók?**

Amikor létrehoz egy tárfiókot, válassza ki a fiók elsődleges régióját. A másodlagos régió kiválasztása az elsődleges régión alapul, és nem módosítható. További információ: [Georedundáns tárolás (GRS): Az Azure Storage régiók közötti replikációja.](storage-redundancy.md)

**Hol kaphatok további információt az Azure Storage Service Encryption (SSE) szolgáltatásról?**  

Lásd az alábbi cikkeket:

-  [Az Azure Storage biztonsági útmutatója](../blobs/security-recommendations.md)

-  [Az Azure Storage Service titkosítása inaktív adatokhoz](storage-service-encryption.md)

**Hogyan titkosíthatom az adatokat egy tárfiókban?**

Miután engedélyezte a titkosítást egy tárfiókban, a meglévő adatok nem lesznek titkosítva. A meglévő adatok titkosításához újra fel kell töltenie azadatokat a tárfiókba.

Az AzCopy segítségével másolja az adatokat egy másik tárfiókba, majd helyezze vissza az adatokat. Inaktív [titkosítást](storage-service-encryption.md)is használhat.

**Vannak-e előfeltételei a tárfiók replikációjának georedundáns tárolásról helyileg redundáns tárolásra történő módosítására?**

Nem.

**Hogyan konvertálhatok Azure Premium Storage-ra egy fájlmegosztáshoz?**

Prémium szintű storage nem engedélyezett az Azure-fájlmegosztás.

**Hogyan frissíthetek egy normál tárfiókról prémium szintű tárfiókra? Hogyan válthatok le prémium szintű tárfiókról normál tárfiókra?**

Létre kell hoznia a céltárfiókot, adatokat kell másolnia a forrásfiókból a célfiókba, majd törölnie kell a forrásfiókot. Az adatok másolásához az AzCopy eszköz is használható.

Ha virtuális gépekkel rendelkezik, további lépéseket kell tennie a tárfiók adatainak áttelepítése előtt. További információ: [Áttelepítés az Azure Premium Storage -ba (nem felügyelt lemezek)](storage-migration-to-premium-storage.md).

**Hogyan adhatok hozzáférést másoknak a tárolóerőforrásaimhoz?**

Ahhoz, hogy mások is hozzáférhessenek a tárolóerőforrásokhoz:

-   Használjon egy közös hozzáférésű aláírás (SAS) jogkivonatot az erőforráshoz való hozzáférés biztosításához.

-   Adja meg a felhasználó a tárfiók elsődleges vagy másodlagos kulcsát. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](storage-account-keys-manage.md)

-   Módosítsa a hozzáférési szabályzatot a névtelen hozzáférés engedélyezéséhez. További információ: [Névtelen felhasználók engedélyeinek megadása tárolókhoz és blobokhoz című témakörben.](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)

**Hol van telepítve az AzCopy?**

-   Ha az AzCopy programot a Microsoft Azure Storage parancssorából éri el, írja be az **AzCopy parancsot.** A parancssor az AzCopy mellett van telepítve.

-   Ha telepítette a 32 bites verziót, az itt található: **%ProgramFiles(x86)%\\Microsoft SDKS\\Azure\\AzCopy**.

-   Ha telepítette a 64 bites verziót, az itt található: **%ProgramFiles%\\Microsoft\\SDKs Azure\\AzCopy**.

**Hogyan használhatok HTTPS-es egyéni tartományt a tárfiókommal? Például hogyan tudom a "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" szót\/"https: /www.contoso.com/images/image.gif" néven megjeleníteni?**

TLS/SSL jelenleg nem támogatott az egyéni tartományoktárfiókok.
De használhatja a nem HTTPS-es egyéni tartományokat. További információt a [Blob storage-végpont egyéni tartománynevének konfigurálása](../blobs/storage-custom-domain-name.md)című témakörben talál.

## <a name="access-to-storage"></a>A tároláshoz való hozzáférés

**Hogyan tudok leképezni egy tárolómappát egy virtuális gépen?**

Használjon Azure-fájlmegosztást.

**Hogyan érhetem el az Azure Files redundáns tárolást?**

A redundáns tárolás eléréséhez olvasási hozzáférésű georedundáns tárolás szükséges. Az Azure Files azonban csak helyileg redundáns tárolást és szabványos georedundáns tárolást támogat, amely nem teszi lehetővé az írásvédett hozzáférést.

**Replikált tárfiók (például zónaredundáns tárolás, georedundáns tárolás vagy olvasási hozzáférésű georedundáns tárolás) esetén hogyan férhetek hozzá a másodlagos régióban tárolt adatokhoz?**

-   Zónaredundáns tárolás vagy georedundáns tárolás használata esetén nem férhet hozzá a másodlagos régióból származó adatokhoz, hacsak nem kezdeményez feladatátvételt az adott régióba. A feladatátvételi folyamatról további információt az [Azure Storage vész-helyreállítási és tárfiók-feladatátvételi (előzetes verzió) című témakörben talál.](storage-disaster-recovery-guidance.md)

-   Ha olvasási hozzáférésű georedundáns tárolást használ, bármikor hozzáférhet a másodlagos régióból származó adatokhoz. Használja az alábbi módszerek egyikét:  

    - **AzCopy**: **Fűzze hozzá -másodlagos** a tárfiók nevét az URL-cím eléréséhez a másodlagos végpont eléréséhez. Példa:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-jogkivonat:** SAS-jogkivonat használatával a végpontból származó adatok eléréséhez. További információt a Megosztott hozzáférésű aláírások használata című [témakörben talál.](storage-sas-overview.md)

**Hogyan érhetem el az FTP-t a tárfiókban lévő adatok eléréséhez?**

A tárfiók közvetlen elérése ftp használatával nem érhető el. Azonban beállíthat egy Azure virtuális gépet, majd telepíthet egy FTP-kiszolgálót a virtuális gépen. Az FTP-kiszolgáló tárolja a fájlokat egy Azure Files megosztáson vagy egy adatlemezen, amely elérhető a virtuális gép.

Ha csak a Storage Explorer vagy egy hasonló alkalmazás használata nélkül szeretne adatokat letölteni, előfordulhat, hogy sas-jogkivonatot is használhat. További információt a Megosztott hozzáférésű aláírások használata című [témakörben talál.](storage-sas-overview.md)

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
