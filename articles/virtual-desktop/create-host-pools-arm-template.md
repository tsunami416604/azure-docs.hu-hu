---
title: Windows virtuális asztali gazdagépkészlete Azure Resource Manager – Azure
description: Host készlet létrehozása a Windows virtuális asztalon egy Azure Resource Manager sablonnal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292330"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal

A gazdakészletek egy vagy több azonos virtuális gép gyűjteménye a Windows virtuális asztal bérlői környezetében. Minden gazdagépkészlet tartalmazhat egy alkalmazáscsoportot, amelyet a felhasználók ugyanúgy használhatnak, mint a fizikai asztalon.

Kövesse ezt a szakaszt az utasításokat, hogy hozzon létre egy gazdagépkészletet egy Windows virtuális asztal bérlőhöz a Microsoft által biztosított Azure Resource Manager sablonnal. Ez a cikk ismerteti, hogyan hozhat létre gazdagépkészletet a Windows virtuális asztalon, hogyan hozhat létre egy erőforráscsoportot virtuális gépekkel egy Azure-előfizetésben, hogyan csatlakozhat ezekhez a virtuális gépekhez az AD-tartományhoz, és regisztrálhatja a virtuális gépeket a Windows virtuális asztallal.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Mire van szükség az Azure Resource Manager sablon futtatásához?

Az Azure Resource Manager-sablon futtatása előtt győződjön meg arról, hogy ismeri a következő dolgokat:

- A használni kívánt kép forrása. Az Azure Gallery-ből származik, vagy egyéni?
- A tartomány hozhatja össze a hitelesítő adatokat.
- A Windows virtuális asztal hitelesítő adatai.

Amikor létrehoz egy Windows virtuális asztal gazdakészletet az Azure Resource Manager sablonnal, létrehozhat egy virtuális gépet az Azure-galériából, egy felügyelt lemezképet vagy egy nem felügyelt lemezképet. Ha többet szeretne tudni a virtuálisgép-lemezképek létrehozásáról, [olvassa el a Windows VHD vagy VHDX feltöltésének előkészítése az Azure-ba](../virtual-machines/windows/prepare-for-upload-vhd-image.md) című témakört, és [hozzon létre egy általános virtuális gép felügyelt lemezképét az Azure-ban.](../virtual-machines/windows/capture-image-resource.md)

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Az Azure Resource Manager sablon futtatása új gazdagépkészlet kiépítéséhez

A kezdéshez nyissa meg [ezt a GitHub URL-címet.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezése az Azure-ban

Ha nagyvállalati előfizetésben telepít, görgessen le, és válassza **a Telepítés az Azure-ba**lehetőséget, majd ugorjon előre, töltse ki a paramétereket a lemezképforrása alapján.

Ha felhőszolgáltatói előfizetésben telepít, az alábbi lépésekkel telepítheti az Azure-ba:

1. Görgessen le, és kattintson a jobb gombbal **az Üzembe helyezés az Azure-ba**elemre, majd válassza a Hivatkozás **helyének másolása parancsot.**
2. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömböt, és illessze be oda a hivatkozást.
3. Közvetlenülhttps://portal.azure.com/a " " után, és mielőtt a hashtag (#) beír egy at jelet (@), majd a bérlői tartomány nevét. Íme egy példa a használni kívánt `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`formátumra: .
4. Jelentkezzen be az Azure Portalon rendszergazdai/közreműködői engedélyekkel rendelkező felhasználóként a Felhőszolgáltató-előfizetésbe.
5. Illessze be a szövegszerkesztőbe másolt hivatkozást a címsorba.

A forgatókönyvhöz megírt paraméterekkel kapcsolatos útmutatásért tekintse meg a Windows virtuális asztal [hoz afájl ban található fájlt.](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md) A fontos hír mindig frissül a legújabb módosításokkal.

## <a name="assign-users-to-the-desktop-application-group"></a>Felhasználók hozzárendelése az asztali alkalmazáscsoporthoz

A GitHub Azure Resource Manager-sablon befejezése után rendeljen hozzá felhasználói hozzáférést, mielőtt elkezdené a teljes munkamenet-asztalok tesztelését a virtuális gépeken.

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg.

Ha felhasználókat szeretne hozzárendelni az asztali alkalmazáscsoporthoz, nyisson meg egy PowerShell-ablakot, és futtassa ezt a parancsmalapot a Windows Virtuális asztal környezetbe való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután adja hozzá a felhasználókat az asztali alkalmazáscsoporthoz ezzel a parancsmaggal:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó upn egyeznie kell a felhasználó identitását az user1@contoso.comAzure Active Directoryban (például). Ha több felhasználót szeretne hozzáadni, ezt a parancsmackét minden felhasználószámára futtatnia kell.

Miután elvégezte ezeket a lépéseket, az asztali alkalmazáscsoportba felvett felhasználók bejelentkezhetnek a Windows virtuális asztalra a támogatott Távoli asztali ügyfelekkel, és megnézhetik a munkamenet asztalának erőforrását.

>[!IMPORTANT]
>A Windows virtuális asztali környezet azure-beli védelméhez azt javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows virtuális asztal nem igényel nyitott bejövő port3389 a felhasználók számára, hogy hozzáférjenek a gazdakészlet virtuális gépek. Ha hibaelhárítási célból meg kell nyitnia a 3389-es portot, javasoljuk, hogy [just-in-time virtuális gép-hozzáférést használjon.](../security-center/security-center-just-in-time.md)