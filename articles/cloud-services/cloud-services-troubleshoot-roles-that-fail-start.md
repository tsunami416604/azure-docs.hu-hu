---
title: Nem indítható szerepkörök – problémamegoldás | Microsoft Docs
description: Íme néhány gyakori ok, amiért előfordulhat, hogy a felhőalapú szolgáltatások szerepköre nem indul el. Ezen problémák megoldásait is megadták.
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
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122731"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Nem indítható Cloud Service-szerepkörök – problémamegoldás
Íme néhány gyakori probléma és megoldás az Azure Cloud Services-szerepkörökkel kapcsolatban, amelyek nem indulnak el.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Hiányzó DLL-ek vagy függőségek
Az **inicializálás**, a **foglalt**és a leállítási állapotok közötti kerékpározást nem válaszoló szerepköröket és szerepköröket a hiányzó DLL-ek vagy szerelvények okozzák.

Hiányzó DLL-ek vagy szerelvények tünetei a következőket okozhatják:

* A szerepkör-példány az **inicializálás**, a foglalt és a leállítási állapotok közötti kerékpározás.
* A szerepkör-példánya **készen áll** , de ha a webalkalmazásra navigál, a lap nem jelenik meg.

A problémák kivizsgálásához számos ajánlott módszer áll rendelkezésre.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Egy webes szerepkör hiányzó DLL-problémáinak diagnosztizálása
Amikor egy webes szerepkörbe telepített webhelyre navigál, és a böngésző a következőhöz hasonló kiszolgálóhiba jelenik meg, az azt jelezheti, hogy hiányzik egy DLL.

![Kiszolgálóhiba a következő alkalmazásban: "/".](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problémák diagnosztizálása az egyéni hibák kikapcsolásával
A részletes hibaüzenetek megtekinthetők úgy, hogy a webes szerepkörhöz tartozó web. config fájl konfigurálásával beállítja az egyéni hibaüzenetet a szolgáltatás kikapcsolására és újbóli üzembe helyezésére.

Ha a Távoli asztal használata nélkül szeretné megtekinteni a teljes hibákat:

1. Nyissa meg a megoldást a Microsoft Visual Studióban.
2. A **megoldáskezelő**keresse meg a web. config fájlt, és nyissa meg.
3. A web. config fájlban keresse meg a System. Web szakaszt, és adja hozzá a következő sort:

    ```xml
    <customErrors mode="Off" />
    ```
4. Mentse a fájlt.
5. A szolgáltatás újracsomagolása és újbóli üzembe helyezése.

A szolgáltatás újratelepítése után egy hibaüzenet jelenik meg a hiányzó szerelvény vagy DLL nevével.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problémák diagnosztizálása a távoli hiba megtekintésével
A Távoli asztal használatával elérheti a szerepkört, és távolról is megtekintheti a teljes körű hibaüzeneteket. A következő lépésekkel tekintheti meg a hibákat a Távoli asztal használatával:

1. Győződjön meg arról, hogy az Azure SDK 1,3-as vagy újabb verziója telepítve van.
2. A megoldás a Visual Studióval való üzembe helyezése során engedélyezze a Távoli asztal. További információkért lásd: [Távoli asztali kapcsolat engedélyezése az Azure-ban Cloud Services szerepkörhöz a Visual Studióval](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. A Microsoft Azure Portalban, miután a példány **kész**állapotba került, a távoli a példányba. A távoli asztal Cloud Services használatával történő használatáról további információt a következő témakörben talál: [távoli szerepkör-példányok](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Jelentkezzen be a virtuális gépre a Távoli asztal konfigurálása során megadott hitelesítő adatok használatával.
6. Nyissa meg a parancsablakot.
7. Gépelje be: `IPconfig`.
8. Jegyezze fel az IPV4-címek értékét.
9. Nyissa meg az Internet Explorert.
10. Írja be a webalkalmazás címe és nevét. Például: `http://<IPV4 Address>/default.aspx`.

A webhelyre való navigálás ekkor több explicit hibaüzenetet ad vissza:

* Kiszolgálóhiba a következő alkalmazásban: "/".
* Leírás: Kezeletlen kivétel történt az aktuális webes kérelem végrehajtása során. A hibával kapcsolatos további információkért tekintse át a verem nyomkövetését, és hogy honnan származik a kód.
* Kivétel részletei: System.IO.FIleNotFoundException: Nem tölthető be a következő fájl vagy szerelvény: "Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = semleges, PublicKeyToken = 31bf856ad364e35" vagy annak valamelyik függősége. A számítógép nem találja a megadott fájlt.

Példa:

![Explicit Kiszolgálóhiba a következő alkalmazásban: "/".](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problémák diagnosztizálása a Compute Emulator használatával
A Microsoft Azure Compute Emulator segítségével diagnosztizálhatja és elháríthatja a hiányzó függőségekkel és a web. config hibákkal kapcsolatos problémákat.

A diagnosztikai módszer használatának legjobb eredményeihez olyan számítógépet vagy virtuális gépet kell használnia, amely a Windows tiszta telepítését használja. Az Azure-környezet legjobb szimulálása érdekében használja a Windows Server 2008 R2 x64-et.

1. Telepítse az [Azure SDK](https://azure.microsoft.com/downloads/)önálló verzióját.
2. A fejlesztői gépen hozza létre a Cloud Service-projektet.
3. A Windows Intézőben navigáljon a Cloud Service-projekt bin\debug mappájához.
4. Másolja a. CSX mappát és a. cscfg fájlt arra a számítógépre, amelyet a problémák hibakereséséhez használ.
5. A tiszta gépen nyisson meg egy Azure SDK parancssori ablakot, és írja be `csrun.exe /devstore:start`a következőt:.
6. A parancssorba írja be a `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`következőt:.
7. A szerepkör indításakor a részletes hibaüzenetek jelennek meg az Internet Explorerben. A probléma további diagnosztizálásához használhatja a szabványos Windows hibaelhárítási eszközöket is.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problémák diagnosztizálása a IntelliTrace használatával
A .NET-keretrendszer 4-es verzióit használó feldolgozó és webes szerepkörök esetében használhatja a [IntelliTrace](/visualstudio/debugger/intellitrace), amely a Microsoft Visual Studio Enterprise-ban érhető el.

Az alábbi lépéseket követve telepítheti a szolgáltatást a IntelliTrace engedélyezésével:

1. Ellenőrizze, hogy telepítve van-e az Azure SDK 1,3-as vagy újabb verziója.
2. A megoldás üzembe helyezése a Visual Studio használatával. Az üzembe helyezés során jelölje be a **IntelliTrace engedélyezése a .net 4-szerepkörökhöz** jelölőnégyzetet.
3. A példány elindítása után nyissa meg a **Server Explorert**.
4. Bontsa ki az **Azure\\Cloud Services** csomópontot, és keresse meg a központi telepítést.
5. Bontsa ki a központi telepítést, amíg meg nem jelenik a szerepkör példányai. Kattintson a jobb gombbal az egyik példányra.
6. Válassza a **IntelliTrace-naplók megtekintése**lehetőséget. Ekkor megnyílik a **IntelliTrace összegzése** .
7. Keresse meg az összefoglalás kivételek szakaszát. Ha kivételek vannak, a szakasz a kivételek általjelzett adattípust jelöli.
8. Bontsa ki a kivételeket, és keresse meg a **System. IO. FileNotFoundException** az alábbihoz hasonló hibákat:

![Kivételt képező, hiányzó fájl vagy szerelvény](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Hiányzó dll-EK és szerelvények címe
A DLL-fájl hiányzó és szerelvény-hibáinak elhárításához kövesse az alábbi lépéseket:

1. Nyissa meg a megoldást a Visual Studióban.
2. A **Megoldáskezelőban**nyissa meg a **hivatkozások** mappát.
3. Kattintson a hibában azonosított szerelvényre.
4. A **Tulajdonságok** ablaktáblán keresse meg a **Másolás helyi tulajdonságot** , és állítsa az értéket **igaz**értékre.
5. Telepítse újra a Cloud Service-t.

Miután meggyőződött róla, hogy az összes hibát kijavította, a szolgáltatás központi telepítését a **IntelliTrace engedélyezése a .net 4-szerepkörökhöz** jelölőnégyzet bejelölése nélkül végezheti el.

## <a name="next-steps"></a>További lépések
További [hibaelhárítási cikkek](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) a Cloud Services szolgáltatáshoz.

Ha szeretné megtudni, hogyan lehet elhárítani a Cloud Service szerepkörrel kapcsolatos problémákat az Azure Pásti számítógép-diagnosztikai adataival, tekintse [meg a Kevin Williamson blog-sorozatot](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
