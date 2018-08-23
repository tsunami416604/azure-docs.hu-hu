---
title: Egy Felhőszolgáltatás, Azure-eszközök segítségével közzététele |} A Microsoft Docs
description: Ismerje meg az Azure cloud service-projektek közzététele a Visual Studio használatával.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 78a313e824ee7926ab9f1875407d650d614afce2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059485"
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Közzététel a felhőszolgáltatás a Visual Studio használatával

A Visual Studio közzétesz egy alkalmazást az Azure-felhőszolgáltatás átmeneti és az éles környezetekben támogatással. A közzétételkor kiválaszthatja a központi telepítési környezet és a egy storage-fiókot, amely ideiglenesen szolgál a központi telepítési csomag.

Takarít meg fejlesztés és tesztelés az Azure-alkalmazások, használhatja a Web Deploy a webes szerepkörök növekményes módosítások közzétételére. Miután közzéteszi az alkalmazás központi telepítési környezetre, a Web Deploy lehetővé teszi a közvetlenül üzembe helyezhetők a módosításokat a virtuális gép, amelyen fut a webes szerepkör. Nem kell csomagot, és közzétenni a teljes Azure-alkalmazásokat minden alkalommal, amikor a módosítások teszteléséhez a webes szerepkör frissíteni szeretné. Ezt a módszert használja akkor is az elérhető a webes szerepkör módosítások teszteléshez nélkül szeretné, hogy az alkalmazás közzétenni egy üzembehelyezési környezetet a felhőben.

Az alábbi eljárásokkal közzététele az Azure-alkalmazásokat és a egy webes szerepkör frissítése a Web Deploy használatával:

- Tegyen közzé, illetve a Visual Studióból az Azure alkalmazás becsomagolása
- A fejlesztési és tesztelési ciklus részeként egy webes szerepkör frissítése

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Tegyen közzé, illetve a Visual Studióból az Azure alkalmazás becsomagolása

Ha közzéteszi az Azure-alkalmazásokat, az alábbi feladatok valamelyikét teheti:

- Hozzon létre egy szolgáltatási csomagot: az alkalmazás egy központi telepítési környezetben való közzétételéhez használhatja ezt a csomagot, és a szolgáltatás konfigurációs fájlja a [az Azure portal](https://portal.azure.com).

- A Visual Studióból az Azure projekt közzététele: tesz közzé az alkalmazást közvetlenül az Azure-ba, a közzétételi varázsló használható. További információ: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Szolgáltatási csomag létrehozása a Visual Studióból

1. Amikor készen áll az alkalmazás közzétételéhez, nyissa meg a Megoldáskezelőben, nyissa meg a helyi menü, az Azure projekt, amely tartalmazza a szerepkörök, és válassza a közzététel.

1. Csak egy service-csomag létrehozásához kövesse az alábbi lépéseket:

   a. Az Azure-projekt helyi menüjében válassza a **csomag**.

   b. Az a **Azure-alkalmazás becsomagolása** párbeszédpanelen válassza ki a szolgáltatás konfigurációja, amelyhez hozzá szeretné hozzon létre egy csomagot, és válassza a build konfigurációját.

   c. (Nem kötelező) Kapcsolja be a távoli asztal a felhőalapú szolgáltatás közzététel után, jelölje be **távoli asztal engedélyezése az összes szerepkörhöz**, majd válassza ki **beállítások** távoli asztali kapcsolatok hitelesítő adatainak konfigurálása. További információkért lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Ha azt szeretné, hogy a cloud service hibakeresése a közzététel után, kapcsolja be a távoli hibakeresésről kiválasztásával **távoli hibakeresőt engedélyezése az összes szerepkörhöz**.

   d. A csomag létrehozásához, válassza ki a **csomag** hivatkozásra.

      A fájlkezelő jeleníti meg a fájl helyét, az újonnan létrehozott csomagot. Erre a helyre másolhatja, hogy használhatja az Azure Portalról.

   e. Ez a csomag közzététele egy üzembehelyezési környezetet, kell használnia ezen a helyen a csomag helyével felhőszolgáltatás létrehozása és központi telepítésekor ez a csomag és az Azure portal-környezetben.

1. (Nem kötelező) Megszakítja a telepítési folyamatot a vonal elem a tevékenységnaplóban a helyi menüben válassza ki a **megszakítása és törlése**. Ez a parancs leállítja a telepítési folyamatot, és törli az üzembehelyezési környezetet az Azure-ból. Üzembe helyezés után a környezet eltávolításához használja az Azure Portalon.

1. (Nem kötelező) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a központi telepítési környezet a **Cloud Services** csomópont a Server Explorerben. Itt láthatja az egyes szerepkörpéldányok állapotát. Lásd: [kezelése az Azure-erőforrások a Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Az alábbi ábrán látható a szerepkörpéldányok, miközben továbbra is az inicializálás állapotban vannak:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>A fejlesztési és tesztelési ciklus részeként egy webes szerepkör frissítése

Ha az alkalmazás háttérrendszere infrastruktúra stabil, de a webes szerepkörök több igényelnek gyakori frissítést, használhatja a Web Deploy csak egy webes szerepkör frissítése a projektben. A Web Deploy akkor hasznos, ha nem kívánja újraépítése és ismételt üzembe helyezése a háttérrendszer feldolgozói szerepkörök, vagy ha több webes szerepkört és a webes szerepkörök közül csak a frissíteni kívánt.

### <a name="requirements-for-using-web-deploy"></a>A Web Deploy használatának követelményei

- **Fejlesztési és tesztelési célra használja csak**: A módosítások közvetlenül a virtuális gép a webes szerepkört futtató kiszolgáló. Ha a virtuális gép újraindítására, a módosítás végleg elvész, mert az eredeti csomag közzétett segítségével hozza létre újra a virtuális gép a szerepkörhöz. Az alkalmazások a legutóbbi változtatásokat a webes szerepkör újbóli közzététele.

- **Csak a webes szerepkörök frissíthető**: feldolgozói szerepkörök nem lehet frissíteni. Emellett nem lehet frissíteni a `RoleEntryPoint` a `web role.cs`.

- **Csak egy webes szerepkör egyetlen példányát támogatja**: minden olyan webes szerepkör több példányát a központi telepítési környezet nem rendelkezik. Azonban több webes szerepkört minden egyes csak egy példánnyal rendelkező támogatottak.

- **Engedélyezze a távoli asztali kapcsolatokat**: Ez a követelmény lehetővé teszi, hogy a Web Deploy a felhasználónév és jelszó használata a virtuális gép üzembe helyezése a módosításokat az Internet Information Services (IIS) rendszert futtató kiszolgálóhoz való kapcsolódáshoz. Emellett szüksége lehet csatlakozni a virtuális gép hozzáadása egy megbízható tanúsítványt az IIS-hez a virtuális gépen. (Ez a tanúsítvány biztosítja, hogy az IIS, amelyet a Web Deploy a távoli kapcsolat biztonságos.)

A következő eljárás azt feltételezi, hogy használja a **Azure-alkalmazások közzététele** varázsló.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Engedélyezze a Web Deploy, ha az alkalmazás közzététele

1. Ahhoz, hogy a **Web Deploy engedélyezése az összes webes szerepkörök** lehetőség, először konfigurálnia kell a távoli asztali kapcsolatokat. Válassza ki **távoli asztal engedélyezése** összes szerepköre, majd megadja a távolról, a kapcsolódáshoz használt hitelesítő adatokat a **a távoli asztal konfigurálásának** jelölőnégyzetet a megjelenő. Lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Web Deploy alkalmazását a webes szerepkörök esetében jelöljön ki **Web Deploy engedélyezése az összes webes szerepkörök**.

    Figyelmeztetést jelző sárga háromszög jelenik meg. A Web Deploy, alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, a Web Deploy-kapcsolatokhoz használandó SSL-tanúsítvány is hozzáadhat. Ennek a tanúsítványnak kell lennie egy megbízható tanúsítványt. További információkért lásd: [biztonságosabbá teheti a webalkalmazás üzembe helyezése](#make-web-deploy-secure).

1. Válasszon **tovább** megjelenítéséhez a **összegzése** képernyőn, és válassza **közzététel** a felhőalapú szolgáltatás üzembe helyezéséhez.

    A felhőszolgáltatás közzé van téve. A létrehozott virtuális gép rendelkezik távoli kapcsolatokat, az IIS engedélyezve van, hogy a Web Deploy segítségével a webes szerepkörök frissítése nélkül kijavítható őket.

   > [!NOTE]
   > Ha több példány egy webes szerepkör konfigurálva van, egy figyelmeztető üzenet jelenik meg, hogy minden webes szerepkör egy példány csak a csomagot, amely jön létre az alkalmazás közzétételéhez korlátozódik. A folytatáshoz kattintson az **OK** gombra. A követelmények szakaszban leírtaknak egynél több webes szerepkör, de az egyes szerepkörök csak egy példány lehet.

### <a name="update-your-web-role-by-using-web-deploy"></a>A webes szerepkör frissítése a Web Deploy használatával

1. A Web Deploy használatához módosítsa a kódokat a projekthez, sem a webes szerepkörök a Visual Studióban közzététele, majd kattintson a jobb gombbal a projektcsomópontra, a megoldás, és mutasson a kívánt **közzététel**. A **Publish Web** párbeszédpanel jelenik meg.

1. (Nem kötelező) Ha hozzáadott egy megbízható SSL-tanúsítványt az IIS távoli kapcsolatok használata, törölheti a **engedélyezése nem megbízható tanúsítványt** jelölőnégyzetet. Egy tanúsítványt, hogy a Web Deploy biztonságos adásával kapcsolatos információkért lásd: a szakasz **, győződjön meg arról, webes üzembe biztonságos** a cikk későbbi részében.

1. A Web Deploy használatához a közzétételi mechanizmust kell, a felhasználónevet és jelszót, beállíthat egy, a távoli asztali kapcsolat a csomag első közzétételekor.

   a. A **felhasználónév**, adja meg a felhasználónevet.

   b. A **jelszó**, adja meg a jelszót.

   c. (Nem kötelező) Ha azt szeretné, a jelszó mentéséhez ebben a profilban válassza **jelszó mentése**.

1. Válassza ki a változások közzétételére a webes szerepkör, **közzététel**.

    Állapotsor megjelenítése **lépések közzététel**. A közzététel befejezése után **közzététel sikerült** jelenik meg. A módosítások a webes szerepkör, a virtuális gépen, most már van telepítve. Most elkezdheti az Azure-alkalmazásokat az Azure-környezetben tesztelheti a módosításokat.

### <a name="make-web-deploy-secure"></a>Biztonságosabbá teheti a webalkalmazás üzembe helyezése

1. A Web Deploy, alapértelmezés szerint nem ajánlott a bizalmas adatok feltöltése egy nem megbízható, önaláírt tanúsítványt használ. Ha szeretné megvédeni a folyamatot a bizalmas adatokat, a Web Deploy-kapcsolatokhoz használandó SSL-tanúsítvány is hozzáadhat. Ennek a tanúsítványnak kell lennie egy megbízható tanúsítványt, amely egy hitelesítésszolgáltatótól (CA) beszerezte.

    Ahhoz, hogy a Web Deploy biztonságos, minden egyes virtuális géphez a webes szerepkörök mindegyikéhez, fel kell töltenie a megbízható tanúsítványt, amelyet szeretne használni a webes üzembe helyezése az Azure Portalra. Ez a tanúsítvány gondoskodik arról, hogy a virtuális gép számára a webes szerepkör, az alkalmazás közzétételekor létrehozott hozzáadta a tanúsítványt.

1. Megbízható SSL-tanúsítványt az IIS-hez a távoli kapcsolatokhoz használandó hozzáadásához kövesse az alábbi lépéseket:

   a. Szeretne csatlakozni a virtuális gép, amelyen fut a webes szerepkör, válassza ki a webes szerepkör példányának **Cloud Explorer** vagy **Server Explorer**, majd válassza a **csatlakoztatása a távoli asztal használatával**  parancsot. Hogyan lehet csatlakozni a virtuális gép kapcsolatos részletes lépéseiért lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). A böngésző megkéri, hogy töltse le egy `.rdp` fájlt.

   b. SSL-tanúsítvány hozzáadásához nyissa meg a felügyeleti szolgáltatás az IIS-kezelőben. Az IIS-kezelőben az SSL engedélyezéséhez nyissa meg a **kötések** hivatkozásra a **művelet** ablaktáblán. A **hely kötésének hozzáadása** párbeszédpanel jelenik meg. Válasszon **Hozzáadás**, majd válassza a HTTPS a **típus** legördülő listából válassza ki. Az a **SSL-tanúsítvány** menüben válassza ki az SSL-tanúsítványt, hogy Ön volt hitelesítésszolgáltató által aláírt és feltöltése az Azure Portalra. További információkért lásd: [Biztonságoskapcsolat-beállításainak konfigurálása a felügyeleti szolgáltatás](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Ha hozzáad egy megbízható SSL-tanúsítványt, figyelmeztetést jelző sárga háromszög nem fog többé megjelenni a **közzététele varázsló**.

## <a name="include-files-in-the-service-package"></a>Beágyazott fájlok a szolgáltatási csomag

Szüksége lehet meghatározott fájlok szerepeljenek az szolgáltatáscsomag, hogy elérhetők a szerepkör számára létrehozott virtuális gépen. Például előfordulhat, hogy szeretné hozzáadni egy `.exe` vagy egy `.msi` fájlt egy indítási szkriptet a service-csomag a által használt. Vagy szüksége lehet egy olyan webes szerepkört vagy feldolgozói szerepkör projekt igénylő szerelvény hozzáadása. Fájlokat tartalmazza, azokat hozzá kell adni a megoldás az Azure alkalmazáshoz.

1. Egy szerelvény hozzáadása egy szolgáltatáscsomag, használja az alábbi lépéseket:

   a. A **Megoldáskezelőben**, nyissa meg a projekt csomópontjára a projekthez, hogy hiányzik a hivatkozott szerelvényt.
   b. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüjének a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A hivatkozás hozzáadása párbeszédpanel jelenik meg.
   c. Válassza ki a hivatkozást, amelyet szeretne hozzáadni, majd **OK**. A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.
   d. A szerelvény hozzáadott a helyi menü megnyitásához, és válassza ki **tulajdonságok**. A **tulajdonságok** ablak jelenik meg.

      Ezt a szerelvényt a szervizcsomagban szerepeljenek a **másolási helyi listát** válassza **igaz**.
1. A **Megoldáskezelőben** nyissa meg a projekt csomópontjára a projekthez, hogy hiányzik a hivatkozott szerelvényt.

1. A szerelvény hozzáadása a projekthez, nyissa meg a helyi menüjének a **hivatkozások** mappa majd **hivatkozás hozzáadása**. A **hivatkozás hozzáadása** párbeszédpanel jelenik meg.

1. Válassza ki a hivatkozást, amelyet szeretne hozzáadni, majd a **OK** gombra.

    A hivatkozás alatti listában hozzáadódik a **hivatkozások** mappát.

1. A szerelvény hozzáadott a helyi menü megnyitásához, és válassza ki **tulajdonságok**. A Tulajdonságok ablak jelenik meg.

1. Ezt a szerelvényt a szervizcsomagban szerepeljenek a **másolási helyi** menüben válassza ki **igaz**.

1. A csomag, amely a webes szerepkör projekt lettek hozzáadva a beágyazott fájlok, a fájl a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **tartalom** a a **Build Action** lista.

1. A csomag, amely a feldolgozói szerepkör projektje lettek hozzáadva a beágyazott fájlok, a fájl a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **tulajdonságok** ablakban válassza a **másolás, ha újabb** a a **Másolás a kimeneti könyvtár** lista.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a közzététel az Azure-bA a Visual Studióból, lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).
