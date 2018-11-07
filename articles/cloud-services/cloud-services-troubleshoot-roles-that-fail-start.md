---
title: Szerepkörök, amelyek nem indulnak el hibaelhárítása |} A Microsoft Docs
description: Az alábbiakban néhány gyakori okáról, miért egy Cloud Service szerepkörre sikertelen indításának lehetséges. Ezek a problémák megoldásában is biztosítja.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: a73559e233f647d5bf0812a5acdf5e19f05b0858
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255421"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>A nem induló Felhőszolgáltatási szerepkörök hibaelhárítása
Az alábbiakban néhány gyakori problémát és az Azure Cloud servicesben kapcsolódó megoldások szerepköröket, amelyek nem indulnak el.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Hiányzik a DLL-ek vagy a függőségek
Nem válaszoló szerepkörök és a szerepköröket, amelyek között vannak recyklování **inicializálás**, **foglalt**, és **leállítása** állapotok hiányzik a DLL-ek vagy szerelvények okozhatja.

Hiányzik a DLL-ek vagy szerelvények tünetei lehet:

* A szerepkörpéldány cykluje keresztül **inicializálás**, **foglalt**, és **leállítása** állapotok.
* A szerepkörpéldány át lett helyezve **készen** , de ha manuálisan lép a webes alkalmazások, a lap nem jelenik meg.

Többféleképpen is ajánlott történő ezek a problémák kivizsgálását.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Egy webes szerepkörben hiányzó DLL problémák diagnosztizálása
Amikor átvált egy webhely, amelyet a webes szerepkör és a böngésző megjeleníti a következőhöz hasonló kiszolgálóhiba, azt jelezheti, hogy a dll-fájl hiányzik.

![Kiszolgálóhiba a "/" alkalmazás.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Egyéni hibák kikapcsolásával diagnosztizálhatja a problémákat
További hiba információt is megtekinthetők a Web.config fájl kikapcsolása az egyéni hiba üzemmód beállítása a webes szerepkör konfigurálásával, valamint a szolgáltatás újratelepítése.

A távoli asztal használata nélkül teljes hibák megtekintéséhez:

1. Nyissa meg a megoldást a Microsoft Visual Studio.
2. Az a **Megoldáskezelőben**, keresse meg a web.config fájlt, és nyissa meg.
3. A web.config fájlban keresse meg a system.web szakaszt, és adja hozzá a következő sort:

    ```xml
    <customErrors mode="Off" />
    ```
4. Mentse a fájlt.
5. Csomagolja újra, és telepítse újra a szolgáltatást.

Ha a szolgáltatás újratelepítése van, látni fogja a egy hibaüzenet jelenik meg a hiányzó szerelvény vagy dll-fájl neve.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problémák diagnosztizálása távolról a hiba megtekintésével
Használhatja a távoli asztali eléréséhez a szerepkört, és teljes hibaadatok megtekintéséhez távolról. Használja az alábbi lépéseket a hibák megtekintése a távoli asztal használatával:

1. Győződjön meg arról, hogy az Azure SDK 1.3-as vagy újabb verziója telepítve van-e.
2. A megoldás üzembe helyezése közben a Visual studióval a távoli asztal engedélyezése. További információkért lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. A Microsoft Azure Portalon, ha a példány egy állapotát jeleníti meg a **készen**, távoli másolatát a példányon. A távoli asztal az Cloud Services és a további információkért lásd: [szerepkörpéldányok távolról](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Jelentkezzen be a virtuális géphez a távoli asztal konfigurálása során megadott hitelesítő adataival.
6. Nyisson meg egy parancsablakot.
7. Gépelje be: `IPconfig`.
8. Jegyezze fel az IPV4-cím értékét.
9. Nyissa meg az Internet Explorert.
10. Írja be a címet és a webalkalmazás nevére. Például: `http://<IPV4 Address>/default.aspx`.

Ellenőrizheti, hogy a webhely most visszatér több explicit hibaüzenetek:

* Kiszolgálóhiba a "/" alkalmazás.
* Leírás: Nem kezelt kivétel történt az aktuális webes kérelem végrehajtása közben. Tekintse át a híváslánc a hibával kapcsolatban, és ahol, adja meg a kódot a további információt.
* Kivétel részletei: System.IO.FIleNotFoundException: nem sikerült betölteni a fájlt vagy a szerelvény "Microsoft.WindowsAzure.StorageClient, a verzió 1.1.0.0-s, Culture = neutral, PublicKeyToken = = 31bf856ad364e35" vagy a hozzá tartozó függőségek egyike. A rendszer a megadott fájl nem található.

Példa:

![A "/" alkalmazás kifejezett kiszolgálóhiba](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problémák diagnosztizálása a compute emulator használatával
A Microsoft Azure compute emulator használatával a hiányzó függőségeit, és web.config hibák problémák diagnosztizálása és hibaelhárítása.

Ezzel a módszerrel a diagnosztikai a legjobb eredmények érdekében egy számítógép vagy virtuális gépen, amelyen a Windows tiszta telepítését kell használnia. Legjobb szimulálása az Azure-környezethez, használja a Windows Server 2008 R2 x64.

1. Önálló verziójának telepítése a [Azure SDK](https://azure.microsoft.com/downloads/).
2. A fejlesztői gépen a felhőszolgáltatás-projekt létrehozása.
3. A Windows Intézőben keresse meg a felhőszolgáltatás-projekt bin\debug mappájába.
4. Másolja a .csx és a .cscfg fájljához a számítógépen, amely megkönnyíti a hibakeresést a használ.
5. A tiszta gépen nyisson meg egy Azure SDK parancssort és írja be `csrun.exe /devstore:start`.
6. A parancssorba írja be a `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. A szerepkör indulásakor látni fogja a hibával kapcsolatos részletes információk az Internet Explorerben. Használhatja a hibaelhárítási eszközei szabványos Windows a probléma további diagnosztizálása érdekében.

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace segítségével diagnosztizálhatja a problémákat
A feldolgozó és használó .NET-keretrendszer 4 webes szerepkörök esetében használható [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), amely is elérhető a Microsoft Visual Studio Enterprise.

Kövesse az alábbi lépéseket az IntelliTrace engedélyezve van a szolgáltatás üzembe helyezéséhez:

1. Győződjön meg arról, hogy az Azure SDK 1.3-as vagy újabb verziója telepítve van-e.
2. A megoldás üzembe helyezése a Visual Studio használatával. Ellenőrizze a telepítés során a **IntelliTrace engedélyezése a Microsoft .NET-4 szerepkörök** jelölőnégyzetet.
3. Miután elindult a példány, nyissa meg a **Server Explorer**.
4. Bontsa ki a **Azure\\Cloud Services** csomópont, és keresse meg a központi telepítést.
5. Bontsa ki az üzembe helyezés, amíg meg nem látja a szerepkörpéldányok. A jobb gombbal egy példányt.
6. Válasszon **nézet IntelliTrace-naplók**. A **IntelliTrace összefoglalás** nyílik meg.
7. Az összefoglalás kivételek szakaszában találja. Ha vannak olyan kivételek, a szakasz neve **Kivételadat**.
8. Bontsa ki a **Kivételadat** , és keressen **System.IO.FileNotFoundException** az alábbihoz hasonló hibák:

![Kivételadat, hiányzó fájl vagy szerelvény](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Hiányzó dll-EK és szerelvények
Hiányzó DLL és az Összeállítási hibák megoldása érdekében kövesse az alábbi lépéseket:

1. Nyissa meg a megoldást a Visual Studióban.
2. A **Megoldáskezelőben**, nyissa meg a **hivatkozások** mappát.
3. Kattintson a hibát azonosított sestavení.
4. Az a **tulajdonságok** ablaktáblán keresse meg **másolási helyi tulajdonság** , és állítsa az értékét **igaz**.
5. Telepítse újra a felhőalapú szolgáltatást.

Miután ellenőrizte, hogy az összes hibák javítását, a szolgáltatás ellenőrzése nélkül telepítheti a **IntelliTrace engedélyezése a Microsoft .NET-4 szerepkörök** jelölőnégyzetet.

## <a name="next-steps"></a>További lépések
Továbbiak megtekintése [hibaelhárítási cikkek](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) a cloud services.

Cloud service szerepkör kapcsolatos problémák elhárítása Azure PaaS számítógép-diagnosztikai adatok használatával kapcsolatban lásd: [Kevin Williamson blogsorozatot](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
