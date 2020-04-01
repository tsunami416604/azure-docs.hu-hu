---
title: Feladatátvétel elhárítása azure-hibákkal kapcsolatban | Microsoft dokumentumok
description: Ez a cikk az Azure-ba való átadás során előforduló gyakori hibák elhárításának módjait ismerteti
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 54e44a12f593d2074eefe5b2ff890863db3199f7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478960"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Hibák elhárítása a VMware VM vagy fizikai gép Azure-ba való felborulása esetén

A következő hibaüzenetek egyike jelenhet meg, miközben egy virtuális gépet az Azure-ba való átvétele során elvégezhet. A hibaelhárításhoz kövesse az egyes hibaállapotok leírt lépéseit.

## <a name="failover-failed-with-error-id-28031"></a>A feladatátvétel sikertelen a 28031-es azonosítóval

A Site Recovery nem tudott feladatátvételi feladatátvételt az Azure-ban létrehozni. Ez a következő okok valamelyike miatt történhet meg:

* Nincs elegendő kvóta a virtuális gép létrehozásához: A rendelkezésre álló kvótát az Előfizetés -> használat + kvóták oldalon ellenőrizheti. A kvóta növeléséhez [új támogatási kérelmet](https://aka.ms/getazuresupport) nyithat meg.

* Különböző méretű családokból álló virtuális gépeket próbál meg feladatátvételre ugyanazon rendelkezésre állási csoportban. Győződjön meg arról, hogy ugyanazt a méretcsaládot választja az azonos rendelkezésre állási csoportban lévő összes virtuális géphez. Módosítsa a méretet a virtuális gép számítási és hálózati beállításainak megdöntésével, majd próbálja meg újra a feladatátvételt.

* Van egy szabályzat az előfizetésen, amely megakadályozza a virtuális gép létrehozását. Módosítsa a házirendet, hogy egy virtuális gép létrehozása, majd próbálja meg újra feladatátvétel.

## <a name="failover-failed-with-error-id-28092"></a>A feladatátvétel sikertelen a 28092-es azonosítóval

A Site Recovery nem tudott hálózati adaptert létrehozni a feladatátvevő virtuális gépszámára. Győződjön meg arról, hogy elegendő kvóta áll rendelkezésre a hálózati adapterek létrehozásához az előfizetésben. A rendelkezésre álló kvótát az Előfizetés -> használat + kvóták című részében ellenőrizheti. A kvóta növeléséhez [új támogatási kérelmet](https://aka.ms/getazuresupport) nyithat meg. Ha elegendő kvótával rendelkezik, akkor ez időszakos probléma lehet, próbálkozzon újra a művelettel. Ha a probléma az újrapróbálkozások után is fennáll, akkor a dokumentum végén hagyjon megjegyzést.  

## <a name="failover-failed-with-error-id-70038"></a>A feladatátvétel a 70038-as azonosítóval sikertelen

A Site Recovery nem tudott létrehozni egy feladatátvételi klasszikus virtuális gépet az Azure-ban. Ez azért történhet meg, mert:

* Az egyik az erőforrások, például egy virtuális hálózat, amely szükséges a virtuális gép létrehozásához nem létezik. Hozza létre a virtuális hálózatot a virtuális gép Számítási és hálózati beállításai ban megadott módon, vagy módosítsa a beállítást egy már létező virtuális hálózatra, majd próbálkozzon újra feladatátvételsel.

## <a name="failover-failed-with-error-id-170010"></a>A feladatátvétel sikertelen a 170010-es azonosítóval

A Site Recovery nem tudott feladatátvételi feladatátvételt az Azure-ban létrehozni. Ez azért fordulhat elő, mert a helyszíni virtuális gép hidratálás belső tevékenysége nem sikerült.

Az Azure-környezetben bármely gép fel, az Azure-környezetben néhány illesztőprogram ot kell rendszerindítási indítási állapotban, és a szolgáltatások, például a DHCP automatikus indítási állapotban kell lennie. Így a hidratációs tevékenység, a feladatátvétel időpontjában, átalakítja az indítási típusa **atapi, intelide, storflt, vmbus, és storvsc illesztőprogramok** boot start. Néhány szolgáltatás, például a DHCP indítási típusát is automatikus indítássá alakítja. Ez a tevékenység környezetspecifikus problémák miatt sikertelen lehet. 

A **Windows Vendég operációs rendszer**illesztőprogramjainak indítási típusának manuális módosításához kövesse az alábbi lépéseket:

1. [Töltse le](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) a hidratációs szkriptet, és futtassa az alábbiak szerint. Ez a parancsfájl ellenőrzi, hogy a virtuális gép hidratálást igényel-e.

    `.\Script-no-hydration.ps1`

    Ez adja a következő eredményt, ha hidratálásra van szükség:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Abban az esetben, ha a virtuális gép megfelel a hidratálási követelménynek, a parancsfájl megadja az "Ez a rendszer megfelel a hidratálás nélküli követelménynek" eredményt. Ebben az esetben az összes illesztőprogram és szolgáltatás állapotban van az Azure által megkövetelt állapotban, és a virtuális gép hidratálása nem szükséges.

2. Futtassa a hidratálás-készlet parancsfájl az alábbiak szerint, ha a virtuális gép nem felel meg a hidratálási követelmény.

    `.\Script-no-hydration.ps1 -set`
    
    Ez átalakítja az indítási típusú vezetők, és adja meg az eredményt, mint az alábbi:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nem lehet csatlakozni/RDP/SSH a feladatátvételi virtuális géphez a virtuális gépen lévő szürkénykedő Csatlakozás gomb miatt

Ha az Azure-beli virtuális gép en a **Csatlakozás** gomb szürkén jelenik meg, és nem kapcsolódik az Azure-hoz expressz útvonalon vagy helyek közötti VPN-kapcsolaton keresztül, akkor a következő

1. Nyissa meg a **Virtuálisgép-hálózat** > **menüt,** kattintson a szükséges hálózati adapter nevére.  ![hálózati interfész](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Keresse meg az **Ip-konfigurációk**, majd kattintson a név mező szükséges IP-konfiguráció. ![IP-konfigurációk](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. A nyilvános IP-cím engedélyezéséhez kattintson az **Engedélyezés gombra.** ![IP engedélyezése](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kattintson a Szükséges >  **beállítások konfigurálása**Új létrehozása**elemre.** ![Új létrehozása](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Adja meg a nyilvános cím nevét, adja meg a **Termékváltozat** és **hozzárendelés**alapértelmezett beállításait, majd kattintson az **OK**gombra.
6. A végrehajtott módosítások mentéséhez kattintson a **Mentés gombra.**
7. Zárja be a paneleket, és keresse meg a virtuális gép **áttekintése** szakaszt a csatlakozáshoz/RDP-hez.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nem lehet csatlakozni/RDP/SSH - A Vm Connect gomb elérhető

Ha a **Csatlakozás** gomb a feladatátvételi virtuális gép az Azure-ban elérhető (nem szürkén ki), majd ellenőrizze **a rendszerindítási diagnosztika** a virtuális gépen, és ellenőrizze a hibákat a [jelen cikkben](../virtual-machines/windows/boot-diagnostics.md)felsorolt.

1. Ha a virtuális gép még nem indult el, próbálja meg egy régebbi helyreállítási pont.
2. Ha a virtuális gépen belüli alkalmazás nem működik, próbálja meg átlépni egy alkalmazáskonzisztens helyreállítási pontra.
3. Ha a virtuális gép tartományhoz csatlakozik, győződjön meg arról, hogy a tartományvezérlő pontosan működik. Ezt az alábbi lépések követésével teheti meg:

    a. Hozzon létre egy új virtuális gépet ugyanabban a hálózatban.

    b.  Győződjön meg arról, hogy képes csatlakozni ugyanahhoz a tartományhoz, amelyen a feladatátvételi virtuális gép várhatóan megjelenik.

    c. Ha a tartományvezérlő **nem** működik megfelelően, próbálja meg a feladatátvételi rendszerbe való bejelentkezést egy helyi rendszergazdai fiókkal.
4. Ha egyéni DNS-kiszolgálót használ, győződjön meg arról, hogy az elérhető. Ezt az alábbi lépések követésével teheti meg:

    a. Hozzon létre egy új virtuális gépet ugyanabban a hálózatban, és

    b. Annak ellenőrzése, hogy a virtuális gép képes-e névfeloldásra az egyéni DNS-kiszolgáló használatával

>[!Note]
>A rendszerindítási diagnosztika bármely más beállításengedélyezéséhez az Azure VM Agent kell telepíteni a virtuális gépbe a feladatátvétel előtt

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Nem lehet megnyitni a soros konzolt, miután egy UEFI-alapú gép feladatátvételt ért el az Azure-ban

Ha rdp használatával tud csatlakozni a számítógéphez, de nem tudja megnyitni a soros konzolt, kövesse az alábbi lépéseket:

* Ha a gép operációs rendszer Red Hat vagy Oracle Linux 7.*/8.0, futtassa a következő parancsot a feladatátvevő Azure virtuális gép root engedélyekkel. Indítsa újra a virtuális gépet a parancs után.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Ha a gép operációs rendszer CentOS 7.*, futtassa a következő parancsot a feladatátvevő Azure virtuális gép root engedélyekkel. Indítsa újra a virtuális gépet a parancs után.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Váratlan leállítási üzenet (6008-as azonosítójú esemény)

Windows virtuális gép utáni feladatátvétel indításakor, ha váratlan leállítási üzenetet kap a helyreállított virtuális gépen, azt jelzi, hogy a virtuális gép leállítási állapota nem lett rögzítve a feladatátvételhez használt helyreállítási ponton. Ez akkor fordul elő, ha helyreáll egy olyan pontra, amikor a virtuális gép nem volt teljesen leállítva.

Ez általában nem ad okot aggodalomra, és általában figyelmen kívül hagyható a nem tervezett feladatátvételek. Ha a feladatátvétel tervezett, győződjön meg arról, hogy a virtuális gép megfelelően leállt a feladatátvétel előtt, és elegendő időt biztosít a függőben lévő replikációs adatok helyszíni küldése az Azure-ba. Ezután használja a **Legújabb** beállítást a [feladatátvételi képernyőn,](site-recovery-failover.md#run-a-failover) hogy az Azure-ban függőben lévő adatok feldolgozása egy helyreállítási pont, amely ezután virtuális gép feladatátvételhez használt.

## <a name="unable-to-select-the-datastore"></a>Nem lehet kiválasztani az adattárolót

Ezt a problémát akkor jelzi, ha nem látja az adattár az Azure-ban a portálon, amikor megpróbálja újra megvédeni a feladatátvételt tapasztalt virtuális gép. Ennek az az oka, hogy a fő cél nem ismeri fel a virtuális gép az Azure Site Recovery-hez hozzáadott vCenters alatt.

A virtuális gépek újbóli védelméről további információt az [Újravédelem és a gépek visszaadása a helyszíni helyre az Azure-ba való feladatátvétel után című témakörben talál.](vmware-azure-reprotect.md)

A probléma megoldása:

Manuálisan hozza létre a fő cél a vCenter, amely kezeli a forrásgép. Az adattár lesz elérhető a következő vCenter felderítési és a háló frissítése műveletek után lesz elérhető.

> [!Note]
> 
> A felderítési és frissítési háló műveletek akár 30 percet is igénybe vehet. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Linux Master Target regisztráció CS sikertelen a TLS hiba 35 

Az Azure Site Recovery Master Target regisztráció a konfigurációs kiszolgáló sikertelen, mert a hitelesített proxy engedélyezve van a fő cél. 
 
Ezt a hibát a telepítési naplóban szereplő következő karakterláncok jelzik: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

A probléma megoldása:
 
1. A konfigurációs kiszolgáló virtuális gépén nyisson meg egy parancssort, és ellenőrizze a proxybeállításokat a következő parancsokkal:

    cat /etc/environment echo $http_proxy echo $https_proxy 

2. Ha az előző parancsok kimenete azt mutatja, hogy a http_proxy vagy https_proxy beállítások definiálva vannak, az alábbi módszerek egyikével oldja fel a főcél konfigurációs kiszolgálóval folytatott kommunikációblokkolását:
   
   - Töltse le a [PsExec eszközt](https://aka.ms/PsExec).
   - Az eszközzel elérheti a Rendszer felhasználói környezetét, és meghatározhatja, hogy a proxycím konfigurálva van-e. 
   - Ha a proxy konfigurálva van, nyissa meg az IE-t a rendszer felhasználói környezetében a PsExec eszközzel.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Annak ellenőrzése, hogy a fő célkiszolgáló képes-e kommunikálni a konfigurációs kiszolgálóval:
  
     - Módosítsa az Internet Explorer proxybeállításait, hogy megkerülje a fő célkiszolgáló IP-címét a proxyn keresztül.   
     Vagy
     - Tiltsa le a proxyt a főtárolókiszolgálón. 


## <a name="next-steps"></a>További lépések
- [RdP-kapcsolat](../virtual-machines/windows/troubleshoot-rdp-connection.md) a Windows virtuális géphez – problémamegoldás
- [SSH-kapcsolat linuxos virtuális géppel –](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md) problémamegoldás

Ha további segítségre van szüksége, tegye közzé kérdését a [Site Recovery fórumon,](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) vagy hagyjon megjegyzést a dokumentum végén. Van egy aktív közösség, amely képesnek kell lennie arra, hogy segítsen.
