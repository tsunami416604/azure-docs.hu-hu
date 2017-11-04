---
title: "Távolítsa el a kiszolgálókat, és tiltsa le a védelmet |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan a Site Recovery-tároló kiszolgálók regisztrációját, és tiltsa le a védelmet a virtuális gépek és fizikai kiszolgálók."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/3/2017
ms.author: raynew
ms.openlocfilehash: 471d68742668e2b1b1c72579cee9dd493f1bd042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="remove-servers-and-disable-protection"></a>Kiszolgálók eltávolítása és a védelem letiltása
Ez a cikk ismerteti a Recovery Services-tároló kiszolgálók regisztrációját, és tiltsa le a védelmet a Site Recovery által védett gépek.


## <a name="unregister-a--configuration-server"></a>A konfigurációs kiszolgáló regisztrációját

Ha VMware virtuális gépek vagy windowsos/Linuxos fizikai kiszolgálók replikálása az Azure-ba, az alábbiak szerint tudja törölni a tárolóból egy frissíthető konfigurációs kiszolgáló:

1. [Tiltsa le a védelmet a virtuális gépek](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Szüntesse meg](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) és [törlése](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) minden replikációs házirendhez
3. [A konfigurációs kiszolgáló törlése](site-recovery-vmware-to-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>A VMM-kiszolgáló regisztrációjának törlése

1. Állítsa le a felhők eltávolítani kívánt VMM-kiszolgálón lévő virtuális gépek replikálása.
2. Törölje a VMM-kiszolgálón, amely a törölni kívánt felhők által használt hálózati leképezések. A **Site Recovery-infrastruktúra** > **System Center VMM** > **Hálózatleképezés**, kattintson a jobb gombbal a hálózatra való leképezés > **törlése**.
3. Vegye figyelembe a VMM-kiszolgáló Azonosítóját.
4. Felhőből a VMM-kiszolgálón szeretné eltávolítani a replikációs házirend társítását.  A **Site Recovery-infrastruktúra** > **System Center VMM** >  **replikációs házirendek**, kattintson duplán a tartozó házirend. Kattintson a jobb gombbal a felhő > **Disassociate**.
5. Törölje a VMM-kiszolgáló vagy az aktív csomópontra. A **Site Recovery-infrastruktúra** > **System Center VMM** > **VMM-kiszolgálókon**, kattintson a jobb gombbal a kiszolgáló > **törlése**.
6. Ha a VMM-kiszolgáló leválasztott állapotban volt, majd töltse le és futtassa a [karbantartási parancsprogramot](http://aka.ms/asr-cleanup-script-vmm) a VMM-kiszolgálón. Nyissa meg a PowerShell használata a **Futtatás rendszergazdaként** kapcsoló, a végrehajtási házirend az alapértelmezett (LocalMachine) hatókör módosítására. A parancsfájl adja meg az Azonosítót, a VMM-kiszolgáló el szeretné távolítani. A parancsfájl a regisztrációs és felhőpárosítási adatait a kiszolgálóról távolítja el.
5. Futtassa a karbantartási parancsprogramot bármely másodlagos VMM-kiszolgálón.
6. Futtassa a karbantartási parancsprogramot a bármely más passzív VMM-fürtcsomóponton, hogy a szolgáltató telepítve.
7. Távolítsa el manuálisan a VMM-kiszolgálón a szolgáltató. Ha rendelkezik fürttel, távolítsa el az összes csomópontot.
8. Ha a virtuális gépek replikálása Azure-bA volt, távolítsa el a Microsoft Recovery Services Agent ügynököt a Hyper-V gazdagépekről, a törölt felhőkben szeretné.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>A Hyper-V gazdagépet a Hyper-V hely regisztrációjának törlése

A VMM által nem felügyelt Hyper-V-gazdagépek egy Hyper-V helyre gyűjti az adatokat. Távolítsa el a gazdagép egy Hyper-V hely az alábbiak szerint:

1. Tiltsa le a replikációt a gazdagépen található Hyper-V virtuális gépek esetén.
2. Szüntesse meg a Hyper-V hely házirendek. A **Site Recovery-infrastruktúra** > **a Hyper-V helyek** >  **replikációs házirendek**, kattintson duplán a tartozó házirend. Kattintson a jobb gombbal a hely > **Disassociate**.
3. Hyper-V-gazdagépek törlése. A **Site Recovery-infrastruktúra** > **a Hyper-V helyek** > **Hyper-V-gazdagépek**, kattintson a jobb gombbal a kiszolgáló > **törlése** .
4. Hyper-V hely törlése után minden gazdagép el lettek távolítva belőle. A **Site Recovery-infrastruktúra** > **a Hyper-V helyek** > **Hyper-V helyek**, kattintson a jobb gombbal a hely > **törlése** .
5. Ha a Hyper-V gazdagépen található egy **Disconnected** állapotban, majd futtassa az alábbi parancsfájlt minden Hyper-V állomáson eltávolított. A parancsfájl a szükségtelenné vált beállításait a kiszolgálón, és megszünteti azt a tárolóból.



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

            # First retrive all the certificates to be deleted
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



## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Tiltsa le a védelmet a VMware virtuális gép vagy a fizikai kiszolgálón (az Azure-bA VMware)

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gép > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt** lapon, válassza ki az alábbi lehetőségek egyikét:
    - **Tiltsa le a replikációt és eltávolítása (ajánlott)** – Ez a beállítás a replikált elem eltávolítása az Azure Site Recovery és az a gép replikálása leállt. Törölni a replikációs konfiguráció a konfigurációs kiszolgálón van, és a Site Recovery számlázási a védett kiszolgáló leállt.
    - **Távolítsa el** -kellene ezt a beállítást használja, csak ha a forrás környezetében törölték, vagy nem érhető el (nem csatlakozik). Ez eltávolítja a replikált Azure Site Recovery (a számlázási le van állítva). A konfigurációs kiszolgálón replikációkonfiguráló **sem fog** törlődnek. 

> [!NOTE]
> A mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálók mind a beállításokat távolítsa el manuálisan kell. Ha a kiszolgáló ismét ugyanarra a konfigurációs kiszolgálóra védeni kívánja, kihagyhatja a mobilitási szolgáltatás eltávolítása.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Tiltsa le a védelmet a Hyper-V virtuális gép (Hyper-V az Azure-bA)

> [!NOTE]
> Ez az eljárás használható, ha az Azure Hyper-V virtuális gépek a VMM-kiszolgáló nem replikál. Ha a virtuális gépek használja replikálja a **System Center VMM Azure** forgatókönyv, és kövesse az utasításokat [tiltsa le a védelmet a Hyper-V virtuális gépek replikálásához használata a System Center VMM szolgáltatással Az Azure forgatókönyv](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gép > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, a következő lehetőségek közül választhat:
     - **Tiltsa le a replikációt és eltávolítása (ajánlott)** – Ez a beállítás a replikált elem eltávolítása az Azure Site Recovery és az a gép replikálása leállt. A helyszíni virtuális gépen replikációs konfiguráció törlődnek, és a Site Recovery számlázási a védett kiszolgáló leállt.
    - **Távolítsa el** -kellene ezt a beállítást használja, csak ha a forrás környezetében törölték, vagy nem érhető el (nem csatlakozik). Ez eltávolítja a replikált Azure Site Recovery (a számlázási le van állítva). A helyszíni virtuális gépen replikációkonfiguráló **sem fog** törlődnek. 

    > [!NOTE]
    > Ha úgy döntött, hogy a **eltávolítása** lehetőséget, majd futtassa a következő parancsfájlok a replikációs beállításokat a helyszíni Hyper-V kiszolgálón.
1. A forrás Hyper-V gazdagép-kiszolgálón, a virtuális gép replikáció megszüntetéséhez. SQLVM1 cserélje le a virtuális gép nevét, és futtassa a parancsfájlt egy felügyeleti PowerShell


    
    $vmName = "SQLVM1" $vm = Get-WmiObject – Namespace "root\virtualization\v2"-lekérdezés "kiválasztása * Msvm_ComputerSystem a ahol ElementName"$vmName"=" $replicationService = Get-WmiObject – Namespace "root\virtualization\v2"-"Válasszon * a Msvm_ lekérdezése ReplicationService"$replicationService.RemoveReplicationRelationship($vm.__PATH)
    

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Tiltsa le a védelmet a Hyper-V virtuális gépek replikálása Azure-bA a System Center VMM-forgatókönyvet az Azure használatával

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gép > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, válassza ki az alábbi lehetőségek egyikét:

    - **Tiltsa le a replikációt és eltávolítása (ajánlott)** – Ez a beállítás a replikált elem eltávolítása az Azure Site Recovery és az a gép replikálása leállt. A helyszíni virtuális gépen replikációs konfiguráció törlődnek, és a Site Recovery számlázási a védett kiszolgáló leállt.
    - **Távolítsa el** -kellene ezt a beállítást használja, csak ha a forrás környezetében törölték, vagy nem érhető el (nem csatlakozik). Ez eltávolítja a replikált Azure Site Recovery (a számlázási le van állítva). A helyszíni virtuális gépen replikációkonfiguráló **sem fog** törlődnek. 

    > [!NOTE]
    > Ha úgy döntött, hogy a **eltávolítása** lehetőséget, majd az alábbi parancsfájl a replikációs beállításokat tun helyszíni VMM-kiszolgálón.
3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón, PowerShell (a rendszergazda megfelelő jogosultsággal szükséges) a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevével.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A fenti lépések törölje a replikációs beállításokat a VMM-kiszolgálón. Állítsa le a replikációt a virtuális gép a Hyper-V kiszolgálón futó, futtassa a parancsfájlt. SQLVM1 cserélje le a virtuális gép, és a host01.contoso.com nevű, a Hyper-V gazdakiszolgáló nevét.

    
    $vmName = "SQLVM1" $hostName "host01.contoso.com" $vm = Get-WmiObject – Namespace "root\virtualization\v2" =-lekérdezés "Válassza ki * Msvm_ComputerSystem a ahol ElementName ="$vmName"" - computername $hostName $replicationService = Get-WmiObject – Namespace "root\virtualization\v2"-"Válasszon * a Msvm_ReplicationService" - computername $hostName $replicationService.RemoveReplicationRelationship($vm.__PATH) lekérdezése
    
       
 
## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Tiltsa le a védelmet a Hyper-V virtuális gépek replikálása másodlagos VMM-kiszolgálón a System Center VMM VMM forgatókönyvhöz

1. A **védett elemek** > **replikált elemek**, kattintson a jobb gombbal a gép > **tiltsa le a replikációt**.
2. A **tiltsa le a replikációt**, válassza ki az alábbi lehetőségek egyikét:

    - **Tiltsa le a replikációt és eltávolítása (ajánlott)** – Ez a beállítás a replikált elem eltávolítása az Azure Site Recovery és az a gép replikálása leállt. A helyszíni virtuális gépen replikációs konfiguráció törlődnek, és a Site Recovery számlázási a védett kiszolgáló leállt.
    - **Távolítsa el** -kellene ezt a beállítást használja, csak ha a forrás környezetében törölték, vagy nem érhető el (nem csatlakozik). Ez eltávolítja a replikált Azure Site Recovery (a számlázási le van állítva). A helyszíni virtuális gépen replikációkonfiguráló **sem fog** törlődnek. Futtassa az alábbi parancsfájlok a replikációs beállításokat a helyszíni virtuális gépek tisztítása.
> [!NOTE]
> Ha úgy döntött, hogy a **eltávolítása** lehetőséget, majd az alábbi parancsfájl a replikációs beállításokat tun helyszíni VMM-kiszolgálón.

3. Futtassa ezt a parancsfájlt a forrás VMM-kiszolgálón, PowerShell (a rendszergazda megfelelő jogosultsággal szükséges) a VMM-konzolról. Cserélje le a helyőrző **SQLVM1** a virtuális gép nevével.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. A másodlagos VMM-kiszolgálón futtassa ezt a parancsfájlt a másodlagos virtuális gép beállításait:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. A másodlagos VMM-kiszolgálón frissítse a virtuális gépek a Hyper-V gazdagép-kiszolgálón, hogy a másodlagos virtuális gép újra a VMM-konzol lekérdezi észlelte.
6. A fenti lépések kapcsolja ki a replikációs beállításokat a VMM-kiszolgálón. Ha le kívánja állítani a virtuális gép replikálását, futtassa a következő parancsfájlt bizony az elsődleges és másodlagos virtuális gépeket. SQLVM1 cserélje le a virtuális gép nevét.

        Remove-VMReplication –VMName “SQLVM1”




