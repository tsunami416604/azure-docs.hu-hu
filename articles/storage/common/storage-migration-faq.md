---
title: Az Azure tárterület-áttelepítés – gyakori kérdések |} Microsoft Docs
description: Áttelepítése az Azure Storage gyakori kérdésekre adott válaszok
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 89d1a4767c240c7e4fedb9d7ac47d6d4fb0aa737
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Azure tárterület-áttelepítés kapcsolatos gyakori kérdések

Ez a cikk Azure tárterület-áttelepítés kapcsolatos gyakori kérdésekre. 

## <a name="faq"></a>GYIK

**Hogyan hozható létre egy parancsfájlt, amely a fájlok másolása egy tároló a másikra?**

Másolja a fájlokat tároló között, AzCopy is használhat. Tekintse meg a következő példát:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy használja a [másolási Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) minden fájl másolása a tárolóban.  
  
Minden virtuális gép vagy a helyi számítógép AzCopy futtatásához internet-hozzáféréssel rendelkező is használhatja. Egy Azure Batch ütemezést ehhez automatikusan is használhatja, de több bonyolult.  
  
Az automatizálási parancsfájl készült Azure Resource Manager deployment tárolási tartalom kezelése helyett. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../../azure-resource-manager/resource-group-template-deploy.md).

**Van bármilyen díjat a tárfiókon belül ugyanabban a régióban található két fájlmegosztások között az adatok másolásának?**

Nem. Nincs a folyamat nem kell fizetni.

**Hogyan készíthetek biztonsági másolatot a teljes tárfiókot egy másik tárfiókkal?**

Nincs biztonsági mentése egy teljes tárfiókot közvetlenül beállítás. De manuálisan áthelyezheti a tároló, hogy a tárfiók egy másik fiókot AzCopy vagy Tártallózó használatával. A következő lépések bemutatják, hogyan AzCopy használatával helyezze át a tárolót:  
 

1.  Telepítse a [AzCopy](storage-use-azcopy.md) parancssori eszközt. Az eszköz segítségével helyezze át a VHD-fájl tárfiókok között.

2.  Miután a telepítő használatával telepíti a Windows az AzCopy, nyisson meg egy parancssori ablakot, és tallózással keressen meg az AzCopy telepítési mappát a számítógépén. Alapértelmezés szerint AzCopy telepítés **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** vagy **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  A következő paranccsal helyezze át a tárolót. A tényleges értékeket kell cseréli le a szöveget.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Adja meg az URI a forrás tárolási fiók (akár a tároló).  
    - `/Dest`: Adja meg az URI a céloldali tárfiók (akár a tároló).  
    - `/SourceKey`: Adja meg a forrás tárfiók elsődleges kulcsát. Ezt a kulcsot a tárfiók kiválasztásával másolhatja az Azure-portálon.  
    - `/DestKey`: Adja meg a céloldali tárfiók elsődleges kulcsát. Ezt a kulcsot a tárfiók kiválasztásával másolhatja a portálról.

Ez a parancs futtatása után a tároló fájlokat áthelyezik a céloldali tárfiók.

> [!NOTE]
> Az AzCopy parancssori felület nem működik együtt a **mintát** egy Azure-ból másolásakor Váltás másik blob.
>
> Közvetlenül másolása és szerkesztheti az AzCopy parancs, és győződjön meg arról, hogy a keresztellenőrzés **mintát** megegyezik a forrás. Győződjön meg arról is, amely **/S** helyettesítő karakterek vannak érvényben. További információkért lásd: [AzCopy paraméterek](storage-use-azcopy.md).

**Hogyan helyezhető át adatokat a tároló egy másik?**

Kövesse az alábbi lépéseket:

1.  A tároló (mappa) létrehozása a cél BLOB.

2.  Használjon [AzCopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) másolásához az eredeti blob tárolóból egy másik blob-tárolóba.

**Hogyan hozható létre adatok áthelyezése egy Azure fájlmegosztás között, az Azure Storage PowerShell parancsfájlt?**

AzCopy segítségével az adatok áthelyezése egy Azure fájlmegosztás között, az Azure Storage. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan nagy .csv fájlok feltöltése a Azure Storage?**

AzCopy segítségével nagy .csv fájlok feltöltése az Azure Storage. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Történő áthelyezéséhez a naplók az Azure storage-fiókom naponta D meghajtón van. Hogyan ez automatizálni?**

AzCopy használja, és hozzon létre egy feladatot a Feladatütemező. Fájlok feltöltése az Azure storage-fiók egy AzCopy kötegelt parancsfájl használatával. További információkért lásd: [hogyan lehet konfigurálni és futtatni egy felhőalapú szolgáltatás indítási feladatokat](../../cloud-services/cloud-services-startup-tasks.md).

**Hogyan helyezze át a tárfiókot előfizetések között?**

AzCopy használatával helyezze át a tárfiók előfizetések között. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan helyezhető 10 TB-nyi tárhelyre, egy másik régióban adatok?**

AzCopy használatával helyezi át az adatokat. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan tudom átmásolhatja adatokat a helyszíni Azure Storage?**

AzCopy használatával másolja az adatokat. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan helyezhető át adatokat a helyszíni az Azure Fileshoz?**

AzCopy használatával helyezi át az adatokat. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan hozzárendelését a virtuális gépen egy tároló mappát?**

Egy Azure fájlmegosztás használja.

**Hogyan készíthetek biztonsági másolatot az Azure file storage?**

Jelenleg nincs biztonsági mentési megoldás. Azure-fájlok is aszinkron másolási támogatja. Igen másolhatja a fájlokat:

- Egy megosztáson egy tárfiókon belül egy másik megosztott vagy egy másik tárfiókhoz.

- Egy megosztáson egy blob-tárolóba, a tárfiókon belül, vagy egy másik tárfiókhoz.

További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md).

**Hogyan felügyelt lemezek áthelyezése másik tárolási fiókot?**

Kövesse az alábbi lépéseket:

1.  Állítsa le a virtuális gépet, amely a felügyelt lemezes csatlakozik.

2.  Másolja a felügyelt lemezes virtuális Merevlemezt egy területről egy másikra a következő Azure PowerShell-parancsfájl futtatásával:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Hozzon létre egy felügyelt lemezes a VHD-fájlt egy másik régióban, amelybe másolta a virtuális merevlemez használatával. Ehhez futtassa a következő Azure PowerShell-parancsfájlt:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Felügyelt lemezes virtuális gép telepítésével kapcsolatos további információkért lásd: [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hogyan tölthetők le az Azure portálról 1-2 TB adatot?**

AzCopy segítségével töltse le az adatokat. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**A másodlagos hely módosítása a tárfiókon Európa régió**

A storage-fiók létrehozásakor ki kell választania a fiók az elsődleges régióban. A másodlagos régióba kiválasztása az elsődleges régióban alapul, és nem módosítható. További információkért lásd: [georedundáns tárolás (GRS): az Azure Storage kereszt-területi replikációs](storage-redundancy.md).

**Hol kaphatok további információt Azure Storage szolgáltatás titkosítási (SSE)?**  
  
Lásd az alábbi cikkeket:

-  [Biztonsági útmutató az Azure Storage-hoz](storage-security-guide.md)

-  [Az Azure Storage szolgáltatás titkosítási az inaktív adatok](storage-service-encryption.md)

**Hogyan helyezze át vagy adatok letöltése a tárfiókból?**

AzCopy segítségével töltse le az adatokat. További információkért lásd: [adatátvitel az AzCopy Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).


**Hogyan titkosítják a tárfiókban lévő adatokat?**

Miután engedélyezte a tárfiók-titkosításra, a meglévő adatok nem titkosítottak. A meglévő adatok titkosítása, fel kell tölteni azt újra a tárfiókot.

AzCopy segítségével az adatok másolása során eltérő tárfiók, és ezután helyezze vissza az adatokat. Is [titkosítását](storage-service-encryption.md).

**Hogyan tölthetők le a VHD-t a helyi gépen, másik a letöltése beállítás a portál használatával?**

Használhat [Tártallózó](https://azure.microsoft.com/features/storage-explorer/) letölteni egy virtuális Merevlemezt.

**Van valamilyen előfeltétele a tárfiók replikációs módosítása az georedundáns tárolás helyileg redundáns tárolás?**

Nem. 

**Hogyan érhetem el az Azure-fájlok redundáns tárolás?**

Írásvédett georedundáns tárolás redundáns tárolás eléréséhez szükséges. Azure fájlok azonban csak a helyileg redundáns tárolás és a csak olvasható hozzáférést nem engedélyező standard georedundáns tárolás támogatja. 

**Hogyan helyezhető át a prémium szintű tárfiókból szabványos tárfiókba?**

Kövesse az alábbi lépéseket:

1.  Standard szintű storage-fiók létrehozása. (Vagy egy meglévő szabványos tárfiók az előfizetésben.)

2.  Töltse le az AzCopy. A következő AzCopy parancsok egyikét futtatja.
      
    Teljes lemezek másolása a tárfiókban lévő:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Csak egy lemezre másolásához adja meg a nevét, a lemez **mintát**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
A művelet több órát vehet igénybe.

Győződjön meg arról, hogy az átvitel sikeresen befejeződött-e, vizsgálja meg a célként megadott fiókot a tároló az Azure portálon. Miután a lemezek másolása, a standard szintű tárfiókot, csatolhat azokat a virtuális gép egy meglévő lemezként. További információkért lásd: [hogyan felügyelt adatlemezt csatolni egy Windows rendszerű virtuális gép az Azure portálon](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Alakítása prémium szintű Azure Storage a fájlmegosztás?**

Prémium szintű Storage nem engedélyezett az Azure fájlmegosztások.

**Hogyan lehet frissíteni egy standard szintű tárfiók egy prémium szintű tárfiók? Hogyan tegye szeretnék használni a prémium szintű tárfiókból szabványos tárfiókba?**

Kell a cél tárfiók létrehozása, a forrás-fiók adatainak másolása a célként megadott fiók és törölje a forrás-fiók. Egy eszköz, például az AzCopy segítségével másolja az adatokat.

Ha virtuális gépeket, mielőtt áttelepítené a tárolási fiók adatait további lépéseket kell végrehajtani. További információkért lásd: [áttelepítés prémium szintű Azure Storage (nem felügyelt lemezek)](storage-migration-to-premium-storage.md).

**Hogyan helyezhető át a klasszikus tárfiókból az Azure Resource Manager tárfiókkal?**

Használhatja a **Move-AzureStorageAccount** parancsmag. Ez a parancsmag több lépést rendelkezik (érvényesítése, előkészítése, véglegesítése). Ellenőrizheti az áthelyezés előtt.

Ha virtuális gépeket, mielőtt áttelepítené a tárolási fiók adatait további lépéseket kell végrehajtani. További információkért lásd: [át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure PowerShell használatával](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hogyan Azure storage-fiók egy Linux-alapú számítógép adatletöltéshez, vagy töltse fel az adatokat a Linux gépről?**

Az Azure parancssori felület is használhatja.

- Töltse le egy blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Egy blob feltöltése: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hogyan tudom mások hozzáférést biztosíthat a tárolási erőforrások?**

A más felhasználók hozzáférésének a tárolási erőforrások:

-   Egy közös hozzáférésű jogosultságkód (SAS) token használatával adja meg az erőforráshoz való hozzáférés. 

-   A felhasználó részére a tárfiók elsődleges vagy másodlagos kulcsot. További információkért lásd: [a tárfiók kezelése](storage-create-storage-account.md#manage-your-storage-account).

-   A névtelen hozzáférés engedélyezése a házirend módosítása. További információkért lásd: [tárolók és blobok névtelen felhasználók engedélyeket](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**AzCopy futtató?**

-   AzCopy fér hozzá a Microsoft Azure Storage parancssorból, írja be a **AzCopy**. A parancssor AzCopy együtt települ.

-   Ha telepítette a 32 bites verziója, az itt található: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Ha telepítette a 64 bites verziója, az itt található: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**A replikált tárfiókon (például zónaredundáns tárolás, a georedundáns tárolást, vagy írásvédett georedundáns tárolás) hogyan érhető el a másodlagos régióban tárolt adatokat?**

-   Zónaredundáns tárolás vagy georedundáns tárolás használata, nem éri a másodlagos régióba kivéve, ha a feladatátvételt hajt végre. A feladatátvételi folyamat kapcsolatos további információkért lásd: [mi történik, ha egy storage feladatátvételt hajt végre](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Ha írásvédett georedundáns tárolás használata esetén elérhető adatokat a másodlagos régióba bármikor. Az alábbi módszerek valamelyikével:  
      
    - **AzCopy**: hozzáfűzése **-másodlagos** a tárfiók nevét a másodlagos végpont elérésére az URL-cím számára. Példa:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-jogkivonat**: egy SAS-jogkivonat segítségével érheti el az adatait a végpontról. További információkért lásd: [használata közös hozzáférésű jogosultságkód](storage-dotnet-shared-access-signature-part-1.md).

**Hogyan használható egy HTTPS egyéni tartomány a storage-fiókom? Például Hogyan tehetem "https://mystorageaccountname.blob.core.windows.net/images/image.gif"tűnnek"https://www.contoso.com/images/image.gif"?**

SSL jelenleg nem támogatott az egyéni tartomány tárfiókokban.
De egyéni tartományok nem HTTPS. További információkért lásd: [állítson be egy egyéni tartománynevet a Blob storage-végponthoz](../blobs/storage-custom-domain-name.md).

**Hogyan FTP segítségével, amely a tárfiókban lévő adatokat hozzáférést?**

Nincs mód FTP segítségével közvetlenül a tárfiók eléréséhez. Azonban állítson be egy Azure virtuális gépet, és telepítse az FTP-kiszolgáló a virtuális gépen. A fájlok tárolására az Azure-fájlok megosztáson vagy a virtuális gép számára elérhető adatlemezt FTP-kiszolgáló lehet.

Ha azt szeretné, csak az adatok letöltése Tártallózó vagy más hasonló alkalmazás használata nélkül, előfordulhat, hogy fogja tudni használni az SAS-tokent. További információkért lásd: [használata közös hozzáférésű jogosultságkód](storage-dotnet-shared-access-signature-part-1.md).

**Hogyan tudom át Blobok egy tárfiókot a másik?**

 Ehhez használja a [Blob-áttelepítési parancsfájl](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
