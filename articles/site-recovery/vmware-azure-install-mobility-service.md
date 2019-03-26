---
title: Készítse elő a forrásgépek telepíteni a mobilitási szolgáltatás leküldéses telepítése vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálókon keresztül az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő a kiszolgálót, hogy telepíteni a mobilitási ügynök leküldéses telepítése a VMware virtuális gépek vészhelyreállítása és fizikai kiszolgálók az Azure-bA az Azure Site Recovery szolgáltatással.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 628be573d03d42ec62a358071074facfe228852d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418669"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Készítse elő a forrásgép mobilitási ügynökök leküldéses telepítés

Amikor, vészhelyreállítás beállítása VMware virtuális gépek és fizikai kiszolgálók [Azure Site Recovery](site-recovery-overview.md), telepíti a [Site Recovery mobilitási szolgáltatás](vmware-physical-mobility-service-overview.md) minden helyszíni VMware virtuális gépek és fizikai kiszolgálón.  A mobilitási szolgáltatást kell-e a gépen adatírásokat, és továbbítja őket a Site Recovery is folyamatkiszolgálón.

## <a name="install-on-windows-machine"></a>Telepítse a Windows-számítógép

Az összes Windows szeretné a védelmét tegye a következőket:

1. Győződjön meg arról, hogy nincs-e a gép és a folyamatkiszolgáló közötti hálózati kapcsolatot. Ha nem állított be különálló folyamatkiszolgálót, majd alapértelmezés szerint azt fut a konfigurációs kiszolgálón.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fiók vagy helyi vagy tartományi rendszergazdai jogosultsággal kell rendelkeznie. Használja ezt a fiókot, csak a leküldéses telepítéshez és az ügynökök frissítésével.
2. Ha nem használ tartományi fiókot, tiltsa le a következő távoli felhasználói hozzáférés-vezérlés a helyi számítógépen:
    - Adja hozzá egy új DWORD localaccounttokenfilterpolicy beállításjegyzékbeli kulcs: **LocalAccountTokenFilterPolicy**. Állítsa az értékét **1**.
    -  Ehhez egy parancssorban futtassa a következő parancsot:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. A Windows tűzfal a védeni kívánt számítógépen, válassza ki a **lehetővé teszik az alkalmazás vagy szolgáltatás tűzfalán keresztül**. Engedélyezése **fájl- és nyomtatómegosztás** és **Windows Management Instrumentation (WMI)**. Tartományi számítógépek esetében konfigurálhatja a tűzfal beállításai csoportházirend-objektum (GPO) használatával.

   ![Tűzfalbeállítások](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
5. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonja az asztalon, és a %ProgramData%\home\svsystems\bin mappában érhető el.
6. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.
7. Adja hozzá a létrehozott fiókot.
8. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.

## <a name="install-on-linux-machine"></a>Linux rendszerű gépen telepítse

Az összes Linux, amely számára védelmet kíván tegye a következőket:

1. Győződjön meg arról, hogy nincs-e hálózati kapcsolat a Linux-gép és a folyamatkiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. Használja ezt a fiókot, csak a leküldéses telepítéshez és frissítésekhez.
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
5. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
4. Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban. Ehhez jelentkezzen be felhasználóként **legfelső szintű**.
5. Az a **/etc/ssh/sshd_config** fájlt és keresse meg azt a sort, kezdődik **PasswordAuthentication**.
6. Állítsa vissza a sort, és módosítsa az értéket **Igen**.
7. Keresse meg a sort, amely a következővel kezdődik: **alrendszer**, és állítsa vissza a sort.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Indítsa újra az **sshd** szolgáltatást.
9. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
10. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonja az asztalon, és a %ProgramData%\home\svsystems\bin mappában érhető el.
11. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.
12. Adja hozzá a létrehozott fiókot.
13. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.

## <a name="anti-virus-on-replicated-machines"></a>A replikált gépek víruskereső

Ha replikálni kívánt gépek aktív víruskereső szoftvert, ellenőrizze, hogy a mobilitási szolgáltatás telepítési mappa kizárása víruskereső operations (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció megfelelően működik-e.

## <a name="next-steps"></a>További lépések

A mobilitási szolgáltatás telepítése után, az Azure Portalon válassza ki a **+ replikálás** ezek a virtuális gépek védelmének megkezdéséhez. További információ a replikáció engedélyezése [VMware VMs(vmware-azure-enable-replication.md) és [fizikai kiszolgálók](physical-azure-disaster-recovery.md#enable-replication).


