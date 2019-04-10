---
title: Windows virtuális asztal előzetes gazdagép-készlet létrehozása az Azure Resource Manager-sablonok – Azure
description: Hogyan gazdagép-készlet létrehozása a Windows virtuális asztal előzetes verzióban érhető el egy Azure Resource Manager-sablonnal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279227"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal

Gazdagép-készletekre egy gyűjtemény egy vagy több egyforma virtuális gépek virtuális Windows Desktop előzetes verziójához bérlői környezetekben. Minden gazdagép címkészlet tartalmazhat egy alkalmazás-csoportot, amely a felhasználók használhatják, mint a fizikai számítógépen.

Ez a szakasz utasítások gazdagép-készlet létrehozása egy virtuális asztali Windows-bérlő esetében a Microsoft által biztosított Azure Resource Manager-sablonnal. Ebben a cikkben megtudhatja, hogyan lehet Windows virtuális asztal a gazdagép-készlet létrehozása, hozzon létre egy erőforráscsoportot az Azure-előfizetéssel rendelkező virtuális gépek, virtuális gépek csatlakoztatása az Active Directory-tartománynak és regisztrálja a virtuális gépek virtuális asztali Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Mit kell futtatnia az Azure Resource Manager-sablon

Győződjön meg arról, hogy ismeri a következőkre az Azure Resource Manager-sablon futtatása előtt:

- Ha a használni kívánt kép forrása. Ennyi az egész Azure-katalógus vagy egyéni?
- A tartományhoz való csatlakozás hitelesítő adatait.
- A Windows virtuális asztal hitelesítő adatait.

Ha Windows virtuális asztali állomás készletet hoz létre az Azure Resource Manager-sablonnal, az Azure-katalógus, egy felügyelt rendszerképet vagy nem felügyelt rendszerkép egy virtuális gépet hozhat létre. Virtuálisgép-rendszerképek létrehozásával kapcsolatos további tudnivalókért lásd: [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) és [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Futtassa az új gazdagép-készlet kiépítése az Azure Resource Manager-sablon

Megkezdéséhez lépjen a [a GitHub URL-CÍMÉT](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezéséhez az Azure-bA

Ha egy vállalati-előfizetésben helyezi üzembe, görgessen lefelé, és válassza ki **üzembe helyezés az Azure**, majd a skip előre töltse ki a paramétereket a képforrás alapján.

Ha a Cloud Solution Provider-előfizetésben helyezi üzembe, helyezze üzembe az Azure az alábbi lépésekkel:

1. Görgessen lefelé, és kattintson a jobb gombbal **üzembe helyezés az Azure**, majd **másolási csatolás helye**.
2. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömbben, és illessze be a hivatkozás van.
3. Után "https://portal.azure.com/" és a hashtaggel (#) előtt adjon meg egy kukacjelet (@) a bérlő tartományneve követ. Íme egy példa a használandó formátum: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Jelentkezzen be az Azure Portalon a Felhőszolgáltatói előfizetéshez rendszergazdai/közreműködői engedélyekkel rendelkező felhasználóként.
5. Illessze be a címet a címsorba a szövegszerkesztőbe másolt hivatkozást.

Kapcsolatos paramétereket kell megadnia a forgatókönyvnek, tekintse át a Windows virtuális asztal [információs fájl](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). A legutóbbi módosítások mindig frissül információs fájlban olvasható.

## <a name="assign-users-to-the-desktop-application-group"></a>Felhasználók hozzárendelése az asztali alkalmazás csoporthoz

A GitHub Azure Resource Manager-sablon befejezése után rendelje hozzá a felhasználói hozzáférés a virtuális gépeken a teljes munkamenet asztalok tesztelés megkezdése előtt.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Felhasználók hozzárendelése az asztali alkalmazás csoporthoz, nyisson meg egy PowerShell-ablakot, és jelentkezzen be a Windows virtuális asztali környezetben, a parancsmag futtatásához:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután állítsa be a környezetet a bérlő csoporthoz, ez a parancsmag az Azure Resource Manager-sablonban megadott:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Ezt követően a felhasználók hozzáadása az asztali csoporthoz ezzel a parancsmaggal:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó egyszerű Felhasználónevének meg kell egyeznie a felhasználó identitását az Azure Active Directoryban (például user1@contoso.com). Ha azt szeretné, több felhasználó hozzáadásának, ez a parancsmag minden felhasználó számára kell futtatni.

Miután végrehajtotta ezeket a lépéseket, az asztali alkalmazás csoportba felvett felhasználók Windows virtuális asztal támogatott a távoli asztal ügyfelek jelentkezzen be, és tekintse meg a munkamenet asztali erőforrás.

>[!IMPORTANT]
>Védelme érdekében az Azure-ban, a Windows virtuális asztali környezetben ajánlott ne nyissa meg a 3389-es porton bejövő a virtuális gépeken. Windows virtuális asztal nyílt bejövő port felhasználók számára hozzáférést a gazdagép-készlet virtuális gépek a 3389-es nem igényel. Ha a 3389-es port hibaelhárítás céljából kell megnyitni, azt javasoljuk, használja [just-in-time VM access](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).