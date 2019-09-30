---
title: Windows rendszerű virtuális asztali címkészlet létrehozása Azure Resource Manager sablonnal – Azure
description: A Windows rendszerű virtuális asztalban lévő gazdagépek létrehozása Azure Resource Manager sablonnal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: b96ecad93c481a762b81a7888940850ed3a73e5f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679614"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

Ez a szakasz útmutatást nyújt a Windows rendszerű virtuális asztali bérlőhöz tartozó gazdagépek létrehozásához a Microsoft által biztosított Azure Resource Manager sablonnal. Ebből a cikkből megtudhatja, hogyan hozhat létre egy gazdagépet a Windows rendszerű virtuális asztalon, hogyan hozhat létre egy Azure-előfizetésben virtuális géppel rendelkező erőforráscsoportot, hogyan csatlakozhat ezekhez a virtuális gépekhez az AD-tartományhoz, és hogyan regisztrálhatja a virtuális gépeket a Windows

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Mire van szükség a Azure Resource Manager sablon futtatásához

A Azure Resource Manager sablon futtatása előtt győződjön meg arról, hogy ismeri a következő dolgokat:

- A használni kívánt rendszerkép forrása. Ez az Azure-katalógusból vagy egyéni?
- A tartományhoz való csatlakozáshoz szükséges hitelesítő adatok.
- A Windows rendszerű virtuális asztali hitelesítő adatai.

Amikor létrehoz egy Windows rendszerű virtuális asztali címkészletet a Azure Resource Manager sablonnal, létrehozhat egy virtuális gépet az Azure-katalógusból, egy felügyelt képből vagy egy nem felügyelt rendszerképből. A virtuálisgép-lemezképek létrehozásával kapcsolatos további információkért lásd: [Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) , valamint [egy általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Az új címkészlet kiépítés Azure Resource Manager sablonjának futtatása

A kezdéshez nyissa meg [ezt a GitHub URL-címet](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezése az Azure-ban

Ha vállalati előfizetésben végzi a üzembe helyezést, görgessen le, és válassza az **üzembe helyezés az Azure**-ban lehetőséget, majd ugorjon ki a megfelelő paramétereket a lemezkép forrása alapján.

Ha felhőalapú megoldás-szolgáltatói előfizetést használ, kövesse az alábbi lépéseket az Azure-ba való üzembe helyezéshez:

1. Görgessen le, és kattintson **a jobb gombbal az Azure**-ba, majd válassza a **hivatkozás helyének másolása**lehetőséget.
2. Nyisson meg egy szövegszerkesztőt, például a jegyzettömböt, és illessze be a hivatkozást.
3. Közvetlenül a "https://portal.azure.com/" után és a hashtag (#) előtt adja meg a (z) (@) jelet a bérlői tartomány neve után. Íme egy példa a használni kívánt formátumra: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Jelentkezzen be a Azure Portal felhasználóként rendszergazdai/közreműködői engedélyekkel a felhőalapú megoldás-szolgáltató előfizetéséhez.
5. Illessze be a szövegszerkesztőbe másolt hivatkozást a címsorba.

A forgatókönyvhöz szükséges paraméterekkel kapcsolatos útmutatásért tekintse meg a Windows rendszerű virtuális asztal [információs fájlját](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). A readme mindig frissül a legújabb módosításokkal.

## <a name="assign-users-to-the-desktop-application-group"></a>Felhasználók társítása az asztali alkalmazás csoportjához

Miután a GitHub Azure Resource Manager sablon befejeződik, rendeljen hozzá felhasználói hozzáférést, mielőtt megkezdené a teljes munkamenet-asztalok tesztelését a virtuális gépeken.

Először [töltse le és importálja a](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg.

Ha felhasználókat szeretne hozzárendelni az asztali alkalmazás csoportjához, nyisson meg egy PowerShell-ablakot, és futtassa ezt a parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezt követően adja hozzá a felhasználókat az asztali alkalmazás csoportjához a következő parancsmaggal:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó UPN-nek meg kell egyeznie a felhasználó identitásával Azure Active Directoryban (például user1@contoso.com). Ha több felhasználót szeretne felvenni, minden felhasználóhoz futtatnia kell ezt a parancsmagot.

A lépések elvégzése után az asztali alkalmazás csoportba felvett felhasználók bejelentkezhetnek a Windows rendszerű virtuális asztalra a támogatott Távoli asztal ügyfelekkel, és megtekinthetik a munkamenet-asztal erőforrásait.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).