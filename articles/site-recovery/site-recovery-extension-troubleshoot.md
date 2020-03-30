---
title: Az Azure VM-bővítmény hibaelhárítása az Azure Site Recovery szolgáltatással a vészhelyreállításhoz
description: Az Azure Site Recovery használatával kapcsolatos problémák elhárítása az Azure Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190725"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Az Azure VM-bővítményekkel kapcsolatos problémák elhárítása

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segíthetnek az Azure Site Recovery virtuálisgép-ügynökkel és -bővítménysel kapcsolatos hibáinak megoldásában.


## <a name="azure-site-recovery-extension-time-out"></a>Az Azure Site Recovery bővítmény időbeli meghosszabbítása  

Hibaüzenet: "A feladat végrehajtása idővel leállt, miközben nyomon követte a bővítményművelet elindítását"<br>
Hibakód: "151076"

 Az Azure Site Recovery egy bővítményt telepített a virtuális gépen egy engedélyezési védelmi feladat részeként. Az alábbi feltételek bármelyike megakadályozhatja a védelem indítását, és a feladat sikertelensedését okozhatja. Hajtsa végre az alábbi hibaelhárítási lépéseket, majd próbálkozzon újra a művelettel:

- [Az ügynök telepítve van a virtuális gépben, de nem válaszol (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A Site Recovery bővítmény frissítése vagy betöltése sikertelen](#the-site-recovery-extension-fails-to-update-or-load)

Hibaüzenet: "A Webhely-helyreállítási bővítmény korábbi művelete a vártnál több időt vesz igénybe."<br>
Hibakód: "150066"

- [Az ügynök telepítve van a virtuális gépben, de nem válaszol (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A Hely-helyreállítási bővítmény állapota helytelen](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A védelem sikertelen, mert a virtuálisgép-ügynök nem válaszol

Hibaüzenet: "A feladat végrehajtása idővel leállt, miközben nyomon követte a bővítményművelet elindítását."<br>
Hibakód: "151099"

Ez a hiba akkor fordulhat elő, ha az Azure vendégügynök a virtuális gép nem kész állapotban van.

Az Azure-vendégügynök állapotát az [Azure Portalon](https://portal.azure.com/)ellenőrizheti. Nyissa meg a védeni kívánt virtuális gépet, és ellenőrizze az állapotot a **Virtuálisgép-beállítások** > **Settings** > **tulajdonságai ügynök** > **állapotában.** Az idő nagy részében az ügynök állapota készen áll a virtuális gép újraindítása után. Ha azonban nem tudja újraindítani a számítógépet, vagy továbbra is a problémával szembesül, hajtsa végre az alábbi hibaelhárítási lépéseket:

- [Az ügynök telepítve van a virtuális gépben, de nem válaszol (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Hibaüzenet: "A feladat végrehajtása idővel leállt, miközben nyomon követte a bővítményművelet elindítását."<br>
Hibakód: "151095"

Ez a hiba akkor fordul elő, ha a Linux-gépen lévő ügynökverziója elavult. Hajtsa végre a következő hibaelhárítási lépést:

- [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépben, de nem válaszol (Windows virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
Lehet, hogy a virtuális gép ügynöke sérült, vagy a szolgáltatás leállt. A virtuálisgép-ügynök újratelepítése segít a legújabb verzió bekéselésén. Ez is segít újraindítani a kommunikációt a szolgáltatással.

1. Határozza meg, hogy a Windows Azure vendégügynök szolgáltatás fut-e a virtuális gép szolgáltatások (services.msc). Indítsa újra a Windows Azure vendégügynök szolgáltatást.    
1. Ha a Windows Azure vendégügynök szolgáltatás a szolgáltatásokban nem látható, nyissa meg a Vezérlőpultot. A **Programok és szolgáltatások** című témakörből megállapítsa, hogy telepítve van-e a Windows vendégügynök szolgáltatása.
1. Ha a Windows Azure vendégügynök e programok **és szolgáltatások,** távolítsa el a Windows Azure vendégügynök.
1. Töltse le és telepítse [az MSI ügynök legújabb verzióját.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) A telepítés befejezéséhez rendszergazdai jogokra van szükség.
1. Ellenőrizze, hogy a Windows Azure vendégügynök szolgáltatás jelenik-e meg a szolgáltatásokban.
1. Indítsa újra a védelmi feladatot.

Ellenőrizze azt is, hogy [a Microsoft .NET 4.5 telepítve van-e](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépben. A virtuálisgép-ügynök a szolgáltatással való kommunikációhoz .NET 4.5 szükséges.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A legtöbb ügynökkel vagy a kiterjesztetttel kapcsolatos linuxos virtuális gépek hibák okozta problémák, amelyek befolyásolják az elavult virtuálisgép-ügynök. A probléma elhárításához kövesse az alábbi általános irányelveket:

1. Kövesse a [Linux virtuálisgép-ügynök frissítésére](../virtual-machines/linux/update-agent.md)vonatkozó utasításokat.

   > [!NOTE]
   > *Javasoljuk,* hogy az ügynök frissítése csak egy terjesztési tárház. Nem javasoljuk, hogy az ügynökkódját közvetlenül a GitHubról töltse le és frissítse. Ha a legújabb ügynök a terjesztés nem érhető el, forduljon a terjesztési támogatási szolgálathoz a telepítésével kapcsolatos utasításokért. A legújabb ügynök ellenőrzéséhez keresse fel a [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) lapját a GitHub-tárházban.

1. Győződjön meg arról, hogy az Azure-ügynök fut a virtuális gép en a következő parancs futtatásával:`ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsokkal:

   - Ubuntu esetén:`service walinuxagent start`
   - Egyéb disztribúciók esetében:`service waagent start`

1. [Konfigurálja az automatikus újraindítási ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. A virtuális gép védelmének engedélyezése.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A Site Recovery bővítmény frissítése vagy betöltése sikertelen

A bővítmény állapota "Üres", "NotReady" vagy "Átmenet" néven jelenik meg.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, és indítsa újra újra a műveletet.

A bővítmény eltávolítása:

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a biztonsági mentési hibát tapasztaló virtuális gép.
1. Válassza a **Beállítások lehetőséget.**
1. Válassza **a Bővítmények lehetőséget**.
1. Válassza a **Hely-helyreállítási bővítmény lehetőséget**.
1. Válassza az **Eltávolítás** lehetőséget.

Linux os virtuális gép esetén, ha a VMSnapshot bővítmény nem jelenik meg az Azure Portalon, [frissítse az Azure Linux-ügynököt.](../virtual-machines/linux/update-agent.md) Akkor futtassa a védelmet.

Ha végrehajtja ezeket a lépéseket, a bővítmény újratelepítése a védelem során történik.
