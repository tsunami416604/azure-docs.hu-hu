---
title: Azure Disk Encryption – mintaszkriptek
description: Ez a cikk a Microsoft Azure lemeztitkosítás linuxos virtuális gépekhez függeléke.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b54f9f3466fe5f7e2da622077f53575d6f43f72d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585958"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption – mintaszkriptek 

Ez a cikk mintaparancsfájlokat tartalmaz az előre titkosított virtuális gépek és egyéb feladatok előkészítéséhez.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-parancsfájlok mintája az Azure lemeztitkosításhoz 

- **Az összes titkosított virtuális gép listázása az előfizetésben**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **A kulcstartóban lévő virtuális gépek titkosításához használt összes lemeztitkosítási titok listázása** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Az Azure Disk Encryption előfeltételeinek PowerShell-parancsfájl használata
Ha már ismeri az Azure disk encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételei PowerShell-parancsfájlt.](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) A PowerShell-parancsfájl használatával például tekintse meg a [Virtuálisgép titkosítása rövid útmutató című témakört.](disk-encryption-powershell-quickstart.md) Eltávolíthatja a megjegyzéseket a parancsfájl egy szakaszáról a 211-es sortól kezdve, hogy titkosítsa a meglévő virtuális gépek összes lemezét egy meglévő erőforráscsoportban. 

Az alábbi táblázat bemutatja, hogy mely paraméterek használhatók a PowerShell-parancsfájlban: 


|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Annak az erőforráscsoportnak a neve, amelyhez a KeyVault tartozik.  Egy új erőforráscsoport jön létre ezzel a névvel, ha nem létezik.| True (Igaz)|
|$keyVaultName|Annak a KeyVault-nak a neve, amelyben a titkosítási kulcsokat el kell helyezni. Egy új tároló ezzel a névvel jön létre, ha nem létezik.| True (Igaz)|
|$location|A KeyVault helye. Győződjön meg arról, hogy a keyvault és a virtuális gépek titkosítani kell ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|True (Igaz)|
|$subscriptionId|A használandó Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|True (Igaz)|
|$aadAppName|Az Azure AD-alkalmazás neve, amely titkos kulcsok keyvault-írási használatával lesz használva. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha ez az alkalmazás már létezik, adja át az aadClientSecret paramétert a parancsfájlnak.|False (Hamis)|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás ügyféltkati titka.|False (Hamis)|
|$keyEncryptionKeyName|A keyvaultban nem kötelező kulcstitkosítási kulcs neve. Ha nem létezik ilyen nevű új kulcs jön létre.|False (Hamis)|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazás nélkül

- [Lemeztitkosítás engedélyezése meglévő vagy linuxos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Titkosítás letiltása egy futó Linux virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Virtuális gépek titkosítása vagy visszafejtése Egy Azure AD-alkalmazással (előző kiadás) 
 
- [Lemeztitkosítás engedélyezése meglévő vagy linuxos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Titkosítás letiltása egy futó Linux virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett. 


- [Új titkosított felügyelt lemez létrehozása előre titkosított virtuális merevlemez/tárolóblobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új, titkosított felügyelt lemezt, amely előre titkosított virtuális merevlemezt és a hozzá tartozó titkosítási beállításokat biztosít





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Operációs rendszermeghajtó titkosítása linuxos virtuális számítógépen

### <a name="prerequisites-for-os-disk-encryption"></a>Az operációs rendszer lemeztitkosításának előfeltételei

* A virtuális gépnek az Azure Disk Encryption által támogatott operációs rendszerekben felsorolt operációsrendszer-titkosítással kompatibilis terjesztést kell [használnia.](disk-encryption-overview.md#supported-vms) 
* A virtuális gép létre kell hozni a Marketplace-rendszerkép az Azure Resource Manager.
* Legalább 4 GB RAM-mal rendelkező Azure virtuális gép (az ajánlott méret 7 GB).
* (RHEL és CentOS esetén) Tiltsa le az SELinux-ot. Az SELinux letiltásához lásd: "4.4.2. SELinux letiltása" a [SELinux felhasználói és rendszergazdai útmutatójában](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális gépen.
* Az SELinux letiltása után legalább egyszer indítsa újra a virtuális gépet.

### <a name="steps"></a>Lépések
1. Hozzon létre egy virtuális gép segítségével a korábban megadott disztribúciók egyikével.

   A CentOS 7.2 esetében az operációs rendszer lemeztitkosítása speciális lemezképen keresztül támogatott. A lemezkép használatához adja meg a "7.2n" értéket termékváltozatként a virtuális gép létrehozásakor:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurálja a virtuális gép az igényeinek megfelelően. Ha az összes (OS + adat) meghajtót titkosítja, az adatmeghajtókat meg kell adni, és az /etc/fstab könyvtárból kell csatlakoztatni.

   > [!NOTE]
   > Use UUID=... adatmeghajtók megadása az /etc/fstab kapcsolóban a blokkeszköz nevének megadása helyett (például /dev/sdb1). A titkosítás során a meghajtók sorrendje megváltozik a virtuális gépen. Ha a virtuális gép a blokkeszközök adott sorrendjére támaszkodik, nem fogja csatlakoztatni őket a titkosítás után.

3. Jelentkezzen ki az SSH-munkamenetekből.

4. Az operációs rendszer titkosításához adja meg a volumeType értéket **All** vagy **os értéket,** ha engedélyezi a titkosítást.

   > [!NOTE]
   > Minden olyan felhasználói térfolyamatot, `systemd` amely nem szolgáltatásként `SIGKILL`fut, le kell ölni egy . Indítsa újra a virtuális gépet. Ha engedélyezi az operációs rendszer lemeztitkosítását egy futó virtuális gépen, tervezze meg a virtuális gép állásidejét.

5. Rendszeresen figyelemmel kíséri a titkosítás előrehaladását a [következő szakaszutasításainak](#monitoring-os-encryption-progress)használatával.

6. Miután a Get-AzVmDiskEncryptionStatus a "VMRestartPending" állapotot jeleníti meg, indítsa újra a virtuális gépét, vagy jelentkezzen be, vagy a portál, a PowerShell vagy a CLI használatával.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Újraindítás előtt azt javasoljuk, hogy mentse a virtuális gép [rendszerindítási diagnosztikája.](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)

## <a name="monitoring-os-encryption-progress"></a>Az operációs rendszer titkosítási folyamatának figyelése
Az operációs rendszer titkosításának előrehaladását háromféleképpen figyelheti:

* Használja `Get-AzVmDiskEncryptionStatus` a parancsmagot, és vizsgálja meg a ProgressMessage mezőt:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Miután a virtuális gép elérte az "operációs rendszer lemeztitkosítás indítása", körülbelül 40–50 percet vesz igénybe egy prémium szintű storage-alapú virtuális gép.

  Mert a [probléma #388](https://github.com/Azure/WALinuxAgent/issues/388) WALinuxAgent, és `OsVolumeEncrypted` `DataVolumesEncrypted` megjelenik, mint `Unknown` néhány disztribúciók. A WALinuxAgent 2.1.5-ös és újabb verziójával ez a probléma automatikusan megoldódik. Ha látja `Unknown` a kimenetben, ellenőrizheti a lemeztitkosításállapotát az Azure Resource Explorer használatával.

  Nyissa meg az [Azure Resource Explorer](https://resources.azure.com/)t , majd bontsa ki ezt a hierarchiát a bal oldali választópanelen:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  A InstanceView nézetben görgessen le a meghajtók titkosítási állapotának megtekintéséhez.

  ![Virtuálisgép-példány nézete](./media/disk-encryption/vm-instanceview.png)

* Nézd [meg boot diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Az ADE kiterjesztésből származó `[AzureDiskEncryption]`üzeneteket előtaggal kell előzni.

* Jelentkezzen be a virtuális gépre SSH-n keresztül, és a bővítmény naplóját a következőtől kapja meg:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Azt javasoljuk, hogy ne jelentkezzen be a virtuális gép, amíg az operációs rendszer titkosítása folyamatban van. Másolja a naplókat csak akkor, ha a másik két módszer nem sikerült.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Előre titkosított Linux-virtuális merevlemez előkészítése
Az előre titkosított virtuális gépek előkészítése a disztribúciótól függően változhat. Példák az Ubuntu 16, openSUSE 13.2 és CentOS 7 előkészítésére. 

### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurálja a titkosítást a telepítési telepítés során az alábbi lépésekkel:

1. Válassza **a Titkosított kötetek konfigurálása** a lemezek particionálásakor lehetőséget.

   ![Ubuntu 16.04 beállítás - Titkosított kötetek konfigurálása](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy külön rendszerindító meghajtót, amelyet nem szabad titkosítani. Titkosítsa a gyökérmeghajtót.

   ![Ubuntu 16.04 telepítés - Válassza ki a titkosítandó eszközöket](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszó, amelyet feltöltött a kulcstartóba.

   ![Ubuntu 16.04 beállítás - Jelszó biztosítása](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. A particionálás befejezése.

   ![Ubuntu 16.04 beállítás - A particionálás befejezése](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Amikor elindítja a virtuális gép, és kéri a jelszót, használja a jelszót a 3.

   ![Ubuntu 16.04 telepítő - Jelszó benyújtása a rendszerindításkor](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Készítse elő a virtuális gép feltöltésre az Azure-ba [ezeket az utasításokat.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/) Ne futtassa az utolsó lépést (a virtuális gép deprovisioning) még.

Konfigurálja a titkosítást az Azure-ral való együttműködésre az alábbi lépésekkel:

1. Hozzon létre egy fájlt a /usr/local/sbin/azure_crypt_key.sh fájl alatt, a következő parancsfájl tartalmával. Ügyeljen a KeyFileName, mert ez a jelszó fájlneve által használt Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Módosítsa a kripta konfigurációt az */etc/crypttab fájlban.* A listának így kell kinéznie:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Végrehajtható engedélyek hozzáadása a parancsfájlhoz:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edit */etc/initramfs-tools/modules* by appending lines:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Futtassa `update-initramfs -u -k all` az initramfok `keyscript` frissítéséhez, hogy a hatályba lépjen.

7. Most már megszüntetheti a virtuális gép.

   ![Ubuntu 16.04 telepítés - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Folytassa a következő lépéssel, és töltse fel a virtuális merevlemezt az Azure-ba.

### <a name="opensuse-132"></a>openSUSE 13.2
A titkosítás telepítési telepítés során történő konfigurálásához tegye a következő lépéseket:
1. A lemezek particionálásakor válassza a **Kötetcsoport titkosítása**lehetőséget, majd adjon meg egy jelszót. Ezt a jelszót fogja feltölteni a kulcstartóba.

   ![openSUSE 13.2 telepítő – Kötetcsoport titkosítása](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gép jelszavát.

   ![openSUSE 13.2 telepítő - Jelszó benyújtása rendszerindításkor](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Készítse elő a virtuális gépet az Azure-ba való feltöltésre az [SLES vagy openSUSE virtuális gép előkészítése az Azure-hoz](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)című útmutatóban. Ne futtassa az utolsó lépést (a virtuális gép deprovisioning) még.

Ha titkosítást szeretne beállítani az Azure-ral való együttműködésre, tegye a következő lépéseket:
1. Eded az /etc/dracut.conf kapcsolót, és add hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. A /usr/lib/dracut/modules.d/90crypt/module-setup.sh fájl végéig fűzi hozzá ezeket a sorokat:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh fájl elejéhez:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És változtassa meg az összes előfordulása:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   erre:
   ```bash
    if [ 1 ]; then
   ```
4. Edit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh és csatolja a "# Open LUKS eszközhöz":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Futtassa `/usr/sbin/dracut -f -v` az initrd frissítéséhez.

6. Most már megszüntetheti a virtuális gép, és töltse fel a virtuális merevlemezt az Azure-ba.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 és RHEL 8.1

A titkosítás telepítési telepítés során történő konfigurálásához tegye a következő lépéseket:
1. A lemezek **particionálásakor válassza az Adataim titkosítása** lehetőséget.

   ![CentOS 7 telepítés – telepítési célhely](./media/disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg **arról,** hogy a Titkosítás lehetőség van kiválasztva a gyökérpartícióhoz.

   ![CentOS 7 telepítő -A gyökérpartíció titkosításának kiválasztása](./media/disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszó, amelyet fel fog tölteni a kulcstartóba.

   ![CentOS 7 telepítő – jelszó megadására](./media/disk-encryption/centos-encrypt-fig3.png)

4. Amikor elindítja a virtuális gép, és kéri a jelszót, használja a jelszót a 3.

   ![CentOS 7 beállítás - Adja meg a jelszót a rendszerindításkor](./media/disk-encryption/centos-encrypt-fig4.png)

5. Készítse elő a virtuális gépet az Azure-ba való feltöltésre a "CentOS 7.0+" utasítások használatával a [CentOS-alapú virtuális gép előkészítése az Azure-hoz](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)című részben. Ne futtassa az utolsó lépést (a virtuális gép deprovisioning) még.

6. Most már megszüntetheti a virtuális gép, és töltse fel a virtuális merevlemezt az Azure-ba.

Ha titkosítást szeretne beállítani az Azure-ral való együttműködésre, tegye a következő lépéseket:

1. Eded az /etc/dracut.conf kapcsolót, és add hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. A /usr/lib/dracut/modules.d/90crypt/module-setup.sh fájl végéig fűzi hozzá ezeket a sorokat:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh fájl elejéhez:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És változtassa meg az összes előfordulása:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   erre:
   ```bash
    if [ 1 ]; then
   ```
4. Edit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh és hozzáfűzze a következőután a "# Open LUKS eszköz":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Futtassa a "/usr/sbin/dracut -f -v" kapcsolót az initrd frissítéséhez.

    ![CentOS 7 beállítás - futtatás /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure-tárfiókba
A DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemezt fel kell tölteni a tárfiókba.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a key vaultba
Ha egy Azure AD-alkalmazás használatával (előző kiadás) titkosítása során a korábban beszerzett lemeztitkosítási titkos kulcsot titkos ítva kell feltölteni a kulcstárolóban. A key vault kell lemeztitkosítás és engedélyek engedélyezve az Azure AD-ügyfél.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemeztitkosítás titkos kulcsa nincs kek-vel titkosítva
A titkos kulcs beállítása a key vaultban használja a [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó base64 karakterláncként van kódolva, majd feltöltve a key vaultba. Emellett győződjön meg arról, hogy a következő címkék vannak beállítva, amikor létrehozza a titkos kulcsot a key vaultban.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Használja `$secretUrl` a következő lépésben [az operációs rendszer lemezének kek használata nélküli csatlakoztatásához.](#without-using-a-kek)

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK-vel titkosított lemeztitkosítási titkoskulcs
Mielőtt feltöltene a titkos kulcsot a key vaultba, tetszés szerint titkosíthatja egy kulcstitkosítási kulcs használatával. A wrap [API segítségével](https://msdn.microsoft.com/library/azure/dn878066.aspx) először titkosítsa a titkos kulcsot a kulcs titkosítási kulcs használatával. A wrap művelet kimenete egy base64 URL-kódolású karakterlánc, amelyet a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával titkosként tölthet fel.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

`$KeyEncryptionKey` Használja, `$secretUrl` és a következő lépésben [az operációs rendszer lemezének kek használatával történő csatlakoztatásához.](#using-a-kek)

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL megadása operációsrendszer-lemez csatolásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációs rendszer lemezének csatlakoztatása közben `$secretUrl`át kell adnia a . Az URL-cím a "KEK-vel nem titkosított lemeztitkosítási titok" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK használata
Amikor csatlakoztatja az operációs `$KeyEncryptionKey` `$secretUrl`rendszer lemezét, adja át, és . Az URL-cím a "KEK-vel titkosított lemeztitkosítási titkosítatlan" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
