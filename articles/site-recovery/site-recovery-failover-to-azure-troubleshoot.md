---
title: Hibák az Azure-ba irányuló feladatátvétel hibaelhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a módszereket, az Azure-ba irányuló feladatátvétel előforduló gyakori hibák elhárítása
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 75c97a7feb63a100d322610b7e6d2e5c57bebda2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889692"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>VMware virtuális gép vagy fizikai gép – Azure feladatátvétele során előforduló hibák elhárítása

Az Azure-bA egy virtuális gép feladatátvétele közben hibák a következők egyike jelenhet meg. A hiba elhárításához használja az itt ismertetett lépéseket az egyes hibaállapotokhoz.

## <a name="failover-failed-with-error-id-28031"></a>Hibaazonosító: 28031 feladatátvétel sikertelen

A Site Recovery nem tudta egy nem sikerült létrehozni a feladatátviteli virtuális géphez az Azure-ban. Ez a következő okok egyike miatt fordulhat elő:

* Nem áll rendelkezésre elegendő mag érhető el a virtuális gép létrehozásához: A rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](https://aka.ms/getazuresupport) a kvóta növeléséhez.

* A feladatátvételi virtuális gépek ugyanazon rendelkezésre állási csoportban lévő különböző méretcsaládokhoz szeretne. Győződjön meg arról, hogy ugyanabban a rendelkezésre állási csoportban válassza ki az összes virtuális gép ugyanazon virtuálisgépméret-családhoz. Nyissa meg a virtuális gép számítási és hálózati beállításainál módosíthatja a méretét, és próbálkozzon újra a feladatátvételt.

* Nincs szabályzat az előfizetést, amely megakadályozza a virtuális gép létrehozását. Módosítsa a házirendet, engedélyezi a virtuális gépet, majd próbálkozzon újra a feladatátvételt.

## <a name="failover-failed-with-error-id-28092"></a>Hibaazonosító: 28092 feladatátvétel sikertelen

A Site Recovery nem tudta hozzon létre egy hálózati adaptert a sikertelen a feladatátviteli virtuális géphez. Győződjön meg arról, hogy elegendő kvótával a hálózati adapterek létrehozása az előfizetésben elérhető. A rendelkezésre álló kvótát ellenőrizheti a előfizetés -> használat + kvóták. Megnyithatja a [új támogatási kérelem](https://aka.ms/getazuresupport) a kvóta növeléséhez. Ha elegendő kvótával rendelkezik, akkor ez lehet egy átmeneti probléma, próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, ismétlés után sem, majd a dokumentum végén található Megjegyzés írása.  

## <a name="failover-failed-with-error-id-70038"></a>Feladatátvétel sikertelen Hibaazonosító: 70038

A Site Recovery nem tudta egy nem sikerült létrehozni a klasszikus virtuális gép az Azure-ban keresztül. Ez akkor fordulhat elő, mert:

* Például egy virtuális hálózatot a virtuális gépet létrehozni a szükséges erőforrások egyike nem létezik. A virtuális hálózat létrehozása a virtuális gép számítási és hálózati beállításaiban meg vagy módosítsa a beállítást, amely már létezik, és próbálkozzon újra a feladatátvételi virtuális hálózathoz.

## <a name="failover-failed-with-error-id-170010"></a>Hiba azonosítója 170010 feladatátvétel sikertelen

A Site Recovery nem tudta egy nem sikerült létrehozni a feladatátviteli virtuális géphez az Azure-ban. Ez akkor fordulhat elő, mivel hidratálási belső tevékenységeinek indítása sikertelen volt, a helyszíni virtuális gép.

Csatlakozva az Azure-ban minden olyan gép, az Azure-környezethez szükséges néhány illesztőprogramot kell a rendszerindító start állapota és a szolgáltatások, például DHCP autostart állapotban kell lennie. Ebből kifolyólag hidratálási tevékenység, a feladatátvétel időpontjában konvertálja indítási típusát **atapi, intelide, storflt, vmbus és storvsc illesztőprogramok** rendszerindító elindítani. Is átalakítja néhány szolgáltatásokhoz, például DHCP indítási típusa automatikus indítása. Ez a tevékenység sikertelen lehet a környezet konkrét problémák miatt. 

Manuális módosítása az indítási típusaként válassza az illesztőprogramok **Windows vendég operációs rendszer**, kövesse az az alábbi lépéseket:

1. [Töltse le](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) a no-hidratálási parancsfájlt, és futtassa azt követi. Ez a szkript ellenőrzi, hogy ha a virtuális gép igényel-e a hidratálási.

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

1. Lépjen a **virtuális gép** > **hálózatkezelés**, kattintson a szükséges hálózati kapcsolat neve.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigáljon a **Ip-konfigurációk**, majd kattintson a szükséges IP-konfiguráció a név mező. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
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

Ez általában nem egy okának aggodalomra és a nem tervezett feladatátvételeket általában figyelmen kívül hagyhatja. A feladatátvétel tervezett, győződjön meg arról, hogy a virtuális gép megfelelően leállt feladatátvétel előtt, és a függőben lévő replikációs adatokat a helyszíni Azure-bA küldendő elegendő időt biztosít. Ezután a **legújabb** beállítást a [feladatátvételi képernyő](site-recovery-failover.md#run-a-failover) úgy, hogy a függőben lévő adatokat az Azure-ban egy helyreállítási pontot, majd használja a virtuális gép feladatátvételre való feldolgozása.

## <a name="unable-to-select-the-datastore"></a>Nem sikerült az adattároló kiválasztása

A probléma az jelzi, ha nem látja a a portálon az adattárhoz, az Azure-ban észlelt a feladatátvevő virtuális gép ismételt védelme tett kísérlet során. Ennek oka, hogy a fő cél virtuális gépként az Azure Site Recoveryhez hozzáadott vCenters alatt nem ismerhető fel.

A virtuális gépek ismételt védelme kapcsolatos további információkért lásd: [ismételt védelme, és a egy helyszíni helyhez vissza gépek feladatai az Azure-bA a feladatátvételt követően](vmware-azure-reprotect.md).

A probléma megoldásához:

Manuálisan hozzon létre a fő cél az, hogy a forrásgép felügyeli a vcenter-kiszolgáló. Az adattár a következő vCenter felderítése és frissítése fabric műveletek után lesz elérhető.

> [!Note]
> 
> A felderítés és a frissítési fabric műveleteket akár 30 percet is igénybe vehet. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>CS Linuxos fő célkiszolgáló regisztráció meghiúsul, és 35 SSL hiba 

A konfigurációs kiszolgálóval az Azure Site Recovery fő cél regisztrálása meghiúsul az miatt a hitelesített proxyt a fő célkiszolgálón való engedélyezése. 
 
Ez a hiba a következő karakterláncot a telepítési naplóban jelzi: 

RegisterHostStaticInfo észlelt kivétel config/talwrapper.cpp(107) [post] CurlWrapper bejegyzés sikertelen volt: kiszolgáló: 10.38.229.221, port: 443-as, phpUrl: request_handler.php, biztonságos: true, ignoreCurlPartialError: False (hamis) a hiba: [címen curlwrapperlib/curlwrapper.cpp:processCurlResponse:231] nem sikerült elküldeni a kérelmet: (35) - SSL connect error. 
 
A probléma megoldásához:
 
1. A virtuális gép konfigurációs kiszolgálón nyisson meg egy parancssort, és ellenőrizze a proxybeállításokat a következő parancsokkal:

    cat /etc/environment echo $http_proxy $https_proxy echo 

2. Ha az előző parancsok kimenetét mutatja, hogy a http_proxy vagy https_proxy beállítások vannak definiálva, használja a következő módszerek egyikét a konfigurációs kiszolgálót a fő célkiszolgáló kommunikáció feloldása:
   
   - Töltse le a [PsExec eszköz](https://aka.ms/PsExec).
   - Az eszköz segítségével elérheti a felhasználói környezetet, és határozza meg, hogy konfigurálva van-e a proxykiszolgáló címét. 
   - Ha a proxy van konfigurálva, nyissa meg az Internet Explorer egy rendszer felhasználói környezetben a PsExec eszköz használatával.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Annak érdekében, hogy a fő célkiszolgáló és a konfigurációs kiszolgáló közötti kommunikációhoz:
  
     - Módosítsa a proxybeállításokat az Internet Explorerben a fő célkiszolgáló IP-címét a proxyn keresztül kihagyásához.   
     Vagy
     - Tiltsa le a fő célkiszolgálón a proxy. 


## <a name="next-steps"></a>További lépések
- Hibaelhárítás [Windows virtuális gép RDP-kapcsolatának](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Hibaelhárítás [Linux rendszerű virtuális gép SSH-kapcsolatot](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Ha további segítségre van szüksége, majd el a lekérdezést az [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy ez a dokumentum végén található Megjegyzés írása. Van egy aktív Közösség, amely segítségére képesnek kell lennie.
