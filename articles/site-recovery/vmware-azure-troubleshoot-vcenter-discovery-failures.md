---
title: VMware vCenter felderítési hibák elhárításának elhárítása az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan háríthatja el a VMware vCenter felderítési hibák az Azure Site Recovery hibaelhárítása.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091243"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>A vCenter Server felderítési hibáinak elhárítása

Ez a cikk segít a VMware vCenter felderítési hibák miatt felmerülő problémák elhárításában.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Nem numerikus értékek a maxSnapShots tulajdonságban

A 9.20 előtti verziókon a vCenter bontja a kapcsolatot, `snapshot.maxSnapShots` amikor nem numerikus értéket kér le a tulajdonság tulajdonságára egy virtuális gépen.

Ezt a problémát a 95126-os azonosítóval azonosított hiba azonosítja.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
A probléma megoldása:

- Azonosítsa a virtuális gép, és állítsa be az értéket egy numerikus érték (VM szerkesztési beállítások vCenter).

Vagy

- Frissítse a konfigurációs kiszolgálót a 9.20-as vagy újabb verzióra.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Proxykonfigurációs problémák a vCenter-kapcsolathoz

A vCenter Discovery tiszteletben tartja a rendszer felhasználó által konfigurált rendszer alapértelmezett proxybeállításait. A DRA szolgáltatás tiszteletben tartja a felhasználó által a konfigurációs kiszolgáló telepítése során az egyesített telepítőtelepítő telepítő vagy ova sablon használatával biztosított proxybeállításokat. 

A proxy általában a nyilvános hálózatokkal való kommunikációra szolgál; például az Azure-ral való kommunikáció. Ha a proxy konfigurálva van, és a vCenter helyi környezetben van, nem fog tudni kommunikálni a DRA-val.

A probléma előfordulásakor a következő esetek fordulnak elő:

- A vCenter-kiszolgáló \<> a hiba miatt nem érhető el: A távoli kiszolgáló hibát adott vissza: (503) A kiszolgáló nem érhető el
- A vCenter-kiszolgáló \<vCenter> a hiba miatt nem érhető el: A távoli kiszolgáló hibát adott vissza: Nem lehet csatlakozni a távoli kiszolgálóhoz.
- Nem lehet csatlakozni a vCenter/ESXi kiszolgálóhoz.

A probléma megoldása:

Töltse le a [PsExec eszközt](https://aka.ms/PsExec). 

A PsExec eszközzel érheti el a rendszer felhasználói környezetét, és állapítsa meg, hogy a proxycím konfigurálva van-e. Ezután a következő eljárások használatával hozzáadhatja a vCentert a megkerülési listához.

Felderítési proxy konfigurációesetén:

1. Nyissa meg az IE-t a rendszer felhasználói környezetében a PsExec eszközzel.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Módosítsa az Internet Explorer proxybeállításait a vCenter IP-cím ének megkerüléséhez.
3. Indítsa újra a tmanssvc szolgáltatást.

Dra proxykonfiguráció esetén:

1. Nyisson meg egy parancssort, és nyissa meg a Microsoft Azure Site Recovery Provider mappát.
 
    **cd C:\Program Files\Microsoft Azure webhely-helyreállítási szolgáltató**

3. A parancssorból futtassa a következő parancsot.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [A vCenter-kiszolgáló IP-címe/FQDN azonosítója a vCenter hozzáadásakor]**

4. Indítsa újra a DRA-szolgáltatót.

## <a name="next-steps"></a>További lépések

[A VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
