---
title: Egy felhőalapú szolgáltatás, az Azure-eszközökkel közzététele |} Microsoft Docs
description: További tudnivalók az Azure cloud service projektek közzététele a Visual Studio használatával.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 59a34f92937136079d8c693a1747b2d54f37e6d8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Egy felhőalapú szolgáltatás, a Visual Studio használatával közzététele

A Visual Studio közvetlenül az Azure-ba, átmeneti és üzemi környezetben is egy felhőszolgáltatás-támogatással rendelkező alkalmazások közzététele. Közzétételekor, a környezet és a központi telepítési csomag ideiglenesen használt tárfiók választja.

Ha Ön fejlesztés és tesztelés az Azure-alkalmazások, segítségével a Web Deploy a webes szerepkörök Növekményesen változtatásokat. Miutá közzéteszi az alkalmazás központi telepítési környezetben, a Web Deploy segítségével telepíthet módosításokat közvetlenül a virtuális gépet, hogy fut a webes szerepkör. Nem kell csomagot, és tegye közzé a teljes Azure alkalmazást minden alkalommal, amikor frissíti a webes szerepkör segítségével tesztelje a módosításokat. Ezt a módszert használja elérhető a webes szerepkör módosítások nélkül vár a központi telepítési környezetben közzétett alkalmazás teszteléséhez a felhőben lehet.

Az alábbi eljárásokkal az Azure alkalmazás közzétételére és a webes szerepkör frissítése a Web Deploy használatával:

- Közzététel vagy a csomag a Visual Studio Azure-alkalmazásfejlesztő
- A webes szerepkör részeként a fejlesztési és tesztelési ciklus frissítése

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Közzététel vagy a csomag a Visual Studio Azure-alkalmazásfejlesztő

Az Azure-alkalmazásában közzétételekor teheti az alábbi műveletek közül:

- Service-csomag létrehozása: a központi telepítési környezetben az alkalmazás közzétételére használhatja ezt a csomagot és a szolgáltatás konfigurációs fájlja a [Azure-portálon](https://portal.azure.com).

- Az Azure a Visual Studio-projekt közzététele: az alkalmazás közvetlenül az Azure-bA közzétételére, a varázslóval közzététele. További információ: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Service-csomag létrehozása a Visual Studio eszközből

1. Ha készen áll az alkalmazás közzétételére, nyissa meg a Megoldáskezelőben, amely tartalmazza a szerepkörök Azure-projekt helyi menüjének megnyitásához, és válassza a Publish.

1. A service-csomag létrehozásához kövesse az alábbi lépéseket:

   a. Az Azure-projekt helyi menüjében válassza **csomag**.

   b. Az a **Azure alkalmazás becsomagolása** párbeszédpanelen válassza ki a szolgáltatás konfigurációját, amelyekhez hozzon létre egy csomagot, és válassza a a build konfigurációját.

   c. (Választható) Kapcsolja be a távoli asztal a felhőszolgáltatás közzététel után, jelölje be **összes szerepkör távoli asztal engedélyezése**, majd válassza ki **beállítások** konfigurálása a távoli asztal hitelesítő adatokat. További információkért lásd: [engedélyezése a távoli asztali kapcsolat egy szerepkör esetén az Azure Felhőszolgáltatások Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Ha azt szeretné, a felhőalapú szolgáltatás hibakeresési közzététel után, kapcsolja be a távoli hibakeresés kiválasztásával **távoli hibakereső engedélyezése az összes szerepkör**.

   d. A csomag létrehozásához válassza ki a **csomag** hivatkozásra.

      A Fájlkezelőben az újonnan létrehozott csomag fájl helye látható. Erre a helyre másolhatja, hogy használhassa az Azure portálról.

   e. Ezt a csomagot a központi telepítési környezetekben való közzétételéhez kell használnia ezen a helyen csomag helye felhőalapú szolgáltatás létrehozása és központi telepítésekor ez a csomag és az Azure portál környezetbe.

1. (Választható) Megszakítja a telepítési folyamatot, a helyi menüben a műveletnaplóban, sor elem kiválasztása **szakítsa meg, és távolítsa el**. Ez a parancs a telepítési folyamat leáll, és törli a környezet az Azure-ból. Telepítés után a környezet eltávolításához használja az Azure-portálon.

1. (Választható) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a telepítési környezet a **Felhőszolgáltatások** a Server Explorer csomópont. Itt láthatja az egyéni szerepkörpéldányok állapotát. Lásd: [erőforrások kezelése Azure Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Az alábbi ábrán a szerepkörpéldányok, amíg azok továbbra is az inicializálás állapotban:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>A webes szerepkör részeként a fejlesztési és tesztelési ciklus frissítése

Ha az alkalmazás háttér-infrastruktúra stabil, de a webes szerepkörök ki kell gyakoribb, frissítése, használhatja a Web Deploy csak egy webes szerepkör frissítése a projektben. A Web Deploy akkor hasznos, ha nem kívánja építse újra, és telepítse újra a háttérrendszer feldolgozói szerepköröket, vagy ha még több webes szerepkör, és frissíti a webes szerepkörök közül csak.

### <a name="requirements-for-using-web-deploy"></a>A Web Deploy használatának követelményei

- **A fejlesztéshez és teszteléshez csak jellegű**: A módosításai közvetlenül a virtuális gép amelyen fut a webes szerepkör. Ha a virtuális gép újrahasznosításra kerül, a módosítások elvesznek az eredeti csomagot közzétett használatával hozza létre újra a virtuális gép a szerepkörhöz. Az alkalmazás számára elérhetők legyenek a legutóbbi változtatások a webes szerepkör közzé.

- **Csak a webes szerepkörök frissíthető**: feldolgozói szerepkörök nem lehet frissíteni. Emellett nem lehet frissíteni a `RoleEntryPoint` a `web role.cs`.

- **Csak egyet támogat a webes szerepkör egyetlen példányát**: a webes szerepkör több példánya a központi telepítési környezetben nem lehet. Azonban több webes szerepkör minden csak egy példány támogatott.

- **Engedélyezze a távoli asztali kapcsolatokat**: Ez a követelmény lehetővé teszi, hogy a Web Deploy a felhasználói és jelszót használják a virtuális gép központi telepítése a módosításokat az Internet Information Services (IIS) rendszert futtató kiszolgálóhoz való kapcsolódáshoz. Ezenkívül szükség lehet csatlakozni a virtuális gép hozzáadása a megbízható tanúsítvány az IIS a virtuális gépen. (Ez a tanúsítvány biztosítja, hogy az IIS által a Web Deploy használt távoli kapcsolat biztonságos.)

A következő eljárás feltételezi, hogy a **Azure-alkalmazás közzététele** varázsló.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Engedélyezze a Web Deploy, amikor az alkalmazás közzétételére

1. Ahhoz, hogy a **engedélyezéséhez a Web Deploy minden webes szerepkörök** beállítás, először konfigurálnia kell a távoli asztali kapcsolatokhoz. Válassza ki **távoli asztal engedélyezése** összes szerepkörhöz, majd megadja a távolról a kapcsolódáshoz használt hitelesítő adatait a **a távoli asztal konfigurálásának** meg. Lásd: [engedélyezése a távoli asztali kapcsolat egy szerepkör esetén az Azure Felhőszolgáltatások Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Annak engedélyezéséhez a Web Deploy az alkalmazás a webes szerepkörök **engedélyezéséhez a Web Deploy minden webes szerepkörök**.

    Figyelmeztetést jelző sárga háromszög jelenik meg. A Web Deploy alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, a Web Deploy kapcsolatokhoz használt SSL-tanúsítvány is hozzáadhat. Ez a tanúsítvány megbízható tanúsítvány szükséges. További információkért lásd: [telepítése webes biztonságosabbá teheti](#make-web-deploy-secure).

1. Válassza ki **tovább** megjelenítése a **összegzés** képernyőn, és válassza a **közzététel** a felhőalapú szolgáltatás telepítéséhez.

    A felhőszolgáltatás közzé van téve. A létrehozott virtuális gépek rendelkezik távoli kapcsolatok engedélyezése az IIS-hez, így a Web Deploy segítségével a webes szerepkörök frissítése nélkül újbóli közzétételét őket.

   > [!NOTE]
   > Ha a konfigurált webes szerepkör több példánya van, egy figyelmeztető üzenet jelenik meg, arról, hogy minden webes szerepkör csak a csomagban, amely az alkalmazás közzétételére létrejön egy példányra korlátozott. A folytatáshoz kattintson az **OK** gombra. A követelményeket ismertető részben leírtaknak egynél több webes szerepkör, de csak egy példányát minden egyes szerepkör lehet.

### <a name="update-your-web-role-by-using-web-deploy"></a>A webes szerepkör frissítése a Web Deploy használatával

1. A Web Deploy használatához módosításokat kód a projektet a Visual Studio közzététele, majd kattintson a jobb gombbal a projektcsomópontra, a megoldásban, és mutasson a kívánt webes szerepkörök bármelyikéhez **közzététel**. A **webhely közzététele** párbeszédpanel jelenik meg.

1. (Választható) Ha egy megbízható SSL-tanúsítványt az IIS a távoli kapcsolatokhoz használandó, törölheti a **engedélyezése nem megbízható tanúsítvány** jelölőnégyzetet. Annak a Web Deploy biztonságos tanúsítvány adásával kapcsolatos információkért lásd: a szakasz **való ellenőrizze webalkalmazás telepítése biztonságos** című cikkben.

1. A Web Deploy használatához a közzététel mechanizmus a felhasználónevet és jelszót, amely állít be a távoli asztali kapcsolat esetén a csomag először közzé kell.

   a. A **felhasználónév**, írja be a felhasználónevet.

   b. A **jelszó**, írja be a jelszót.

   c. (Választható) Ha azt szeretné, a jelszó mentése a ebbe a profilba, **jelszó mentése**.

1. A változások közzétételére a webes szerepkör, válassza ki a **közzététel**.

    Az állapotsor megjelenítése **lépések közzététel**. A közzétételi befejezése után **sikeres közzététel** jelenik meg. A módosítások most már van telepítve a webkiszolgáló szerepkör a virtuális gépen. Most már megkezdheti az Azure alkalmazás tesztelése a módosításokat az Azure környezetben.

### <a name="make-web-deploy-secure"></a>Biztonságosabbá teheti a webszolgáltatás telepítése

1. A Web Deploy alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, a Web Deploy kapcsolatokhoz használt SSL-tanúsítvány is hozzáadhat. Ezt a tanúsítványt kell lennie egy megbízható tanúsítványt, amely a hitelesítésszolgáltatótól (CA) kapott.

    Ahhoz, hogy a Web Deploy biztonságos az egyes virtuális gépek a webes szerepkörök, fel kell töltenie a megbízható tanúsítványok, amelyek a weben használni kívánt központi telepítése az Azure-portálon. Ez a tanúsítvány lehetővé teszi, hogy a tanúsítvány hozzá van adva a virtuális gépet, amely a webes szerepkör jön létre, amikor az alkalmazás közzétételére.

1. Megbízható az SSL-tanúsítvány hozzáadása az IIS használata a távoli kapcsolatokhoz, kövesse az alábbi lépéseket:

   a. A webes szerepkör-példányt jelöli ki, hogy a webes szerepkör fut a virtuális gép csatlakozni **Cloud Explorer** vagy **Server Explorer**, majd válassza a **csatlakozzon a távoli asztali kapcsolattal**  parancsot. A virtuális géphez történő csatlakozáshoz kapcsolatos részletes lépéseiért lásd: [engedélyezése a távoli asztali kapcsolat egy szerepkör esetén az Azure Felhőszolgáltatások Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). A böngésző felszólítja, hogy töltse le egy `.rdp` fájlt.

   b. Az SSL-tanúsítvány hozzáadása, nyissa meg a felügyeleti szolgáltatás az IIS-kezelőben. Az IIS-kezelőben SSL engedélyezéséhez nyissa meg a **kötések** hivatkozásra a **művelet** ablaktáblán. A **hely kötésének hozzáadása** párbeszédpanel jelenik meg. Válasszon **hozzáadása**, és válassza ki a HTTPS a **típus** legördülő listából. Az a **SSL-tanúsítvány** menüben válassza ki az SSL-tanúsítványt, hogy meg kellett egy hitelesítésszolgáltató által aláírt és feltöltése az Azure portálon. További információkért lásd: [kapcsolat konfigurálása a felügyeleti szolgáltatás](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Ha hozzáad egy megbízható az SSL-tanúsítvány, figyelmeztetést jelző sárga háromszög nem fog többé megjelenni a **közzététele varázsló**.

## <a name="include-files-in-the-service-package"></a>Fájlok szerepeljenek a service-csomag

Szükség lehet ahhoz, hogy adott fájlok szerepeljen a szolgáltatáscsomagot, hogy elérhetők a virtuális gépen, amely a szerepkör jön létre. Például előfordulhat, hogy hozzáadandó egy `.exe` vagy egy `.msi` fájlt egy indítási parancsfájl a szolgáltatáscsomag által használt. Vagy előfordulhat, hogy szeretné egy szerelvény, amelyhez a webes szerepkör vagy feldolgozói szerepkör projekt hozzáadása. A beágyazandó fájlokat, azokat hozzá kell adni a megoldáshoz az Azure alkalmazáshoz.

1. Egy szerelvény hozzáadása a szolgáltatáscsomagot, tegye a következőket:

   a. A **Megoldáskezelőben**, nyissa meg a projekt csomópontjára a projekthez, hiányzik a hivatkozott szerelvényt.
   b. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüje a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A hivatkozás hozzáadása párbeszédpanel jelenik meg.
   c. Válassza ki a hivatkozás hozzáadása, és válassza a kívánt **OK**. A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.
   d. Nyissa meg a helyi menü hozzáadott szerelvénynek, és válassza a **tulajdonságok**. A **tulajdonságok** ablak jelenik meg.

      Ez a szolgáltatás csomagban szerelvényét bele szeretné a **másolása helyi lista** válasszon **igaz**.
1. A **Megoldáskezelőben** nyissa meg a projekt csomópontjára a projekthez, hiányzik a hivatkozott szerelvényt.

1. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüje a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A **hivatkozás hozzáadása** párbeszédpanel jelenik meg.

1. Válassza ki a hivatkozást, amelyet szeretne hozzáadni, és válassza ki a **OK** gombra.

    A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.

1. Nyissa meg a helyi menü hozzáadott szerelvénynek, és válassza a **tulajdonságok**. A Tulajdonságok ablak.

1. Ez a szolgáltatás csomagban szerelvényét bele szeretné a **másolása helyi** menüben válassza ki **igaz**.

1. A szervizcsomagban, amely a webes szerepkör projekt lettek hozzáadva a beágyazandó fájlokat, a fájlt a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **tartalom** a a **Build művelet** lista.

1. Fájlok szerepeljenek a service-csomagot, amely a feldolgozói szerepkör projekt lettek hozzáadva, a fájlt a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **másolhatja, ha újabb** a a **másolása a kimeneti könyvtárba** lista.

## <a name="next-steps"></a>További lépések

További információt a közzététel az Azure-bA a Visual Studio eszközből, lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).
