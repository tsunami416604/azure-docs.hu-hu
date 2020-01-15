---
title: A vész-helyreállítási Azure-beli virtuálisgép-bővítmény hibáinak elhárítása Azure Site Recovery
description: Az Azure virtuálisgép-bővítményével kapcsolatos hibák elhárítása Azure Site Recovery-mel.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: 7f9ae32b95d629ef79f085ed590d9057b0414911
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941540"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure-beli virtuálisgép-bővítményekkel kapcsolatos problémák elhárítása

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segítségével megoldhatja a virtuálisgép-ügynökkel és-bővítménysel kapcsolatos Azure Site Recovery hibákat.


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery bővítmény időtúllépése  

Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151076"

 Azure Site Recovery telepítsen egy bővítményt a virtuális gépen a védelem engedélyezése művelet részeként. A következő feltételek bármelyike megakadályozhatja a védelem indítását, és a feladat sikertelen lehet. Hajtsa végre az alábbi hibaelhárítási lépéseket, majd próbálja megismételni a műveletet:

**1. ok: [az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**2. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a site Recovery bővítmény frissítése vagy betöltése sikertelen](#the-site-recovery-extension-fails-to-update-or-load)**  

Hibaüzenet: "az előző Site Recovery bővítmény művelet a vártnál több időt vesz figyelembe."<br>
Hibakód: "150066"<br>

**1. ok: [az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**2. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a site Recovery bővítmény állapota helytelen](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A védelem sikertelen, mert a virtuálisgép-ügynök nem válaszol

Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151099"<br>

Ez a hiba akkor fordulhat elő, ha a virtuális gépen lévő Azure Guest Agent ügynök nem üzemkész állapotban van.
Az Azure Guest Agent állapotát [Azure Portalban](https://portal.azure.com/)is megtekintheti. Nyissa meg a védelemmel ellátni kívánt virtuális gépet, és tekintse meg az állapotot a "VM > Beállítások > Tulajdonságok > ügynök állapota". Az ügynök állapota az idő nagy részében a virtuális gép újraindítása után válik üzemkész állapotba. Ha azonban az újraindítás nem lehetséges, vagy a probléma továbbra is fennáll, hajtsa végre az alábbi hibaelhárítási lépéseket.

**1. ok: [az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**2. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151095"<br>

Ez akkor fordul elő, ha a Linux rendszerű gépen található ügynök verziója régi. Kérjük, végezze el az alábbi hibaelhárítási lépést.<br>
  **1. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
Lehet, hogy a virtuálisgép-ügynök sérült, vagy a szolgáltatást leállították. A virtuálisgép-ügynök újratelepítése segíti a legújabb verzió beszerzését. A szolgáltatással folytatott kommunikáció újraindítását is lehetővé teszi.

1. Állapítsa meg, hogy a "Windows Azure Guest Agent Service" fut-e a VM-szolgáltatásokban (Services. msc). Próbálja meg újraindítani a "Windows Azure Guest Agent Service" szolgáltatást.    
2. Ha a Windows Azure Guest Agent ügynök szolgáltatás nem látható a szolgáltatások területen, a Vezérlőpulton lépjen a **programok és szolgáltatások** elemre, és állapítsa meg, hogy telepítve van-e a Windows Guest Agent szolgáltatás.
4. Ha a Windows Azure Guest Agent ügynök megjelenik a **programok és szolgáltatások szolgáltatásban**, távolítsa el a Windows vendég ügynököt.
5. Töltse le és telepítse az [Agent MSI legújabb verzióját](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
6. Ellenőrizze, hogy a Windows Azure Guest Agent szolgáltatás megjelenik-e a szolgáltatások között.
7. Indítsa újra a védelmi feladatot.

Ellenőrizze azt is, hogy a [Microsoft .NET 4,5 telepítve van](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -e a virtuális gépen. A .NET 4,5 szükséges ahhoz, hogy a virtuálisgép-ügynök kommunikáljon a szolgáltatással.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A Linux rendszerű virtuális gépek esetében a legtöbb ügynökkel kapcsolatos vagy kiterjesztéssel kapcsolatos hibát az elavult virtuálisgép-ügynököt érintő problémák okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse a Linux rendszerű [virtuális gép ügynökének frissítésével](../virtual-machines/linux/update-agent.md)kapcsolatos utasításokat.

   > [!NOTE]
   > Javasoljuk *, hogy csak* terjesztési tárházon keresztül frissítse az ügynököt. Nem javasoljuk, hogy közvetlenül a GitHubról töltse le az ügynököt, és frissítse azt. Ha a disztribúcióhoz tartozó legújabb ügynök nem érhető el, a telepítésével kapcsolatos útmutatásért forduljon az elosztási támogatáshoz. A legutóbbi ügynök kereséséhez nyissa meg a [Windows Azure Linux Agent](https://github.com/Azure/WALinuxAgent/releases) lapot a GitHub-tárházban.

2. A következő parancs futtatásával győződjön meg arról, hogy az Azure-ügynök fut a virtuális gépen: `ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsok használatával:

   * Ubuntu esetén: `service walinuxagent start`
   * Egyéb disztribúciók esetén: `service waagent start`

3. [Konfigurálja az automatikus újraindítási ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Engedélyezze a virtuális gép védelmét.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A Site Recovery bővítmény frissítése vagy betöltése sikertelen
Ha a bővítmények állapota "Empty", "nem taposó" vagy áttérés.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, majd indítsa újra a műveletet.

A bővítmény eltávolítása:

1. A [Azure Portal](https://portal.azure.com/)lépjen a biztonsági mentési hibát észlelő virtuális gépre.
2. Válassza a **Beállítások** lehetőséget.
3. Kattintson az **Extensions** (Bővítmények) gombra.
4. Válassza ki **site Recovery bővítményt**.
5. Válassza az **Eltávolítás** lehetőséget.

Linux rendszerű virtuális gépek esetén, ha az VMSnapshot bővítmény nem jelenik meg a Azure Portalban, [frissítse az Azure Linux-ügynököt](../virtual-machines/linux/update-agent.md), majd futtassa a védelmet. 

A lépések végrehajtásával a bővítmény újratelepíthető a védelem során.


