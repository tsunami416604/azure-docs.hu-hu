---
title: A nem induló szerepkörök – problémamegoldás | Microsoft dokumentumok
description: Íme néhány gyakori oka annak, hogy a Cloud Service-szerepkör nem indul el. Ezekre a problémákra is kínálunk megoldásokat.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122731"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>A sikertelen enna-alapú felhőszolgáltatás-szerepkörök – problémamegoldás
Íme néhány gyakori probléma és megoldás az Azure Cloud Services szerepkörökkel kapcsolatban, amelyek nem indulnak el.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Hiányzó DEL-ek vagy függőségek
**Az Inicializálás**, **Foglalt**és **Leállítási** állapotok között futó nem válaszoló szerepköröket és szerepköröket a hiányzó DEL-ek vagy összeállítások okozhatják.

A hiányzó DL-ek vagy szerelvények tünetei lehetnek:

* A szerepkörpéldány az **Inicializálás,** **foglalt**és **leállítási** állapotok között.
* A szerepkörpéldány **átkerült a** Ready-re, de ha a webalkalmazásra navigál, a lap nem jelenik meg.

A problémák kivizsgálására számos ajánlott módszer létezik.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Hiányzó DLL-problémák diagnosztizálása webes szerepkörben
Amikor egy webes szerepkörben telepített webhelyre lép, és a böngésző az alábbihoz hasonló kiszolgálóhibát jelenít meg, az azt jelezheti, hogy hiányzik egy DLL.

![Kiszolgálóhiba a '/' alkalmazásban.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problémák diagnosztizálása az egyéni hibák kikapcsolásával
További hibainformációk a web.config webes szerepkörkonfigurálásával az egyéni hibamód Ki értékre való beállításával és a szolgáltatás újratelepítésével tekinthetők meg.

További teljes hibák megtekintése a Távoli asztal használata nélkül:

1. Nyissa meg a megoldást a Microsoft Visual Studio programban.
2. A **Megoldáskezelőben**keresse meg a web.config fájlt, és nyissa meg.
3. A web.config fájlban keresse meg a system.web szakaszt, és adja hozzá a következő sort:

    ```xml
    <customErrors mode="Off" />
    ```
4. Mentse a fájlt.
5. Újracsomagolni és újratelepíteni a szolgáltatást.

A szolgáltatás újratelepítése után megjelenik egy hibaüzenet a hiányzó szerelvény vagy DLL nevével.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>A problémák diagnosztizálása a hiba távolról történő megtekintésével
A Távoli asztal segítségével elérheti a szerepkört, és távolról megtekintheti a teljesebb hibainformációkat. A hibák távoli asztallal történő megtekintéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az Azure SDK 1.3-as vagy újabb telepítése.
2. A megoldás Visual Studio használatával történő telepítése során engedélyezze a Távoli asztal szolgáltatást. További információt a [Távoli asztali kapcsolat engedélyezése szerepkörhöz az Azure Cloud Servicesben a Visual Studio használatával című témakörben talál.](cloud-services-role-enable-remote-desktop-visual-studio.md)
3. A Microsoft Azure portalon, ha a példány állapota **Ready**állapotának megjelenítése, a példány távoli állapotát. A távoli asztal felhőszolgáltatásokkal való használatáról a [Távoli szerepkörpéldányok ba](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)című témakörben talál további információt.
5. Jelentkezzen be a virtuális gépre a Távoli asztal konfigurációja során megadott hitelesítő adatokkal.
6. Nyisson meg egy parancsablakot.
7. Gépelje be: `IPconfig`.
8. Jegyezze fel az IPV4-cím értékét.
9. Nyissa meg az Internet Explorert.
10. Írja be a webalkalmazás címét és nevét. Például: `http://<IPV4 Address>/default.aspx`.

A webhelyre való navigálás mostantól egyértelműbb hibaüzeneteket ad vissza:

* Kiszolgálóhiba a '/' alkalmazásban.
* Leírás: Nem kezelt kivétel történt az aktuális webes kérelem végrehajtása során. Kérjük, tekintse át a verem nyomkövetési további információt a hiba, és honnan származik a kódot.
* Kivétel részletei: System.IO.FIleNotFoundException: Nem tölthető be a fájl vagy a szerelvény 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' vagy annak egyik függősége. A megadott fájl nem található.

Példa:

![Explicit kiszolgálóhiba a '/' alkalmazásban](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problémák diagnosztizálása a számítási emulátor használatával
A Microsoft Azure számítási emulátorsegítségével diagnosztizálhatja és elháríthatja a hiányzó függőségekkel és web.config hibákkal kapcsolatos problémákat.

A legjobb eredmény a diagnosztikai módszer használata érdekében olyan számítógépet vagy virtuális gépet használjon, amely tiszta Windows-telepítéssel rendelkezik. Az Azure-környezet legjobb szimulálása érdekében használja a Windows Server 2008 R2 x64 rendszert.

1. Telepítse az [Azure SDK](https://azure.microsoft.com/downloads/)önálló verzióját.
2. A fejlesztői gépen készítse el a felhőszolgáltatási projektet.
3. A Windows Intézőben keresse meg a felhőszolgáltatás-projekt bin\debug mappáját.
4. Másolja a .csx mappát és a .cscfg fájlt arra a számítógépre, amelyen a problémák hibakereséséhez használja.
5. A tiszta gépen nyisson meg egy Azure SDK parancssori ablakot, és írja be a parancsot. `csrun.exe /devstore:start`
6. A parancssorba `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`írja be a parancsot.
7. Amikor a szerepkör elindul, részletes hibainformációkat fog látni az Internet Explorer programban. A probléma további diagnosztizálására a Windows szabványos hibaelhárító eszközeit is használhatja.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problémák diagnosztizálása az IntelliTrace használatával
A .NET Framework 4 rendszert használó feldolgozói és webes szerepkörök esetében használhatja az [IntelliTrace programot,](/visualstudio/debugger/intellitrace)amely a Microsoft Visual Studio Enterprise programban érhető el.

A szolgáltatás üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy az Azure SDK 1.3-as vagy újabb telepítése telepítve van-e.
2. Telepítse a megoldást a Visual Studio használatával. Az üzembe helyezés során jelölje be az **IntelliTrace engedélyezése a .NET 4 szerepkörökhöz** jelölőnégyzetet.
3. A példány indítása után nyissa meg a **Kiszolgálókezelőt**.
4. Bontsa ki az **Azure\\Cloud Services** csomópontot, és keresse meg a központi telepítést.
5. Bontsa ki a központi telepítést, amíg meg nem jelennek a szerepkörpéldányok. Kattintson a jobb gombbal az egyik példányra.
6. Válassza **az IntelliTrace naplók megtekintése lehetőséget.** Megnyílik **az IntelliTrace Összefoglaló.**
7. Keresse meg az összefoglaló kivételek szakaszát. Ha vannak kivételek, a szakasz **kivételadatok**címkével lesz ellátva.
8. Bontsa ki a **kivételadatokat,** és keresse meg a **System.IO.FileNotFoundException** hibákat az alábbihoz hasonló módon:

![Kivételadatok, hiányzó fájl vagy szerelvény](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Hiányzó DL-ek és szerelvények címe
A hiányzó DLL- és összeállítási hibák megoldásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a megoldást a Visual Studióban.
2. A **Megoldáskezelőben**nyissa meg a **Hivatkozások mappát.**
3. Kattintson a hibában azonosított kódösszeállításra.
4. A **Tulajdonságok** ablaktáblán keresse meg a **Helyi másolás tulajdonságot,** és állítsa az értéket **Igaz**értékre.
5. Telepítse újra a felhőszolgáltatást.

Miután meggyőződött arról, hogy minden hibát kijavítottak, telepítheti a szolgáltatást anélkül, hogy bejelölné az **IntelliTrace engedélyezése a .NET 4 szerepkörökhöz** jelölőnégyzetet.

## <a name="next-steps"></a>További lépések
További [hibaelhárítási cikkek](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) a felhőszolgáltatásokhoz.

Ha meg szeretné tudni, hogyan háríthatja el a felhőalapú szolgáltatási szerepkörrel kapcsolatos problémákat az Azure PaaS-számítógép diagnosztikai adatainak használatával, olvassa el [Kevin Williamson blogsorozatát.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
