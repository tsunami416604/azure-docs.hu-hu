---
title: Windows virtuális asztali környezet – Azure
description: A Windows virtuális asztali környezet alapvető elemei.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127920"
---
# <a name="windows-virtual-desktop-environment"></a>A Windows Virtual Desktop környezete

A Windows virtuális asztal egy olyan szolgáltatás, amely egyszerű és biztonságos hozzáférést biztosít a felhasználók számára a virtualizált asztalokhoz és a RemoteApps-hez. Ez a témakör egy kicsit többet fog mondani a Windows Virtual Desktop környezet általános szerkezetéről.

## <a name="tenants"></a>Bérlők

A Windows virtuális asztal bérlője az elsődleges felület a Windows virtuális asztali környezet kezeléséhez. Minden Windows virtuális asztal bérlőkell társítható az Azure Active Directory, amely a felhasználók, akik bejelentkeznek a környezetbe. A Windows virtuális asztal bérlője, megkezdheti a gazdagépkészletek futtatásához a felhasználók számítási feladatok.

## <a name="host-pools"></a>Gazdamedencék

A gazdakészlet olyan Azure virtuális gépek gyűjteménye, amelyek munkamenet-gazdagépként regisztrálnak a Windows virtuális asztal szolgáltatásra a Windows virtuális asztali ügynök futtatásakor. A gazdagépkészletben lévő összes munkamenetgazda virtuális gépet ugyanabból a lemezképből kell beszerezni a konzisztens felhasználói élmény érdekében.

A gazdakészlet kétféle lehet:

- Személyes, ahol minden munkamenet-állomás az egyes felhasználókhoz van rendelve.
- Készletezve, ahol a munkamenet-állomások bármely olyan felhasználótól fogadhatnak kapcsolatot, aki jogosult a gazdagépkészleten belüli alkalmazáscsoportra.

A gazdakészlet további tulajdonságait beállíthatja a terheléselosztási viselkedés, az egyes munkamenet-gazdagépek által levehető munkamenetek számának, valamint a gazdagépkészletmunkamenet-gazdagépekkel való kapcsolatfelvételhez, miközben be van jelentkezve a Windows virtuális asztal munkameneteibe. Alkalmazáscsoportokon keresztül szabályozhatja a felhasználók számára közzétett erőforrásokat.

## <a name="app-groups"></a>Alkalmazáscsoportok

Az alkalmazáscsoport a gazdagépkészletmunkamenet-gazdagépein telepített alkalmazások logikai csoportja. Az alkalmazáscsoport két típusa lehet:

- RemoteApp, ahol a felhasználók hozzáférhetnek az egyénileg kiválasztott és az alkalmazáscsoportban közzéteszett RemoteApps alkalmazásokhoz
- Asztal, ahol a felhasználók a teljes asztalhoz férnek hozzá

Alapértelmezés szerint egy asztali alkalmazáscsoport (az "Asztali alkalmazáscsoport") automatikusan létrejön, amikor gazdakészletet hoz létre. Ezt az alkalmazáscsoportot bármikor eltávolíthatja. Azonban nem hozhat létre másik asztali alkalmazáscsoportot a gazdakészletben, amíg létezik asztali alkalmazáscsoport. A RemoteApps közzétételéhez létre kell hoznia egy RemoteApp-alkalmazáscsoportot. Több RemoteApp-alkalmazáscsoportot is létrehozhat a különböző munkavégző forgatókönyvek befogadásához. A különböző RemoteApp-alkalmazáscsoportok egymást átfedő RemoteApps-alkalmazásokat is tartalmazhatnak.

Erőforrások felhasználók számára való közzétételéhez hozzá kell rendelnie őket az alkalmazáscsoportokhoz. Amikor felhasználókat rendel alkalmazáscsoportokhoz, vegye figyelembe az alábbi dolgokat:

- A felhasználó nem rendelhető hozzá egyszerre asztali alkalmazáscsoporthoz és egy RemoteApp-alkalmazáscsoporthoz ugyanabban a gazdakészletben.
- Egy felhasználó több alkalmazáscsoporthoz is hozzárendelhető ugyanazon a gazdakészleten belül, és a hírcsatorna mindkét alkalmazáscsoport felhalmozódása lesz.

## <a name="tenant-groups"></a>Bérlői csoportok

A Windows virtuális asztal rendszerben a Windows virtuális asztal bérlője az a hely, ahol a legtöbb beállítás és konfiguráció történik. A Windows virtuális asztal bérlője tartalmazza a gazdakészleteket, az alkalmazáscsoportokat és az alkalmazáscsoport felhasználói hozzárendeléseit. Előfordulhatnak azonban olyan helyzetek, amikor egyszerre több Windows virtuális asztal-bérlőt kell kezelnie, különösen akkor, ha Ön felhőszolgáltató (CSP) vagy üzemeltetési partner. Ilyen esetekben egyéni Windows virtuális asztal bérlői csoport használatával elhelyezheti az ügyfelek Windows virtuális asztal bérlőit, és központilag kezelheti a hozzáférést. Ha azonban csak egyetlen Windows virtuális asztal bérlőt kezel, a bérlői csoport fogalma nem érvényes, és továbbra is működtetheti és kezelheti az alapértelmezett bérlői csoportban létező bérlőt.

## <a name="end-users"></a>Végfelhasználók

Miután hozzárendelte a felhasználókat az alkalmazáscsoportjaikhoz, bármelyik Windows virtuális asztali ügyféllel csatlakozhatnak a Windows virtuális asztal központi telepítéséhez.

## <a name="next-steps"></a>További lépések

További információ a delegált hozzáférésről és aszerepkörök felhasználókhoz rendeléséről a [Windows virtuális asztal Delegált hozzáférés szolgáltatásában.](delegated-access-virtual-desktop.md)

A Windows virtuális asztal bérlőjének beállításáról a Bérlő létrehozása a Windows virtuális asztalon című [témakörben](tenant-setup-azure-active-directory.md)olvashat.

A Windows Virtuális asztalhoz való csatlakozásról az alábbi cikkek ben olvashat:

- [Csatlakozás Windows 10 vagy Windows Server 7 rendszerről](connect-windows-7-and-10.md)
- [Csatlakozás webböngészőről](connect-web.md)
