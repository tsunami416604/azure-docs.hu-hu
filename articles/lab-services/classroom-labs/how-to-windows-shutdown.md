---
title: Útmutató a Windows leállítási viselkedésének szabályozásához a Azure Lab Servicesban | Microsoft Docs
description: A tétlen Windowsos virtuális gépek automatikus leállításának és a Windows leállítás parancs eltávolításának lépései.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113122"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Útmutató a Windows leállítási viselkedésének szabályozásához

A Azure Lab Services számos költségtakarékos szolgáltatást biztosít annak biztosítására, hogy a Windows rendszerű virtuális gépek (VM-EK) ne fussanak váratlanul:
 - [Ütemterv beállítása](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Kvóták beállítása a felhasználók számára](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Ezek a költségadatok még akkor is előfordulhatnak, ha egy Windows rendszerű virtuális gép váratlanul tovább fut; Ennek eredményeképpen a tanulói kvóta levonása:

- **Az RDP-ablak nyitva marad**
  
    Ha egy diák RDP-kapcsolaton keresztül kapcsolódik a virtuális géphez, előfordulhat, hogy az RDP-ablakot véletlenül megnyitva hagyja.  Amíg az RDP-ablak nyitva marad, a **leválasztási beállítás automatikus leállítása** nem lép érvénybe, mert csak az RDP-munkamenet leválasztása után aktiválódik.

- **A Windows Shutdown parancs a virtuális gép kikapcsolására szolgál**
  
    A tanulók a Windows leállítási parancsát vagy a Windowson belül elérhető egyéb leállítási mechanizmusokat is használhatják a virtuális gép kikapcsolására [Azure Lab Services "Leállítás gomb](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)használata helyett.  Ha ez történik, a Azure Lab Services szempontjából a virtuális gép továbbra is használatban van.
    
Ennek az útmutatónak a segítségével megakadályozhatja, hogy ezek a helyzetek ne legyenek elérhetők. Ez az útmutató a Windows rendszerű virtuális gépek automatikus leállítását és a **Start** menüből történő eltávolítását ismerteti.  

> [!NOTE]
> Előfordulhat, hogy a virtuális gép váratlanul levonja a kvótát, ha a tanuló elindítja a virtuális gépet, de soha nem kapcsolódik az RDP használatával.  Ez az útmutató jelenleg *nem* foglalkozik ezzel a forgatókönyvvel.  Ehelyett a diákoknak emlékeztetni kell a virtuális géphez való azonnali kapcsolódásra RDP használatával az indítás után. vagy le kell állítania a virtuális gépet.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatikus RDP-kapcsolat bontása és leállítása üresjárati virtuális gépen

A Windows **helyi csoportházirend** beállításokat biztosít, amelyekkel időkorlátot állíthat be, amely automatikusan leválaszthatja az RDP-munkamenetet, amikor az üresjáratba kerül.  A munkamenet úgy van meghatározva, hogy üresjáratban *legyen, ha* nincs mouse\keyboard-bemenet.  A mouse\keyboard-bevitelt nem érintő hosszú ideig futó tevékenységek esetén a virtuális gép tétlen állapotban van.  Ez magában foglalja a hosszú lekérdezések végrehajtását, a videó lejátszását, fordítását stb.  Az osztály igényeitől függően dönthet úgy, hogy az üresjárati időkorlátot úgy állítja be, hogy elég hosszú legyen az ilyen típusú tevékenységek kezelésére.  Ha szükséges, beállíthatja például, hogy az Üresjárati időkorlát 1 vagy több óráig legyen.

Itt látható a tanulói élmény, ha az **üresjárati munkamenet korlátját** a [**leválasztási beállítás automatikus leállításával**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) együtt konfigurálja:
 1. A diák RDP használatával csatlakozik a Windows rendszerű virtuális géphez.
 2. Ha a tanuló megnyitja az RDP-ablakát, és a virtuális gép üresjáratban van a megadott **tétlen munkamenet-korlátnál** (például 5 perc), a tanuló a következő párbeszédpanelt fogja látni:

    ![Üresjárati időkorlát lejárt párbeszédpanele](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Ha a tanuló nem *kattint* az **OK**gombra, akkor az RDP-munkamenetük 2 perc elteltével automatikusan leszakad.
2. Az RDP-munkamenet leválasztása után a rendszer automatikusan leállítja a virtuális gépet a **kapcsolat bontása** beállításnál megadott időkereten belül Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Az RDP üresjárati munkamenet időkorlátjának beállítása a sablon virtuális gépe számára

Az RDP-munkamenet üresjárati időkorlátjának beállításához csatlakozhat a sablon virtuális géphez, és végrehajthatja az alábbi PowerShell-szkriptet.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
A következő manuális lépéseket is végrehajthatja a sablon virtuális gép használatával:

1. Nyomja le a Windows billentyűt, írja be a következőt: **gpedit**, majd válassza a **csoportházirend szerkesztése (Vezérlőpult)** lehetőséget.

1. Nyissa meg a **Számítógép konfigurációja > Felügyeleti sablonok > Windows-összetevők > távoli asztali szolgáltatások > távoli asztal munkamenet-állomás > munkamenetek időbeli korlátait**.  

    ![Helyi csoportházirend-szerkesztő](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Kattintson a jobb gombbal **az aktív, de üresjáratban lévő távoli asztali szolgáltatások munkamenetek időkorlátjának beállítása**elemre, majd kattintson a **Szerkesztés**elemre.

1. Adja meg a következő beállításokat, majd kattintson **az OK**gombra:
   1. Válassza az **Engedélyezve** lehetőséget.
   1. A **Beállítások**területen határozza meg az **üresjárati munkamenet korlátját**.

    ![Üresjárati munkamenet korlátja](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Végül, ha ezt a viselkedést a **kapcsolat bontása beállítás automatikus leállításával** szeretné kombinálni, kövesse a útmutató cikk lépéseit: a [virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Ha ezt a beállítást a PowerShell használatával vagy a Csoportházirend-szerkesztővel közvetlenül vagy manuálisan módosítja a beállításjegyzék beállításával, akkor a beállítások érvénybe léptetéséhez először újra kell indítania a virtuális gépet.  Emellett, ha a beállításjegyzék használatával konfigurálja a beállítást, akkor a Csoportházirend-szerkesztő nem mindig frissül, hogy tükrözze a beállításjegyzék-beállítás módosításait; a beállításjegyzék-beállítás azonban továbbra is az elvárt módon lép érvénybe, és az RDP-munkamenet leválasztva állapotba kerül, amikor a megadott időtartamra üresjáratban van.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>A Windows leállítás parancs eltávolítása a Start menüből

A Windows **helyi csoportházirend** beállításai lehetővé teszik a leállítási parancs eltávolítását is a **Start** menüből.

A leállítási parancs eltávolításához csatlakozhat a sablon virtuális géphez, és végrehajthatja az alábbi PowerShell-szkriptet.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

A következő manuális lépéseket is végrehajthatja a sablon virtuális gép használatával:

1. Nyomja le a Windows billentyűt, írja be a következőt: **gpedit**, majd válassza a **csoportházirend szerkesztése (Vezérlőpult)** lehetőséget.

1. Nyissa meg a **Számítógép konfigurációja > Felügyeleti sablonok > a Start menü és a tálca menüpontot**.  

    ![Helyi csoportházirend-szerkesztő](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Kattintson a jobb gombbal az Eltávolítás elemre, **és tiltsa le a leállítási, újraindítási, alvó és hibernált parancsok elérését**, majd kattintson a **Szerkesztés**elemre.

1. Jelölje be az **engedélyezett** beállítást, majd kattintson **az OK**gombra:
 
   ![Leállítás beállítása](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Figyelje meg, hogy a leállítási parancs már nem jelenik meg a Windows **Start** menüjében; csak a **leválasztási** parancs jelenik meg.

    ![Leállítás parancs](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a Windows-sablonos virtuális gép előkészítéséről szóló cikket: [útmutató a Windows-sablonok számítógépének beállításához Azure Lab Services](how-to-prepare-windows-template.md)