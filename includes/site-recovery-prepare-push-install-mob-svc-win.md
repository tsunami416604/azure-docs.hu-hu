---
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122794"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Egy leküldéses telepítését egy Windows-számítógép előkészítése

1. Győződjön meg arról, hogy nincs-e a Windows-számítógép és a folyamatkiszolgáló közötti hálózati kapcsolatot.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fiók vagy helyi vagy tartományi rendszergazdai jogosultsággal kell rendelkeznie. Használja ezt a fiókot, csak a leküldéses telepítéshez és az ügynökök frissítésével.

   > [!NOTE]
   > Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Tiltsa le a távoli felhasználói hozzáférés-vezérlést, a localaccounttokenfilterpolicy beállításjegyzékbeli kulcs hozzáadása egy új DWORD: **LocalAccountTokenFilterPolicy**. Állítsa az értékét **1**. A parancssorba a feladat végrehajtásához futtassa a következő parancsot:  
   > `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. A Windows tűzfal a védeni kívánt számítógépen, válassza ki a **lehetővé teszik az alkalmazás vagy szolgáltatás tűzfalán keresztül**. Engedélyezése **fájl- és nyomtatómegosztás** és **Windows Management Instrumentation (WMI)**. Tartományi számítógépek esetében konfigurálhatja a tűzfal beállításai csoportházirend-objektum (GPO) használatával.

   ![Tűzfalbeállítások](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be a konfigurációs kiszolgálóra.

    b. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonja az asztalon, és a %ProgramData%\home\svsystems\bin mappában érhető el.

    c. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.

    d. Adja hozzá a létrehozott fiókot.

    e. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
<!--Update_Description: wording update-->
<!--ms.date: 09/17/2018-->