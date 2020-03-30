---
title: Az Azure DevTest Labs bővítése az Azure Functions használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan bővítheti az Azure DevTest Labs-t az Azure Functions használatával.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014359"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>A DevTest Labs kiterjesztése az Azure Functions használatával
Az Azure Functions használatával a DevTest Labs által már támogatott a már támogatottforgatókönyveken kívül további forgatókönyveket is támogathat. Az Azure Functions segítségével kiterjesztheti a szolgáltatás beépített funkcióit az üzleti igényeknek megfelelően. Az alábbi lista néhány lehetséges forgatókönyvet tartalmaz. Ez a cikk bemutatja, hogyan valósítható meg az egyik ilyen minta forgatókönyvek.

- A virtuális gépek (VM-ek) legfelső szintű összegzésének biztosítása a laborban
- [Tesztkörnyezet konfigurálása távoli asztali átjáró használatához](configure-lab-remote-desktop-gateway.md)
- Megfelelőségi jelentés a belső támogatási oldalon
- A felhasználók számára a megnövekedett engedélyeket igénylő műveletek végrehajtásának engedélyezése az előfizetésben
- [Munkafolyamatok indítása DevTest Labs-események alapján](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Áttekintés
[Az Azure Functions](../azure-functions/functions-overview.md) egy kiszolgáló nélküli számítástechnikai platform az Azure-ban. Az Azure Functions használatával a DevTest Labs megoldáslehetővé teszi számunkra, hogy bővítse a meglévő funkciók saját egyéni kódot. Az Azure Functions szolgáltatásról az [Azure Functions dokumentációjában](../azure-functions/functions-overview.md)olvashat bővebben. Annak bemutatására, hogy az Azure Functions hogyan segíthet idomításában a követelmények teljesítésében vagy a DevTest Labs teljes forgatókönyveinek teljesítésében, ez a cikk egy példa segítségével a labbeli virtuális gépek legfelső szintű összegzését mutatja be az alábbiak szerint:

**Példa követelmény/forgatókönyv:** A felhasználók láthatják a tesztkörnyezetben lévő összes virtuális gép részleteit, beleértve az operációs rendszert, a tulajdonost és az alkalmazott összetevőket.  Ezenkívül ha a **Windows-frissítések alkalmazása** összetevőt nem alkalmazták a közelmúltban, egyszerűen alkalmazhatja azt.

A forgatókönyv végrehajtásához két függvényt kell használnia az alábbi ábrán leírtak szerint:  

![Teljes áramlás](./media/extend-devtest-labs-azure-functions/flow.png)

Ezek a mintafüggvények forráskódja a [DevTest Labs GitHub-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) található (mind a C# és a PowerShell-implementációk érhetők el).

- **UpdateInternalSupportPage**: Ez a függvény lekérdezi a DevTest Labs-t, és közvetlenül frissíti a belső támogatási lapot a virtuális gépek részleteivel.
- **ApplyWindowsUpdateArtifact**: Egy tesztkörnyezetben lévő virtuális gép esetén ez a funkció a **Windows frissítési** összetevőt alkalmazza.

## <a name="how-it-works"></a>Működés
Amikor a felhasználók a DevTest Labs **belső támogatási** lapját választják, egy előre kitöltött laplal rendelkeznek, amely a virtuális gépekre, a tesztkörnyezet-tulajdonosokra és a támogatási kapcsolattartókra vonatkozó információkat tartalmazza.  

Ha a **Frissítés hez itt lehetőséget választja,** a lap meghívja az első Azure-függvényt: **UpdateInternalSupportPage**. A függvény lekérdezi a DevTest Labs-t az információkért, majd átírja a **belső támogatási** lapot az új információkkal.

Van egy további művelet, amely lehet tenni, minden olyan virtuális gépek, amelyeken a Windows Update-összetevők nem alkalmazták a közelmúltban, lesz egy gomb, hogy alkalmazza a Windows-frissítéseket a virtuális gépre. Ha a * Windows update gomb**futtatása** lehetőséget választja egy virtuális géphez, a lap meghívja a második Azure-függvényt: **ApplyWindowsUpdateArtifact**. Ez a függvény ellenőrzi, hogy a virtuális gép fut-e, és ha igen, közvetlenül alkalmazza a [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) összetevőt.

## <a name="step-by-step-walkthrough"></a>Részletes forgatókönyv
Ez a szakasz lépésenként ismerteti a **belső támogatási** lap frissítéséhez szükséges Azure-erőforrások beállítását. Ez a forgatókönyv egy példa a DevTest Labs kiterjesztésére. Ezt a mintát más esetekben is használhatja.

### <a name="step-1-create-a-service-principal"></a>1. lépés: Egyszerű szolgáltatás létrehozása 
Az első lépés az, hogy egy szolgáltatásnév engedélyével az előfizetés, amely tartalmazza a labor. A szolgáltatásnévnek a jelszóalapú hitelesítést kell használnia. Ezt az [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [az Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)vagy az [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)segítségével teheti meg. Ha már rendelkezik egy egyszerű szolgáltatás használata, kihagyhatja ezt a lépést.

Vegye figyelembe az **alkalmazásazonosítót**, **a kulcsot**és a **bérlői azonosítót** a szolgáltatásnévhez. A forgatókönyv későbbi részében szüksége lesz rájuk. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2. lépés: Töltse le a mintát, és nyissa meg a Visual Studio 2019
Töltse le a [C# Azure Functions minta egy](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) példányát helyileg (akár a tárház klónozásával, akár a tárház innen való letöltésével). [here](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Nyissa meg a mintamegoldást a Visual Studio 2019-es alkalmazással.  
1. Telepítse az **Azure fejlesztési** számítási feladatait a Visual Studio számára, ha még nincs telepítve. Ez lehet telepíteni keresztül **Tools** -> **Get Eszközök és funkciók** menüpont).

    ![Az Azure fejlesztési munkaterhelése](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Hozza létre a megoldást. Válassza **a Build,** majd **a Build Solution** menüelemet.

### <a name="step-3-deploy-the-sample-to-azure"></a>3. lépés: A minta üzembe helyezése az Azure-ban
A Visual Studio ban a **Megoldáskezelő** ablakban kattintson a jobb gombbal az **AzureFunctions** projektre, majd válassza a **Közzététel parancsot.** Kövesse a varázslót egy új vagy meglévő Azure-függvényalkalmazás közzétételének befejezéséhez. Az Azure-függvények Visual Studio használatával történő fejlesztéséről és üzembe helyezéséről az [Azure-függvények fejlesztése a Visual Studio használatával](../azure-functions/functions-develop-vs.md)című témakörben talál részletes információt.

![Közzététel párbeszédpanel](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4. lépés: Alkalmazásbeállítások összegyűjtése
A függvények közzététele után be kell szereznie ezeket a függvényeket az Azure Portalról. 

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) 
1. Keresse meg a függvényalkalmazást.
1. A **Függvényalkalmazások** lapon válassza ki a funkciót. 
1. Válassza **a Függvény URL-címének bekerülése** lehetőséget az alábbi képen látható módon. 

    ![Az Azure függvények URL-címei](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Másolja és mentse az URL-címet. Ismételje meg ezeket a lépéseket a másik Azure-függvényben. 

További információkra is szüksége lesz a szolgáltatásnévről, például az alkalmazásazonosítóról, a kulcsról és a bérlői azonosítóról.


### <a name="step-5--update-application-settings"></a>5. lépés: Alkalmazásbeállítások frissítése
A Visual Studio az Azure-függvény közzététele után válassza ki az **Azure App Service beállításainak szerkesztése** **a Műveletek**csoportban. Frissítse a következő alkalmazásbeállításokat (távoli):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (alapértelmezés szerint 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Alkalmazásbeállítások](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6. lépés: Az Azure-függvény tesztelése
A forgatókönyv utolsó lépése az Azure-függvény tesztelése.  

1. Keresse meg az **UpdateInternalSupportPage** függvényt a 3. 
1. Válassza a lap jobb oldalán a **Teszt** lehetőséget. 
1. Írja be az útvonal tulajdonságait (LABNAME, RESOURCEGROUPNAME és SUBSCRIPTIONID).
1. A függvény végrehajtásához válassza a **Futtatás** lehetőséget.  

    Ez a funkció frissíti a megadott tesztkörnyezet belső támogatási lapját. Ez is tartalmaz egy gombot a felhasználók számára, hogy közvetlenül hívja a funkciót legközelebb

    ![Teszt funkció](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>További lépések
Az Azure Functions segítségével a DevTest Labs funkciói a már beépített lehetőségeken túlra is kiterjeszthetők, és segíthetnek az ügyfeleknek a csapataikra vonatkozó egyedi igényeik teljesítésében. Ez a minta kiterjeszthető & tovább bővíthető, hogy még jobban lefedje.  Ha többet szeretne megtudni a DevTest Labs-ről, olvassa el az alábbi cikkeket: 

- [DevTest Labs vállalati referenciaarchitektúra](devtest-lab-reference-architecture.md)
- [Gyakori kérdések](devtest-lab-faq.md)
- [A DevTest labs felskálázása](devtest-lab-guidance-scale.md)
- [A DevTest labs automatizálása a PowerShell segítségével](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








