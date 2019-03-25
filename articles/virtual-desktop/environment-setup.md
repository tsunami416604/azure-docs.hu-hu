---
title: Windows virtuális asztal előzetes verziójú környezet – Azure
description: Egy Windows virtuális asztal előzetes verziójú környezet alapvető elemeit.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403510"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Windows virtuális asztal előzetes verziójú környezet

Windows virtuális asztal előzetes szolgáltatása a virtualizált asztali környezet és a távoli alkalmazások könnyű és biztonságos hozzáférést biztosít a felhasználóknak. Ebben a témakörben megtudhatja, egy kis további információk a Windows virtuális asztali környezet általános szerkezetét.

## <a name="tenants"></a>Bérlők

A Windows virtuális asztal bérlő az az elsődleges kapcsolódási felületet a Windows virtuális asztali környezet kezeléséhez. Minden Windows virtuális asztal bérlő az Azure Active Directoryval a felhasználók, akik fog jelentkezzen be a környezetet tartalmazó kell rendelni. A virtuális asztali Windows-bérlőből elkezdheti a felhasználók számítási feladatok futtatásához gazdagép-címkészletek létrehozása.

## <a name="host-pools"></a>Gazdagép-készletek

Egy gazdagép-készletet az Azure virtual machines, hogy a virtuális asztali Windows-ügynök futtatásakor munkamenet gazdagépként Windows virtuális asztali regisztrálhatnak gyűjteménye. Minden munkamenet gazdagép virtuális gépek a gazdagép készlet kell lennie származási hely ugyanazt a lemezképet, egységes felhasználói élményt.

Egy gazdagép készlet két típus egyike lehet:

- Személyes, ahol minden egyes munkamenetgazda egyes felhasználók kapnak.
- Készletbe vont, ahol munkamenet gazdagépek is képes kapcsolatokat fogadni bármely felhasználó jogosult a gazdagép készleten belüli alkalmazás csoporthoz.

További tulajdonságok adhatók meg a gazdagép-készlet módosításához a terheléselosztó viselkedését egyes munkamenetgazda is igénybe vehet, hogy hány munkamenetben, és mi a felhasználó visszaállíthatja a gazdagép-készlet, a Windows virtuális asztali munkamenetekhez bejelentkezve munkamenet-gazdagépekre. A felhasználók számára az alkalmazás keresztül közzétett erőforrások szabályozhatja.

## <a name="app-groups"></a>Alkalmazáscsoportok

Az alkalmazás csoport logikus csoportosításai, a gazdagép-készletben lévő munkamenet gazdagépeken telepített alkalmazások. Az alkalmazás csoport két típus egyike lehet:

- RemoteApp, ahol a felhasználók a távoli alkalmazások eléréséhez, külön-külön jelölje ki, és tegye közzé az alkalmazás csoport
- Asztali, ahol a felhasználók a teljes asztali eléréséhez

Alapértelmezés szerint egy asztali alkalmazás csoportot (a "Desktop alkalmazáscsoport" nevű) automatikusan létrejön, amikor a gazdagép-készletet hoz létre. Az alkalmazás csoport bármikor eltávolíthatja. Azonban nem hozhat létre asztali alkalmazás egy másik csoportot a gazdagép készlet közben egy asztali alkalmazás a csoport létezik. Távoli alkalmazások közzétételéhez létre kell hoznia egy RemoteApp-alkalmazás csoportot. Létrehozhat több RemoteApp alkalmazáscsoportok eltérő feldolgozói olyan forgatókönyvek esetére. Távoli alkalmazások átfedő RemoteApp alkalmazás másik csoportokat is tartalmazhat.

Erőforrások közzétételét a felhasználók számára, meg kell rendelnie őket az alkalmazás. Felhasználók hozzárendelése az alkalmazás, vegye figyelembe az alábbiakat:

- A felhasználó nem lehet hozzárendelni, a gazdagép ugyanazon készlet RemoteApp alkalmazás csoport és a egy asztali alkalmazás levő csoporthoz.
- A felhasználó a gazdagép-készleten belül több alkalmazás csoporthoz rendelhető, és az adatcsatorna lesz mindkét alkalmazáscsoportok felgyűlnek.

## <a name="tenant-groups"></a>Bérlő csoportok

A virtuális asztali Windows a Windows virtuális asztal bérlő az, ahol történik, a beállítás és konfiguráció a legtöbb. A virtuális asztali Windows-bérlő tartalmazza a gazdagép-készletek, az alkalmazás és alkalmazás csoport-felhasználói hozzárendeléseit. Előfordulhatnak azonban olyan helyzetekben, ahol kezeléséhez szükséges Windows virtuális asztal több bérlő egyszerre, különösen akkor, ha Ön egy Felhőszolgáltató (CSP) vagy az üzemeltetési partner. Ilyen esetekben egy egyéni Windows virtuális asztali bérlői csoport használatával helyezze el az ügyfelek virtuális asztali Windows-bérlők mindegyike, és központilag kezelheti a hozzáférést. Ha a Windows virtuális asztal létrehozása egyetlen bérlő csak felügyeli, a bérlő csoport fogalma nem vonatkozik, és továbbra is működnek, és a bérlőt, hogy az alapértelmezett bérlői csoportja megtalálható.

## <a name="end-users"></a>Végfelhasználók

Miután az alkalmazás csoportokhoz társított felhasználók, képesek csatlakozni egy Windows virtuális asztali telepítése a Windows virtuális asztali ügyfelek bármelyikével.

## <a name="next-steps"></a>További lépések

További tudnivalók a delegált hozzáférés és a szerepkörök hozzárendelése a felhasználók [Windows virtuális asztal előzetes verzióban elérhető delegált hozzáférés](delegated-access-virtual-desktop.md).

Ismerje meg, hogyan állítható be a virtuális asztali Windows-bérlőhöz, lásd: [bérlő létrehozása a Windows virtuális asztal Preview](tenant-setup-azure-active-directory.md).

Megtudhatja, hogyan csatlakozhat a virtuális asztali Windows, az alábbi cikkekben talál:

- [Csatlakozás a távoli asztali ügyfél Windows 7 és Windows 10 rendszeren](connect-windows-7-and-10.md)
- [Csatlakozás a Windows virtuális asztal előzetes webes ügyféllel](connect-web.md)
