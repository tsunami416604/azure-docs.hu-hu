---
title: A Project beállítása a laborok bevezetésének módja a Azure Lab Services
description: Megtudhatja, hogyan állíthatja be a laborokat a projekt vezetésének tanításához.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496888"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>A laborok beállítása a projecthez a következőképpen: osztályok

A [Project Lead the way (PLTW)](https://www.pltw.org/) egy nonprofit szervezet, amely PreK-12 tananyagot biztosít a számítógépes tudományok, a mérnöki tudományok és Egyesült Államok a biogyógyászat terén.  Minden PLTW osztályban a tanulók számos különböző alkalmazást használnak a gyakorlati tanulási élmény részeként.  Számos alkalmazáshoz gyors CPU vagy bizonyos esetekben egy GPU szükséges.  Ebből a cikkből megtudhatja, hogyan állíthatja be a laborokat a következő PLTW-osztályokhoz, amelyeket általában az 9-12-es osztályba tartozó tanulóknak ajánlunk:

- **A mérnöki tervezés bemutatása**

    A tanulók bemutatják a mérnöki tervezés folyamatát, amely magában foglalja az Autodesk a 3D-modellezéshez készült [Inventor számítógépes tervezési (CAD)](https://www.autodesk.com/products/inventor/new-features) szoftvereit.

- **A mérnöki alapelvek**
    
    A tanulók megismerhetik a mérnöki mechanizmusokat, a structural\material erősségét és az automatizálást.  Ez az osztály olyan szoftvereket használ, mint például az [MD szilárdtestek](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), a [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)és az [America 's Army szimuláció](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Mélyépítés és architektúra**

    A diákok az [Autodesk Revit](https://www.autodesk.com/products/revit/overview) Architecture Design szoftver for 3D Building Information Modeling (BIM) használatával tanítják meg a fejlesztést és a telephely kialakítását és fejlesztését.

- **Számítógép-integrált gyártás**

    A tanulók a robotika és az Automation szolgáltatással foglalkozó modern gyártási folyamatokat is megismerhetik.   Ebben az osztályban a tanulók az [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) és az [Autodesk Inventor számítógép-támogatású Manufacturer (CAM)](https://www.autodesk.com/products/inventor-cam/overview) szoftverét használják. 

- **Digitális elektronika**

    A tanulók elektronikus logikai áramköröket és eszközöket tanulnak a [nemzeti eszközök Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) szimulációs és áramköri tervezési szoftverével.

- **Mérnöki tervezés és fejlesztés**

    A tanulók olyan teljes körű megoldást jelentenek, amely összekapcsolja a kutatást, a tervezést és a tesztelést, és a mérnökök egy paneljén jelennek meg.  Ebben az osztályban a tanulók az [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) szoftvert használják.

- **Computer Science Essentials**

    A tanulók a számítási fogalmakhoz és eszközökhöz lettek bevezetve.  Ezek a blokk-alapú programozással kezdődnek, majd a szöveg alapú kódolást használják a kódolási környezetek, például a [VEXcode V5-blokkok](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)használatával.

- **Számítógép-tudományi alapelvek**
    
    A tanulók a [Microsoft Visual Studio Code fejlesztői környezetével](https://code.visualstudio.com/)fejlesztik programozási szakértelmét a [Python](https://www.python.org/) használatával. 

- **Computer Science A**

    A tanulók a mobil alkalmazások fejlesztésének megismerésével fejlesztik a programozási szakértelmét ebben az osztályban.  Ebben az osztályban a [Microsoft Visual Studio Code fejlesztői környezet](https://code.visualstudio.com/)használatával tanulják meg a [Java](https://www.java.com/) -t.  A tanulók olyan emulátort is használnak, amely lehetővé teszi a felhasználók számára a Mobile App-kód futtatását és tesztelését.  Az emulátor Azure Labs-beli beállításával kapcsolatos információkért lépjen kapcsolatba velünk a következő címen: azlabspilot@microsoft.com .

Az egyes osztályokhoz tartozó [szoftverek teljes listájáért](https://www.pltw.org/pltw-software) tekintse meg a PLTW webhelyét.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A laborok PLTW való beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért tekintse meg a [labor-fiók beállítását](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)ismertető oktatóanyagot. Használhat meglévő labor-fiókot is.

Ha már rendelkezik labor-fiókkal, hozzon létre külön Labs-t egy PLTW osztály minden egyes munkamenetéhez, amelyet az iskolája biztosít.  Azt is javasoljuk, hogy hozzon létre külön rendszerképeket az egyes PLTW-osztályokhoz.  A laborok és a lemezképek felépítésével kapcsolatos további információkért olvassa el a következő blogbejegyzést: [áttérés fizikai laborból Azure Lab Servicesra](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások
Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért tekintse meg a [Piactéri rendszerképek elérhetővé tétele a labor-készítők](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)számára című cikket.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| -------------------- | ----- |
| Marketplace-beli rendszerkép | Engedélyezze a Windows 10 Pro-rendszerkép használatát a labor-fiókjában. |

### <a name="lab-settings"></a>Tesztkörnyezet beállításai
A PLTW osztályokhoz használni kívánt virtuális gép mérete a tanulók által az osztályban végzett munkaterhelések típusaitól függ.  A fenti osztályok esetében javasoljuk, hogy nagyméretű és kis GPU (vizualizáció) virtuálisgép-méreteket használjon.  A PLTW osztályokhoz tartozó Labs beállításakor tekintse meg az alábbi táblázatban található útmutatást.

| Tesztkörnyezet-beállítás | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| **Kis GPU (vizualizáció)**.  Ez a virtuális gép a távoli vizualizációk, a folyamatos átvitel, a játékok, a kódolók, például az OpenGL és a DirectX keretrendszerekkel való használatának legmegfelelőbb.  Azt javasoljuk, hogy ezt a méretet a következő PLTW osztályokba használja: mélyépítés és architektúra, digitális elektronika, számítógép-integrált gyártás; Tervezés és fejlesztés.
|Virtuális gép mérete| **Nagyméretű**.  Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott.  Azt javasoljuk, hogy ezt a méretet a következő PLTW osztályokba használja: Bevezetés a mérnöki tervezésbe, a mérnöki alapelvekbe, a számítástechnika alapjaiba, A számítástechnikai alapelvekbe és A számítógép-tudományba.

### <a name="licensing-server"></a>Licencelési kiszolgáló
A fenti PLTW-osztályokban használt szoftverek többsége *_nem igényel_* hozzáférést a licencelési kiszolgálóhoz.  Ha azonban az Autodesk hálózati licencelési modelljét szeretné használni a következő szoftverekhez, akkor hozzá kell férnie egy licencelési kiszolgálóhoz:
-   Revit
-   Feltaláló
-   Inventor CAM

Ha a hálózati licencelést az Autodesk szoftverrel szeretné használni, a [PLTW részletesen ismerteti](https://www.pltw.org/pltw-software) az Autodesk licenckezelő telepítését a licencelési kiszolgálón.  Ez a licencelési kiszolgáló általában a helyszíni hálózatban található, vagy egy Azure-beli virtuális gépen (VM) fut az Azure Virtual Networkben (VNet) belül.

A licenckiszolgáló beállítása után meg kell adni [a VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) a [labor-fiókjához](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). A hálózati társítást _before * kell létrehozni a labor létrehozásához, hogy a labor virtuális gépek hozzáférhessenek a licenckiszolgálóra és a másik megoldáshoz.

Az Autodesk által generált licencfájl beágyazza a licencelési kiszolgáló MAC-címeit.  Ha úgy dönt, hogy egy Azure-beli virtuális gép használatával üzemelteti a licenckiszolgálót, fontos, hogy a licenckiszolgáló MAC-címe ne változzon.   Ellenkező esetben a MAC-címek megváltozásakor a rendszer újból létrehozza a licencelési fájlokat.  A MAC-címek módosításának megakadályozásához kövesse az alábbi tippeket:

- [Állítson be egy statikus magánhálózati IP-címet és MAC-címet](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) a licencelési kiszolgálót futtató Azure-beli virtuális géphez.
- Győződjön meg arról, hogy a labor-fiók és a licencelési kiszolgáló VNet is rendelkezik olyan region\location, amely elegendő virtuálisgép-kapacitással rendelkezik, így nem kell újabb region\location áthelyeznie ezeket az erőforrásokat.

További információért olvassa el a [licencelési kiszolgáló megosztott erőforrásként való beállításával](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) foglalkozó cikket is.

### <a name="template-machine"></a>Sablon számítógép
A PLTW szükséges telepítési fájlok némelyike nagyméretű, és hosszú időt vesz igénybe a másolás, amikor letölti őket a labor sablonjának gépére.

Nem kell letöltenie a telepítési fájlokat a sablon számítógépére, és ott kell telepítenie a PLTW-lemezképeket a fizikai környezetben.  Ezután importálhatja a lemezképeket a megosztott rendszerkép-katalógusba, így az Egyéni rendszerképek használatával létrehozhatja a laborokat.  A részletekért olvassa el a következő cikket: [Egyéni rendszerkép feltöltése a megosztott képgyűjteménybe](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

Ezt a javaslatot követve a tesztkörnyezet beállításának főbb feladatai a következők:

1. A fizikai környezetben hozza létre az osztály rendszerképét.

    a.  Használja a PLTW részletes lépéseit a telepítési fájlok letöltéséhez és a szükséges szoftverek telepítéséhez.

    > [!NOTE]    
    > Az Autodesk alkalmazásai telepítésekor az Autodesk telepítését végző számítógépnek képesnek kell lennie kommunikálni a licencelési kiszolgálóval (az Autodesk telepítővarázsló megkéri, hogy adja meg annak a gépnek a számítógépnevét, amelyen a licenckiszolgáló található).  Ha egy Azure-beli virtuális gépen üzemelteti a licenckiszolgálót, előfordulhat, hogy várnia kell az Autodesk telepítését a labor sablonjának gépére, hogy az Autodesk telepítési varázslója hozzáférhessen a licencelési kiszolgálóhoz

    b.  [Telepítse és konfigurálja a OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (vagy az iskolája által használható egyéb biztonsági mentési beállításokat).
    
    c.  [Windows-frissítések telepítése és konfigurálása](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Töltse fel az egyéni rendszerképet a [labor-fiókjához csatolt megosztott képgyűjteménybe](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery).

1.  Hozzon létre egy labort, és válassza ki az előző lépésben feltöltött egyéni rendszerképet.

1.  A tesztkörnyezet létrehozása után indítsa el a sablont, és kapcsolódjon a sablonhoz, és ellenőrizze, hogy a rendszerkép a várt módon működik-e.

1.  Végül tegye közzé a sablon gépet a tanulók virtuális gépek létrehozásához.

## <a name="student-devices"></a>Tanulói eszközök
A tanulók Windows\Mac számítógépekről és Chromebook is csatlakozhatnak a labor virtuális gépekhez.  Az alábbi hivatkozásokra kattintva megtekintheti az egyes beállításokra vonatkozó utasításokat:

- [Kapcsolódjon a Windows rendszerből](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Mac-kapcsolat](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Kapcsolat a Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Költség
A fenti PLTW osztályok lehetséges költségbecslés.  Ez a becslés nem tartalmazza a licenckiszolgáló futtatásának költségeit vagy a megosztott képtárat.  25 tanulós osztályt fogunk használni.  20 órányi ütemezett idő van.  Emellett minden tanuló 10 órás kvótát kap a munkahelyi vagy az ütemezett osztályon kívüli hozzárendelésekhez.  A **nagyméretű** és a **kis GPU-(vizualizáció-)** méretek esetében tekintse meg a következő költségbecslést.

- **Nagyméretű virtuális gép**

    25 tanuló x (20 ütemezett óra + 10 kvóta óra) x 70 labor egység x 0,01 USD/óra = 525,00 USD

- **Kis GPU (vizualizáció)**

    25 tanuló x (20 ütemezett óra + 10 kvóta óra) x 160 labor egység x 0,01 USD/óra = 1200,00 USD

> [!IMPORTANT] 
> A költségbecslés csak példaként szolgál.  A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> A PLTW számos osztálya olyan alkalmazásokat használ, amelyek böngészőn keresztül érhetők el, például az MIT app Inventor használatával.  Ezek a böngészőalapú alkalmazások nem igényelnek gyors PROCESSZORt vagy GPU-t, és az internetkapcsolattal rendelkező eszközökről is elérhetők.  Ha a tanulók ezeket az alkalmazásokat használják, javasoljuk, hogy a böngészőt a fizikai eszközön használják, ahelyett, hogy a böngészőt használják a laboratóriumi virtuális gépeken.  Ez segít a költségek csökkentésében azáltal, hogy csak a tesztkörnyezetben működő virtuális gépeket használja a gyors CPU vagy GPU-t igénylő alkalmazásokhoz.

## <a name="next-steps"></a>További lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users). 