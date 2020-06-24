---
title: A Windows rendszerű virtuális asztal újdonságai – Azure
description: Új szolgáltatások és Termékfrissítések a Windows rendszerű virtuális asztali gépekhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 383b59d10fa1de1b2ee0ea8b505c164577487255
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974381"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztal újdonságai

A Windows rendszerű virtuális asztali frissítések rendszeres időközönként. Ebből a cikkből megismerheti a következőket:

- A legújabb frissítések
- Új funkciók
- A meglévő funkciók fejlesztése
- Hibajavítások

Ez a cikk havonta frissül. Ügyeljen arra, hogy az új frissítések megtartása érdekében gyakran térjen vissza ide.

## <a name="june-2020"></a>2020. június

A múlt hónapban bevezetjük a Windows rendszerű virtuális asztali Spring 2020 frissítést előzetes verzióban. Ez a frissítés számos izgalmas új funkciót kínál, melyekről szívesen tájékoztatjuk Önt. A Spring 2020 frissítésének újdonságai.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>A Windows rendszerű virtuális asztal már integrálva van Azure Resource Manager (előzetes verzió)

A Windows rendszerű virtuális asztal már integrálva van Azure Resource Managerba. A legújabb frissítés során a Windows rendszerű virtuális asztali objektumok mostantól Azure Resource Manager erőforrásai. Ez a frissítés az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) is integrálva van. További információ: [What is Azure Resource Manager?](../azure-resource-manager/management/overview.md) .

Ezt a változást a következőképpen teheti meg:

- A Windows rendszerű virtuális asztal már integrálva van a Azure Portal. Ez azt jelenti, hogy közvetlenül a portálon, a PowerShell, a webalkalmazások és a harmadik féltől származó eszközök nélkül is kezelhet mindent. Az első lépésekhez tekintse meg a [Host Pool létrehozása a Azure Portal](create-host-pools-azure-marketplace.md)használatával című oktatóanyagot.

- A Spring 2020 frissítés előtt csak a RemoteApp-és asztali számítógépeket teheti közzé az egyes felhasználók számára. A Azure Resource Manager segítségével mostantól Azure Active Directory csoportokba teheti közzé az erőforrásokat.

- A Windows rendszerű virtuális asztal korábbi verziója négy beépített rendszergazdai szerepkörrel rendelkezik, amelyeket hozzárendelhet egy bérlőhöz vagy egy gazdagéphez. Ezek a szerepkörök mostantól az Azure [szerepköralapú hozzáférés-vezérléssel](../role-based-access-control/overview.md)rendelkeznek. Ezeket a szerepköröket minden Windows rendszerű virtuális asztali Azure Resource Manager objektumra alkalmazhatja, amely lehetővé teszi, hogy teljes körű, sokoldalú delegálási modellel rendelkezzen.

- A Spring 2020 Update-ben már nem kell többé futtatnia az Azure Marketplace-t vagy a GitHub-sablont a gazdagépek kibővítéséhez. A gazdagépek kibővítéséhez mindössze a Azure Portalban kell megnyitnia a gazdagépet, majd a **+ Hozzáadás** lehetőségre kattintva további munkamenet-gazdagépeket telepíthet.

- A gazdagép-készlet üzembe helyezése mostantól teljes mértékben integrálva van az [Azure megosztott rendszerkép](../virtual-machines/windows/shared-image-galleries.md)-katalógusával. A megosztott képkatalógus egy különálló Azure-szolgáltatás, amely a virtuális gép (VM) képdefinícióit tárolja, beleértve a rendszerkép verziószámozását is. A globális replikálás használatával a rendszerképeket más Azure-régiókba is másolhatja és elküldheti a helyi telepítéshez.

- A PowerShellen vagy a diagnosztikai szolgáltatás webalkalmazásán keresztül elvégzendő figyelési függvények mostantól a Azure Portal Log Analyticsba kerültek. Mostantól két lehetőség közül választhat a jelentések megjelenítéséhez. Kusto-lekérdezéseket futtathat, és a munkafüzetek használatával vizuális jelentéseket hozhat létre.

- A Windows virtuális asztal használatához már nem szükséges Azure Active Directory (Azure AD) beleegyeznie. A Spring 2020 Update-ben az Azure AD-bérlő hitelesíti a felhasználókat, és RBAC-vezérlőket biztosít a rendszergazdáknak.


### <a name="powershell-support"></a>PowerShell-támogatás

Új AzWvd-parancsmagokat adtunk hozzá a Azure PowerShell az modulhoz a Spring 2020 frissítéssel. Ez az új modul támogatott a PowerShell Core-ban, amely a .NET Core-on fut.

A modul telepítéséhez kövesse a [PowerShell-modul beállítása a Windows rendszerű virtuális asztalhoz](powershell-module.md)című témakör utasításait.

Az elérhető parancsok listáját a [AzWvd PowerShell-referenciában](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)is megtekintheti.

Az új funkciókkal kapcsolatos további információkért tekintse meg a [blogbejegyzését](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>További átjárók

Új átjáró-fürtöt adtunk hozzá Dél-Afrikában a kapcsolatok késésének csökkentése érdekében.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams a Windows Virtual Desktopban (előzetes verzió)

Javítottunk a Microsoft Teams for Windows Virtual Desktop szolgáltatásban. Ami a legfontosabb, hogy a Windows virtuális asztal mostantól támogatja a hívások hang-és vizualizációs átirányítását. Az átirányítás úgy javítja a késést, hogy a felhasználók között közvetlen elérési utakat hoz létre a hang vagy a videó használatával. A kisebb távolság kevesebb ugrást jelent, így a hívások megjelenése és a hang gördülékenyebb.

További információt [a blogbejegyzésben](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)talál.

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a cikkeket a Windows rendszerű virtuális asztali és Távoli asztali szolgáltatások-ügyfelek frissítéseinek megismeréséhez:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
