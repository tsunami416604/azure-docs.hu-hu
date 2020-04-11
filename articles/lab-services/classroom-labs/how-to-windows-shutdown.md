---
title: Útmutató a Windows leállítási viselkedésének szabályozásához az Azure Lab Services szolgáltatásban | Microsoft dokumentumok
description: Az alapjárati Windows virtuális gépek automatikus leállításának lépései és a Windows leállítási parancsának eltávolítása.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 7b839df5940ab26e5c1a99a1bda1fbd2545f8cc4
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113122"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Útmutató a Windows leállítási viselkedésének szabályozásához

Az Azure Lab Services számos költségszabályozást biztosít annak biztosítására, hogy a Windows virtuális gépek (VM-ek) ne fussanak le váratlanul:
 - [Ütemezés beállítása](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Kvóták beállítása a felhasználók számára](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Még ezek a költségvezérlők, vannak olyan helyzetek, amikor a Windows virtuális gép váratlanul továbbra is fut; és ennek eredményeképpen levonjuk a tanuló kvótájából:

- **Az RDP-ablak nyitva marad**
  
    Amikor egy diák rdp használatával csatlakozik a virtuális géphez, véletlenül nyitva hagyhatja az RDP ablakot.  Amíg az RDP ablak nyitva marad, a **kapcsolat bontásakor az automatikus leállítás** beállítás soha nem lép érvénybe, mivel az csak az RDP-munkamenet leválasztása után aktiválódik.

- **A Windows leállítási parancs a virtuális gép kikapcsolására szolgál**
  
    Egy diák használhatja a Windows leállítási parancsot, vagy a Windows rendszeren belül biztosított egyéb leállítási mechanizmusokat a virtuális gép kikapcsolására az [Azure Lab Services leállítási gombja](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)használata helyett.  Ha ez megtörténik, az Azure Lab Services szempontjából a virtuális gép továbbra is használatban van.
    
Az ilyen helyzetek elkerülése érdekében ez az útmutató lépéseket tartalmaz egy tétlen Windows virtuális gép automatikus leállításához és a Windows leállítási parancsának eltávolításához a **Start** menüből.  

> [!NOTE]
> A virtuális gép is váratlanul levonhatja a kvótából, amikor a tanuló elindítja a virtuális gép, de soha nem csatlakozik hozzá rdp használatával.  Ez az útmutató jelenleg *nem* foglalkozik ezzel a forgatókönyvvel.  Ehelyett a diákokat emlékeztetni kell arra, hogy azonnal csatlakozzanak a virtuális gépükhöz az RDP használatával, miután elindítják; vagy le kell állítania a virtuális gép.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatikus RDP-kapcsolat bontása és leállítása az automatikus virtuális gép esetén

A Windows **helyi csoportházirend-beállításokat** biztosít, amelyekkel időkorlátot állíthat be az RDP-munkamenet automatikus leválasztására, amikor az tétlenné válik.  A munkamenet akkor dől tétlennek, ha *nincs* egér\billentyűzetbevitel.  Minden olyan hosszú ideig futó tevékenység, amely nem foglalja magában az egér\billentyűzet bevitelt, a virtuális gép tétlen állapotba kerül.  Ez magában foglalja a hosszú lekérdezés, streaming videó, összeállítása, stb.  Az osztály igényeitől függően beállíthatja az adatlejárati időkorlátot, hogy az elég hosszú legyen az ilyen típusú tevékenységek kezeléséhez.  Beállíthatja például, hogy az üzemi időkorlát 1 vagy több órára van állítva.

Íme a tanuló tapasztalata, amikor az **alapjárati munkamenet korlátját** a [**kapcsolat bontásakor történő automatikus leállításbeállítással**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) együtt konfigurálja:
 1. A diák rdp használatával csatlakozik a Windows virtuális géphez.
 2. Amikor a tanuló nyitva hagyja az RDP-ablakot, és a virtuális gép tétlen a megadott **tétlen munkamenet-korláthoz** (például 5 perc), a tanuló a következő párbeszédpanelt fogja látni:

    ![Az idle időkorlát lejárt párbeszédpanel](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Ha a tanuló *nem* kattint **az OK**gombra, az RDP-munkamenet 2 perc elteltével automatikusan leválik.
2. Miután az RDP-munkamenet bontja a kapcsolatot, miután elérte a megadott időkeretet az **automatikus leállítás a kapcsolat bontása beállítás elérésekor,** a virtuális gép automatikusan leáll az Azure Lab Services által.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>RdP tétlen munkamenet időkorlátjának beállítása a sablon virtuális gépén

Az RDP-munkamenet tétlen időkorlátjának beállításához csatlakozhat a sablon virtuális géphez, és végrehajthatja az alábbi PowerShell-parancsfájlt.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Vagy az alábbi manuális lépéseket is követheti a vm sablon használatával:

1. Nyomja le a Windows billentyűt, írja be **a gpedit parancsot,** majd válassza **a Csoportházirend szerkesztése (Vezérlőpult)** lehetőséget.

1. Nyissa meg a **Számítógép konfigurációja > felügyeleti sablonok > a Windows-összetevők > a távoli asztali szolgáltatások > a távoli asztali munkamenetgazda > a munkamenet-időkorlátokat.**  

    ![Helyi csoportházirend-szerkesztő](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Kattintson a jobb gombbal **az Aktív, de tétlen Távoli asztali szolgáltatások munkamenetek időkorlátjának beállítására,** majd kattintson a **Szerkesztés parancsra.**

1. Adja meg a következő beállításokat, majd kattintson **az OK**gombra:
   1. Válassza az **Engedélyezve** lehetőséget.
   1. A **Beállítások csoportban**adja meg az **Idle-munkamenet korlátját**.

    ![Az idle-munkamenet korlátja](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Végül, ha ezt a viselkedést kombinálni szeretné a **kapcsolat bontásakor történő automatikus leállítással,** kövesse az útmutató cikk lépéseit: [A virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

> [!WARNING]
> Miután konfigurálta ezt a beállítást a PowerShell használatával a beállításjegyzék-beállítás közvetlen vagy manuális módosításához a Csoportházirend-szerkesztő használatával, először újra kell indítania a virtuális gépet a beállítások érvénybe léptetéséhez.  Ha a beállítást a beállításjegyzék használatával konfigurálja, a Csoportházirend-szerkesztő nem mindig frissül, hogy tükrözze a beállításjegyzék-beállítás változásait; a beállításjegyzék-beállítás azonban továbbra is a várt módon lép érvénybe, és az RDP-munkamenet megszakad, ha a megadott ideig tétlen.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>A Windows leállításparancs eltávolítása a Start menüből

A Windows **helyi csoportházirend** beállításai lehetővé teszik a leállítási parancs eltávolítását a **Start** menüből.

A leállítási parancs eltávolításához csatlakozhat a sablon virtuális géphez, és végrehajthatja az alábbi PowerShell-parancsfájlt.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Vagy az alábbi manuális lépéseket is követheti a vm sablon használatával:

1. Nyomja le a Windows billentyűt, írja be **a gpedit parancsot,** majd válassza **a Csoportházirend szerkesztése (Vezérlőpult)** lehetőséget.

1. Nyissa meg **a Számítógép > felügyeleti sablonok > Start menü és a Tálca lapot.**  

    ![Helyi csoportházirend-szerkesztő](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Kattintson a jobb gombbal **az Eltávolítás gombra, és akadályozza meg a hozzáférést a Leállítás, Újraindítás, Alvó állapot és Hibernálás parancsokhoz,** majd kattintson a **Szerkesztés parancsra.**

1. Jelölje be az **Engedélyezve beállítást,** majd kattintson az **OK**gombra:
 
   ![Leállítási beállítás](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Figyelje meg, hogy a leállítási parancs már nem jelenik meg a Windows **Start** menüben; csak a **Kapcsolat bontása** parancs jelenik meg.

    ![Leállítás parancs](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a Windows-sablon virtuális gép ének előkészítéséről szóló cikket: [Útmutató a Windows-sablongépek azure Lab Services ben történő beállításához](how-to-prepare-windows-template.md)