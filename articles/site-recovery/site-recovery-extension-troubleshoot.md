---
title: 'Az Azure Site Recovery-ügynök hibák elhárítása: Vendég ügynök állapota nem érhető el |} A Microsoft Docs'
description: Tünetek okok és az Azure Site Recovery hibák az ügynök és a bővítmény kapcsolatos megoldások
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: c0429e87f6c58ef2b9c7a268bee596d769e95910
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919804"
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Az Azure Site Recovery-bővítmény hibáinak elhárítása: az ügynök vagy a bővítmény kapcsolatos problémák

Ez a cikk ismerteti a hibaelhárítási lépéseket tartalmaz, amely segítségével kapcsolódó Virtuálisgép-ügynök és a bővítmény az Azure Site Recovery-hibák elhárításához.


## <a name="azure-site-recovery-extension-time-out"></a>Az Azure Site Recovery bővítmény időtúllépés  

Hibaüzenet: "feladat végrehajtása túllépte az időkorlátot elindítandó bővítményművelet nyomon követése közben"<br>
Hibakód: "151076"

 Az Azure Site Recovery-bővítmény telepítése a virtuális gépen a védelemengedélyezési feladatot részeként. Az alábbi feltételek bármelyike megakadályozhatja, hogy a védelem aktiválása és a feladat sikertelen lesz. A következő hibaelhárítási lépéseket, és ismételje meg a műveletet:

**1. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a Site Recovery-bővítmény frissítése és betöltése sikertelen](#the-site-recovery-extension-fails-to-update-or-load)**  

Hibaüzenet: "előző site recovery bővítmény műveletét tart a vártnál több időt."<br>
Hibakód: "150066"<br>

**1. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a Site Recovery bővítmény állapota nem megfelelő](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Védelem sikertelen lesz, mivel a Virtuálisgép-ügynök nem válaszol

Hibaüzenet: "feladat végrehajtása túllépte az időkorlátot elindítandó bővítményművelet nyomon követése közben."<br>
Hibakód: "151099"<br>

Ez a hiba akkor fordulhat elő, ha az Azure-vendégügynök a virtuális gép nem üzemkész állapotba kerül.
Ellenőrizheti az állapotát az Azure-vendégügynök [az Azure portal](https://portal.azure.com/). Nyissa meg a virtuális gép védelmét, majd ellenőrizze kívánt "virtuális gép > Beállítások > Tulajdonságok > ügynök állapota". A legtöbbször az ügynök állapotát felkészülésére a virtuális gép újraindítása után. Azonban ha az újraindítás nem használható lehetőség, vagy a probléma továbbra is fennálló, majd hajtsa végre az alábbi hibaelhárítási lépésekkel.

**1. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Hibaüzenet: "feladat végrehajtása túllépte az időkorlátot elindítandó bővítményművelet nyomon követése közben."<br>
Hibakód: "151095"<br>

Ez történhet, ha az ügynök verziója, a Linux rendszerű gépen régi. Kérjük, végezze el a következő hibaelhárítási lépéseket.<br>
  **1. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszoló (Windows virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A Virtuálisgép-ügynök sérült, vagy a szolgáltatás előfordulhat, hogy leállt. A Virtuálisgép-ügynök újbóli telepítése révén a legújabb verzióra. Emellett segít, indítsa újra a szolgáltatással való kommunikáció.

1. Határozza meg hogy a "Windows Azure Guest Agent szolgáltatás" fut a virtuális gép szolgáltatások között (services.msc). Próbálja meg újraindítani a "Windows Azure Guest Agent szolgáltatás".    
2. Ha a Windows Azure Guest Agent szolgáltatás nem látható a szolgáltatások, a Vezérlőpulton **programok és szolgáltatások** meghatározni, hogy telepítve van-e a Windows Vendégügynöke szolgáltatást.
4. Ha megjelenik a Windows Azure-Vendégügynök **programok és szolgáltatások**, távolítsa el a Windows Vendégügynöke.
5. Töltse le és telepítse a [az ügynök MSI legfrissebb verzióját](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
6. Ellenőrizze, hogy a Windows Azure-Vendégügynök services szolgáltatások megjelenik-e.
7. Indítsa újra a feladatot.

Emellett ellenőrizze, hogy [telepítve van a Microsoft .NET 4.5-ös](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépen. .NET 4.5-ös szükség a Virtuálisgép-ügynök a szolgáltatással való kommunikációra.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)

#### <a name="solution"></a>Megoldás
Legtöbb ügynök vagy bővítmény kapcsolatos hibák Linux rendszerű virtuális gépek egy elavult Virtuálisgép-ügynök érintő problémák okozzák. A probléma elhárításához kövesse az alábbi általános irányelveket:

1. Kövesse az utasításokat [Linux rendszerű virtuális gép-ügynök frissítése](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Hogy *erősen ajánlott* frissíteni az ügynök csak egy terjesztési tárház keresztül. A kód letöltése a közvetlenül a githubból, és frissítéskor nem ajánlott. Ha a legújabb ügynököt a disztribúció nem érhető el, forduljon terjesztési támogatása útmutatást a telepítéshez. A legújabb ügynök ellenőrzéséhez nyissa meg a [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) lap a GitHub-adattárában.

2. Győződjön meg arról, hogy az Azure-ügynököt a virtuális gépen fut a következő parancs futtatásával: `ps -e`

 Ha a folyamat nem fut, indítsa újra a következő parancsokat:

 * Az ubuntu rendszeren: `service walinuxagent start`
 * Más disztribúciók: `service waagent start`

3. [Az automatikus újraindítás ügynök konfigurálása](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Lehetővé teszi a virtuális gép védelmét.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A Site Recovery-bővítmény frissítése és betöltése sikertelen
Ha bővítményeket állapota "üres", "NotReady" vagy az áttérés.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, és indítsa újra a műveletet.

A bővítmény eltávolítása:

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a virtuális gép, amelyen a biztonsági mentési hiba jelentkezik.
2. Válassza ki **beállítások**.
3. Válassza ki **bővítmények**.
4. Válassza ki **Site Recovery-bővítmény**.
5. Válassza ki **eltávolítása**.

A Linux rendszerű virtuális gép, ha a VMSnapshot-bővítményt nem jeleníti meg az Azure Portalon [az Azure Linux-ügynök frissítése](../virtual-machines/linux/update-agent.md), és futtassa újra a védelmet. 

Ezen lépések elvégzése hatására a védelem során újra kell telepíteni a bővítményt.


