---
title: "Egy felhőalapú szolgáltatás, az Azure-eszközökkel közzététele |} Microsoft Docs"
description: "További tudnivalók az Azure cloud service projektek közzététele a Visual Studio használatával."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Egy felhőalapú szolgáltatás, az Azure-eszközökkel közzététele
A Microsoft Visual Studióhoz készült Azure-eszközök segítségével közvetlenül a Visual Studióból lehet közzétenni az Azure-alkalmazásokat. A Visual Studio a felhőszolgáltatások átmeneti és éles környezetének esetében egyaránt támogatja az integrált közzétételt.

Az Azure-alkalmazások közzététele előtt Azure-előfizetéssel kell rendelkeznie. Is be kell állítania egy felhőalapú szolgáltatás és a tárolási fiók az alkalmazás által használható. Állíthat be ezeket az [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> Ha közzéteszi a, kiválaszthatja a környezet a felhőalapú szolgáltatáshoz. Választania kell is, amelyek a központi telepítés alkalmazáscsomag tárolására szolgál. A központi telepítést követően az alkalmazáscsomag a tárfiók törlődik.
> 
> 

Ha fejlesztés és tesztelés az Azure-alkalmazások, segítségével a Web Deploy a webes szerepkörök Növekményesen változtatásokat. Miutá közzéteszi az alkalmazás központi telepítési környezetben, a Web Deploy segítségével telepíthet módosításokat közvetlenül a virtuális gépet, hogy fut a webes szerepkör. Nem kell csomagot, és tegye közzé a teljes Azure alkalmazást minden alkalommal, amikor frissíti a webes szerepkör segítségével tesztelje a módosításokat. Ezt a módszert lehet elérhető a webes szerepkör módosítások nélkül vár a központi telepítési környezetben közzétett alkalmazás teszteléséhez a felhőben.

Az alábbi eljárásokkal az Azure alkalmazás közzétételére és a webes szerepkör frissítése a Web Deploy használatával:

* Közzététel vagy a csomag a Visual Studio Azure-alkalmazásfejlesztő
* A webes szerepkör részeként a fejlesztési és tesztelési ciklus frissítése

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Közzététel vagy a csomag az Azure-alkalmazások Visual studióból
Az Azure-alkalmazásában közzétételekor teheti az alábbi műveletek közül:

* Service-csomag létrehozása: a központi telepítési környezetben az alkalmazás közzétételére használhatja ezt a csomagot és a szolgáltatás konfigurációs fájlja a [klasszikus Azure portál](http://go.microsoft.com/fwlink/?LinkID=213885).
* Az Azure a Visual Studio-projekt közzététele: az alkalmazás közvetlenül az Azure-bA közzétételére, a varázslóval közzététele. További információ: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Service-csomag létrehozása a Visual Studio eszközből
1. Ha készen áll az alkalmazás közzétételére, nyissa meg a Megoldáskezelőben, amely tartalmazza a szerepkörök Azure-projekt helyi menüjének megnyitásához, és válassza a Publish.
2. A service-csomag létrehozásához kövesse az alábbi lépéseket:  
   
   1. Az Azure-projekt helyi menüjében válassza **csomag**.
   2. Az a **Azure alkalmazás becsomagolása** párbeszédpanelen válassza ki a szolgáltatás konfigurációját, amelyekhez hozzon létre egy csomagot, és válassza a a build konfigurációját.
   3. (választható) Kapcsolja be a távoli asztal a felhőszolgáltatás közzététel után, jelölje be a **összes szerepkör távoli asztal engedélyezése** jelölje be a jelölőnégyzetet, majd válassza ki **beállítások** távoli asztal konfigurálása. Ha azt szeretné, a felhőalapú szolgáltatás hibakeresési közzététel után, kapcsolja be a távoli hibakeresés kiválasztásával **távoli hibakereső engedélyezése az összes szerepkör**.
      
      További információkért lásd: [a távoli asztal Azure szerepkörök](vs-azure-tools-remote-desktop-roles.md).
   4. A csomag létrehozásához válassza ki a **csomag** hivatkozásra.
      
      A Fájlkezelőben az újonnan létrehozott csomag fájl helye látható. Ezen a helyen, hogy használhassa az másolhatja a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Közzétenni a csomagot a központi telepítési környezetekben, kell használnia ezen a helyen, a csomag helyével felhőalapú szolgáltatás létrehozása és központi telepítésekor ez a csomag a környezetbe a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Választható) Megszakítja a telepítési folyamatot, a helyi menüben a műveletnaplóban, sor elem kiválasztása **szakítsa meg, és távolítsa el**. Ez a telepítési folyamat leáll, és törli a környezet az Azure-ból.
   
   > [!NOTE]
   > A telepítési környezet után üzembe helyezéséig eltávolításához kell használnia a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Választható) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a telepítési környezet a **Felhőszolgáltatások** a Server Explorer csomópont. Itt láthatja az egyéni szerepkörpéldányok állapotát. Lásd: [erőforrások kezelése Azure Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Az alábbi ábrán a szerepkörpéldányok, amíg azok továbbra is az inicializálás állapotban:
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>A webes szerepkör részeként a fejlesztési és tesztelési ciklus frissítése
Ha az alkalmazás háttér-infrastruktúra stabil, de a webes szerepkörök ki kell gyakoribb, frissítése, használhatja a Web Deploy csak egy webes szerepkör frissítése a projektben. Ez akkor hasznos, nem szeretnénk építse újra, és telepítse újra a háttérrendszer feldolgozói szerepköröket, vagy ha több webes szerepkör rendelkezik, és frissíti a webes szerepkörök közül csak.

### <a name="requirements"></a>Követelmények
Az alábbiakban vonatkozó követelmények és a Web Deploy használatához a webes szerepkör frissítése:

* **A fejlesztéshez és teszteléshez csak jellegű:** a módosításai közvetlenül a virtuális gép amelyen fut a webes szerepkör. Ha a virtuális gép újrahasznosításra kerül, a módosítások elvesznek az eredeti csomagot közzétett használatával hozza létre újra a virtuális gép a szerepkörhöz. Újra tegye közzé az alkalmazás számára elérhetők legyenek a legutóbbi változtatások a webes szerepkör.
* **Csak a webes szerepkörök frissíthető:** feldolgozói szerepkörök nem lehet frissíteni. Emellett a webes role.cs RoleEntryPoint nem frissíthető.
* **Csak egyet támogat a webes szerepkör egyetlen példányát:** bármely webes szerepkör több példánya a központi telepítési környezetben nem lehet. Azonban több webes szerepkör minden csak egy példány támogatott.
* **Engedélyeznie kell a távoli asztali kapcsolatok:** Erre azért szükség, így a Web Deploy használhatja a felhasználói és a jelszót a virtuális gép központi telepítése a módosításokat az Internet Information Services (IIS) rendszert futtató kiszolgálóhoz való kapcsolódáshoz. Ezenkívül szükség lehet csatlakozni a virtuális gép hozzáadása a megbízható tanúsítvány az IIS a virtuális gépen. (Ez biztosítja, hogy az IIS által a Web Deploy használt távoli kapcsolat biztonságos.)

A következő eljárás feltételezi, hogy a **Azure-alkalmazás közzététele** varázsló.

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Ahhoz, hogy a Web Deploy, amikor az alkalmazás közzétételére
1. Ahhoz, hogy a **engedélyezéséhez a Web Deploy** az összes webes szerepkörök jelölőnégyzetet, először konfigurálnia kell távoli asztali kapcsolatokhoz. Válassza ki **távoli asztal engedélyezése** az összes szerepkört, majd megadja a távolról a kapcsolódáshoz használt hitelesítő adatait a **a távoli asztal konfigurálásának** meg. Lásd: [a távoli asztal Azure szerepkörök](vs-azure-tools-remote-desktop-roles.md) további információt.
2. Annak engedélyezéséhez a Web Deploy az alkalmazás a webes szerepkörök **engedélyezéséhez a Web Deploy minden webes szerepkörök**.
   
    Figyelmeztetést jelző sárga háromszög jelenik meg. A Web Deploy alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, egy a Web Deploy kapcsolatokhoz használt SSL-tanúsítványt is hozzáadhat. Ez a tanúsítvány megbízható tanúsítvány szükséges. Ezzel kapcsolatos további információkért lásd: a szakasz **való ellenőrizze webalkalmazás telepítése biztonságos** a témakör későbbi részében.
3. Válassza ki **tovább** megjelenítése a **összegzés** képernyőn, és válassza a **közzététel** a felhőalapú szolgáltatás telepítéséhez.
   
    A felhőszolgáltatás közzé van téve. A létrehozott virtuális gépek rendelkezik távoli kapcsolatok engedélyezése az IIS-hez, így a Web Deploy segítségével a webes szerepkörök frissítése nélkül újbóli közzétételét őket.
   
   > [!NOTE]
   > Ha a konfigurált webes szerepkör több példánya van, egy figyelmeztető üzenet jelenik meg, arról, hogy csak a csomagban, amely az alkalmazás közzétételére létrejön egy példányra korlátozott lesz-e a minden webes szerepkör. Válassza ki **OK** folytatja. A követelményeket ismertető részben leírtaknak egynél több webes szerepkör, de csak egy példányát minden egyes szerepkör lehet.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>A Web Deploy használatával a webes szerepkör frissítése
1. A Web Deploy használatához módosításokat kód a projektet a Visual Studio közzététele, majd kattintson a jobb gombbal a projektcsomópontra, a megoldásban, és mutasson a kívánt webes szerepkörök bármelyikéhez **közzététel**. A **webhely közzététele** párbeszédpanel jelenik meg.
2. (Választható) Ha egy megbízható SSL-tanúsítványt az IIS a távoli kapcsolatokhoz használandó, törölheti a **engedélyezése nem megbízható tanúsítvány** jelölőnégyzetet. Annak a Web Deploy biztonságos tanúsítvány adásával kapcsolatos információkért lásd: a szakasz **való ellenőrizze webalkalmazás telepítése biztonságos** a témakör későbbi részében.
3. A Web Deploy használatához a közzététel mechanizmus a felhasználónevet és jelszót, amely állít be a távoli asztali kapcsolat esetén a csomag először közzé kell.
   
   1. A **felhasználónév**, írja be a felhasználónevet.
   2. A **jelszó**, írja be a jelszót.
   3. (Választható) Ha azt szeretné, a jelszó mentése a ebbe a profilba, **jelszó mentése**.
4. A változások közzétételére a webes szerepkör, válassza ki a **közzététel**.
   
    Az állapotsor megjelenítése **lépések közzététel**. A közzétételi befejezése után **sikeres közzététel** jelenik meg. A módosítások most már van telepítve a webkiszolgáló szerepkör a virtuális gépen. Most már megkezdheti az Azure alkalmazás tesztelése a módosításokat az Azure környezetben.

### <a name="to-make-web-deploy-secure"></a>Végrehajtásához webes telepítése biztonságos
1. A Web Deploy alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, egy a Web Deploy kapcsolatokhoz használt SSL-tanúsítványt is hozzáadhat. Ezt a tanúsítványt kell lennie egy megbízható tanúsítványt, amely a hitelesítésszolgáltatótól (CA) kapott.
   
    Ahhoz, hogy a Web Deploy biztonságos az egyes virtuális gépek a webes szerepkörök, fel kell töltenie a megbízható tanúsítványok, amelyek a weben használni kívánt központi telepítése a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885). Ez lehetővé teszi, hogy a tanúsítvány hozzá van adva a virtuális gépet, amely a webes szerepkör jön létre, amikor az alkalmazás közzétételére.
2. Megbízható az SSL-tanúsítvány hozzáadása az IIS használata a távoli kapcsolatokhoz, kövesse az alábbi lépéseket:
   
   1. A webes szerepkör-példányt jelöli ki, hogy a webes szerepkör fut a virtuális gép csatlakozni **Cloud Explorer** vagy **Server Explorer**, majd válassza a **csatlakozzon a távoli asztali kapcsolattal**  parancsot. A virtuális géphez történő csatlakozáshoz kapcsolatos részletes lépéseiért lásd: [a távoli asztal Azure szerepkörök](vs-azure-tools-remote-desktop-roles.md).
      
      A böngésző felszólítja a letölteni egy. RDP-fájlt.
   2. Az SSL-tanúsítvány hozzáadása, nyissa meg a felügyeleti szolgáltatás az IIS-kezelőben. Az IIS-kezelőben SSL engedélyezéséhez nyissa meg a **kötések** hivatkozásra a **művelet** ablaktáblán. A **hely kötésének hozzáadása** párbeszédpanel jelenik meg. Válasszon **Hozzáadás**, és válassza ki a HTTPS a **típus** legördülő listából. Az a **SSL-tanúsítvány** menüben válassza ki az SSL-tanúsítvány, amely meg kellett egy hitelesítésszolgáltató által aláírt és a feltöltött a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885). További információkért lásd: [kapcsolat konfigurálása a felügyeleti szolgáltatás](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Ha hozzáad egy megbízható az SSL-tanúsítvány, figyelmeztetést jelző sárga háromszög nem fog többé megjelenni a **közzététele varázsló**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Fájlok szerepeljenek a Service-csomag
Szükség lehet ahhoz, hogy adott fájlok szerepeljen a szolgáltatáscsomagot, hogy elérhetők a virtuális gépen, amely a szerepkör jön létre. Például előfordulhat, hogy hozzáadandó .exe vagy az .msi fájlt egy indítási parancsfájl a szolgáltatáscsomag által használt. Vagy előfordulhat, hogy szeretné egy szerelvény, amelyhez a webes szerepkör vagy feldolgozói szerepkör projekt hozzáadása. Fájlokat kell adni őket a megoldást az Azure-alkalmazásában.

### <a name="to-include-files-in-the-service-package"></a>A service-csomag fájlokat felvenni
1. Egy szerelvény hozzáadása a szolgáltatáscsomagot, tegye a következőket:
   
   1. A **Megoldáskezelőben** nyissa meg a projekt csomópontjára a projekthez, hiányzik a hivatkozott szerelvényt.
   2. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüje a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A hivatkozás hozzáadása párbeszédpanel jelenik meg.
   3. Válassza ki a hivatkozást, amelyet szeretne hozzáadni, és válassza ki a **OK** gombra.
      
      A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.
   4. Nyissa meg a helyi menü hozzáadott szerelvénynek, és válassza a **tulajdonságok**. A **tulajdonságok** ablak jelenik meg.
      
      Ez a szolgáltatás csomagban szerelvényét bele szeretné a **másolása helyi lista** válasszon **igaz**.
2. A **Megoldáskezelőben** nyissa meg a projekt csomópontjára a projekthez, hiányzik a hivatkozott szerelvényt.
3. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüje a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A **hivatkozás hozzáadása** párbeszédpanel jelenik meg.
4. Válassza ki a hivatkozást, amelyet szeretne hozzáadni, és válassza ki a **OK** gombra.
   
    A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.
5. Nyissa meg a helyi menü hozzáadott szerelvénynek, és válassza a **tulajdonságok**. A Tulajdonságok ablak.
6. Ez a szolgáltatás csomagban szerelvényét bele szeretné a **másolása helyi** menüben válassza ki **igaz**.
7. A szervizcsomagban, amely a webes szerepkör projekt lettek hozzáadva a beágyazandó fájlokat, a fájlt a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **tartalom** a a **Build művelet** lista.
8. Fájlok szerepeljenek a service-csomagot, amely a feldolgozói szerepkör projekt lettek hozzáadva, a fájlt a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **másolhatja, ha újabb** a a **másolása a kimeneti könyvtárba** lista.

## <a name="next-steps"></a>Következő lépések
További információt a közzététel az Azure-bA a Visual Studio eszközből, lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

