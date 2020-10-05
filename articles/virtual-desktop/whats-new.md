---
title: A Windows rendszerű virtuális asztal újdonságai – Azure
description: Új szolgáltatások és Termékfrissítések a Windows rendszerű virtuális asztali gépekhez.
author: Heidilohr
ms.topic: overview
ms.date: 10/01/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: 0191d6ad74a9b6349f5d1724f9483607dce2d926
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91630014"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztal újdonságai

A Windows rendszerű virtuális asztali frissítések rendszeres időközönként. Ebből a cikkből megismerheti a következőket:

- A legújabb frissítések
- Új funkciók
- A meglévő funkciók fejlesztése
- Hibajavítások

Ez a cikk havonta frissül. Ügyeljen arra, hogy az új frissítések megtartása érdekében gyakran térjen vissza ide.

## <a name="september-2020"></a>2020. szeptember

A következőképpen módosult a 2020 szeptemberében:

- A következő Azure-földrajzi területeken a kapcsolatok késésének csökkentésével optimalizáltuk a teljesítményt:
    - Németország
    - Dél-Afrika (csak ellenőrzési környezetek esetén)

Most már használhatja a [Experience kalkulátort](https://azure.microsoft.com/services/virtual-desktop/assessment/) a felhasználói élmény minőségének becsléséhez ezekben a területeken.

- Megjelent a Windows rendszerű virtuális asztali Windows asztali ügyfél 1.2.1364 verziója. Ebben a frissítésben a következő módosításokat hajtottuk végre:
    - Kijavított egy problémát, amelyben az egyszeri bejelentkezés (SSO) nem működött a Windows 7 rendszeren.
    - Kijavított egy problémát, amely miatt az ügyfél leválasztott egy olyan felhasználót, aki engedélyezte a csapatok számára a média optimalizálását, vagy csatlakozhat egy Teams-értekezlethez, míg egy másik alkalmazás kizárólagos módban nyitott meg egy hangadatfolyamot.
    - Kijavított egy problémát, amelyben a csapatok nem észleltek hang-és video-eszközöket, amikor engedélyezve lett a csapatok multimédia-optimalizálása.
    - "Segítségre van szüksége a beállításokhoz?" az asztali beállítások lapra mutató hivatkozás.
    - A nagy kontrasztú sötét témák használatakor megjelenő "subscribe" (előfizetés) gombra vonatkozó probléma kijavítva.
    
- A felhasználók óriási segítségének köszönhetően javítottunk két kritikus problémát a Microsoft Store Távoli asztal-ügyfél számára. Folytatjuk a visszajelzések áttekintését és a problémák megoldását, mivel az ügyfél szakaszos kiadását bővítjük világszerte több felhasználó számára.
    
- Új funkciót adtunk hozzá, amely lehetővé teszi a virtuális gép helyének, rendszerképének, erőforráscsoportának, előtagjának és hálózati konfigurációjának módosítását a munkafolyamatok részeként, hogy a virtuális gépet hozzáadja a központi telepítéshez a Azure Portal.

- Az informatikai szakemberek mostantól a Microsoft Endpoint Manager használatával kezelhetik a hibrid Azure Active Directory csatlakoztatott Windows 10 Enterprise rendszerű virtuális gépeket. További információt [a blogbejegyzésben](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)talál.

## <a name="august-2020"></a>2020. augusztus

A következőképpen módosult a 2020 augusztusában:

- Továbbfejlesztettük a teljesítményt a kapcsolódási késés csökkentése érdekében a következő Azure-régiókban: 

    - Egyesült Királyság
    - Franciaország
    - Norvégia
    - Dél-Korea

   Az [Experience kalkulátor](https://azure.microsoft.com/services/virtual-desktop/assessment/) használatával általános képet kaphat arról, hogy ezek a változások milyen hatással lesznek a felhasználókra.

- A Microsoft Store Távoli asztal ügyfél (v 10.2.1522 +) mostantól általánosan elérhető! A Microsoft Store Távoli asztal ügyfél ezen verziója kompatibilis a Windows Virtual Desktop szolgáltatással. A felhasználói élmény fokozása céljából a frissített felhasználói FELÜLETi folyamatokat is bevezetjük. Ez a frissítés magában foglalja a folyékony kialakítást, a világos és a sötét üzemmódot, valamint számos más izgalmas változást. A-ügyfelet az iOS-, macOS-és Android-ügyfelekhez is ugyanezen alapul szolgáló Remote Desktop Protocol (RDP) motor használatára is átírta. Ez lehetővé teszi, hogy az összes platformon gyorsabban elérhetővé tegyük az új funkciókat. [Töltse le az ügyfelet](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) , és próbálja ki!

- Kijavítottunk egy problémát a csapatok asztali ügyfelének (1.3.00.21759-verzió), ahol az ügyfél csak az UTC időzónát mutatta a csevegésben, a csatornákban és a naptárban. A frissített ügyfél most már megjeleníti a távoli munkamenet időzónáját.

- Azure Advisor mostantól a Windows rendszerű virtuális asztal része. Ha a Azure Portalon keresztül fér hozzá a Windows rendszerű virtuális asztalhoz, a Windows rendszerű virtuális asztali környezet optimalizálására vonatkozó javaslatok láthatók. További információ: [Azure Advisor](azure-advisor.md).

- Az Azure CLI mostantól támogatja a Windows rendszerű virtuális asztal () használatát a `az desktopvirtualization` Windows rendszerű virtuális asztali környezetek automatizálásának elősegítése érdekében. Tekintse meg a [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest) listáját a kiterjesztési parancsok listájához.

- Frissítettük a központi telepítési sablonokat, hogy teljes mértékben kompatibilisek legyenek a Windows rendszerű virtuális asztali Azure Resource Manager felületekkel. A sablonok a [githubon](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)találhatók.

- A Windows rendszerű virtuális asztali US Gov portál már nyilvános előzetes verzióban érhető el. További információkért tekintse meg [a bejelentést](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>2020. július  

Július volt, amikor általánosan elérhetővé vált a Windows virtuális asztal az Azure Resource Management-integrációval.

Az új kiadás változása: 

- A "Fall 2019 Release" néven ismert "Windows virtuális asztal (klasszikus)", míg a "Spring 2020 kiadás" most csak "Windows virtuális asztal". További információkért tekintse meg [ezt a blogbejegyzést](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Ha többet szeretne megtudni az új funkciókról, tekintse meg [ezt a blogbejegyzést](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Automatikus skálázási eszköz frissítése

Az előzetes verzióban már általánosan elérhető az automatikus skálázási eszköz legújabb verziója. Ez az eszköz egy Azure Automation-fiók és az Azure Logic App használatával automatikusan leállítja és újraindítja a munkamenetgazda virtuális gépeket a gazdagépen belül, így csökkentve az infrastrukturális költségeket. További információ: [Azure Automation használatával méretezhető munkamenet-gazdagépek](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Mostantól a következő műveleteket végezheti el a Windows rendszerű virtuális asztali Azure Portalban: 

- Felhasználók közvetlen kiosztása a személyes asztali munkamenet-gazdagépekhez  
- Alkalmazáskészletek érvényesítési környezete beállításának módosítása 

### <a name="diagnostics"></a>Diagnosztika

Megjelent néhány új előre elkészített lekérdezés a Log Analytics munkaterülethez. A lekérdezések eléréséhez nyissa meg a **naplók** elemet, és a **Kategória**területen válassza a **Windows virtuális asztal**lehetőséget. További információ [a diagnosztikai szolgáltatás log Analytics használatáról](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Az Android rendszerhez készült Távoli asztal-ügyfél frissítése

Az [Androidhoz készült Távoli asztal-ügyfél](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) mostantól támogatja a Windows rendszerű virtuális asztali kapcsolatokat. A 10.0.7 verziótól kezdődően az Android-ügyfél egy új felhasználói FELÜLETET kínál a jobb felhasználói élmény érdekében. Az ügyfél a Windows rendszerű virtuális asztali munkaterületekre való feliratkozáskor az Android-eszközökön Microsoft Authenticator is integrálva van.  

A Távoli asztal-ügyfél előző verziója már "Távoli asztal 8" néven is ismert. Az ügyfél korábbi verziójában meglévő kapcsolatok zökkenőmentesen lesznek továbbítva az új ügyfélhez. Az új ügyfél ugyanarra a mögöttes RDP alapmotorra lett írva, mint az iOS-és macOS-ügyfelek, az új funkciók gyorsabb kiadása minden platformon. 

### <a name="teams-update"></a>Csapatok frissítése

Javítottuk a Microsoft Teams for Windows virtuális asztali verzióját. Ami a legfontosabb, hogy a Windows rendszerű virtuális asztal mostantól támogatja a Windows asztali ügyfél hang-és video-optimalizálását. Az átirányítás javítja a késést azáltal, hogy közvetlen elérési utakat hoz létre a felhasználók között, ha hang-vagy videohívásokat használ a hívások és értekezletek A kisebb távolság kevesebb ugrást jelent, így a hívások megjelenése és a hang gördülékenyebb. További információt a [Windows rendszerű virtuális asztal használatáról szóló csapatokban](teams-on-wvd.md)olvashat.

## <a name="june-2020"></a>2020. június

A múlt hónapban bevezetjük a Windows rendszerű virtuális asztalt az előzetes verzióban Azure Resource Manager integrációval. Ez a frissítés számos izgalmas új funkciót kínál, melyekről szívesen tájékoztatjuk Önt. A Windows rendszerű virtuális asztal ezen verziójának újdonságai.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>A Windows rendszerű virtuális asztal már integrálva van Azure Resource Manager

A Windows rendszerű virtuális asztal már integrálva van Azure Resource Managerba. A legújabb frissítés során a Windows rendszerű virtuális asztali objektumok mostantól Azure Resource Manager erőforrásai. Ez a frissítés az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) is integrálva van. További információ: [What is Azure Resource Manager?](../azure-resource-manager/management/overview.md) .

Ezt a változást a következőképpen teheti meg:

- A Windows rendszerű virtuális asztal már integrálva van a Azure Portal. Ez azt jelenti, hogy közvetlenül a portálon, a PowerShell, a webalkalmazások és a harmadik féltől származó eszközök nélkül is kezelhet mindent. Az első lépésekhez tekintse meg a [Host Pool létrehozása a Azure Portal](create-host-pools-azure-marketplace.md)használatával című oktatóanyagot.

- A frissítés előtt csak a RemoteApp-és asztali számítógépeket teheti közzé az egyes felhasználók számára. A Azure Resource Manager segítségével mostantól Azure Active Directory csoportokba teheti közzé az erőforrásokat.

- A Windows rendszerű virtuális asztal korábbi verziója négy beépített rendszergazdai szerepkörrel rendelkezik, amelyeket hozzárendelhet egy bérlőhöz vagy egy gazdagéphez. Ezek a szerepkörök már [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/overview.md)rendelkeznek. Ezeket a szerepköröket minden Windows rendszerű virtuális asztali Azure Resource Manager objektumra alkalmazhatja, amely lehetővé teszi, hogy teljes körű, sokoldalú delegálási modellel rendelkezzen.

- Ebben a frissítésben már nincs szükség az Azure Marketplace vagy a GitHub-sablon ismételt futtatására a gazdagépek kibővítéséhez. A gazdagépek kibővítéséhez mindössze a Azure Portalban kell megnyitnia a gazdagépet, majd a **+ Hozzáadás** lehetőségre kattintva további munkamenet-gazdagépeket telepíthet.

- A gazdagép-készlet üzembe helyezése mostantól teljes mértékben integrálva van az [Azure megosztott rendszerkép](../virtual-machines/windows/shared-image-galleries.md)-katalógusával. A megosztott képkatalógus egy különálló Azure-szolgáltatás, amely a virtuális gép (VM) képdefinícióit tárolja, beleértve a rendszerkép verziószámozását is. A globális replikálás használatával a rendszerképeket más Azure-régiókba is másolhatja és elküldheti a helyi telepítéshez.

- A PowerShellen vagy a diagnosztikai szolgáltatás webalkalmazásán keresztül elvégzendő figyelési függvények mostantól a Azure Portal Log Analyticsba kerültek. Mostantól két lehetőség közül választhat a jelentések megjelenítéséhez. Kusto-lekérdezéseket futtathat, és a munkafüzetek használatával vizuális jelentéseket hozhat létre.

- A Windows virtuális asztal használatához már nem szükséges Azure Active Directory (Azure AD) beleegyeznie. Ebben a frissítésben az Azure AD-bérlő az Azure-előfizetésében hitelesíti a felhasználókat, és biztosítja az Azure RBAC-vezérlőket a rendszergazdák számára.


### <a name="powershell-support"></a>PowerShell-támogatás

Új AzWvd-parancsmagokat adtunk hozzá a Azure PowerShell az modulhoz ezzel a frissítéssel. Ez az új modul támogatott a PowerShell Core-ban, amely a .NET Core-on fut.

A modul telepítéséhez kövesse a [PowerShell-modul beállítása a Windows rendszerű virtuális asztalhoz](powershell-module.md)című témakör utasításait.

Az elérhető parancsok listáját a [AzWvd PowerShell-referenciában](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)is megtekintheti.

Az új funkciókkal kapcsolatos további információkért tekintse meg a [blogbejegyzését](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>További átjárók

Új átjáró-fürtöt adtunk hozzá Dél-Afrikában a kapcsolatok késésének csökkentése érdekében.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams a Windows Virtual Desktopban (előzetes verzió)

Javítottunk a Microsoft Teams for Windows Virtual Desktop szolgáltatásban. Ami a legfontosabb, hogy a Windows virtuális asztal mostantól támogatja a hívások hang-és vizualizációs átirányítását. Az átirányítás úgy javítja a késést, hogy a felhasználók között közvetlen elérési utakat hoz létre a hang vagy a videó használatával. A kisebb távolság kevesebb ugrást jelent, így a hívások megjelenése és a hang gördülékenyebb.

További információt [a blogbejegyzésben](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)talál.

## <a name="client-updates"></a>Ügyfelek frissítései

Tekintse meg ezeket a cikkeket a Windows rendszerű virtuális asztali és Távoli asztali szolgáltatások-ügyfelek frissítéseinek megismeréséhez:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a jövőbeli tervekkel a [Microsoft 365 Windows rendszerű virtuális asztali menetrendben](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
