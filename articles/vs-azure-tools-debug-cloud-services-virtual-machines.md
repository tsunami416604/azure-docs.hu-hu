---
title: "Egy Azure-felhőszolgáltatásban vagy a virtuális gép a Visual Studio hibakeresési |} Microsoft Docs"
description: "Egy felhőalapú szolgáltatás, vagy a virtuális gép a Visual Studio hibakereső"
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 32f8ba565904682b3bcb9860c641bac18705ad5f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Egy Azure-felhőszolgáltatásban vagy a virtuális gép a Visual Studio hibakereső

A Visual Studio hibakeresési Azure felhőszolgáltatások és virtuális gépek különböző lehetőséget biztosít.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>A felhőalapú szolgáltatás a helyi számítógépen hibakeresése

Időt takaríthat meg, és az Azure használatával pénzt számítási emulátor hibakeresése a felhőalapú szolgáltatás a helyi számítógépen. Által a szolgáltatás telepítése előtt helyileg hibakeresés, akkor is megbízhatóságának és teljesítményének javítása számítási alkalommal fizető nélkül. Azonban néhány hiba is felléphet csak futtatásakor egy felhőalapú szolgáltatás az Azure-ban magát. Ezek a hibák megoldhassuk, ha engedélyezi a távoli hibakeresés, majd csatolja a hibakereső a szerepkör példánya és a szolgáltatást teszik közzé.

Az emulátor szimulálja az Azure számítási szolgáltatás, és a helyi környezetben fut, így tesztelése és hibakeresése a felhőalapú szolgáltatás telepítése előtt. Az emulátor kezeli a szerepkörpéldányok életciklusát, és szimulált erőforrások, például a helyi tároló hozzáférést biztosít. Hibakeresési, illetve a szolgáltatás futtatásához a Visual Studio automatikusan elindul az emulátor a háttér-alkalmazásként, és majd központilag telepíti a szolgáltatást a emulátorhoz. Az emulátor segítségével megtekintheti a szolgáltatás, ha fut a helyi környezetben. A teljes verziója vagy a emulator express verziója is futtathatja. (Az Azure 2.3-től kezdődően az express verzió az emulátor az alapértelmezett beállítás.) Lásd: [Emulator Express használatával futtatni, és egy felhőalapú szolgáltatás helyileg hibakeresési](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>A felhőalapú szolgáltatás a helyi számítógépen hibakeresése

1. A menüsávban válassza **Debug**, **Start Debugging** az Azure-felhőszolgáltatás-projekt futtatásához. Alternatív megoldásként nyomja le az F5 billentyűt. Egy üzenet, amely megkezdi a Compute Emulator jelenik meg. Amikor elindul az emulátor, a rendszer tálcaikon megerősíti, hogy azt.

    ![A tálcán Azure emulátorban](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. A felhasználói felület megjelenítése a compute Emulator nyissa meg a helyi menü az Azure ikont az értesítési területen, majd válassza ki **Show Compute Emulator felhasználói felületén**.

    A felhasználói felület a bal oldali ablaktáblán látható a szolgáltatásokat, amelyeket a compute emulator és a szerepkörpéldányok, hogy fut-e minden service jelenleg telepítve vannak. Kiválaszthatja a szolgáltatás- vagy szerepkörök életciklusát, naplózási és diagnosztikai információk megjelenítéséhez a jobb oldali ablaktáblán. Ha a fókusz be a felső margó egy befoglalt ablak, bővíti kitöltse a jobb oldali ablaktáblán.

3. Az alkalmazás lépésenként parancsok választva módosíthatja a **Debug** menü és beállítása a töréspontokat a kódban. Az alkalmazás a hibakeresőben lépéseit, mert az ablak az alkalmazás az aktuális állapotát is frissül. Ha kikapcsolja a hibakeresést, az alkalmazás központi telepítése törlődik. Ha az alkalmazás tartalmaz a webes szerepkör, és beállította az indítási műveletet tulajdonság elindítani a webböngészőt, a Visual Studio a webes alkalmazás elindítja a böngészőben. Ha módosítja a szolgáltatás konfigurációját a szerepkör-példányok száma, akkor leállíthatja a felhőszolgáltatást, és majd indítsa újra a hibakeresést, hogy ezek a szerepkör új példányát is hibakeresése.

    **Megjegyzés:** leállításakor fut, vagy a szolgáltatás hibakeresés, a helyi a compute emulator és a storage emulator nem leállt. Le kell állítani azokat explicit módon az értesítési területről.

## <a name="debug-a-cloud-service-in-azure"></a>Hibakeresése az Azure-felhőszolgáltatás

Debug egy távoli számítógépről egy felhőalapú szolgáltatás, engedélyeznie kell, hogy a funkció explicit módon a felhőalapú szolgáltatás, hogy a szükséges szolgáltatások (például msvsmon.exe) telepítve legyen a szerepkörpéldányok futó virtuális gépek központi telepítésekor. Ha nem engedélyezi a távoli hibakeresés, a szolgáltatás közzétételekor, akkor a szolgáltatás távoli hibakeresés engedélyezésével közzé.

Ha engedélyezi a távoli hibakeresés egy felhőalapú szolgáltatás, ez nem csökkent teljesítményt mutat, vagy további költségek. Ne használjon távoli hibakeresés egy éles szolgáltatásra, mert a szolgáltatást használó ügyfelek negatív hatással lehet.

> [!NOTE]
> Ha közzéteszi a Visual Studio felhőszolgáltatás, engedélyezheti a **IntelliTrace** , hogy a szolgáltatás a .NET-keretrendszer 4 vagy a .NET-keretrendszer 4.5 célzó szerepkörök. A **IntelliTrace**, vizsgálja meg az eseményeket, amelyek az elmúlt egy szerepkörpéldányt történt, és Reprodukálja kezdve a környezetben. Lásd: [egy közzétett felhőszolgáltatás IntelliTrace és a Visual Studio hibakeresési](http://go.microsoft.com/fwlink/?LinkID=623016) és [használatával IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Egy felhőalapú szolgáltatás távoli hibakeresés engedélyezése

1. Az Azure-projekt helyi menüjének megnyitásához, majd válassza ki **közzététel**.

2. Válassza ki a **átmeneti** környezet és a **Debug** konfigurációs.

    Ez az útmutató csak. Dönthet úgy is, a tesztkörülmények között éles környezetben való futtatásához. Azonban ez káros hatással lehet felhasználók Ha engedélyezi a távoli hibakeresés a termelési környezetben. Választhat, hogy a kiadás configuration, de a hibakeresési konfiguráció esetén könnyebb hibakeresést.

    ![Válassza ki a hibakeresési konfiguráció](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Kövesse a szokásos lépéseket, de válassza ki a **összes szerepkör távoli hibakereső engedélyezése** jelölőnégyzetet a **speciális beállítások** fülre.

    ![Konfigurációs hibakeresése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>A hibakereső csatlakoztatni az Azure-felhőszolgáltatás

1. A Server Explorer eszközben bontsa ki a csomópontot, a felhőalapú szolgáltatáshoz.

2. Nyissa meg a helyi menü csatolja, és válassza ki a kívánt szerepkör vagy szerepkörpéldány **csatolása hibakereső**.

    Ha hibakeresési szerepkör, a Visual Studio hibakereső feltünteti az adott szerepkör csatlakozik. A hibakereső megszakítja a töréspont az első szerepkörpéldányhoz, amely futtatja a sort, és azokat a feltételeket, hogy az megfelel-e. Ha a példány, a hibakereső rendeli csak adott példányt és a töréspont csak akkor, ha az adott példány fut a sort, és a töréspont feltételeknek megfelelő oldaltörések hibakeresését.

    ![Hibakereső csatlakoztatása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. A hibakereső példányhoz csatolja, miután hibakeresési a szokásos módon. A hibakereső automatikusan csatolja a megfelelő gazdagép-folyamat az adott szerepkörhöz. Attól függően, hogy mi a szerepkört a hibakereső csatolja a w3wp.exe, WaWorkerHost.exe vagy WaIISHost.exe. A folyamat, amely a hibakereső csatlakozik ellenőrzéséhez bontsa ki a példány csomópontot, a Server Explorer. Lásd: [Azure szerepkör architektúra](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Azure folyamatokkal kapcsolatos további információkat.

    ![Válassza ki a kód párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. Azonosítására a folyamatok, amelyek a hibakereső csatlakozik, a folyamatok párbeszédpanel megnyitásához, a menüsoron, hibakeresési, a Windows, a folyamatok kiválasztása. (Billentyűzet: Ctrl + Alt + Z) Válassza le a megadott folyamatáról, nyissa meg a helyi menüt, és válassza **leválasztani folyamat**. Vagy, a példány csomópont található a Server Explorer, a folyamat található, a helyi menü megnyitásához, és válassza **leválasztani folyamat**.

    ![Folyamatok hibakeresése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Hosszú leáll, amikor távoli töréspontok elkerülése hibakeresést. Azure értékként kezelje-e a folyamat, amely válaszol néhány percnél hosszabb ideig leáll, és leállítja a forgalom küldése annak a példánynak. Ha túl sokáig megszakítja, msvsmon.exe leválik a folyamatot.

A hibakereső összes folyamatok leválasztása a példány vagy a szerepkör, nyissa meg a szerepkör vagy a példányt, hibakeresése, és válassza a helyi menü **leválasztani hibakereső**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Korlátozások a távoli hibakeresés az Azure-ban

Az Azure SDK 2.3 távoli hibakeresés rendelkezik a következő korlátozások vonatkoznak:

* Távoli hibakeresés engedélyezésével, egy felhőalapú szolgáltatás, amelyben minden olyan szerepkört 25-nél több példánya van nem tehető közzé.
* A hibakereső 30400 való 30424, 31400 való 31424 és 32400 való 32424 portot használ. Ha használja ezeket a portokat valamelyikét, sem lesz a szolgáltatás közzététele, és az alábbi hibaüzenetek valamelyike jelenik meg a műveletnaplóban Azure:

  * Hiba történt a .cscfg fájl .csdef fájl ellenőrzése.
    A fenntartott tartomány "porttartomány" végpont "szerepkör" szerepkör Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector átfedésben van egy már definiált portot vagy porttartományt.
  * A lefoglalás sikertelen. Próbálkozzon újra később, próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkörpéldányok számát, vagy próbálja más régióban üzembe helyezni.

## <a name="debugging-azure-virtual-machines"></a>Az Azure virtuális gépek hibakeresés

A Visual Studio Server Explorer használatával az Azure virtuális gépeken futó programok megoldhassuk. Ha engedélyezi az Azure virtuális géphez távoli hibakeresés, az Azure a távoli hibakeresési bővítmény telepítését a virtuális gépen. Ezután csatolása a virtuális gépen folyamatok és hibakeresése a szokásos módon.

> [!NOTE]
> Az Azure resource manager-készletben használatával létrehozott virtuális gépeket a Visual Studio 2015 Cloud Explorer használatával távolról indítja is. További információkért lásd: [Azure-erőforrások kezelése Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Egy Azure virtuális gép hibakeresése

1. A Server Explorer eszközben bontsa ki a virtuális gépek csomópontot, és jelölje ki a virtuális gép hibakeresése kívánt csomópontot.

2. Nyissa meg a helyi menüt, és válassza ki **hibakeresés engedélyezése**. Amikor a rendszer kéri, ha biztos benne, ha engedélyezi a virtuális gépet, jelölje be a hibakeresés **Igen**.

    Azure-hibakeresés engedélyezése a virtuális gépen a távoli hibakeresési bővítmény telepítését.

    ![Virtuális gép hibakeresési parancs engedélyezése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure tevékenységnapló](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. Telepíti a távoli hibakeresési bővítmény befejeződése után nyissa meg a virtuális gép helyi menüt, majd válassza **csatolása hibakereső...**

    Azure a folyamatok listájának lekérése a virtuális gépen, és megjeleníti őket a csatolási folyamat párbeszédpanel.

    ![A hibakereső parancs csatolása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. Az a **folyamat csatolása** párbeszédpanelen jelölje ki **kiválasztása** csak hibakeresési kívánt kódot típusú megjelenítendő eredmények-lista korlátozását. 32 bites vagy 64 bites felügyelt kódot, natív kóddal vagy mindkettő megoldhassuk.

    ![Válassza ki a kód párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Válassza ki a hibakeresési a virtuális gépen, és válassza ki a kívánt folyamatokat **Attach**. Például a w3wp.exe folyamat célszerű használni, ha szeretné a webes alkalmazás a virtuális gép hibakeresése. Lásd: [Debug egy vagy több folyamatot, a Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) és [Azure szerepkör architektúra](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) további információt.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Webes projektet és a hibakereséshez a virtuális gép létrehozása

Az Azure-projekt közzététele, hasznosak lehetnek, ha kipróbálja tartalmazott környezetben, amely támogatja a Hibakeresés és tesztelési forgatókönyvek, és ahol telepítése teszteléshez és programok figyelése. Egy ilyen tesztek futtatásához módja távolról alkalmazás hibakeresése a virtuális gépen.

A Visual Studio ASP.NET projektek ajánlatot egy lehetőség, hogy az alkalmazás teszteléséhez használható lesz szüksége virtuális gép létrehozása. A virtuális gép tartalmaz, például a PowerShell, a távoli asztal és a WebDeploy gyakran szükséges végpontok.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Webes projektet és a hibakereséshez a virtuális gép létrehozása

1. A Visual Studióban hozzon létre egy új ASP.NET Webalkalmazásként való kezelése.

2. Új ASP.NET projekt párbeszédpanelen Azure területen válasszon **virtuális gép** a legördülő listában. Hagyja a **távoli erőforrások létrehozása** jelölőnégyzet be van jelölve. Válassza ki **OK** a folytatáshoz.

    A **virtuális gép létrehozása az Azure** párbeszédpanel jelenik meg.

    ![ASP.NET webes projekt párbeszédpanel létrehozása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Megjegyzés:** meg kell adnia az Azure-fiókjával bejelentkezni, ha van már nincs bejelentkezve.

3. Válassza ki a virtuális gép különböző beállításait, és válassza ki **OK**. Lásd: [virtuális gépek](http://go.microsoft.com/fwlink/?LinkId=623033) további információt.

    A név DNS-név lesz a virtuális gép nevét.

    ![Virtuális gép létrehozása Azure párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure létrehozza a virtuális gép, és a rendelkezések, és konfigurálja a végpontokat, például a távoli asztal és a Web Deploy

4. A virtuális gép teljes konfigurálása után válassza ki a virtuális gép csomópontját a Server Explorer.

5. Nyissa meg a helyi menüt, és válassza ki **hibakeresés engedélyezése**. Amikor a rendszer kéri, ha biztos benne, ha engedélyezi a virtuális gépet, jelölje be a hibakeresés **Igen**.

    Azure-hibakeresés engedélyezése a virtuális géphez a távoli hibakeresési bővítmény telepítését.

    ![Virtuális gép hibakeresési parancs engedélyezése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure tevékenységnapló](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. A projekt közzététele a [Útmutató: a webes projekt használ egy kattintással közzététele a Visual Studio telepítése](https://msdn.microsoft.com/library/dd465337.aspx). Mivel a virtuális gépen, a debug szeretné a **beállítások** oldalán a **webhely közzététele** varázslóban válassza **Debug** beállításként. Ez biztosítja, hogy kód szimbólumok hibakeresés során rendelkezésre álló is.

    ![Közzétételi beállítások](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. Az a **Fájlközzétételi beállítás**, jelölje be **célhelyen további fájlok eltávolítása** Ha a projekt korábban már telepítve lett.

8. Kiválasztása után a projekt tesz közzé, a virtuális gép helyi menüben a Server Explorer eszközben **csatolása hibakereső...**

    Azure a folyamatok listájának lekérése a virtuális gépen, és megjeleníti őket a csatolási folyamat párbeszédpanel.

    ![A hibakereső parancs csatolása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. Az a **folyamat csatolása** párbeszédpanelen jelölje ki **kiválasztása** csak hibakeresési kívánt kódot típusú megjelenítendő eredmények-lista korlátozását. 32 bites vagy 64 bites felügyelt kódot, natív kóddal vagy mindkettő megoldhassuk.

    ![Válassza ki a kód párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Válassza ki a hibakeresési a virtuális gépen, és válassza ki a kívánt folyamatokat **Attach**. Például a w3wp.exe folyamat célszerű használni, ha szeretné a webes alkalmazás a virtuális gép hibakeresése. Lásd: [Debug egy vagy több folyamatot, a Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) további információt.

## <a name="next-steps"></a>További lépések

* Használjon **Intellitrace** hívások és események naplózása gyűjteni a kiadási kiszolgáló. Lásd: [egy közzétett Felhőszolgáltatás IntelliTrace és a Visual Studio hibakeresési](http://go.microsoft.com/fwlink/?LinkID=623016).

* Használjon **Azure Diagnostics** részletes adatok naplózására kód futását szerepköröket, az, hogy a szerepkörök fut a fejlesztési környezet vagy az Azure-ban. Lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](http://go.microsoft.com/fwlink/p/?LinkId=400450).
