---
title: Egy Azure-felhőszolgáltatás vagy a virtuális gépen a Visual Studio hibakereső |} A Microsoft Docs
description: Hibakeresés a Felhőszolgáltatások és virtuális gép a Visual Studióban
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 9bbd7f72be6ef65a0cd4178b31f18a9765690837
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969467"
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Egy Azure-felhőszolgáltatás vagy a virtuális gépen a Visual Studio hibakereső

A Visual Studio az Azure cloud services és virtual machines hibakeresési különböző lehetőségeket kínál.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>A helyi számítógépen a cloud service hibakeresése

Időt takaríthat meg, és költséget takaríthat meg az Azure compute emulator egy helyi gépen a felhőszolgáltatás hibakeresést. A szolgáltatás helyi hibakeresés a telepítése előtt, növelheti megbízhatóságát és teljesítményét anélkül számítási időért. Azonban néhány olyan hiba is felléphet csak futtatásakor egy felhőalapú szolgáltatás az Azure-ban magát. Ezek a hibák is hibakeresési, ha engedélyezi a távoli hibakeresés, amikor közzéteszi a szolgáltatást, és a egy szerepkörpéldány majd csatolja a hibakeresőt.

Az emulator szimulálja az Azure számítási szolgáltatás, és a helyi környezetben futtatja, hogy tesztelje, és a cloud service hibakeresése, üzembe helyezés előtt. Az emulator a szerepkörpéldányok életciklusának kezeli, és hozzáférést biztosít a szimulált erőforrásaihoz, például a helyi tárterület. Hibakeresés vagy a szolgáltatás futtatásakor a Visual Studióból, automatikusan elindul az emulátor egy háttér-alkalmazásként, és majd üzembe helyezi a szolgáltatást az emulátorban. Az emulator használatával megtekintheti a szolgáltatás a helyi környezetben futtatásakor. A teljes verziót vagy az emulator express verziója fut. (Az Azure 2.3-as verziótól kezdődően az emulator express verziója a az alapértelmezett.) Lásd: [Emulator Express használatával futtassa, és a helyi Cloud Service hibakeresése a](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>A helyi számítógépen a cloud service hibakeresése

1. A menüsávban válassza **Debug**, **Start Debugging** futtatásához az Azure felhőszolgáltatás-projekt. Alternatív megoldásként megnyomja az F5 billentyűt. Megjelenik egy üzenet, amely a Compute Emulator indítása folyamatban van. Amikor elindul az emulátor, a rendszer tálcai ikonja megerősíti azt.

    ![Azure-emulátorban a tálcán](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. A felhasználói felület megjelenítése a compute Emulator nyissa meg a helyi menü, az Azure ikont az értesítési területről, és válassza ki **Show Compute Emulator felhasználói felületén**.

    A bal oldali ablaktáblán a felhasználói felület, amely jelenleg a compute emulator és a szerepkörpéldányok, amely minden egyes szolgáltatás fut a szolgáltatások jeleníti meg. A szolgáltatás vagy -szerepkörök életciklusa, naplózási és diagnosztikai információk megjelenítéséhez a jobb oldali ablaktáblán választhat. Ha a fókusz egy része (Tulajdonságok) ablak felső margó helyezi, akkor kitölti a jobb oldali ablaktáblán.

3. A parancsok kiválasztásával az alkalmazáson keresztül. lépés a **Debug** menü és a töréspontok beállításával a kódban. Az alkalmazás a hibakeresőt elvégezhető, mivel az ablaktáblák frissülnek az alkalmazás aktuális állapotát. Ha a hibakeresés leállításához az alkalmazás központi telepítése törlődik. Ha az alkalmazás tartalmaz egy webes szerepkörben, és beállította az indítási műveletet tulajdonság a webböngésző indítása, a Visual Studio elindítja a webalkalmazás a böngészőben. Ha módosítja a szolgáltatáskonfiguráció szolgáltatásbeállítása szerepkör példányainak számát, meg kell leállíthatja a felhőszolgáltatást, és indítsa újra, hogy ezek a szerepkör új példányát is hibakeresése hibakeresés.

    **Megjegyzés:** leállításakor fut, vagy a szolgáltatás hibakeresés, a helyi compute emulator és a storage emulator nem leállt. Le kell állítani őket explicit módon az értesítési területről.

## <a name="debug-a-cloud-service-in-azure"></a>Az Azure cloud service hibakeresése

Egy távoli számítógépről egy felhőszolgáltatás hibakeresést, engedélyeznie kell a funkció explicit módon, hogy a szükséges szolgáltatások (például msvsmon.exe) telepítve vannak a virtuális gépek, amelyek a szerepkör-példány futtatása a cloud Services telepítésekor. Ha nem engedélyezi a távoli hibakeresés mellett tette közzé a szolgáltatást, akkor tegye közzé újra a szolgáltatás engedélyezve van a távoli hibakeresésnek köszönhetően.

Ha engedélyezi a felhőszolgáltatás távoli hibakeresési eszközeivel, ez nem mutatnak, a teljesítmény csökkenését, vagy további díjak vonatkoznak. Ne használjon távoli hibakeresése egy éles szolgáltatást, mert az ügyfelek, akik a szolgáltatást negatívan befolyásolhatja.

> [!NOTE]
> Amikor közzétesz egy felhőalapú szolgáltatás, a Visual Studióból, engedélyezheti **IntelliTrace** a szolgáltatás, amely a .NET-keretrendszer 4 vagy a .NET-keretrendszer 4.5-ös szerepkörök. Használatával **IntelliTrace**, vizsgálja meg, amely egy szerepkörpéldány a múltban történt eseményeket, és Reprodukálja a környezet ettől. Lásd: [IntelliTrace és a Visual Studio egy közzétett felhőszolgáltatás hibakeresést](http://go.microsoft.com/fwlink/?LinkID=623016) és [használatával IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>A felhőszolgáltatások távoli hibakeresés engedélyezése

1. Az Azure-projekt helyi menüjének megnyitásához, és válassza ki **közzététel**.

2. Válassza ki a **átmeneti** környezet és a **Debug** konfigurációja.

    Ez az útmutató csak. Kérheti, hogy a tesztelési környezetek éles környezetben való futtatásához. Azonban Ön kedvezőtlen hatással lehet felhasználók Ha engedélyezi a távoli hibakeresése az éles környezetben. Kiválaszthatja, hogy a kiadás konfigurációban, de a hibakeresési konfiguráció lehetővé teszi, hogy könnyebb hibakeresés.

    ![Válassza ki a hibakeresési konfiguráció](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Hajtsa végre a szokásos lépéseket, de válassza ki a **távoli hibakeresőt engedélyezése az összes szerepkörhöz** jelölőnégyzet be van jelölve a **speciális beállítások** fülre.

    ![Konfigurációs hibakeresése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>A Hibakereső csatlakoztatása egy felhőalapú szolgáltatás, az Azure-ban

1. A Server Explorerben bontsa ki a csomópont a felhőszolgáltatáshoz.

2. Nyissa meg a helyi menü, amelyhez csatolni, és válassza ki a kívánt szerepkör vagy szerepkörpéldány **csatolja a hibakeresőt**.

    Egy szerepkör hibakeresést, a Visual Studio hibakereső funkcióját csatolja a szerepkör minden példányára. A hibakereső megszakítja az első a szerepkörpéldányhoz, amely adott kódsor fut, és azokat a feltételeket, hogy megfelel-e egy töréspontot a. Ha egy példányt, a hibakereső rendeli, csak a-példány és a egy töréspontot, csak akkor, ha az adott példány adott kódsor fut, és megfelel-e a töréspont az oldaltörések hibakeresést.

    ![Hibakereső csatlakoztatása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. Csatolja a hibakeresőt egy példányt, miután hibakeresése a szokásos módon. Automatikusan csatolja a hibakeresőt a megfelelő gazdagép a folyamathoz a szerepkörhöz. A hibakereső megfelelően a szerepkört, w3wp.exe, WaWorkerHost.exe vagy WaIISHost.exe konzisztenciája érdekben. Ellenőrizze a folyamat, amely a hibakeresőt csatlakozik, bontsa ki a példány csomópontot, a Server Explorerben. Lásd: [Azure szerepkör-architektúra](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Azure folyamatokkal kapcsolatos további információkat.

    ![Válassza ki a kód típusú párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. Azonosíthatja a folyamatokat, amelyhez a hibakeresőt csatlakozik, a menüsor, kiválasztása hibakeresése, a Windows, a folyamatok által, folyamatok párbeszédpanel megnyitásához. (Billentyűzet: Ctrl + Alt + Z) Egy adott folyamat leválasztása, nyissa meg a helyi menüt, és válassza **leválasztása folyamatban**. Vagy, a Kiszolgálókezelőben keresse meg a példány csomópontot, keresse meg a folyamat, a helyi menü megnyitásához, és kattintson **leválasztása folyamatban**.

    ![Hibakeresési folyamatok](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Elkerülése érdekében hosszú leállítja a töréspontok keresése, ha a távoli hibakeresés. Az Azure olyan folyamat, amely válaszol néhány percnél hosszabb ideig le van állítva, és nem irányít több forgalmat az adott példány kezeli. Ha túl sokáig leállítja, a msvsmon.exe leválasztja a folyamatból.

Válassza le az összes folyamat a hibakeresőt a példány vagy szerepkör, nyissa meg a szerepkör vagy-példányt, hibakeresés, és válassza a helyi menü **leválasztása hibakereső**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Korlátozások a távoli hibakeresés az Azure-ban

Azure SDK 2.3-as, a távoli hibakeresés rendelkezik a következő korlátozások vonatkoznak:

* A távoli hibakeresést engedélyezve van, nem tehet közzé egy felhőalapú szolgáltatás, amelyben minden olyan szerepkört a 25-nél több példánnyal rendelkezik.
* A hibakereső 30400 való 30424, 31400 való 31424 és 32400 való 32424 portokat használ. Ha meg ezeket a portokat használja, nem lehet közzétenni a szolgáltatás, és az alábbi hibaüzenetek valamelyike jelenik meg a tevékenységnaplóban Azure:

  * Hiba történt a .cscfg fájl .csdef-fájl érvényesítése.
    A fenntartott tartomány "porttartomány" végpont "szerepkör" szerepkör Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector átfedésben van egy már meghatározott portot vagy porttartományt.
  * A lefoglalás sikertelen. Próbálkozzon újra később, csökkentse a virtuális gép méretét vagy a szerepkörpéldányok számát, vagy próbálja más régióban üzembe.

## <a name="debugging-azure-virtual-machines"></a>Hibakeresés az Azure-beli virtuális gépek

A Visual Studio Server Explorer használatával Azure-beli virtuális gépeken futó programok hibakeresése is. Ha engedélyezi a távoli hibakeresés az Azure virtuális gépen, Azure telepíti a távoli hibakeresési bővítményt a virtuális gépen. Ezután folyamatok a virtuális gép csatlakoztatása és hibakeresése a megszokott módon.

> [!NOTE]
> Az Azure resource manager-készletben keresztül létrehozott virtuális gépek távoli hibakereséséhez is a Visual Studio 2015-öt a Cloud Explorer használatával. További információkért lásd: [Azure-erőforrások kezelése a Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Az Azure virtuális gép hibakeresése

1. A Server Explorerben bontsa ki a virtuális gépek csomópontot, majd válassza ki a virtuális gép, amelyen hibakeresést végez.

2. A megnyíló helyi menüből, és válassza ki **hibakeresés engedélyezése**. Ha a rendszer kéri, hogy biztos benne, hogy szeretné engedélyezni a virtuális gép, jelölje be a hibakeresés, **Igen**.

    Azure a virtuális gépet a hibakeresés engedélyezése a távoli hibakeresési bővítmény telepítése.

    ![Virtuális gép hibakeresési parancs engedélyezése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-tevékenységnapló](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. A távoli hibakeresési bővítmény a telepítés befejezését követően a virtuális gép helyi menü megnyitásához, és válassza ki **Hibakereső csatlakoztatása...**

    Az Azure a folyamatok listáját kéri le a virtuális gépen, és megjeleníti őket a csatolási folyamat párbeszédpanel.

    ![Csatolja a hibakeresőt parancs](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. Az a **folyamat csatolása** párbeszédpanelen jelölje ki **kiválasztása** korlátozása csak a kódot, amelyen hibakeresést végez típusú megjeleníthető eredmények listájában. Lehetősége nyílik a hibakeresésére 32 bites vagy 64 bites felügyelt kódot, natív kódú vagy mindkettőt.

    ![Válassza ki a kód típusú párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Válassza ki a kívánt hibakeresése a virtuális gépen, és válassza ki a folyamatok **Attach**. Választhatja például a w3wp.exe folyamat, ha a virtuális gépen webalkalmazás hibakeresése szeretne. Lásd: [hibakeresése egy vagy több folyamatot a Visual Studióban](https://msdn.microsoft.com/library/jj919165.aspx) és [Azure szerepkör-architektúra](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) további információt.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Hozzon létre egy webes projekt és a egy virtuális gépet a hibakereséshez

Az Azure-projekt közzététele, akkor hasznosak lehetnek teszteléséhez tartalmazott környezetben, amely támogatja a hibakeresési és tesztelési forgatókönyvek, és ahol telepítheti a teszteléshez és programok figyelése. Egy ilyen tesztek futtatása módja távoli hibakeresése az alkalmazás virtuális gépen.

A Visual Studio ASP.NET-projektek egy alkalmazás teszteléséhez használható praktikus virtuális gép létrehozása lehetőséget kínálnak. A virtuális gép például PowerShell, a távoli asztal és a WebDeploy gyakran szükséges végpontokat tartalmazza.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Hozhat létre webes projektet és a egy virtuális gépet a hibakereséshez

1. A Visual Studióban hozzon létre egy új ASP.NET-alkalmazás.

2. Az Azure szakaszban, az új ASP.NET projekt párbeszédpanelén válassza **virtuális gép** a legördülő listában. Hagyja a **létre távoli erőforrásokat** jelölőnégyzet be van jelölve. Válassza ki **OK** a folytatáshoz.

    A **virtuális gép létrehozása az Azure-ban** párbeszédpanel jelenik meg.

    ![ASP.NET webes projekt párbeszédpanel létrehozása](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Megjegyzés:** meg kell adnia az Azure-fiókkal bejelentkezni, ha még nem jelentkezett.

3. Válassza ki a virtuális gépet a különböző beállításait, majd **OK**. Lásd: [virtuális gépek](http://go.microsoft.com/fwlink/?LinkId=623033) további információt.

    A név, DNS-nevet adja meg a virtuális gép neve lesz.

    ![Virtuális gép létrehozása az Azure párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Az Azure létrehozza a virtuális gépet, és a rendelkezések, és konfigurálja a végpontot, például a távoli asztal és a Web Deploy

4. A virtuális gép teljes konfigurálása után válassza ki a virtuális gép csomópontját a Server Explorerben.

5. A megnyíló helyi menüből, és válassza ki **hibakeresés engedélyezése**. Ha a rendszer kéri, hogy biztos benne, hogy szeretné engedélyezni a virtuális gép, jelölje be a hibakeresés, **Igen**.

    Az Azure a virtuális gépet a hibakeresés engedélyezése a távoli hibakeresési bővítmény telepítése.

    ![Virtuális gép hibakeresési parancs engedélyezése](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-tevékenységnapló](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. A projekt közzététele a leírt módon [hogyan: egy Web projektet használatával kattintásos közzététellel a Visual Studio telepítése](https://msdn.microsoft.com/library/dd465337.aspx). Mivel a virtuális gépen, a debug szeretné a **beállítások** lapján a **Publish Web** varázslóban válassza **Debug** , a konfiguráció. Ez biztosítja, hogy a kód szimbólumok állnak rendelkezésre a hibakeresés során.

    ![Közzétételi beállítások](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. Az a **Fájlközzétételi beállítás**válassza **célhelyen további fájlok eltávolítása** Ha a projekt már üzemel egy korábbi időpontra.

8. Miután közzéteszi a projektet, a virtuális gép helyi menüben a Server Explorerben válassza **Hibakereső csatlakoztatása...**

    Az Azure a folyamatok listáját kéri le a virtuális gépen, és megjeleníti őket a csatolási folyamat párbeszédpanel.

    ![Csatolja a hibakeresőt parancs](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. Az a **folyamat csatolása** párbeszédpanelen jelölje ki **kiválasztása** korlátozása csak a kódot, amelyen hibakeresést végez típusú megjeleníthető eredmények listájában. Lehetősége nyílik a hibakeresésére 32 bites vagy 64 bites felügyelt kódot, natív kódú vagy mindkettőt.

    ![Válassza ki a kód típusú párbeszédpanel](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Válassza ki a kívánt hibakeresése a virtuális gépen, és válassza ki a folyamatok **Attach**. Választhatja például a w3wp.exe folyamat, ha a virtuális gépen webalkalmazás hibakeresése szeretne. Lásd: [hibakeresése egy vagy több folyamatot a Visual Studióban](https://msdn.microsoft.com/library/jj919165.aspx) további információt.

## <a name="next-steps"></a>További lépések

* Használat **Intellitrace** naplózása a hívások és az események gyűjtését kiadás kiszolgáló. Lásd: [IntelliTrace és a Visual Studio egy közzétett Felhőszolgáltatás hibakeresést](http://go.microsoft.com/fwlink/?LinkID=623016).

* Használat **Azure Diagnostics** jelentkezhet be a részletes információkat a kód futtatásának belül szerepköröket, a szerepkörök a fejlesztői környezetben vagy az Azure-ban fut-e. Lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](http://go.microsoft.com/fwlink/p/?LinkId=400450).
