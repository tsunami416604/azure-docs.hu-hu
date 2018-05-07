---
title: 'Azure Site Recovery Agent hiba elhárítása: Vendég ügynök állapota nem érhető el |} Microsoft Docs'
description: A jelenség, az az oka, és a megoldások Azure Site Recovery hibák ügynök és a bővítmény kapcsolatos
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 44f2016dacf1433cfe3a61058a167c42700e37d6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Azure Site Recovery bővítmény hibák elhárítása: az ügynök vagy a bővítmény problémái

Ez a cikk ismerteti a hibaelhárítási lépéseket, melyek segíthetnek ügynök és Virtuálisgép-bővítmény Azure Site Recovery hibáinak megoldásához.


## <a name="azure-site-recovery-extension-time-out"></a>Az Azure Site Recovery bővítmény túllépi az időkorlátot  

Hibaüzenet: "feladat végrehajtása túllépte az időkorlátot műveletet kell elindítani nyomon követése közben"<br>
Hibakód: "151076"

 Az Azure Site Recovery telepíthető a védelemengedélyezési feladatot részeként a virtuális gépen. A következő feltételek megakadályozhatja, hogy a védelem alatt álló indított és a feladat sikertelen lesz. Az alábbi hibaelhárítási lépésekkel, és próbálja megismételni a műveletet:

**1. ok: [az ügynök telepítve legyen a virtuális Géphez, de azok nem válaszoló (a Windows-alapú virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [frissítése vagy nem tölthető be a Site Recovery-kiterjesztés sikertelen](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Védelem sikertelen lesz, mivel a Virtuálisgép-ügynök nem válaszol

Hibaüzenet: "feladat végrehajtása túllépte az időkorlátot követési bővítmény indítható el művelet közben."<br>
Hibakód: "151099"<br>

Ez a hiba akkor fordulhat elő, ha a virtuális gép Azure Vendég ügynöke a vendégügynök nincs üzemkész állapotban.
A állapotának Azure Vendég ügynöke a [Azure-portálon](https://portal.azure.com/). Ugrás a virtuális gép védelmét, és tekintse meg kívánt "virtuális gép > Beállítások > Tulajdonságok > ügynök állapota". Az esetek többségében az ügynök állapotát felkészülésére a virtuális gép újraindítása után. Azonban ha újraindítás használható lehetőség, és továbbra is a probléma felől elérhető, majd hajtsa végre az alábbi hibaelhárítási lépésekkel.

**1. ok: [az ügynök telepítve legyen a virtuális Géphez, de azok nem válaszoló (a Windows-alapú virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  



## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gép, de nem válaszoló (a Windows-alapú virtuális gépek)

#### <a name="solution"></a>Megoldás
A Virtuálisgép-ügynök előfordulhat, hogy sérült, vagy a szolgáltatás esetleg leállt. Újratelepítése a Virtuálisgép-ügynök segít a legújabb verzióra. Emellett segít, indítsa újra a szolgáltatással folytatott kommunikáció.

1. Határozza meg hogy a "Windows Azure Vendég ügynökszolgáltatás" a virtuális gép szolgáltatásainak (services.msc) futtatja. Próbálja meg újraindítani a "Windows Azure Vendég ügynökszolgáltatás".    
2. Ha a Windows Azure Vendég Agent szolgáltatás nem látható a szolgáltatások Vezérlőpulton, folytassa a **programok és szolgáltatások** annak meghatározásához, hogy telepítve van-e a Windows vendég Agent szolgáltatást.
4. Ha megjelenik a Windows Azure Vendégügynök **programok és szolgáltatások**, a Windows-Vendégügynök eltávolításához.
5. Töltse le és telepítse a [az ügynök MSI legfrissebb verzióját](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultsággal kell rendelkeznie.
6. Győződjön meg arról, hogy megjelenik-e a Windows Azure Vendégügynök services szolgáltatásokban.
7. Indítsa újra a feladatot.

Azt is ellenőrizze, hogy [telepítve van a Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépen. .NET 4.5-ös verziója szükséges a Virtuálisgép-ügynök a szolgáltatással való kommunikációra.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)

#### <a name="solution"></a>Megoldás
Legtöbb ügynök vagy bővítmény kapcsolódó hibák Linux virtuális gépek elavult Virtuálisgép-ügynök befolyásoló problémákat okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse az utasításokat [Linux ügynök frissítése](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > A Microsoft *erősen ajánlott* frissíteni az ügynököt csak egy terjesztési-tárházat. Nem ajánlott közvetlenül a Githubból a kód letöltése és frissítése. Ha a legújabb ügynököt, hogy a terjesztési nem érhető el, forduljon terjesztési támogatása a telepítésre vonatkozó útmutatást. A legújabb ügynököt ellenőrzéséhez nyissa meg a [Windows Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/releases) a GitHub-tárházban lapján.

2. Győződjön meg arról, hogy az Azure-ügynök fut a virtuális Gépet a következő parancs futtatásával: `ps -e`

 Ha a folyamat nem fut, indítsa újra a következő parancsokkal:

 * Az Ubuntu: `service walinuxagent start`
 * Az egyéb terjesztéseket: `service waagent start`

3. [Konfigurálja az automatikus újraindítás ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Engedélyezze a virtuális gép védelmét.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A Site Recovery-bővítmény nem tudja frissíteni vagy nem tölthető be
Ha bővítményeket állapota "üres", "NotReady" vagy az áttérés.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, és indítsa újra a műveletet.

A bővítmény eltávolítása:

1. Az a [Azure-portálon](https://portal.azure.com/), keresse fel a virtuális Gépet, amely a biztonsági mentési hibát tapasztal.
2. Válassza ki **beállítások**.
3. Válassza ki **bővítmények**.
4. Válassza ki **helyreállítási bővítmény hely**.
5. Válassza ki **eltávolítása**.

A Linux virtuális gép, ha a VMSnapshot bővítmény nem jeleníti meg az Azure-portálon a [Azure Linux ügynök frissítése](../virtual-machines/linux/update-agent.md), és futtassa újra a védelmet. 

A lépések végrehajtása azt eredményezi, a bővítmény a védelem során újra kell telepíteni.


