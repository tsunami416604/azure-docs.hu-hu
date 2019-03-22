---
title: A Windows virtuális asztal (előzetes verzió) – az Azure-bérlő létrehozása
description: Ismerteti, hogyan állíthatja be Windows virtuális asztal bérlők számára az Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2dafb58a805a2da89852c84ebad08f1c495adb83
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318119"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Oktatóanyag: Bérlő létrehozása a Windows virtuális asztal (előzetes verzió)

Bérlő létrehozása a Windows virtuális asztal (előzetes verzió) első lépése az asztali virtualizálási megoldás kiépítésének felé. A bérlő egy olyan egy vagy több gazdagép-készletek. Minden gazdagép készlet több munkamenet gazdagépek, virtuális gépként az Azure-ban futó és a Windows virtuális asztal szolgáltatásban regisztrált áll. Minden gazdagép készlet egy vagy több távoli asztal és a Távsegítség alkalmazás-erőforrások közzététele a felhasználók számára használt alkalmazáscsoportok is áll. A bérlőhöz hozhatja létre a gazdagép-készletek, az alkalmazás létrehozása, hozzárendelése a felhasználók és kapcsolatok a szolgáltatáson keresztül.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A virtuális asztali Windows-szolgáltatás biztosítása az Azure Active Directory engedélyekkel.
> * Az alkalmazás-szerepkör TenantCreator hozzárendelni egy felhasználóhoz, az Azure Active Directory-bérlőben.
> * Hozzon létre egy virtuális asztali Windows-bérlő.

A következő szükséges állítsa be a Windows virtuális asztal bérlő:

* A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) bérlői azonosító a Windows virtuális asztal felhasználói.
* Egy globális rendszergazdai fiókkal az Azure Active Directory-bérlőn belül.
   * Ez a Cloud Solution Provider (CSP) szervezetek ügyfelek számára, akik Windows virtuális asztal bérlő létrehozására is vonatkozik. Ha egy CSP-szervezet, jelentkezhet be az ügyfél az Azure Active Directory globális rendszergazdájaként kell lennie.
* Egy Azure-előfizetés azonosítója

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-service"></a>A virtuális asztali Windows-szolgáltatás biztosítása az Azure Active Directory engedélyekkel

Ha az Azure Active Directory Windows virtuális asztali már kapott engedélyt, kihagyhatja ezt a szakaszt.

Hogyan adható engedély a virtuális asztali Windows-szolgáltatás lehetővé teszi, hogy azt az Azure Active Directory, a lekérdezés rendszergazdai és végfelhasználói feladatokkal.

A szolgáltatás-engedélyek megadására:

1. Nyisson meg egy böngészőt, és csatlakozzon a [Windows virtuális asztal hozzájárulást kérő lap](https://rdweb.wvd.microsoft.com).
2. A **hozzájárulás beállítás** > **kiszolgálóalkalmazás**, adja meg az Azure Active Directory-bérlő neve vagy a címtár-azonosító, majd válassza ki **küldés**.
        A Cloud Solution Provider rendelkező ügyfelek esetén a Azonosítóra az ügyfél a Microsoft ID a partnerportálról. Nagyvállalati ügyfeleink esetén az azonosító alatt található **Azure Active Directory** > **tulajdonságok** > **címtár-azonosító**.
3. Jelentkezzen be a Windows virtuális asztal hozzájárulást kérő lap egy globális rendszergazdai fiókkal. Például, ha a Contoso vállalattal, a fiók lehet admin@contoso.com vagy admin@contoso.onmicrosoft.com.  
4. Válassza ki az **Elfogadás** lehetőséget.
5. Várjon egy percet.
6. Lépjen vissza a [Windows virtuális asztal hozzájárulást kérő lap](https://rdweb.wvd.microsoft.com).
7. Lépjen a **hozzájárulás beállítás** > **ügyfélalkalmazás**, adja meg az ugyanazon az Azure AD-bérlő neve vagy a címtár-azonosító, majd válassza ki **küldés**.
8. Jelentkezzen be globális rendszergazdaként, például vissza a 3. lépés a Windows virtuális asztal hozzájárulást kérő lap.
9. Válassza ki az **Elfogadás** lehetőséget.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Az alkalmazás-szerepkör TenantCreator hozzárendelni egy felhasználóhoz, az Azure Active Directory-bérlőben

A TenantCreator alkalmazás-szerepkör hozzárendelése egy Azure Active Directory-felhasználó lehetővé teszi, hogy a felhasználó az Azure Active Directory társított Windows virtuális asztal bérlő létrehozása. Szüksége lesz a TenantCreator szerepkör hozzárendelése a globális rendszergazdai fiókjával.

A TenantCreator alkalmazás-szerepkör hozzárendelése a globális rendszergazdai fiókjával:

1. Nyisson meg egy böngészőt, és csatlakozzon a [Azure Active Directory portálon](https://aad.portal.azure.com) globális rendszergazdai fiókjával.
   - Ha több Azure AD-bérlő dolgozik, nyisson meg egy privát böngésző-munkamenetet, és másolja és illessze be a címet URL-címek ajánlott eljárás.
2. Válassza ki **vállalati alkalmazások**, keressen **Windows virtuális asztal** válassza ki az alkalmazást.
3. Válassza ki **felhasználók és csoportok**, majd **felhasználó hozzáadása**.
4. Válassza ki a felhasználók és csoportok a **hozzárendelés hozzáadása** panel
5. Keressen rá egy felhasználói fiókot, amely létrehozza a virtuális asztali Windows-bérlő. 
   - Az egyszerűség kedvéért ez a globális rendszergazdai fiók is lehet.
6. Válassza ki a felhasználói fiókot, kattintson a **kiválasztása** gombra, és válassza ki **hozzárendelése**.

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows virtuális asztal bérlő létrehozása

Most, hogy jogosultságot kap a Windows virtuális asztal szolgáltatás az Azure Active Directory lekérdezése, és rendelje hozzá a TenantCreator szerepkört egy felhasználói fiókhoz, egy Windows virtuális asztal bérlőt is létrehozhat.

Először [töltse le és importálja a modult a Windows virtuális asztal](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Jelentkezzen be Windows virtuális asztalok használata a TenantCreator felhasználói fiók ezzel a parancsmaggal:

```powershell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
```

Ezt követően hozzon létre egy új Windows virtuális asztal bérlőhöz társított Azure Active Directory-bérlő:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

A zárójeles értékek le kell cserélni, a szervezet és a bérlői megfelelő értékeket. Például tegyük fel, Ön a Windows virtuális asztal TenantCreator a Contoso szervezet számára. A következő parancsot kellene futtatnia parancsmag kellene kinéznie:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>További lépések

A bérlő létrehozása után kell tenni egy gazdagép-készletet. Gazdagép-készletek kapcsolatos további információkért folytassa az oktatóanyag során egy gazdagép-készlet létrehozása a Windows virtuális asztal.

> [!div class="nextstepaction"]
> [Windows virtuális asztali állomás készlet oktatóanyag](./create-host-pools-azure-marketplace.md)
