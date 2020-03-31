---
title: A forrásgépek előkészítése a mobilitási szolgáltatás leküldéses telepítésen keresztül a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő vészhelyreállításához | Microsoft dokumentumok
description: Ismerje meg, hogyan készítheti fel a kiszolgálót a Mobility ügynök telepítésére a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállítási telepítésével az Azure-ba az Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186454"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Készítse elő a forrásgépet a mobilitási ügynök leküldéses telepítéséhez

Ha az [Azure Site Recovery](site-recovery-overview.md)használatával vészhelyreállítást állít be a VMware virtuális gépekhez és a fizikai kiszolgálókhoz, a Site Recovery Mobility [szolgáltatást](vmware-physical-mobility-service-overview.md) minden helyszíni VMware virtuális gépre és fizikai kiszolgálóra telepíti.  A Mobilitás szolgáltatás rögzíti az adatok írásait a számítógépen, és továbbítja azokat a Site Recovery folyamatkiszolgálóra.

## <a name="install-on-windows-machine"></a>Telepítés Windows rendszerű számítógépre

Minden védeni kívánt Windows-számítógépen tegye a következőket:

1. Győződjön meg arról, hogy van-e hálózati kapcsolat a számítógép és a folyamatkiszolgáló között. Ha még nem állított be külön folyamatkiszolgálót, akkor az alapértelmezés szerint a konfigurációs kiszolgálón fut.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak rendszergazdai jogokkal kell rendelkeznie, legyen az helyi vagy tartományi. Ezt a fiókot csak a leküldéses telepítéshez és az ügynök frissítésekhez használja.
2. Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés vezérlését a helyi számítógépen az alábbiak szerint:
    - A HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\CurrentVersion\Policies\Registry key csoportban adjon hozzá egy új duplaszót: **LocalAccountTokenFilterPolicy**. Állítsa az **1**értéket 1-re.
    -  Ha ezt a parancssorban szeretné megtenni, futtassa a következő parancsot:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. A védeni kívánt számítógépen a Windows tűzfal ban jelölje be **Az alkalmazás vagy szolgáltatás engedélyezése tűzfalon keresztül**lehetőséget. **Fájl- és nyomtatómegosztás engedélyezése,** valamint **a Windows Management Instrumentation (WMI) engedélyezése.** A tartományhoz tartozó számítógépek esetében a tűzfal beállításait csoportházirend-objektum (GPO) segítségével konfigurálhatja.

   ![Tűzfalbeállítások](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
5. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonként érhető el az asztalon és a %ProgramData%\ASR\home\svsystems\bin mappában.
6. A **Fiókok kezelése** lapon válassza a **Fiók hozzáadása**lehetőséget.
7. Adja hozzá a létrehozott fiókot.
8. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.

## <a name="install-on-linux-machine"></a>Telepítés Linux gépre

Minden védeni kívánt Linux-gépen tegye a következőket:

1. Győződjön meg arról, hogy van-e hálózati kapcsolat a Linux-gép és a folyamatkiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. Ezt a fiókot csak a leküldéses telepítéshez és a frissítésekhez használja.
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
5. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
4. Engedélyezze az SFTP-alrendszer és a jelszóhitelesítést a sshd_config fájlban. Ehhez jelentkezzen be **gyökérként**.
5. Az **/etc/ssh/sshd_config** fájlban keresse meg a **PasswordAuthentication**programmal kezdődő sort.
6. A sor megjegyzésének megjegyzése nélkül, és módosítsa az értéket **igen**értékre.
7. Keresse meg az **Alrendszer**lelkezdődő sort, és vonja vissza a sor megjegyzését.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Indítsa újra az **sshd** szolgáltatást.
9. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
10. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonként érhető el az asztalon és a %ProgramData%\home\svsystems\bin mappában.
11. A **Fiókok kezelése** lapon válassza a **Fiók hozzáadása**lehetőséget.
12. Adja hozzá a létrehozott fiókot.
13. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.

## <a name="anti-virus-on-replicated-machines"></a>Víruselleni szer másolt gépeken

Ha a replikálni kívánt gépek aktív víruskereső szoftvert futtatnak, győződjön meg arról, hogy kizárja a Mobility szolgáltatás telepítési mappáját a víruskereső műveletekből (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció a várt módon működjön.

## <a name="next-steps"></a>További lépések

A mobilszolgáltatás telepítése után az Azure Portalon válassza a **+ Replikálás** lehetőséget a virtuális gépek védelmének megkezdéséhez. További információ a [VMware virtuális gépek](vmware-azure-enable-replication.md) és [a fizikai kiszolgálók](physical-azure-disaster-recovery.md#enable-replication)replikációjának engedélyezéséről.


