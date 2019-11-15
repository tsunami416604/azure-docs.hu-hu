---
title: A VMware vCenter felderítési hibáinak elhárítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a VMware vCenter-felderítési hibákat a Azure Site Recoveryban.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091243"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>vCenter Server felderítési hibák elhárítása

Ez a cikk segítséget nyújt a VMware vCenter-felderítési hibák miatt előforduló problémák elhárításában.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Nem numerikus értékek a maxSnapShots tulajdonságban

A 9,20 előtti verziókban a vCenter leválasztja a kapcsolatot, ha nem numerikus értéket kér le a virtuális gép `snapshot.maxSnapShots` tulajdonságához.

Ezt a problémát a 95126-es AZONOSÍTÓJÚ hiba azonosítja.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
A probléma megoldásához:

- Azonosítsa a virtuális gépet, és állítsa be az értéket egy numerikus értékre (a virtuális gép szerkesztési beállításai a vCenter-ben).

vagy

- Frissítse a konfigurációs kiszolgálót a 9,20-es vagy újabb verzióra.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter-kapcsolat proxy-konfigurációs problémái

a vCenter-felderítés tiszteletben tartja a rendszerfelhasználó által konfigurált alapértelmezett proxybeállításokat. A DRA szolgáltatás a konfigurációs kiszolgáló az egyesített telepítő vagy a petesejtek sablonnal történő telepítésekor a felhasználó által megadott proxybeállításokat értékeli. 

Általánosságban elmondható, hogy a proxy a nyilvános hálózatokkal való kommunikációra szolgál. például az Azure-hoz való kommunikációhoz. Ha a proxy konfigurálva van, és a vCenter helyi környezetben van, akkor nem fog tudni kommunikálni a DRA-val.

A probléma előfordulásakor a következő helyzetek történnek:

- A vCenter-kiszolgáló \<vCenter > nem érhető el a következő hiba miatt: a távoli kiszolgáló hibát jelzett: (503) a kiszolgáló nem érhető el.
- A vCenter-kiszolgáló \<vCenter > nem érhető el a következő hiba miatt: a távoli kiszolgáló hibát jelzett: nem sikerült csatlakozni a távoli kiszolgálóhoz.
- Nem lehet csatlakozni a vCenter/ESXi-kiszolgálóhoz.

A probléma megoldásához:

Töltse le a [PsExec eszközt](https://aka.ms/PsExec). 

A PsExec eszköz használatával férhet hozzá a rendszer felhasználói környezetéhez, és meghatározhatja, hogy a proxy címe konfigurálva van-e. Ezután a következő eljárásokkal adhat hozzá vCenter a megkerülési listához.

Felderítési proxy konfigurálásához:

1. Nyissa meg az IE-t a rendszer felhasználói környezetében a PsExec eszközzel.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Módosítsa a proxybeállításokat az Internet Explorerben a vCenter IP-cím megkerüléséhez.
3. Indítsa újra a tmanssvc szolgáltatást.

DRA-proxy konfigurálásához:

1. Nyisson meg egy parancssort, és nyissa meg a Microsoft Azure Site Recovery Provider mappát.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Futtassa a következő parancsot a parancssorból.
   
   **DRCONFIGURATOR. EXE/configure/AddBypassUrls [a vCenter hozzáadásakor megadott vCenter Server IP-címe/teljes tartományneve]**

4. Indítsa újra a DRA Provider szolgáltatást.

## <a name="next-steps"></a>Következő lépések

[A VMware virtuális gép vész-helyreállítási konfigurációs kiszolgálójának kezelése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
