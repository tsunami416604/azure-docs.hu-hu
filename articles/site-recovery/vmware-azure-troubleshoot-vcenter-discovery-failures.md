---
title: Hibaelhárítás a feladat-visszavételhez helyszíni VMware virtuális gép vészhelyreállítás az Azure-bA az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a módszereket, feladat-visszavétel és ismételt védelem hibáinak elhárítása az Azure-bA az Azure Site Recovery VMware virtuális gép vészhelyreállítás során.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540805"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>A vCenter-felderítési hibák elhárítása

Ez a cikk segít, hogy a VMware vCenter-felderítési hibák miatt előforduló problémák elhárítása.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>A maxSnapShots tulajdonságban nem numerikus értékeket

Korábbi 9.20 verziók, a vCenter bontja a kapcsolatot, amikor egy tulajdonság nem numerikus értéke beolvassa `snapshot.maxSnapShots` tulajdonság egy virtuális gépen.

Hiba azonosítója 95126 azonosítja a probléma.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
A probléma megoldásához:

- Virtuális gépet, és állítsa az értékét (virtuális gép beállításainak szerkesztése a vCenter) numerikus értéket.

Vagy

- Frissítse a konfigurációs kiszolgáló 9.20 vagy újabb verzióra.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>A vCenter-csatlakozást proxykonfigurációs problémák

vCenter felderítése a rendszer alapértelmezett proxybeállításait, a rendszer felhasználó által beállított figyelembe veszi. A DRA szolgáltatás figyelembe veszi a proxybeállításokat az egyesített telepítő telepítő vagy az OVA sablon használatával a konfigurációs kiszolgáló telepítése során a felhasználó által megadott. 

Általánosságban véve a proxy nyilvános hálózathoz; közötti kommunikációra szolgál például a kommunikáció az Azure-ral. Ha a proxy van konfigurálva, és vCenter helyi környezetben, azt nem lehet kommunikálni a DRA.

A következő helyzetekben fordulhat elő, ha a probléma akkor fordul elő:

- A vCenter-kiszolgáló \<vCenter > nem érhető el a hiba miatt: A távoli kiszolgáló hibát adott vissza: (503) kiszolgáló nem érhető el
- A vCenter-kiszolgáló \<vCenter > nem érhető el a hiba miatt: A távoli kiszolgáló hibát adott vissza: Nem sikerült csatlakozni a távoli kiszolgálóhoz.
- Nem lehet csatlakozni a vCenter/ESXi-kiszolgálóhoz.

A probléma megoldásához:

Töltse le a [PsExec eszköz](https://aka.ms/PsExec). 

A PsExec eszköz használatával eléréséhez a felhasználói környezetet, és döntse el, hogy konfigurálva van-e a proxykiszolgáló címét. VCenter majd azon megkerülési listája az alábbi eljárásokkal adhat hozzá.

Felderítés a proxy konfigurálása:

1. Nyissa meg az Internet Explorer: a rendszer a felhasználói környezetben a PsExec eszköz használatával.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Az Internet Explorer megkerülhetik a vCenter-IP-címet a proxybeállítások módosítása.
3. Indítsa újra a tmanssvc szolgáltatást.

A DRA-proxy konfigurálása:

1. Nyisson meg egy parancssort, és nyissa meg a Microsoft Azure Site Recovery Provider mappát.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. A parancssorból futtassa a következő parancsot.
   
   **DRCONFIGURATOR. EXE / /AddBypassUrls konfigurálása [IP cím vagy FQDN a vCenter-kiszolgálót adja meg a vCenter hozzáadása]**

4. Indítsa újra a DRA-szolgáltató szolgáltatás.

## <a name="next-steps"></a>További lépések

[A VMware virtuális gépek vészhelyreállítására használt konfigurációs kiszolgáló kezelése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
