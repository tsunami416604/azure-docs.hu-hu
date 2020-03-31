---
title: Kiszolgálók eltávolítása és védelem letiltása | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy miként lehet törölni a kiszolgálókat a Site Recovery tárolóból, és hogyan tilthatja le a virtuális gépek és a fizikai kiszolgálók védelmét.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257627"
---
# <a name="remove-servers-and-disable-protection"></a>Kiszolgálók eltávolítása és a védelem letiltása

Ez a cikk azt ismerteti, hogyan lehet törölni a kiszolgálók at a Recovery Services tárolóból, és hogyan lehet letiltani a védelmet a hely helyreállítása által védett gépek.


## <a name="unregister-a--configuration-server"></a>Konfigurációs kiszolgáló regisztrációjának megszüntetése

Ha VMware virtuális gépeket vagy Windows/Linux fizikai kiszolgálókat replikál az Azure-ba, a következő képpen csökkentheti a nem csatlakoztatott konfigurációs kiszolgáló regisztrációját a tárolóból:

1. [Tiltsa le a virtuális gépek védelmét.](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
2. A replikációs házirendek [társításának vagy törlésének leválasztani vagy törölni.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
3. [A konfigurációs kiszolgáló törlése](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>VMM-kiszolgáló regisztrációjának megszüntetése

1. Állítsa le a virtuális gépek replikálását az eltávolítani kívánt VMM-kiszolgálófelhőkben.
2. Törölje a törölni kívánt VMM-kiszolgáló felhői által használt hálózati hozzárendeléseket. A System Center VMM > **hálózati leképezéshely-helyreállítási** **infrastruktúrájában** > **For System Center VMM**kattintson a jobb gombbal a hálózati hozzárendelésre > **törlés**.
3. Jegyezze fel a VMM-kiszolgáló azonosítóját.
4. A replikációs házirendek társítása az eltávolítani kívánt VMM-kiszolgáló felhőitől.  A**Rendszerközpont VMM** >  **replikációs házirendjeihely-helyreállítási** **infrastruktúrájában** > kattintson duplán a társított házirendre. Kattintson a jobb gombbal a felhőre > **a társítás leválasztása.**
5. Törölje a VMM-kiszolgálót vagy az aktív csomópontot. A**System Center VMM** > **VMM-kiszolgálók** **hely-helyreállítási infrastruktúrájában** > kattintson a jobb gombbal a törlés > **kiszolgálóra.**
6. Ha a VMM-kiszolgáló leválasztott állapotban volt, töltse le és futtassa a [törlési parancsfájlt](https://aka.ms/asr-cleanup-script-vmm) a VMM-kiszolgálón. Nyissa meg a PowerShellt a **Futtatás rendszergazdaként** beállítással az alapértelmezett (LocalMachine) hatókör végrehajtási házirendjének módosításához. A parancsfájlban adja meg az eltávolítani kívánt VMM-kiszolgáló azonosítóját. A parancsfájl eltávolítja a regisztrációés a felhőpárosítási információkat a kiszolgálóról.
5. Futtassa a törlési parancsfájlt bármely másodlagos VMM-kiszolgálón.
6. Futtassa a törlési parancsfájlt bármely más passzív VMM-fürtcsomóponton, amelyen telepítve van a szolgáltató.
7. Távolítsa el a szolgáltatót manuálisan a VMM-kiszolgálón. Ha fürtje van, távolítsa el az összes csomópontról.
8. Ha a virtuális gépek replikálása az Azure-ba, el kell távolítania a Microsoft Recovery Services ügynök a Hyper-V gazdagépek a törölt felhők.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V állomás regisztrációjának megszüntetése Hyper-V helyen

A Nem a VMM által felügyelt Hyper-V gazdagépek egy Hyper-V-helyre kerülnek. Állomás eltávolítása Hyper-V webhelyen az alábbiak szerint:

1. Tiltsa le az állomáson található Hyper-V virtuális gépek replikációját.
2. A Hyper-V hely házirendjeinek leválaszthatása. A**Hyper-V** >  **helyreplikációs házirendek** **hely-helyreállítási infrastruktúrájában** > kattintson duplán a társított házirendre. Kattintson a jobb gombbal a webhelyre, > **a társítás tól.**
3. Hyper-V-állomások törlése. A**Hyper-V helyek Hyper-V** > **gazdagépeinek** **hely-helyreállítási infrastruktúrájában** > kattintson a jobb gombbal a kiszolgálóra > **törlés**re.
4. Törölje a Hyper-V webhelyet, miután az összes állomást eltávolították belőle. A**Hyper-V webhelyek** > **Hyper-V Sites** **hely-helyreállítási infrastruktúrájában** > kattintson a jobb gombbal a **törlés**>.
5. Ha a Hyper-V gazdagép leválasztott állapotban volt, majd futtassa a következő **parancsfájlt** minden eltávolított Hyper-V gazdagépen. A parancsfájl törli a kiszolgáló beállításait, és törli azt a tárolóból.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>VMware vm vagy fizikai kiszolgáló (VMware az Azure-ba) védelmének letiltása

1. A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
2. A **Replikáció letiltása** lapon válasszon az alábbi lehetőségek közül:
    - **A replikáció letiltása és eltávolítása (ajánlott)** – Ez a beállítás távolítsa el a replikált elemet az Azure Site Recovery szolgáltatásból, és a számítógép replikációja leáll. A konfigurációs kiszolgáló replikációs konfigurációja törlődik, és a védett kiszolgáló hely-helyreállítási számlázása le áll. Ne feledje, hogy ez a beállítás csak akkor használható, ha a Konfigurációs kiszolgáló csatlakoztatott állapotban van.
    - **Eltávolítás** – Ez a beállítás csak akkor használható, ha a forráskörnyezet törölve van, vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból (a számlázás levan állítva). A konfigurációs kiszolgáló replikációs konfigurációja **nem lesz** törölve. 

> [!NOTE]
> Mindkét opcióban a mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról, manuálisan kell eltávolítania. Ha ugyanazt a konfigurációs kiszolgálót használva tervezi a kiszolgáló védelmét, kihagyhatja a mobilitási szolgáltatás eltávolítását.

> [!NOTE]
> Ha már feladatátvételt végzett egy virtuális gép, és az Azure-ban fut, vegye figyelembe, hogy a letiltási védelem nem távolítja el / befolyásolja a virtuális gép feladatátvételi.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Azure-beli virtuális gép védelmének letiltása (Azure-ból azure-ba)

-  A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
> [!NOTE]
> a mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról, manuálisan kell eltávolítania. Ha ismét védeni szeretné a kiszolgálót, kihagyhatja a mobilitási szolgáltatás eltávolítását.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Hyper-V virtuális gép védelmének letiltása (Hyper-V és Azure)

> [!NOTE]
> Ezt az eljárást akkor használja, ha a Hyper-V virtuális gépeket VMM-kiszolgáló nélkül replikálja az Azure-ba. Ha a virtuális gépeket a System Center VMM használatával replikálja az **Azure-ba,** akkor kövesse a Védelem letiltása a System Center VMM használatával azure-ba replikáló Hyper-V virtuális gép védelmének letiltása című témakört.

1. A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
2. A **Replikáció letiltása**párbeszédpanelen a következő beállítások közül választhat:
   - **A replikáció letiltása és eltávolítása (ajánlott)** – Ez a beállítás távolítsa el a replikált elemet az Azure Site Recovery szolgáltatásból, és a számítógép replikációja leáll. A helyszíni virtuális gépen a replikációs konfiguráció törlődik, és a védett kiszolgáló hely-helyreállítási számlázása le áll.
   - **Eltávolítás** – Ez a beállítás csak akkor használható, ha a forráskörnyezet törölve van, vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból (a számlázás levan állítva). A helyszíni virtuális gépen a replikációs konfiguráció **nem lesz** törölve. 

 > [!NOTE]
     > Ha az **Eltávolítás** lehetőséget választotta, futtassa a következő parancsfájlkészletet a helyszíni Hyper-V kiszolgáló replikációs beállításainak karbantartásához.

> [!NOTE]
> Ha már feladatátvételt végzett egy virtuális gép, és az Azure-ban fut, vegye figyelembe, hogy a letiltási védelem nem távolítja el / befolyásolja a virtuális gép feladatátvételi.

1. A forrás Hyper-V gazdakiszolgálón távolítsa el a virtuális gép replikációját. Cserélje le az SQLVM1-et a virtuális gép nevére, és futtassa a parancsfájlt egy felügyeleti PowerShellből

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Az Azure-ba replikáló Hyper-V virtuális gépek védelmének letiltása a System Center VMM és az Azure forgatókönyv használatával

1. A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
2. A **Replikáció letiltása**párbeszédpanelen válasszon az alábbi lehetőségek közül:

   - **A replikáció letiltása és eltávolítása (ajánlott)** – Ez a beállítás távolítsa el a replikált elemet az Azure Site Recovery szolgáltatásból, és a számítógép replikációja leáll. A helyszíni virtuális gépen a replikációs konfiguráció törlődik, és a védett kiszolgáló hely-helyreállítási számlázása le áll.
   - **Eltávolítás** – Ez a beállítás csak akkor használható, ha a forráskörnyezet törölve van, vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból (a számlázás levan állítva). A helyszíni virtuális gépen a replikációs konfiguráció **nem lesz** törölve. 

     > [!NOTE]
     > Ha az **Eltávolítás** lehetőséget választotta, akkor a helyszíni VMM-kiszolgáló replikációs beállításainak karbantartásához tunolja az alábbi parancsfájlokat.
3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón a VMM-konzolpowerShell (rendszergazdai jogosultságok szükséges) használatával. Cserélje le az **SQLVM1** helyőrzőt a virtuális gép nevére.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A fenti lépések törlik a replikációs beállításokat a VMM-kiszolgálón. A Hyper-V gazdakiszolgálón futó virtuális gép replikációjának leállításához futtassa ezt a parancsfájlt. Cserélje le az SQLVM1-et a virtuális gép nevére, és host01.contoso.com a Hyper-V gazdakiszolgáló nevére.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>A másodlagos VMM-kiszolgálóra replikáló Hyper-V virtuális gépek védelmének letiltása a System Center VMM–VMM-forgatókönyv használatával

1. A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
2. A **Replikáció letiltása**párbeszédpanelen válasszon az alábbi lehetőségek közül:

   - **A replikáció letiltása és eltávolítása (ajánlott)** – Ez a beállítás távolítsa el a replikált elemet az Azure Site Recovery szolgáltatásból, és a számítógép replikációja leáll. A helyszíni virtuális gépen a replikációs konfiguráció törlődik, és a védett kiszolgáló hely-helyreállítási számlázása le áll.
   - **Eltávolítás** – Ez a beállítás csak akkor használható, ha a forráskörnyezet törölve van, vagy nem érhető el (nincs csatlakoztatva). Ezzel eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból (a számlázás levan állítva). A helyszíni virtuális gépen a replikációs konfiguráció **nem lesz** törölve. Futtassa a következő parancsfájlokat a helyszíni virtuális gépek replikációs beállításainak karbantartásához.
     > [!NOTE]
     > Ha az **Eltávolítás** lehetőséget választotta, akkor a helyszíni VMM-kiszolgáló replikációs beállításainak karbantartásához tunolja az alábbi parancsfájlokat.

3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón a VMM-konzolpowerShell (rendszergazdai jogosultságok szükséges) használatával. Cserélje le az **SQLVM1** helyőrzőt a virtuális gép nevére.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A másodlagos VMM-kiszolgálón futtassa ezt a parancsfájlt a másodlagos virtuális gép beállításainak karbantartásához:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. A másodlagos VMM-kiszolgálón frissítse a virtuális gépeket a Hyper-V gazdakiszolgálón, hogy a másodlagos virtuális gép újra észlelhető legyen a VMM-konzolban.
6. A fenti lépések törlik a replikációs beállításokat a VMM-kiszolgálón. Ha le szeretné állítani a virtuális gép replikációját, futtassa a következő parancsfájlt, az elsődleges és másodlagos virtuális gépeket. Cserélje le az SQLVM1-et a virtuális gép nevére.

        Remove-VMReplication –VMName “SQLVM1”




