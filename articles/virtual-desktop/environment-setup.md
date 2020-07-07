---
title: Windows rendszerű virtuális asztali környezet – Azure
description: A Windows rendszerű virtuális asztali környezet alapvető elemei.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad535dd18b89cbe2fceab90f73789180ad332b57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612367"
---
# <a name="windows-virtual-desktop-environment"></a>A Windows Virtual Desktop környezete

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/environment-setup-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows Virtual Desktop egy olyan szolgáltatás, amely egyszerű és biztonságos hozzáférést biztosít a felhasználóknak a virtualizált asztali számítógépekhez és a RemoteAppokhoz. Ez a témakör részletesen ismerteti a Windows rendszerű virtuális asztali környezet általános szerkezetét.

## <a name="host-pools"></a>Gazdagépek készletei

A gazdagépek olyan Azure-beli virtuális gépek gyűjteményei, amelyek a Windows rendszerű virtuális asztali ügynök futtatásakor a Windows Virtual Desktop szolgáltatásban futnak. A gazdagép-készletben lévő összes munkamenet-gazda virtuális gépet ugyanabból a rendszerképből kell származnia, amely konzisztens felhasználói élményt nyújt.

A gazdagépek két típus egyike lehet:

- Személyes, ahol minden egyes munkamenet-gazdagép az egyes felhasználókhoz van rendelve.
- Készletezett, ahol a munkamenet-gazdagépek fogadhatnak kapcsolatokat bármely olyan felhasználótól, aki a gazdagépen belül egy alkalmazáscsoport számára van engedélyezve.

A gazdagépen további tulajdonságokat is megadhat a terheléselosztási viselkedés megváltoztatásához, az egyes munkamenet-állomások által elvégezhető munkamenetek számának megadásához, valamint arról, hogy a felhasználó milyen műveleteket végezhet a gazdagépen a gazdaszámítógépen a Windows rendszerű virtuális asztali munkamenetbe való bejelentkezéskor. A felhasználók számára közzétett erőforrásokat az alkalmazás-csoportok segítségével szabályozhatja.

## <a name="app-groups"></a>Alkalmazáscsoportok

Az alkalmazáscsoport a gazdagépen lévő munkamenet-gazdagépekre telepített alkalmazások logikai csoportosítása. Az alkalmazások csoportja a két típus egyike lehet:

- RemoteApp, ahol a felhasználók az egyénileg kiválasztott és az alkalmazás csoportba való közzétételhez hozzáférnek a RemoteApp-hoz
- Asztal, ahol a felhasználók a teljes asztalt érik el

Alapértelmezés szerint a rendszer automatikusan létrehoz egy asztali alkalmazást ("asztali alkalmazáscsoport"), amikor létrehoz egy gazdagép-készletet. Az alkalmazáscsoport bármikor eltávolítható. Azonban nem hozhat létre másik asztali alkalmazást a gazdagépen, amíg létezik egy asztali alkalmazáscsoport. A RemoteApp-alkalmazások közzétételéhez létre kell hoznia egy RemoteApp-alkalmazás csoportot. Több RemoteApp-alkalmazáscsoport is létrehozható a különböző munkavégző forgatókönyvek kielégítése érdekében. A különböző RemoteApp-alkalmazások többek között átfedésben lévő RemoteAppkat is tartalmazhatnak.

Az erőforrások felhasználók számára való közzétételéhez hozzá kell rendelnie azokat az alkalmazás-csoportokhoz. Amikor felhasználókat rendel az alkalmazás-csoportokhoz, vegye figyelembe a következőket:

- Egy felhasználó egy asztali alkalmazáscsoport és egy RemoteApp-alkalmazás ugyanahhoz a készlethez is hozzárendelhető. A felhasználók azonban csak egyféle alkalmazáscsoport indítását tudják elindítani. A felhasználók nem indíthatnak egyszerre mindkét típusú alkalmazást egyetlen munkamenetben.
- Egy felhasználó több, ugyanazon a gazdagépen belüli alkalmazás-csoporthoz is hozzárendelhető, és a hírcsatorna mindkét alkalmazáscsoport összegyűjtését eredményezi.

## <a name="workspaces"></a>Munkaterületek

A munkaterület az alkalmazáscsoport logikai csoportosítása a Windows rendszerű virtuális asztalon. Minden Windows rendszerű virtuális asztali alkalmazás csoportjának társítania kell egy munkaterületet a felhasználók számára a közzétett távoli alkalmazások és asztali számítógépek megtekintéséhez.  

## <a name="end-users"></a>Végfelhasználók

Miután hozzárendelte a felhasználókat az alkalmazás csoportjaihoz, csatlakozhatnak a Windows rendszerű virtuális asztali környezethez a Windows rendszerű virtuális asztali ügyfelek bármelyikével.

## <a name="next-steps"></a>További lépések

További információ a delegált hozzáférésről és a szerepkörök felhasználókhoz való hozzárendeléséről a [Windows Virtual Desktopban](delegated-access-virtual-desktop.md).

A Windows rendszerű virtuális asztali címkészlet beállításával kapcsolatos további információkért lásd: [gazdagép-készlet létrehozása a Azure Portal](create-host-pools-azure-marketplace.md).

A következő cikkekből megtudhatja, hogyan csatlakozhat a Windows rendszerű virtuális asztalhoz:

- [Windows 10 vagy Windows 7 rendszerű kapcsolat](connect-windows-7-and-10.md)
- [Webböngészővel való kapcsolat](connect-web.md)
- [Kapcsolódás az Android-ügyféllel](connect-android.md)
- [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)
- [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)