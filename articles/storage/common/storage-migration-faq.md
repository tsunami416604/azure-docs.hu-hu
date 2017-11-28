---
title: "Az Azure tárterület-áttelepítés – gyakori kérdések |} Microsoft Docs"
description: "Áttelepítése az Azure Storage gyakori kérdésekre adott válaszok"
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
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Azure tárterület-áttelepítés kapcsolatos gyakori kérdések

Ez a cikk Azure tárterület-áttelepítés kapcsolatos gyakori kérdésekre. 

## <a name="faq"></a>GYIK

**Hogyan hozható létre egy parancsfájlt, amely a fájlok másolása egy tároló a másikra?**

Másolja a fájlokat tároló között, AzCopy is használhat. Tekintse meg a következő példát:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy használ [másolási Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) ehhez a példányt a tárolóban lévő összes fájlhoz.  
  
Minden virtuális gép vagy a helyi számítógép AzCopy futtatásához Internet-hozzáféréssel rendelkező is használhatja. Azure Batch ütemezést ehhez automatikusan is használhatja, de több bonyolult.  
  
Az automatizálási parancsfájl készült Azure Resource Manager deployment tárolási tartalom kezelése helyett. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../../azure-resource-manager/resource-group-template-deploy.md).

**Van bármilyen díj tehetők a tárfiókon belül ugyanabban a régióban található két különböző fájlmegosztások között az adatok másolásának?**

Nem. Nincs a folyamat nem kell fizetni.

**Hogyan készíthetek biztonsági másolatot a teljes tárfiókot más tárfiókkal?**

Nincs biztonsági mentése egy teljes tárfiókot közvetlenül beállítás. De manuálisan áthelyezheti a tároló, hogy a tárfiók egy másik fiókot AzCopy vagy Tártallózó használatával. A következő lépések bemutatják, hogyan AzCopy használatával helyezze át a tárolót:  
 

1.  Telepítse a [AzCopy](storage-use-azcopy.md) parancssori eszközt. Az eszköz segítségével helyezze át a VHD-fájl tárfiókok között.

2.  Miután a telepítő használatával telepíti a Windows az AzCopy, nyissa meg a parancs POromprt ablakot, és navigáljon arra az AzCopy telepítési mappát a számítógépén. Alapértelmezés szerint AzCopy telepítés **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** vagy **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   A következő paranccsal helyezze át a tárolót. A tényleges érték cseréli le a szöveget.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - / source: Adja meg a forrás tárfiók URI (legfeljebb tároló)  
        - / dest: Adja meg a cél tárfiók URI (legfeljebb tároló)  
        - /sourcekey: Adja meg a forrás tárolási elsődleges kulcsát, másolhatja ezt a kulcsot a portálról válasszon a tárfiókot.  
        - /destkey: Adja meg a célként megadott elsődleges kulcsát, másolhatja ezt a kulcsot a portálról válasszon a tárfiókot.

Ez a parancs végrehajtása után a tároló fájlokat áthelyezik a céloldali tárfiók.

**Az AzCopy parancssori felület nem működik együtt a "Minta" kapcsolót egy Azure-ból másolásakor blob egy másikra.**

Közvetlenül másolása és szerkesztheti az AzCopy cmd, és győződjön meg arról, hogy a minta megfelel-e a forrás keresztellenőrzés. Győződjön meg arról is, amely **/S** helyettesítő karakterek vannak érvényben. További információkért lásd: [AzCopy paraméterek](storage-use-azcopy.md).

**Hogyan helyezhető át adatokat a tároló egy másik?**

Ehhez kövesse az alábbi lépéseket:

1.  A tároló (mappa) létrehozása a cél BLOB.

2.  Használjon [Azcopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) másolásához az eredeti blob tárolóból egy másik Blob-tárolóba.

**Hogyan hozható létre a tárolt adatok mozgatása egy Azure-fájlmegosztáshoz egy másik Azure Storage PowerShell-parancsfájlt?**

AzCopy használatával helyezze át az adatokat egy Azure-fájlmegosztáshoz egy másik az azure storage. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan nagy .csv fájlokat feltölteni az Azure storage?**

AzCopy segítségével nagy .csv fájlok feltöltése az Azure Storage. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Ugrás a naplók meghajtóról"D" az Azure storage-fiókom minden nap van. Hogyan ez automatizálni?**

AzCopy használja, és hozzon létre egy feladatot a Feladatütemező. Fájlok feltöltése az Azure storage-fiók egy AzCopy kötegelt parancsfájl használatával. További információkért lásd: [hogyan lehet konfigurálni és futtatni egy felhőalapú szolgáltatás indítási feladatokat](../../cloud-services/cloud-services-startup-tasks.md).

**Hogyan helyezze át a tárfiókot előfizetések között?**

AzCopy használatával helyezze át a tárfiók előfizetések között. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan helyezhető 10 TB-nyi tárhelyre, egy másik régióban adatok?**

AzCopy használatával helyezi át az adatokat. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan lehet másolt adatok helyszíni az Azure storage?**

AzCopy használatával másolja az adatokat. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan helyezhető át adatokat a helyszíni Azure fájl szolgáltatásra?**

AzCopy használatával helyezi át az adatokat. További információt a további információk: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**Hogyan hozzárendelését a virtuális gépen egy tároló mappát?**

Az Azure fájlmegosztás használja.

**Hogyan készíthetek biztonsági másolatot az Azure file storage?**

Jelenleg nincs biztonsági mentési megoldás. Azonban Azure fájlokat is támogatja az aszinkron másolása. Igen, akkor tud fájlokat másolni megosztásból (egy tárfiókon belül vagy egy másik tárfiókkal) egy másik megosztásba vagy (a tárfiókon belül vagy egy másik tárfiókkal) egy blob-tárolóba.
További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md).

**Hogyan felügyelt lemezek áthelyezése másik tárolási fiókot?**

Ehhez kövesse az alábbi lépéseket:

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

**Hogyan tölthetők le kapcsolatos 1-2 TB adatot az Azure-portálon?**

AzCopy segítségével töltse le az adatokat. További információkért lásd: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).

**A másodlagos hely módosítása a tárfiókon Európa régió**

A storage-fiók létrehozásakor ki kell választania a fiók az elsődleges régióban. A másodlagos régióba kiválasztása az elsődleges régióban alapul, és nem módosítható. Lásd: [Azure Storage replikációs](storage-redundancy.md).

**Hol kaphatok további információt Azure Storage szolgáltatás titkosítási (SSE)?**  
  
Lásd az alábbi cikkeket:

-  [Az Azure Storage biztonsági útmutató](storage-security-guide.md)

-   [Az Azure Storage szolgáltatás titkosítási az inaktív adatok](storage-service-encryption.md)

**Hogyan helyezze át vagy adatok letöltése a tárfiókból?**

AzCopy segítségével töltse le az adatokat. További információt a további információk: [adatátvitel az AzCopy a Windows](storage-use-azcopy.md) és [adatátvitel az AzCopy Linux](storage-use-azcopy-linux.md).


**Hogyan titkosítják a tárfiókban lévő adatokat?**

Miután engedélyezte a tárfiók-titkosításra, a meglévő adatok nem titkosítottak. A meglévő adatok titkosítása, fel kell tölteni a újra az adatokat a tárfiókhoz.  Ehhez kövesse az alábbi lépéseket:

AZcopy segítségével az adatok másolása során eltérő tárfiók, és ezután helyezze vissza a storage-fiók. Is [titkosítását](storage-service-encryption.md).

**Hogyan tölthetők le a virtuális merevlemez helyi gépen, kivéve a letöltése beállítás, a portál használatával?**

Használhat [Tártallózó](https://azure.microsoft.com/features/storage-explorer/) letölteni egy virtuális Merevlemezt.

**Van valamilyen előfeltétele a tárfiók replikációs módosítása Georedundáns az LRS?**

Nem. 

**Hogyan érhetem el az Azure-fájlok redundáns tárolás?**

Redundáns tárolási eléréséhez szükséges írásvédett Georedundáns tárolás (RA-GRS). Azure-fájlok azonban csak a LRS és a csak olvasható hozzáférést nem engedélyező standard Georedundáns támogatja. 

**Hogyan helyezhető át a prémium szintű Storage szabványos Storage?**

Ehhez kövesse az alábbi lépéseket:

1.  Standard szintű tárfiók létrehozása (vagy használhatja a meglévő standard szintű tárfiók az előfizetésben).

2.  Töltse le az AzCopy. A következő AzCopy parancsok egyikét futtatja.
      
    Teljes lemezek másolása a tárfiókban lévő:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Csak egy lemezre másolja, adja meg a mintában a lemez neve

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
A művelet több órát is igénybe vehet.

Győződjön meg arról, hogy az átvitel sikeresen befejeződött-e, vizsgálja meg a célként megadott fiókot a tároló az Azure portálon. Miután a lemezek másolása, a standard szintű tárfiókot, csatolhat azokat a virtuális gép egy meglévő lemezként. További információkért lásd: [hogyan felügyelt adatlemezt csatolni egy Windows rendszerű virtuális gép az Azure portálon](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Alakítása prémium szintű Storage a fájlmegosztás?**

Prémium szintű storage Azure fájlmegosztás nem engedélyezett.

**Hogyan lehet frissíteni egy Standard tárolási prémium szintű Storage-fiókra? Hogyan tegye szeretnék használni a egy prémium szintű Storage egy Standard tárolási fiók?**

- Kell a cél tárfiók létrehozása, a forrás-fiók adatainak másolása a célként megadott fiók és törölje a forrás-fiók.

- Egy eszköz, például az AzCopy segítségével hajtsa végre az adatok másolását.

- Ha a virtuális gépek is rendelkezik, vannak-e, el kell végeznie néhány további lépést a tárolási fiók adatait az áttelepítés előtt. További információkért lásd: [áttelepítés prémium szintű Azure Storage (nem felügyelt lemezek)](storage-migration-to-premium-storage.md).

**Hogyan helyezhető át a klasszikus tárfiókból az Azure Resource Manager tárfiókkal?**

1.  A Move-AzureStorageAccount parancsmag használható.

2.  Ez a parancsmag több lépést (ellenőrzése, előkészítése, véglegesítési) rendelkezik, és ellenőrizheti az áthelyezést, mielőtt ténylegesen végrehajtaná.

3.  Ha a virtuális gépek is rendelkezik, van néhány további lépést végre kell hajtania a tárolási fiók adatait az áttelepítés előtt. További információkért lásd: [át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure PowerShell használatával](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hogyan Azure storage-fiók egy Linux-alapú számítógép adatletöltéshez, vagy töltse fel az adatokat a Linux gépről?**

Az Azure parancssori felület is használhatja.

-   Egy blob letöltése

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Egy blob feltöltése: 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hogyan tudom mások hozzáférést biztosíthat a tárolási erőforrások?**

A más felhasználók hozzáférésének a tárolási erőforrások:

-   Egy közös hozzáférésű Jogosultságkód (SAS) token használatával adja meg az erőforráshoz való hozzáférés. 

-   A felhasználó részére a tárfiók elsődleges vagy másodlagos kulcsot. További információkért lásd: [a tárfiók kezelése](storage-create-storage-account.md#manage-your-storage-account).

-   A névtelen hozzáférés engedélyezése a házirend módosítása. További információkért lásd: [tárolók és blobok névtelen felhasználók engedélyeket](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**AzCopy futtató?**

-   Ha a "Microsoft Azure Storage-parancssorból" AzCopy már használni, írja be a "AzCopy." A parancssori települ AzCopy mellett.

-   Ha telepítette a 32 bites verzióját, akkor lesz található itt: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy.**

-   Ha telepítette a 64 bites verzióját, akkor lesz található itt: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**A replikált tárfiókon (például a zrs-t, grs-re vagy RA-GRS) hogyan érhető el a másodlagos régióban tárolt adatokat?**

-   Zónaredundáns tárolás (ZRS) vagy a Georedundáns tárolás (GRS) használ, ha nem érhetők el adatok másodlagos régióban kivéve, ha a feladatátvételt hajt végre. A feladatátvételi folyamat kapcsolatos további információkért lásd: [mi történik, ha egy storage feladatátvételt hajt végre](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Ha az írásvédett Georedundáns tárolás (**RA-GRS**), bármikor másodlagos régióban adatok végezheti el. Ehhez használja a következő módszerek egyikét:  
      
    AzCopy: hozzáfűzése "-másodlagos" elérni a másodlagos végponti URL-címét a Tárfiók nevére. Példa:  
     
    https://storageaccountname-Secondary.BLOB.Core.Windows.NET/vhds/BlobName.vhd

    SAS-jogkivonat: egy SAS-jogkivonat segítségével a végpontról a hozzáférési adatok. További információkért lásd: [használata közös hozzáférésű jogosultságkód (SAS)](storage-dotnet-shared-access-signature-part-1.md).

**Hogyan használható egy HTTPS egyéni tartomány a Storage-fiókom? Például Hogyan tehetem "https://mystorageaccountname.blob.core.windows.net/images/image.gif" "https://www.contoso.com/images/image.gif" frissítésként jelenik meg?**

SSL jelenleg nem támogatott a Storage-fiókok az egyéni tartomány.
De egyéni tartományok nem HTTPS. További információkért lásd: [állítson be egy egyéni tartománynevet a Blob storage-végponthoz](../blobs/storage-custom-domain-name.md).

**Hogyan FTP segítségével, amely a Tárfiókban lévő adatokat hozzáférést?**

Nincs mód FTP segítségével közvetlenül a tárfiók eléréséhez. Azonban állítson be egy Azure virtuális gépet, és telepítse az FTP-kiszolgáló a virtuális gépen. A fájlok tárolására az Azure-fájlok megosztások vagy a virtuális gép számára elérhető adatlemezt FTP-kiszolgáló lehet.
Ha azt szeretné, csak az adatok letöltése Tártallózó vagy más hasonló alkalmazás használata nélkül, akkor előfordulhat, hogy fogja tudni használni egy SAS-jogkivonat. További információkért lásd: [használata közös hozzáférésű jogosultságkód (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.