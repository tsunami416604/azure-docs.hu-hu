---
title: Az Azure Storage migrálásával kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Áttelepítése az Azure Storage szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: cf1cba6f6d26d66fc560c86ea42459fa276cc880
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310846"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Az Azure-tárterület-áttelepítés – gyakran ismételt kérdések

Ez a cikk az Azure-tárterület-áttelepítés kapcsolatos általános kérdéseket válaszol. 

## <a name="faq"></a>GYIK

**Hogyan hozhatok létre egy parancsfájlt, hogy fájlokat másoljon egy tárolót a másikba?**

Másolja a fájlokat a tárolók között, az AzCopy is használhatja. Lásd a következő példát:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Az AzCopy használja a [másolás Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) minden egyes fájl másolása a tárolóban.  
  
Minden virtuális gép vagy a helyi gépen, amelyen internet-hozzáférés AzCopy futtatásához használhatja. Egy Azure Batch ütemezési automatikusan ehhez is használhatja, de több bonyolult.  
  
Az automation-szkript az Azure Resource Manager üzembe helyezési helyett tárolási tartalom kezelésének lett tervezve. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md).

**Van valamilyen díja az adott tárfiók ugyanabban a régióban található két fájlmegosztások közötti másolást?**

Nem. Nem jár ez a folyamat.

**Hogyan készíthetek biztonsági másolatot egy másik tárfiókba a teljes storage-fiókomat?**

Nincs biztonsági mentése egy teljes tárfiókot közvetlenül lehetőség. De, manuálisan áthelyezheti a tárolóhoz a tárfiók egy másik fiókba az AzCopy és a Storage Explorer használatával. A következő lépések bemutatják, hogyan a tároló áthelyezése az AzCopy használata:  
 

1.  Telepítse a [AzCopy](storage-use-azcopy.md) parancssori eszköz. Ez az eszköz segít a VHD-fájl áthelyezése a storage-fiókok között.

2.  Miután a telepítő használatával telepít Windows AzCopy, nyisson meg egy parancssori ablakot, és keresse meg az AzCopy telepítési mappát a számítógépén. Alapértelmezés szerint az AzCopy telepítve van a **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** vagy **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Futtassa a következő parancsot a tároló áthelyezéséhez. A szöveg a tényleges értékekre kell cserélnie.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Adja meg az URI-t a forrás tárfiókban (akár a tároló).  
    - `/Dest`: Adja meg az URI-t a céloldali tárfiók (akár a tároló).  
    - `/SourceKey`: Adja meg a forrás tárfiókban elsődleges kulcsát. Ezt a kulcsot az Azure Portalról másolhatja a storage-fiók kiválasztásával.  
    - `/DestKey`: Adja meg a célként megadott tárfiók elsődleges kulcsát. Ezt a kulcsot a portálról másolhatja a storage-fiók kiválasztásával.

Ez a parancs futtatása után a tároló fájlokat áthelyezik a célként megadott tárfiók.

> [!NOTE]
> Az AzCopy parancssori felület nem működik együtt a **minta** váltani, ha másol egy Azure blob egy másikba.
>
> Közvetlenül másolása és szerkesztheti az AzCopy-parancsot, és győződjön meg arról, hogy összevetéséhez **minta** megegyezik a forrás. Győződjön meg arról is, amely **/S** helyettesítő akkor vannak érvényben. További információkért lásd: [AzCopy paraméterek](storage-use-azcopy.md).

**Hogyan helyezhetek át adatokat egy storage-tárolóból egy másikra?**

Kövesse az alábbi lépéseket:

1.  Hozzon létre a tároló (mappa) a cél blob.

2.  Használat [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) , másolja a tartalmat az eredeti blob-tárolóból egy másik blob-tárolóba.

**Hogyan hozhatok létre egy PowerShell-parancsprogram adatok áthelyezése egy Azure-fájlmegosztásról egy másikra, az Azure Storage-ban?**

Az AzCopy segítségével az adatok áthelyezése egy Azure-fájlmegosztás között az Azure Storage-ban. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan tölthetek nagy .csv fájlt az Azure Storage?**

Az AzCopy használatával nagy .csv-fájlok feltöltése az Azure Storage. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Történő áthelyezéséhez a naplókat az Azure storage-fiókom naponta D meghajtón van. Hogyan ezt automatizálni?**

Az AzCopy használata, és hozzon létre egy feladatot a Feladatütemező. Fájlok feltöltése Azure storage-fiók egy AzCopy batch parancsfájl használatával. További információkért lásd: [konfigurálása és a egy felhőalapú szolgáltatás indítási feladatok futtatásának](../../cloud-services/cloud-services-startup-tasks.md).

**Hogyan áthelyezése előfizetések között a storage-fiókomat?**

Az AzCopy segítségével a storage-fiók áthelyezése előfizetések között. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan helyezhetem át 10 TB-nyi adat egy másik régióban a Storage?**

Az adatok áthelyezése az AzCopy használata. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan tudok másolhat adatokat a helyszínről az Azure Storage?**

Az AzCopy használatával másolja az adatokat. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan helyezhetem át adatokat a helyszínről az Azure Files?**

Adatok áthelyezése az AzCopy használata. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan rendelhetek egy tároló mappát egy virtuális gépen?**

Azure-fájlmegosztás használja.

**Hogyan készíthetek biztonsági másolatot az Azure file storage?**

Nincs biztonsági mentési megoldás nincs. Azonban az Azure Files támogatja aszinkron példányt is. Tehát másolhatja a fájlokat:

- A megosztás a tárfiókon belül egy másik megosztást vagy egy másik tárfiókba.

- A megosztás blob-tárolóba a tárfiókon belül vagy egy másik tárfiókba.

További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md).

**Hogyan helyezhetek át a felügyelt lemezek egy másik tárfiókba?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kövesse az alábbi lépéseket:

1.  Állítsa le a virtuális gép, amely a felügyelt lemez csatolva van.

2.  Másolja a felügyelt lemezt VHD-t egy területről egy másikra a következő Azure PowerShell-szkript futtatásával:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Felügyelt lemez létrehozása VHD-fájl egy másik régióban, amelybe a virtuális merevlemez használatával. Ehhez futtassa a következő Azure PowerShell-parancsfájlt:  

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

Egy felügyelt lemezt egy virtuális gép üzembe helyezésével kapcsolatos további információkért lásd: [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hogyan is 1 – 2 TB adat letöltése az Azure Portalon?**

Az AzCopy használatával töltse le az adatokat. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).

**Hogyan módosíthatom a másodlagos hely az Európai régióban a storage-fiók?**

Amikor létrehoz egy tárfiókot, válassza ki az elsődleges régió a fiókhoz. A másodlagos régió kiválasztása az elsődleges régió alapján, és nem módosítható. További információkért lásd: [georedundáns tárolás (GRS): Az Azure Storage-régiók közti replikációs](storage-redundancy.md).

**Hol kaphatok további információt az Azure Storage Service Encryption (SSE)?**  
  
Lásd az alábbi cikkeket:

-  [Biztonsági útmutató az Azure Storage-hoz](storage-security-guide.md)

-  [Az Azure Storage Service Encryption for Data at Rest](storage-service-encryption.md)

**Hogyan áthelyezése vagy adatokat letölteni a storage-fiók?**

Az AzCopy használatával töltse le az adatokat. További információkért lásd: [adatok áthelyezése az AzCopy Windows rendszeren](storage-use-azcopy.md) és [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md).


**Hogyan titkosíthatom az adatok a storage-fiókban?**

Miután engedélyezte a storage-fiókban lévő titkosítási, a meglévő adatok nem titkosítottak. A meglévő adatok titkosításához, fel kell tölteni azt újra a storage-fiókba.

Az AzCopy használatával másolja az adatokat egy másik tárfiókot, és ezután helyezze vissza az adatokat. Is [titkosítás inaktív állapotban](storage-service-encryption.md).

**Hogyan tölthetem le egy virtuális Merevlemezt a helyi számítógépre, más, mint a letöltési lehetőséget a portál használatával?**

Használhat [Tártallózó](https://azure.microsoft.com/features/storage-explorer/) virtuális merevlemez letöltésére.

**Van valamilyen előfeltétele a tárfiók replikálásának módosítására a georedundáns tárolás, helyileg redundáns tárolás?**

Nem. 

**Hogyan érhetem el az Azure Files redundáns tárolást?**

Írásvédett georedundáns tárolás redundáns tároló eléréséhez szükséges. Az Azure Files azonban csak a helyileg redundáns tárolás és a standard georedundáns tárolás, amely nem engedélyezi a csak olvasható hozzáférést támogatja. 

**Hogyan helyezhetek át egy prémium szintű storage-fiókból, Standard szintű tárfiókot?**

Kövesse az alábbi lépéseket:

1.  Hozzon létre egy standard szintű tárfiókot. (Vagy használjon egy meglévő standard szintű tárfiókot az előfizetésében.)

2.  Töltse le az AzCopy. Futtassa a következő AzCopy-parancsok egyikét.
      
    Teljes lemezek másolása a tárfiókban:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Csak egy lemez másolása, adja meg a lemez nevét **minta**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
A művelet több órát is igénybe vehet.

Győződjön meg arról, hogy az átvitel sikeresen befejeződött-e, vizsgálja meg a cél tárfiók tárolójának az Azure Portalon. Miután a lemezek a standard szintű tárfiók másolja, csatlakoztathat azokat a virtuális gép egy meglévő lemezként. További információkért lásd: [felügyelt adatlemez csatlakoztatása az Azure Portalon Windows virtuális gép](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Hogyan tudok áttérni az Azure Premium Storage egy fájlmegosztás?**

A Premium Storage az Azure-fájlmegosztások nem engedélyezett.

**Hogyan frissíthetek standard szintű tárfiók a premium storage-fiók? Hogyan do I visszaléphetnek premium storage-fiók egy standard szintű tárfiókot?**

Kell a cél tárfiók létrehozásához, a forrás-fiók adatainak másolása a cél-fiók és törölje a forrás-fiók. Egy eszköz, például az AzCopy segítségével másolja az adatokat.

Ha a virtuális gépekkel rendelkezik, a storage-fiók adatok áttelepítése előtt további lépéseket kell végrehajtani. További információkért lásd: [áttelepítése az Azure Premium Storage (nem felügyelt lemezek)](storage-migration-to-premium-storage.md).

**Hogyan helyezhetek át klasszikus tárfiók a storage-fiókba az Azure Resource Manager?**

Használhatja a **Move-AzStorageAccount** parancsmagot. Ez a parancsmag több lépésből áll (érvényesítéséhez, előkészítése, véglegesítése). Az áthelyezés előtt ellenőrizheti.

Ha a virtuális gépekkel rendelkezik, a storage-fiók adatok áttelepítése előtt további lépéseket kell végrehajtani. További információkért lásd: [át IaaS-erőforrások klasszikusból Azure Resource Manager Azure PowerShell-lel](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hogyan adatokat letölteni egy Linux-alapú számítógépre egy Azure storage-fiókot, vagy feltölthet egy Linux-gép adatokat?**

Az Azure CLI-vel is használhatja.

- Egy blob letöltése:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Egy blob feltöltése: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hogyan tudok mások hozzáférést biztosíthat a tárolási erőforrások?**

A más hozzáférést biztosít a tárolási erőforrások:

-   Egy közös hozzáférésű jogosultságkód (SAS) használatával adja meg az erőforrásokhoz való hozzáférést. 

-   Adjon egy felhasználónak a tárfiókhoz az elsődleges vagy másodlagos kulcsot. További információkért lásd: [a tárfiók kezelése](storage-account-manage.md#access-keys).

-   Névtelen hozzáférés engedélyezése a hozzáférési szabályzatot módosíthatja. További információkért lásd: [tárolókhoz és blobokhoz való névtelen felhasználók engedélyek](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Az AzCopy telepítési helyéül?**

-   Ha az AzCopy éri el a Microsoft Azure Storage-parancssorból, írja be a **AzCopy**. A parancssor AzCopy együtt települ.

-   Ha a 32 bites verziója telepítve van itt található: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Ha telepítette a 64 bites verziója van itt található: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**A replikált storage-fiókok (például a zónaredundáns tárolás, georedundáns tárolás vagy írásvédett georedundáns tárolás) hogyan férhetek hozzá a másodlagos régió tárolt adatokat?**

-   Zónaredundáns tárolás vagy georedundáns tárolást használ, ha nem érhetők el adatok a másodlagos régióból, kivéve, ha egy adott régióban feladatátvételt kezdeményez. A feladatátvételi folyamattal kapcsolatos további információkért lásd: [katasztrófa utáni helyreállítás és a tárolási fiók feladatátvételi (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

-   Ha írásvédett georedundáns tárolás használata esetén férhet hozzá adataihoz a másodlagos régióból bármikor. Az alábbi módszerek valamelyikével:  
      
    - **Az AzCopy**: Hozzáfűzés **– másodlagos** , a tárfiók nevét a másodlagos végpont elérésére az URL-címben. Példa:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-jogkivonat**: SAS-jogkivonat használatával érheti el adatait a végpontról. További információkért lásd: [a közös hozzáférésű jogosultságkódot](storage-dotnet-shared-access-signature-part-1.md).

**Hogyan egy HTTPS-egyéni tartomány használata a storage-fiókomat? Például Hogyan ellenőrizhetem, "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" jelennek meg "https:\//www.contoso.com/images/image.gif"?**

Az SSL jelenleg nem támogatott az egyéni tartományok a storage-fiókok.
De egyéni tartományokat nem-HTTPS használható. További információkért lásd: [egy egyéni tartománynév beállítása a Blob storage-végponthoz](../blobs/storage-custom-domain-name.md).

**Hogyan használhatom az FTP, amely egy storage-fiókban lévő adatok elérését?**

Nincs lehetőség a FTP használatával közvetlenül a tárfiók eléréséhez. Azonban egy Azure virtuális gép beállításához, és telepítse az FTP-kiszolgáló a virtuális gépen. Az FTP-kiszolgáló egy Azure-fájlmegosztással vagy egy adatlemezt a virtuális gép számára elérhető fájlok tárolására is rendelkezhet.

Ha azt szeretné, csak az adatok letöltése a Storage Explorerben vagy más hasonló alkalmazás használata nélkül, lehet használni egy SAS-tokent. További információkért lásd: [a közös hozzáférésű jogosultságkódot](storage-dotnet-shared-access-signature-part-1.md).

**Hogyan migrálhatok blobokat egyik tárfiókból a másikba?**

 Ehhez használja a [áttelepítési parancsfájl Blob](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
