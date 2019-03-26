---
title: Windows virtuális asztal előzetes gazdagép-készlet létrehozása a PowerShell használatával – Azure
description: Hogyan gazdagép-készlet létrehozása a Windows virtuális asztal előzetes verzióban érhető el a PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: bd46e5f7428bab58508521b2c7d4d7cca25d689b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439073"
---
# <a name="create-a-host-pool-with-powershell"></a>Gazdagépcsoport létrehozása a PowerShell-lel

Gazdagép-készletekre egy gyűjtemény egy vagy több egyforma virtuális gépek virtuális Windows Desktop előzetes verziójához bérlői környezetekben. Minden gazdagép címkészlet tartalmazhat egy alkalmazás-csoportot, amely a felhasználók használhatják, mint a fizikai számítógépen.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Gazdagép-készlet létrehozása a PowerShell-ügyfél használata

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Futtassa a következő parancsmagot a Windows virtuális asztali környezetben való bejelentkezéshez

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Ezt követően állítsa be a környezetet a bérlő csoporthoz a következő parancsmag futtatásával. Nem rendelkezik a bérlő csoport nevére, a bérlő-e nagy valószínűséggel az "alapértelmezett bérlőt csoportban található,", akkor kihagyhatja ezt a parancsmagot.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Ezután futtassa ezt a parancsmagot új gazdagép-készlet létrehozása a Windows virtuális asztal bérlő:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Futtassa a következő parancsmaggal hozzon létre egy regisztrációs jogkivonatot egy munkamenetgazda, a gazdagép-készletre való, és mentse azt egy új fájlt a helyi számítógépen való engedélyezéséhez. Megadhatja, hogy mennyi ideig tart érvényes a regisztrációs jogkivonatot, a - ExpirationHours paraméter használatával.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Ezt követően futtassa ezt a parancsmagot, az Azure Active Directory-felhasználók hozzáadása a gazdagép-készlet alapértelmezett asztali alkalmazás csoporthoz.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

A **Add-RdsAppGroupUser** parancsmag nem támogatja a biztonsági csoportok hozzáadásának, és csak hozzáadja egy felhasználó egyszerre alkalmazáscsoportnak. Ha azt szeretné, több felhasználó hozzáadása az alkalmazáscsoportnak, futtassa újra a parancsmagot a megfelelő egyszerű felhasználónév.

Futtassa a következő parancsmag használatával exportálja a regisztrációs jogkivonatot egy változó, mert ezzel fogja később a [regisztrálja a virtuális gépek, a Windows virtuális asztali állomás készlethez](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek a gazdagép-készlet létrehozása

Most már létrehozhat egy Azure virtuális gépen, amely összekapcsolható a Windows virtuális asztali állomás-készlethez.

Többféle módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása az Azure-katalógus-lemezkép](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [A virtuális gépek nem felügyelt rendszerkép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>A Windows Virtual Desktop előzetes verziójához ügynöktelepítéseket a virtuális gépek előkészítése

Tegye a következőket a virtuális gépek előkészítése ahhoz, hogy a virtuális asztali Windows-ügynökök telepítése és a virtuális gépek regisztrálása a Windows virtuális asztali állomás készlethez kell:

- Kell a tartományhoz való csatlakozás a gépet. Ez lehetővé teszi a bejövő Windows virtuális asztali felhasználók Azure Active Directory-fiókjukból képezhető le az Active Directory-fiókjához, és sikerült engedélyezni a virtuális géphez való hozzáférés.
- Ha a virtuális gép fut a Windows Server operációs rendszer telepítenie kell a távoli asztali munkamenetgazda (RDSH) szerepkör. A távoli asztali Munkamenetgazda szerepkör lehetővé teszi, hogy a Windows virtuális asztal ügynökök telepítéséhez megfelelően.

Sikeresen tartományhoz való csatlakozás tegye az alábbiakat minden egyes virtuális gépen:

1. [Csatlakozzon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Indítsa el a virtuális gépen **Vezérlőpult** válassza **rendszer**.
3. Válassza ki **számítógépnév**válassza **beállításainak módosítása**, majd válassza ki **módosítása...**
4. Válassza ki **tartomány** és írja be az Active Directory-tartomány a virtuális hálózaton.
5. Hitelesítés a tartományhoz csatlakozó gépek jogosultságokkal rendelkező tartományi fiókkal.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>A virtuális gépek, a Windows virtuális asztal előzetes gazdagép készlethez regisztrálása

A virtuális gépeket egy Windows virtuális asztali állomás készlet regisztrálás rendkívül egyszerű, a virtuális asztali Windows-ügynökök telepítése.

Regisztrálja a virtuális asztali Windows-ügynökök, hajtsa végre a műveletet minden egyes virtuális gépen:

1. [Csatlakozzon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Ügynök letöltése és telepítése a Windows virtuális asztal.
   - Töltse le a [Windows virtuális asztali ügynök](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kattintson a jobb gombbal a letöltött telepítőt, jelölje be **tulajdonságok**válassza **feloldása**, majd **OK**. Ez lehetővé teszi a rendszer, hogy bízzon meg a telepítőt.
   - Futtassa a telepítőt. Amikor a telepítő kéri a regisztrációs jogkivonatot, adja meg a kapott érték az **Export-RdsRegistrationInfo** parancsmagot.
3. Töltse le és telepítse a Windows virtuális asztal ügynök rendszertöltő.
   - Töltse le a [Windows virtuális asztali ügynök Rendszerbetöltőt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kattintson a jobb gombbal a letöltött telepítőt, jelölje be **tulajdonságok**válassza **feloldása**, majd **OK**. Ez lehetővé teszi a rendszer, hogy bízzon meg a telepítőt.
   - Futtassa a telepítőt.
4. Telepítse, vagy a Windows Virtual Desktop egymás mellett verem aktiválása. A lépések eltérőek lesznek a virtuális gép használja az operációs rendszer verziójától függően.
   - Ha a virtuális gép operációs rendszer Windows Server 2016-ra:
     - Töltse le a [Windows virtuális asztal egymás mellett stack](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Kattintson a jobb gombbal a letöltött telepítőt, jelölje be **tulajdonságok**válassza **feloldása**, majd **OK**. Ez lehetővé teszi a rendszer, hogy bízzon meg a telepítőt.
     - Futtassa a telepítőt.
   - Ha a virtuális gép operációs rendszer Windows 10-es 1809 vagy újabb vagy Windows Server 2019 vagy újabb:
     - Töltse le a [parancsfájl](https://go.microsoft.com/fwlink/?linkid=2084268) a párhuzamos verem aktiválása.
     - Kattintson a jobb gombbal a letöltött szkript, jelölje be **tulajdonságok**válassza **feloldása**, majd **OK**. Ez lehetővé teszi a rendszert, hogy bízzon meg a parancsfájl.
     - Az a **Start** menüben keresse meg a Windows PowerShell ISE-ben, kattintson a jobb gombbal, majd válassza ki **Futtatás rendszergazdaként**.
     - Válassza ki **fájl**, majd **megnyitása...** , majd keresse meg a PowerShell-parancsprogramot a letöltött fájlokat, és nyissa meg.
     - Válassza ki a zöld lejátszás gombra a szkript futtatásához.

## <a name="next-steps"></a>További lépések

Most, hogy végzett a gazdagép-készletben, töltse fel azt a távoli alkalmazások. Virtuális asztali Windows-alkalmazások kezeléséről további információkért tekintse meg a kezelés alkalmazás csoportok oktatóanyag.

> [!div class="nextstepaction"]
> [Útmutató alkalmazások csoportok kezelése](./manage-app-groups.md)
