---
title: Hibák az Azure-ba irányuló feladatátvétel hibaelhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a módszereket, az Azure-ba irányuló feladatátvétel előforduló gyakori hibák elhárítása
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/11/2018
ms.author: mayg
ms.openlocfilehash: 742e7891ec9c7151f23f1ad6eb57e728dd2a1ddd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255091"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Az Azure virtuális gép feladatátvétele során előforduló hibák elhárítása

Az Azure-bA egy virtuális gép feladatátvétele közben hibák a következők egyike jelenhet meg. A hiba elhárításához használja az itt ismertetett lépéseket az egyes hibaállapotokhoz.

## <a name="failover-failed-with-error-id-28031"></a>Hibaazonosító: 28031 feladatátvétel sikertelen

A Site Recovery nem tudta egy nem sikerült létrehozni a feladatátviteli virtuális géphez az Azure-ban. Ez a következő okok egyike miatt fordulhat elő:

* Nem áll rendelkezésre elegendő mag érhető el a virtuális gép létrehozásához: A rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez.

* A feladatátvételi virtuális gépek ugyanazon rendelkezésre állási csoportban lévő különböző méretcsaládokhoz szeretne. Győződjön meg arról, hogy ugyanabban a rendelkezésre állási csoportban válassza ki az összes virtuális gép ugyanazon virtuálisgépméret-családhoz. Nyissa meg a virtuális gép számítási és hálózati beállításainál módosíthatja a méretét, és próbálkozzon újra a feladatátvételt.

* Nincs szabályzat az előfizetést, amely megakadályozza a virtuális gép létrehozását. Módosítsa a házirendet, engedélyezi a virtuális gépet, majd próbálkozzon újra a feladatátvételt.

## <a name="failover-failed-with-error-id-28092"></a>Hibaazonosító: 28092 feladatátvétel sikertelen

A Site Recovery nem tudta hozzon létre egy hálózati adaptert a sikertelen a feladatátviteli virtuális géphez. Győződjön meg arról, hogy elegendő kvótával a hálózati adapterek létrehozása az előfizetésben elérhető. A rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](http://aka.ms/getazuresupport) a kvóta növeléséhez. Ha elegendő kvótával rendelkezik, akkor ez lehet egy átmeneti probléma, próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, ismétlés után sem, majd a dokumentum végén található Megjegyzés írása.  

## <a name="failover-failed-with-error-id-70038"></a>Feladatátvétel sikertelen Hibaazonosító: 70038

A Site Recovery nem tudta egy nem sikerült létrehozni a klasszikus virtuális gép az Azure-ban keresztül. Ez akkor fordulhat elő, mert:

* Például egy virtuális hálózatot a virtuális gépet létrehozni a szükséges erőforrások egyike nem létezik. A virtuális hálózat létrehozása a virtuális gép számítási és hálózati beállításaiban meg vagy módosítsa a beállítást, amely már létezik, és próbálkozzon újra a feladatátvételi virtuális hálózathoz.

## <a name="failover-failed-with-error-id-170010"></a>Hiba azonosítója 170010 feladatátvétel sikertelen

A Site Recovery nem tudta egy nem sikerült létrehozni a feladatátviteli virtuális géphez az Azure-ban. Ez akkor fordulhat elő, mivel hidratálási belső tevékenységeinek indítása sikertelen volt, a helyszíni virtuális gép.

Csatlakozva az Azure-ban minden olyan gép, az Azure-környezethez szükséges néhány illesztőprogramot kell a rendszerindító start állapota és a szolgáltatások, például DHCP autostart állapotban kell lennie. Ebből kifolyólag hidratálási tevékenység, a feladatátvétel időpontjában konvertálja indítási típusát **atapi, intelide, storflt, vmbus és storvsc illesztőprogramok** rendszerindító elindítani. Is átalakítja néhány szolgáltatásokhoz, például DHCP indítási típusa automatikus indítása. Ez a tevékenység sikertelen lehet a környezet konkrét problémák miatt. Manuálisan módosítsa az indítási típust, illesztőprogramok, kövesse az alábbi lépéseket:

1. [Töltse le](http://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) a no-hidratálási parancsfájlt, és futtassa azt követi. Ez a szkript ellenőrzi, hogy ha a virtuális gép igényel-e a hidratálási.

    `.\Script-no-hydration.ps1`

    A következő eredményt ad, ha hidratálási szükség:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Abban az esetben, ha a virtuális gép nem-hidratálási követelménynek megfelel, a parancsfájl lehetővé teszik az eredmény "a rendszer megfelel-e a követelmény nem-hidratálási". Ebben az esetben illesztőprogramok és a szolgáltatások az Azure által kért állapotban vannak, és a hidratálási virtuális gépen, nem szükséges.

2. A következőképpen futtassa a no-hidratálási-set a parancsfájlt, ha a virtuális gép nem felel meg a no-hidratálási követelménynek.

    `.\Script-no-hydration.ps1 -set`
    
    Ez átalakítja az indítási típusaként válassza az illesztőprogramokat, és az eredményt ad alábbi módon:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nem lehet csatlakozni RDP/SSH a feladatátviteli virtuális géphez oka az, hogy szürkén jelenik meg a virtuális gépen a csatlakozás gomb

Ha a **Connect** a feladatait átadó virtuális gép az Azure-beli gomb szürkén jelenik meg, és az Express Route vagy helyek közötti VPN-kapcsolaton keresztül, ezt követően nem csatlakozik Azure

1. Lépjen a **virtuális gép** > **hálózatkezelés**, kattintson a szükséges hálózati kapcsolat neve.  ![hálózati-adapter](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigáljon a **Ip-konfigurációk**, majd kattintson a szükséges IP-konfiguráció a név mező. ![IP-konfigurációk](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Ahhoz, hogy a nyilvános IP-címet, kattintson a **engedélyezése**. ![IP engedélyezése](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kattintson a **kötelező beállítások konfigurálása** > **új létrehozása**. ![Új létrehozása](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Adja meg a nyilvános cím nevére, és válassza ki az alapértelmezett beállításainak **Termékváltozat** és **hozzárendelés**, majd kattintson a **OK**.
6. Most a módosítások mentéséhez kattintson **mentése**.
7. Zárja be a panelt, és keresse meg a **áttekintése** virtuális gép csatlakoztatása/RDP szakaszában.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nem lehet csatlakozni RDP/SSH - rendelkezésre álló virtuális gép csatlakoztatása gombhoz

Ha a **Connect** gombjára a feladatátvételen átesett virtuális gép az Azure-ban érhető el (szürkén nem jelenik meg), majd ellenőrizze **rendszerindítási diagnosztika** a virtuális gép és a hibakeresés [Ez a cikk](../virtual-machines/windows/boot-diagnostics.md).

1. Ha a virtuális gép nem indult el, próbálja meg átvitelét egy régebbi helyreállítási pontra.
2. Ha az alkalmazás a virtuális gépen belül nem átvitelét, próbálkozzon egy alkalmazáskonzisztens helyreállítási pontra.
3. Ha a virtuális gép tartományhoz, majd győződjön meg arról, hogy a tartományvezérlő pontosan működik. A következő ehhez az alábbiakban megadott lépéseket:

    a. Hozzon létre egy új virtuális gép ugyanazon a hálózaton.

    b.  Győződjön meg arról, hogy csatlakozni tudjon ugyanahhoz a tartományhoz, amely a feladatátviteli virtuális géphez várhatóan merülnek fel.

    c. Ha a tartományvezérlő **nem** működik pontosan, majd próbálja jelentkezzen be a feladatátviteli virtuális gépre a helyi rendszergazdai fiókkal.
4. Ha egyéni DNS-kiszolgálót használ, majd győződjön meg arról, hogy legyen elérhető. A következő ehhez az alábbiakban megadott lépéseket:

    a. Hozzon létre egy új virtuális gép ugyanazon a hálózaton és a

    b. Ellenőrizze, hogy a virtuális gép tud-e névfeloldást végezni az egyéni DNS-kiszolgáló használatával

>[!Note]
>Azure Virtuálisgép-ügynököt kell telepíteni a virtuális gépet a feladatátvétel előtt a rendszerindítási Diagnosztikán kívül más beállítások engedélyezéséhez lenne szükség

## <a name="unexpected-shutdown-message-event-id-6008"></a>Váratlan leállás üzenet (Event ID 6008-as)

Másolatából egy Windows virtuális gép feladatátvétel után, ha a helyreállított virtuális gép egy nem tervezett leállás üzenetet kap, amikor azt jelzi, hogy egy virtuális gép Leállítás utáni állapotba nem lett rögzítve a feladatátvételhez használt helyreállítási pontot. Ez akkor történik, ha egy állít helyre, ha a virtuális Gépet kellett nem teljesen le lett állítva.

Ez általában nem egy okának aggodalomra és a nem tervezett feladatátvételeket általában figyelmen kívül hagyhatja. Egy tervezett feladatátvétel esetén győződjön meg arról, hogy a virtuális gép megfelelően leállt feladatátvétel előtt, és adja meg a replikációs adatokat a helyszíni Azure-bA küldendő függőben lévő elegendő időt. Ezután a **legújabb** beállítást a [feladatátvételi képernyő](site-recovery-failover.md#run-a-failover) úgy, hogy a függőben lévő adatokat az Azure-ban egy helyreállítási pontot, majd használja a virtuális gép feladatátvételre való feldolgozása.

## <a name="next-steps"></a>További lépések
- Hibaelhárítás [Windows virtuális gép RDP-kapcsolatának](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Hibaelhárítás [Linux rendszerű virtuális gép SSH-kapcsolatot](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Ha további segítségre van szüksége, majd el a lekérdezést az [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy ez a dokumentum végén található Megjegyzés írása. Van egy aktív Közösség, amely segítségére képesnek kell lennie.
