---
title: Kiszolgálók eltávolítása és a védelem letiltása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet a Site Recovery-tároló kiszolgálók regisztrációját, és tiltsa le a védelmet a virtuális gépek és fizikai kiszolgálók.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: e5a77117b21c889a72b9dd65b072ce3aa5c34314
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976270"
---
# <a name="remove-servers-and-disable-protection"></a>Kiszolgálók eltávolítása és a védelem letiltása

Ez a cikk bemutatja, hogyan kiszolgálókat a Recovery Services-tároló regisztrációját, és a Site Recovery által védett gépek védelmének letiltása.


## <a name="unregister-a--configuration-server"></a>A konfigurációs kiszolgáló regisztrációjának törlése

Ha az Azure-bA replikált VMware virtuális gépek vagy fizikai Windows/Linux-kiszolgálók regisztrációját is egy tárolóból egy adapterrrel konfigurációs kiszolgáló a következő:

1. [Tiltsa le a védelmet a virtuális gépek](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Szüntesse meg, vagy törölje](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) replikációs házirendek.
3. [A konfigurációs kiszolgáló törlése](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>A VMM-kiszolgáló regisztrációjának törlése

1. Állítsa le a VMM-kiszolgáló el kívánja távolítani a felhőben lévő virtuális gépek replikálása.
2. Törölje a felhőket a VMM-kiszolgálón, amelyet törölni szeretne által használt hálózati leképezéseket. A **Site Recovery-infrastruktúra** > **System Center VMM** > **Hálózatleképezés**, kattintson a jobb gombbal a hálózatleképezést > **Törlése**.
3. Megjegyzés: a VMM-kiszolgáló Azonosítóját.
4. A felhőket a VMM-kiszolgálóra el szeretné távolítani a replikációs házirend társításának megszüntetése.  A **Site Recovery-infrastruktúra** > **System Center VMM** >  **replikációs házirendek**, kattintson duplán a tartozó házirend. Kattintson a jobb gombbal a felhő > **Disassociate**.
5. Törli a VMM-kiszolgáló vagy az aktív csomópontra. A **Site Recovery-infrastruktúra** > **System Center VMM** > **VMM-kiszolgálók**, kattintson a jobb gombbal a kiszolgáló > **törlése** .
6. Ha a VMM-kiszolgálóról a leválasztott állapotban volt, majd töltse le és futtassa a [tisztítószkript](https://aka.ms/asr-cleanup-script-vmm) a VMM-kiszolgálón. Nyissa meg a PowerShell használata a **Futtatás rendszergazdaként** kapcsoló, a végrehajtási házirend az alapértelmezett (helyi gépen lévő) hatókör módosítására. A parancsfájl adja meg a VMM-kiszolgáló el kívánja távolítani a azonosítója. A parancsfájl eltávolítja a regisztráció és a felhőpárosítást adatait a kiszolgálóról.
5. Futtassa a karbantartási parancsprogramot minden olyan másodlagos VMM-kiszolgálón.
6. Futtassa a karbantartási parancsprogramot a bármely más passzív VMM fürtcsomópontokon van telepítve a szolgáltató.
7. Távolítsa el manuálisan a VMM-kiszolgálón a szolgáltató. Ha rendelkezik fürttel, távolítsa el az összes csomópontot.
8. Ha a virtuális gépek az Azure-ba is replikál, az Microsoft Recovery Services-ügynök eltávolítása a törölt felhőben lévő Hyper-V-gazdagépek szeretné.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>A Hyper-V gazdagép egy Hyper-V hely regisztrációját

Hyper-V gazdagépeket nem VMM által felügyelt Hyper-V hely vannak összegyűjtve. Egy gazdagép eltávolítása egy Hyper-V helyet a következő:

1. Tiltsa le a gazdagépen található Hyper-V virtuális gépek replikációját.
2. Szüntesse meg a Hyper-V-hely házirendjeinek. A **Site Recovery-infrastruktúra** > **Hyper-V helyek** >  **replikációs házirendek**, kattintson duplán a tartozó házirend. Kattintson a jobb gombbal a hely > **Disassociate**.
3. A Hyper-V-gazdagépek törlése. A **Site Recovery-infrastruktúra** > **Hyper-V helyek** > **Hyper-V-gazdagépek**, kattintson a jobb gombbal a kiszolgáló > **törlése** .
4. Hyper-V hely törlése után minden gazdagép el lettek távolítva belőle. A **Site Recovery-infrastruktúra** > **Hyper-V helyek** > **Hyper-V helyek**, kattintson a jobb gombbal a hely > **törlése** .
5. A Hyper-V-gazdagép nem található egy **leválasztott** állapot, majd futtassa az alábbi parancsfájlt minden Hyper-V gazdagépen, amely az eltávolítani kívánt. A parancsfájl törli a beállításokat a kiszolgálón, és megszünteti azt a tárolóból.


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
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Tiltsa le a védelmet egy VMware virtuális gép vagy fizikai kiszolgáló (VMware – Azure)

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gépre > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt** lapra, jelölje be az alábbi lehetőségek egyikét:
    - **Tiltsa le a replikációt és Eltávolítás (ajánlott)** – ezzel a beállítással eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból, és a gép replikációja le van állítva. A konfigurációs kiszolgáló replikálási beállításai törlődnek, és a Site Recovery-számlázását a védett kiszolgáló le van állítva.
    - **Távolítsa el** – Ez a beállítás kellene használni, csak ha a forráskörnyezetet törölték, vagy nem érhető el (nem csatlakozik). Ezzel eltávolítja a replikált elemet az Azure Site Recovery (a számlázás megáll). A konfigurációs kiszolgálón replikációs konfiguráció **nem lesz** törlődnek. 

> [!NOTE]
> A mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálók mind a beállításokat távolítsa el manuálisan kell. Ha a kiszolgálót ugyanarra a konfigurációs kiszolgálóra újra használatával védeni szeretné, kihagyhatja a mobilitási szolgáltatás eltávolítása.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Tiltsa le a védelmet egy Hyper-V virtuális gép (a Hyper-V – Azure)

> [!NOTE]
> Ez az eljárás használható, ha az Azure Hyper-V virtuális gépeket a VMM-kiszolgáló nélkül replikál. Ha a virtuális gépek használatával replikál a **System Center VMM-ben az Azure-bA** forgatókönyvet, majd kövesse az utasításokat [tiltsa le a védelmet egy Hyper-V virtuális gép replikálásához használja a System Center VMM és az Az Azure-forgatókönyv](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gépre > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, a következő lehetőségek közül választhat:
     - **Tiltsa le a replikációt és Eltávolítás (ajánlott)** – ezzel a beállítással eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból, és a gép replikációja le van állítva. A helyszíni virtuális gépet a replikálási beállításai törlődnek, és a Site Recovery-számlázását a védett kiszolgáló le van állítva.
    - **Távolítsa el** – Ez a beállítás kellene használni, csak ha a forráskörnyezetet törölték, vagy nem érhető el (nem csatlakozik). Ezzel eltávolítja a replikált elemet az Azure Site Recovery (a számlázás megáll). A helyszíni virtuális gépet a replikálási beállításai **nem lesz** törlődnek. 

    > [!NOTE]
    > Ha úgy döntött a **eltávolítása** lehetőséget, majd futtassa a következő parancsfájlok a replikációs beállításokat a helyszíni Hyper-V kiszolgálón.
1. A forrás Hyper-V gazdakiszolgálón, távolítsa el a virtuális gép replikációját. SQLVM1 cserélje le a virtuális gép nevét, és futtassa a parancsfájlt egy felügyeleti PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Tiltsa le a védelmet egy Hyper-V virtuális gép replikálása az Azure-ban a System Center VMM – Azure forgatókönyvére

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gépre > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, válassza ki az alábbi lehetőségek egyikét:

    - **Tiltsa le a replikációt és Eltávolítás (ajánlott)** – ezzel a beállítással eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból, és a gép replikációja le van állítva. A helyszíni virtuális gépet a replikálási beállításai törlődnek, és a Site Recovery-számlázását a védett kiszolgáló le van állítva.
    - **Távolítsa el** – Ez a beállítás kellene használni, csak ha a forráskörnyezetet törölték, vagy nem érhető el (nem csatlakozik). Ezzel eltávolítja a replikált elemet az Azure Site Recovery (a számlázás megáll). A helyszíni virtuális gépet a replikálási beállításai **nem lesz** törlődnek. 

    > [!NOTE]
    > Ha úgy döntött a **eltávolítása** lehetőséget, majd az alábbi parancsfájlok a replikációs beállításokat tun helyszíni VMM-kiszolgálón.
3. Futtassa ezt a szkriptet a forrás VMM-kiszolgálón (szükséges rendszergazdai jogosultságokkal) PowerShell használatával a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevére.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A fenti lépések törölje a jelet a replikációs beállításokat a VMM-kiszolgálón. A Hyper-V gazdakiszolgálón futó virtuális gép replikálása leállításához futtassa ezt a szkriptet. SQLVM1 cserélje le a virtuális gépet, és host01.contoso.com nevére a Hyper-V gazdakiszolgáló nevét.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Tiltsa le a védelmet egy Hyper-V virtuális gép replikálását a másodlagos VMM-kiszolgálón a System Center VMM a VMM-forgatókönyvet

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gépre > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, válassza ki az alábbi lehetőségek egyikét:

    - **Tiltsa le a replikációt és Eltávolítás (ajánlott)** – ezzel a beállítással eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból, és a gép replikációja le van állítva. A helyszíni virtuális gépet a replikálási beállításai törlődnek, és a Site Recovery-számlázását a védett kiszolgáló le van állítva.
    - **Távolítsa el** – Ez a beállítás kellene használni, csak ha a forráskörnyezetet törölték, vagy nem érhető el (nem csatlakozik). Ezzel eltávolítja a replikált elemet az Azure Site Recovery (a számlázás megáll). A helyszíni virtuális gépet a replikálási beállításai **nem lesz** törlődnek. Futtassa az alábbi parancsfájlok a replikációs beállításokat a helyszíni virtuális gépek karbantartása.
> [!NOTE]
> Ha úgy döntött a **eltávolítása** lehetőséget, majd az alábbi parancsfájlok a replikációs beállításokat tun helyszíni VMM-kiszolgálón.

3. Futtassa ezt a szkriptet a forrás VMM-kiszolgálón (szükséges rendszergazdai jogosultságokkal) PowerShell használatával a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevére.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A másodlagos VMM-kiszolgálón futtassa ezt a szkriptet a másodlagos virtuális gép beállításait:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. A másodlagos VMM-kiszolgálón frissítse a virtuális gépek a Hyper-V gazdakiszolgálón, hogy a másodlagos virtuális gép újra észlelt lekérdezi a VMM-konzolon.
6. A fenti lépések törölje a jelet a replikációs beállításokat a VMM-kiszolgálón. Ha szeretné leállítani a virtuális gép replikációját, futtassa a következő szkriptet hoppá az elsődleges és másodlagos virtuális gépeken. SQLVM1 cserélje le a virtuális gép nevét.

        Remove-VMReplication –VMName “SQLVM1”




