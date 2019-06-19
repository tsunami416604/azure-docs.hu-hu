---
title: Windows virtuális asztal előzetes verziója – Azure-bérlő létrehozása
description: Ismerteti, hogyan állíthatja be Windows virtuális asztal előzetes bérlők számára az Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 3d418d9f18c98e1b6fdf39924ab41dae77fba291
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204752"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Oktatóanyag: Bérlő létrehozása a Windows virtuális asztal előzetes verzióban érhető el

Az első lépés az asztali virtualizálási megoldás létrehozása bérlő létrehozására Windows virtuális asztal előzetes verzióban. A bérlő egy olyan egy vagy több gazdagép-készletek. Minden gazdagép készlet több munkamenet gazdagépek, virtuális gépként az Azure-ban futó és a Windows virtuális asztal szolgáltatásban regisztrált áll. Minden gazdagép készlet egy vagy több távoli asztal és a Távsegítség alkalmazás-erőforrások közzététele a felhasználók számára használt alkalmazáscsoportok is áll. A bérlőhöz gazdagép-készleteket hozhat létre, az alkalmazás létrehozása, felhasználók hozzárendelése és kapcsolatok a szolgáltatáson keresztül.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A virtuális asztali Windows-szolgáltatás biztosítása az Azure Active Directory engedélyekkel.
> * Az alkalmazás-szerepkör TenantCreator hozzárendelni egy felhasználóhoz, az Azure Active Directory-bérlőben.
> * Hozzon létre egy virtuális asztali Windows-bérlő.

A következő szükséges állítsa be a Windows virtuális asztal bérlő:

* A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) bérlői azonosító a Windows virtuális asztal felhasználói.
* Egy globális rendszergazdai fiókkal az Azure Active Directory-bérlőn belül.
   * Ez vonatkozik, az ügyfelek számára, akik Windows virtuális asztal bérlő hoz Cloud Solution Provider (CSP) szervezetek is. Ha Ön egy CSP-szervezethez, jelentkezzen be globális rendszergazdaként az ügyfél az Azure Active Directory-példány tudni kell lennie.
   * A rendszergazdai fiókot kell lennie származási hely az Azure Active Directory-bérlővel, amelyben próbál létrehozni a virtuális asztali Windows-bérlőt. Ez a folyamat nem támogatja az Azure Active Directory B2B (a Vendég).
   * A rendszergazdai fiók egy munkahelyi vagy iskolai fióknak kell lennie.
* Azure-előfizetés.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>A Windows Virtual Desktop előzetes verziójú szolgáltatások biztosítása az Azure Active Directory engedélyekkel

Ha Windows virtuális asztalok esetében az Azure Active Directory-példánynak már kapott engedélyt, kihagyhatja ezt a szakaszt.

A Windows virtuális asztal szolgáltatás lehetővé teszi, hogy az engedélyek lekérdezése az Azure Active Directory rendszergazdai és végfelhasználói feladatokkal.

A szolgáltatás-engedélyek megadására:

1. Nyisson meg egy böngészőt, és csatlakozzon a [Windows virtuális asztal hozzájárulást kérő lap](https://rdweb.wvd.microsoft.com).
2. A **hozzájárulás beállítás** > **kiszolgálóalkalmazás**, adja meg az Azure Active Directory-bérlő neve vagy a címtár-azonosító, és válassza **küldés**.
        
   A Cloud Solution Provider rendelkező ügyfelek esetén a Azonosítóra az ügyfél a Microsoft ID a partnerportálról. Nagyvállalati ügyfeleink esetén az azonosító alatt található **Azure Active Directory** > **tulajdonságok** > **címtár-azonosító**.
3. Jelentkezzen be a Windows virtuális asztal hozzájárulást kérő lap egy globális rendszergazdai fiókkal. Például, ha a Contoso vállalattal, a fiók lehet admin@contoso.com vagy admin@contoso.onmicrosoft.com.  
4. Válassza ki az **Elfogadás** lehetőséget.
5. Várjon egy percet.
6. Lépjen vissza a [Windows virtuális asztal hozzájárulást kérő lap](https://rdweb.wvd.microsoft.com).
7. Lépjen a **hozzájárulás beállítás** > **ügyfélalkalmazás**, adja meg az Azure Active Directory-bérlő neve vagy a címtár-azonosító, és válassza **küldés**.
8. Jelentkezzen be globális rendszergazdaként, a Windows virtuális asztal hozzájárulást kérő lap, akkor fejeződött 3. lépésében.
9. Válassza ki az **Elfogadás** lehetőséget.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Az alkalmazás-szerepkör TenantCreator hozzárendelni egy felhasználóhoz, az Azure Active Directory-bérlőben

A TenantCreator alkalmazás-szerepkör hozzárendelése egy Azure Active Directory-felhasználó lehetővé teszi, hogy a felhasználó az Azure Active Directory-példányhoz társított Windows virtuális asztal bérlő létrehozása. A TenantCreator szerepkör hozzárendelése a globális rendszergazdai fiók használatával kell.

A TenantCreator alkalmazás-szerepkör hozzárendelése:

1. Nyisson meg egy böngészőt, és csatlakozzon a [az Azure portal](https://portal.azure.com) globális rendszergazdai fiókjával.
   
   Ha több Azure Active Directory-bérlő dolgozik, ajánlott eljárás az, nyisson meg egy privát böngésző-munkamenetet, és másolja és illessze be az URL-címeket a címsorba.
2. A keresési sávon az Azure Portalon keresse meg **vállalati alkalmazások** , és válassza ki a bejegyzést, amely megjelenik a **szolgáltatások** kategória.
3. Belül **vállalati alkalmazások**, keressen **Windows virtuális asztal**. Látni fogja, hogy a megadott beleegyezés kérése az előző szakaszban a két alkalmazás. Válassza ki a két alkalmazásokat, **Windows virtuális asztal**.
   ![A keresési eredmények között keresse meg a "Windows Virtual Desktop" a "Nagyvállalati alkalmazásokhoz." Ha képernyőképe A "Windows Virtual Desktop" nevű alkalmazás ki van emelve.](media/tenant-enterprise-app.png)
4. Válassza a **Felhasználók és csoportok** elemet. Láthatja, hogy a rendszergazda, aki hozzájárulás az alkalmazáshoz kapott már szerepel az a **alapértelmezett hozzáférési** hozzárendelt szerepkörrel. Ez a nem elegendő a virtuális asztali Windows-bérlő létrehozásához. Adja hozzá ezeket az utasításokat követve folytassa a **TenantCreator** egy felhasználói szerepkört.
   ![Képernyőkép: a felhasználók és csoportok hozzárendelve a "Windows Virtual Desktop" Vállalati alkalmazás kezeléséhez. A képernyőképen látható, csak egy hozzárendelésre, az "Alapértelmezett hozzáférés."](media/tenant-default-access.png)
5. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** panelen.
6. Keressen rá egy felhasználói fiókot, amely létrehozza a virtuális asztali Windows-bérlő. Az egyszerűség kedvéért ez a globális rendszergazdai fiók is lehet.

   ![Képernyőkép: kiválasztásával a felhasználó hozzáadása "TenantCreator."](media/tenant-assign-user.png)

   > [!NOTE]
   > Jelöljön ki egy felhasználó (vagy egy csoportot, amely tartalmazza a felhasználó), amelyek forrása az Azure Active Directory-példányból. Nem választható, a vendégfelhasználó (B2B) és a egy egyszerű szolgáltatást.

7. Válassza ki a felhasználói fiókot, válassza ki a **kiválasztása** gombra, és válassza ki **hozzárendelése**.
8. Az a **Windows virtuális asztal - felhasználók és csoportok** lapon, győződjön meg arról, hogy megjelenik-e az új bejegyzést a **TenantCreator** hoz létre a Windows virtuális asztal bérlői felhasználó rendelt szerepkör.
   ![Képernyőkép: a felhasználók és csoportok hozzárendelve a "Windows Virtual Desktop" Vállalati alkalmazás kezeléséhez. A képernyőfelvételen mostantól tartalmazza a "TenantCreator" szerepkörhöz rendelt felhasználó egy második bejegyzés.](media/tenant-tenant-creator-added.png)

A folytatás előtt meg a virtuális asztali Windows-bérlő létrehozásához kétféle információra van szükség:
- Az Azure Active Directory-Bérlőazonosító (vagy **címtár-azonosító**)
- Az Azure-előfizetés azonosítója

Az Azure Active Directory-Bérlőazonosító található (vagy **címtár-azonosító**):
1. Az ugyanazon az Azure portál munkamenet, keressen **Azure Active Directory** a keresősáv és válasszon a bejegyzés alatt megjelenő a **szolgáltatások** kategória.
   ![A keresési eredmények az "Azure Active Directory" az Azure Portal képernyőképe. A keresési eredmények a "Szolgáltatások" listában ki van emelve.](media/tenant-search-azure-active-directory.png)
2. Görgessen lefelé, amíg meg nem találja **tulajdonságok**, majd válassza ki azt.
3. Keressen **címtár-azonosító**, majd kattintson a vágólapra ikonra. Illessze be egy kényelmes helyen, ezzel újabb, mint a **AadTenantId** értéket.
   ![Az Azure Active Directory-tulajdonságok képernyőképe. Az egérrel a címtár-azonosító"másolja és illessze be a vágólapra ikon fölé helyezve.](media/tenant-directory-id.png)

Megkeresése az Azure-előfizetés azonosítója:
1. Az ugyanazon az Azure portál munkamenet, keressen **előfizetések** a keresősáv és válasszon a bejegyzés alatt megjelenő a **szolgáltatások** kategória.
   ![A keresési eredmények az "Azure Active Directory" az Azure Portal képernyőképe. A keresési eredmények a "Szolgáltatások" listában ki van emelve.](media/tenant-search-subscription.png)
2. Válassza ki a virtuális asztali Windows-szolgáltatás értesítést szeretne kapni a használni kívánt Azure-előfizetést.
3. Keressen **előfizetés-azonosító**, és ezután az érték fölé, amíg a vágólap ikon megjelenik. A vágólap ikonra, és illessze be egy kényelmes hely, használhatja azt újabb, mint a **AzureSubscriptionId** értéket.
   ![Az Azure-előfizetés tulajdonságainak képernyőképe. Az egérmutatót az "Előfizetés-azonosító" másolja és illessze be a vágólap ikon fölé helyezve.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Windows virtuális asztal előzetes bérlő létrehozása

Most, hogy jogosultságot kap a Windows virtuális asztal szolgáltatás Azure Active Directory lekérdezés, és rendelje hozzá a TenantCreator szerepkört egy felhasználói fiókhoz, egy Windows virtuális asztal bérlőt is létrehozhat.

Először [töltse le és importálja a modult a Windows virtuális asztal](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Jelentkezzen be a Windows virtuális asztal ezzel a parancsmaggal a TenantCreator felhasználói fiók használatával:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezt követően hozzon létre egy új Windows virtuális asztal bérlőhöz társított Azure Active Directory-bérlő:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Cserélje le a zárójeles értékek, a szervezet és a bérlői megfelelő értékeket. Új virtuális asztali Windows-bérlője számára választott névnek globálisan egyedinek kell lennie. Például tegyük fel, Ön a Windows virtuális asztal TenantCreator a Contoso szervezet számára. A következő parancsot kellene futtatnia parancsmag kellene kinéznie:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a bérlőn, szüksége lesz az Azure Active Directory egyszerű szolgáltatás létrehozása és a Windows virtuális asztal belül szerepkör hozzárendelése. Egyszerű szolgáltatás lehetővé teszi, hogy sikerült telepíteni a Windows virtuális asztal Azure Marketplace-en ajánlat gazdagép-készlet létrehozása. Gazdagép-készletek kapcsolatos további információkért folytassa az oktatóanyag során egy gazdagép-készlet létrehozása a Windows virtuális asztal.

> [!div class="nextstepaction"]
> [Szolgáltatásnevek létrehozása és szerepkörök hozzárendelése a PowerShell-lel](./create-service-principal-role-powershell.md)
