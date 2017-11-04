---
title: "Szerepkörök, amelyek nem indulnak el hibaelhárítása |} Microsoft Docs"
description: "Az alábbiakban néhány gyakori okáról, miért a felhőalapú szolgáltatás szerepkör esetleg nem indulnak el. Ezek a problémák megoldások is rendelkezésre állnak."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: ec33ba08c6284e90edc1870eef4bf3059b917efb
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>A felhőalapú szolgáltatás nem indult el szerepkörtől hibaelhárítása
Az alábbiakban néhány gyakori problémát és Azure felhőszolgáltatások kapcsolatos megoldások szerepköröket, amelyek nem indulnak el.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Hiányzó DLL-ek vagy függőségek
Nem válaszoló szerepkörök és a szerepköröket, amelyek között vannak Váltás **inicializálás**, **foglalt**, és **leállítása** állapotok hiányzik a dll-fájl vagy szerelvény okozhatja.

Hiányzik a dll-fájl vagy szerelvény tünetei lehet:

* A szerepkör példánya van Váltás **inicializálás**, **foglalt**, és **leállítása** állapotok.
* A szerepkör példánya át lett helyezve **készen** keresse meg a webalkalmazáshoz, ha a lap nem jelenik meg.

Többféleképpen ajánlott vizsgálata ezeket a problémákat.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>A webes szerepkör hiányzó DLL problémák diagnosztizálása
Ha olyan webhely, amelyet a rendszer egy webes szerepkör, és a böngésző megjeleníti az alábbihoz hasonló kiszolgálóhiba, azt jelezheti, hogy a dll-fájl hiányzik.

![Kiszolgálóhiba a "/" alkalmazás.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Az egyéni hibák kikapcsolásával eseményadatokat
Részletesebb információ hibainformációk az konfigurálása a Web.config fájl, a webes szerepkör ki az egyéni hiba üzemmód beállítása, és a szolgáltatás újbóli tekintheti meg.

A távoli asztal használata nélkül jóval összetettebb hibák megtekintéséhez:

1. Nyissa meg a megoldást a Microsoft Visual Studio.
2. Az a **Megoldáskezelőben**, keresse meg a web.config fájlt, és nyissa meg.
3. A web.config fájlban keresse meg a system.web szakaszt, és adja hozzá a következő sort:

    ```xml
    <customErrors mode="Off" />
    ```
4. Mentse a fájlt.
5. Csomagolja újra, és telepítse újra a szolgáltatást.

Miután a szolgáltatást újra kell telepíteni, megjelenik egy hibaüzenet jelenik meg a hiányzó szerelvény vagy dll-fájl nevét.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>A hiba távolról megtekintésével eseményadatokat
Használhatja a távoli asztal eléréséhez a szerepkör, és távolról tekintheti meg bővebb hibainformáció. Az alábbi lépések segítségével meg a hibákat a távoli asztal használatával:

1. Győződjön meg arról, hogy telepítve van-e az Azure SDK 1.3-as vagy újabb.
2. A megoldás üzembe helyezése közben a Visual Studio használatával válassza a "Konfigurálása távoli asztali kapcsolatokhoz...". A távoli asztali kapcsolat konfigurálásával kapcsolatos további információkért lásd: [a távoli asztal Azure szerepkörök](../vs-azure-tools-remote-desktop-roles.md).
3. A Microsoft Azure klasszikus portálon, amennyiben az a példány állapota **készen**, kattintson a szerepkörpéldányok egyikére.
4. Kattintson a **Connect** ikonra a **távelérési** a menüszalagon látható területe.
5. Jelentkezzen be a virtuális géphez a távoli asztal konfigurálása során megadott hitelesítő adataival.
6. Nyisson meg egy parancsablakot.
7. Gépelje be: `IPconfig`.
8. Jegyezze fel az IPV4-cím értékét.
9. Nyissa meg az Internet Explorert.
10. Írja be a címet és a webes alkalmazás neve. Például: `http://<IPV4 Address>/default.aspx`.

Lépjen a webhely most visszaadható egyértelműbben hibaüzenetek:

* Kiszolgálóhiba a "/" alkalmazás.
* Leírás: Nem kezelt kivétel történt az aktuális webes kérelem végrehajtása közben. Tekintse át az alábbi veremkivonatban találhatók további információk a hiba, és azt okozó kódrészletre a kódban.
* A kivétel részletei: System.IO.FIleNotFoundException: nem sikerült betölteni a fájl vagy szerelvény "Microsoft.WindowsAzure.StorageClient, verzió: 1.1.0.0-s, Culture = neutral, PublicKeyToken = = 31bf856ad364e35" vagy annak valamelyik függősége. A rendszer nem találja a megadott fájlt.

Példa:

![A "/" alkalmazás explicit kiszolgálóhiba](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>A compute emulator használatával eseményadatokat
A Microsoft Azure compute emulator segítségével diagnosztizálhatja és a Hiányzó függőségek és web.config hibák elhárítása.

A legjobb eredmény érdekében ezzel a módszerrel a diagnosztikai egy számítógép vagy virtuális géphez, amelyen a Windows tiszta telepítését kell használnia. Legjobb szimulálja az Azure környezetbe, használja a Windows Server 2008 R2 x64.

1. Az önálló verziójának telepítése a [Azure SDK](https://azure.microsoft.com/downloads/).
2. A fejlesztési számítógépen a felhőszolgáltatás-projekt létrehozása.
3. A Windows Intézőben keresse meg a felhőszolgáltatás-projekt bin\debug mappájába.
4. Másolja a .csx mappa és a .cscfg fájl a számítógépen, amely segítségével a problémák hibakeresését.
5. A tiszta gépen nyissa meg az Azure SDK parancssori ablakot, és írja be `csrun.exe /devstore:start`.
6. A parancssorba írja be a `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. A szerepkör indulásakor látni fogja a hibával kapcsolatos részletes információk az Internet Explorerben. A probléma további diagnosztizálása érdekében használhatja a szabványos Windows hibaelhárító eszközök.

## <a name="diagnose-issues-by-using-intellitrace"></a>Eseményadatokat IntelliTrace használatával
A munkavégző és a .NET-keretrendszer 4 webes szerepkörök is használhatja [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), elérhető a Microsoft Visual Studio Enterprise.

Kövesse az alábbi lépéseket a szolgáltatás telepítése intellitrace engedélyezve:

1. Győződjön meg arról, hogy telepítve van-e az Azure SDK 1.3-as vagy újabb.
2. A megoldás üzembe helyezéséhez, a Visual Studio használatával. Ellenőrizze a telepítés során a **.NET 4-szerepkörök engedélyezése IntelliTrace** jelölőnégyzetet.
3. A példány indításakor, miután nyissa meg a **Server Explorer**.
4. Bontsa ki a **Azure\\Felhőszolgáltatások** csomópont, és keresse meg a központi telepítését.
5. Bontsa ki a központi telepítést, amíg megjelenik a szerepkörpéldányok. Kattintson a jobb gombbal egy példánya.
6. Válasszon **nézet IntelliTrace-naplók**. A **IntelliTrace összegzés** csak akkor nyílik meg.
7. Keresse meg az összegzés kivételek szakasza. Ha a kivételek vannak, a szakasz neve **Kivételadat**.
8. Bontsa ki a **Kivételadat** , és keressen **System.IO.FileNotFoundException** hibákat a következőhöz hasonló:

![Kivételadat, hiányzó fájl vagy szerelvény](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Hiányzó dll-EK és szerelvények
Hiányzó DLL és szerelvény hibák elhárítása, kövesse az alábbi lépéseket:

1. Nyissa meg a megoldást a Visual Studióban.
2. A **Megoldáskezelőben**, nyissa meg a **hivatkozások** mappát.
3. Kattintson a hiba azonosított szerelvény.
4. Az a **tulajdonságok** ablaktáblán keresse meg **másolása helyi tulajdonság** és állítsa be az értéket **igaz**.
5. Telepítse újra a felhőalapú szolgáltatást.

Miután ellenőrizte, hogy az összes hiba javítva lett, a szolgáltatás ellenőrzése nélkül telepítheti a **.NET 4-szerepkörök engedélyezése IntelliTrace** jelölőnégyzetet.

## <a name="next-steps"></a>Következő lépések
További [cikkek hibaelhárítási](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) felhőszolgáltatásai számára.

Felhőalapú szolgáltatás szerepkör kapcsolatos problémák elhárítása az Azure PaaS diagnosztikai adatainak használatával kapcsolatban lásd: [Kevin Williamson blog adatsorozat](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
