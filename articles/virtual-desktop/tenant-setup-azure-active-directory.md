---
title: Bérlő létrehozása a Windows rendszerű virtuális asztal előzetes verziójában – Azure
description: Leírja, hogyan kell beállítani a Windows rendszerű virtuális asztali előnézeti bérlőket a Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 062b815315d7bcdd5d55a86c2447a0b21295e8b6
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014096"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Oktatóanyag: Bérlő létrehozása a Windows rendszerű virtuális asztali verzióban – előzetes verzió

A bérlők létrehozása a Windows Virtual Desktop Preview-ban az első lépés az asztali virtualizálási megoldás felépítése felé. A bérlő egy vagy több gazdagép egy csoportja. Mindegyik gazdagép több munkamenet-gazdagépből áll, és virtuális gépekként fut az Azure-ban, és regisztrálva van a Windows rendszerű virtuális asztali szolgáltatásban. Az egyes címkészlet egy vagy több olyan alkalmazáscsoportből is áll, amelyek a távoli asztal és a távoli alkalmazás erőforrásainak közzétételére szolgálnak a felhasználók számára. A Bérlővel létrehozhatók a gazdagépek, létrehozhatók alkalmazások, felhasználók rendelhetők, és a szolgáltatáson keresztül is csatlakozhatnak.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Active Directory engedélyek megadása a Windows rendszerű virtuális asztali szolgáltatásnak.
> * Rendelje hozzá a TenantCreator-alkalmazás szerepkört a Azure Active Directory bérlő egyik felhasználója számára.
> * Hozzon létre egy Windows rendszerű virtuális asztali bérlőt.

A következőképpen kell beállítania a Windows rendszerű virtuális asztali bérlőt:

* A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) bérlő azonosítója a Windows rendszerű virtuális asztali felhasználók számára.
* Globális rendszergazdai fiók a Azure Active Directory bérlőn belül.
   * Ez olyan felhőalapú megoldás-szolgáltatói (CSP) szervezetekre is vonatkozik, amelyek egy Windows rendszerű virtuális asztali bérlőt hoznak létre ügyfeleiknek. Ha egy CSP-szervezetben van, akkor az ügyfél Azure Active Directory példányának globális rendszergazdájaként kell tudnia bejelentkeznie.
   * A rendszergazdai fióknak a Azure Active Directory bérlőtől kell származnia, amelyben létre kívánja hozni a Windows rendszerű virtuális asztali bérlőt. Ez a folyamat nem támogatja Azure Active Directory B2B-(vendég-) fiókokat.
   * A rendszergazdai fióknak munkahelyi vagy iskolai fióknak kell lennie.
* Azure-előfizetés.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Azure Active Directory engedélyek megadása a Windows rendszerű virtuális asztali előnézet szolgáltatáshoz

Ha már engedélyezte a Windows rendszerű virtuális asztal számára a Azure Active Directory példányra vonatkozó engedélyeket, ugorja át ezt a szakaszt.

A Windows rendszerű virtuális asztali szolgáltatás engedélyeinek megadása lehetővé teszi, hogy lekérdezze Azure Active Directory felügyeleti és végfelhasználói feladatokhoz.

A szolgáltatás engedélyeinek megadása:

1. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai jogosultságokat a [Windows rendszerű virtuális asztali kiszolgáló alkalmazásban](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Ha felügyel egy ügyfelet, és rendszergazdai jóváhagyást kell adnia az ügyfél címtárához, adja meg a következő URL-címet a böngészőben, és cserélje le a {bérlő} értéket az ügyfél Azure AD-tartománynevére. Ha például az ügyfél szervezete regisztrálta a contoso.onmicrosoft.com Azure AD-tartománynevet, cserélje le a (z) {bérlő} nevet a contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Jelentkezzen be a Windows rendszerű virtuális asztal engedélyezési lapjára egy globális rendszergazdai fiókkal. Ha például a contoso szervezete volt, a fiókja lehet admin@contoso.com vagy. admin@contoso.onmicrosoft.com  
3. Válassza ki az **Elfogadás** lehetőséget.
4. Várjon egy percet, hogy az Azure AD jegyezze fel a beleegyező engedélyt.
5. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai jogosultságokat a [Windows rendszerű virtuális asztali ügyfélalkalmazás](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)számára.
   >[!NOTE]
   > Ha felügyel egy ügyfelet, és rendszergazdai jóváhagyást kell adnia az ügyfél címtárához, adja meg a következő URL-címet a böngészőben, és cserélje le a {bérlő} értéket az ügyfél Azure AD-tartománynevére. Ha például az ügyfél szervezete regisztrálta a contoso.onmicrosoft.com Azure AD-tartománynevet, cserélje le a (z) {bérlő} nevet a contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Jelentkezzen be a Windows rendszerű virtuális asztal engedélyezési lapjára globális rendszergazdaként, ahogy azt a 2. lépésben elvégezte.
7. Válassza ki az **Elfogadás** lehetőséget.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>A TenantCreator-alkalmazás szerepkörének kiosztása a Azure Active Directory bérlő egyik felhasználójának

Azure Active Directory felhasználó kiosztása a TenantCreator alkalmazási szerepkör lehetővé teszi, hogy a felhasználó létrehoz egy Windows rendszerű virtuális asztali bérlőt, amely társítva van a Azure Active Directory példányhoz. A TenantCreator szerepkör hozzárendeléséhez globális rendszergazdai fiókot kell használnia.

A TenantCreator-alkalmazás szerepkörének hozzárendeléséhez:

1. Nyisson meg egy böngészőt, és [](https://portal.azure.com) kapcsolódjon a Azure Portalhoz a globális rendszergazdai fiókkal.
   
   Ha több Azure Active Directory Bérlővel dolgozik, ajánlott megnyitnia egy privát böngésző-munkamenetet, és az URL-címeket a címsorba másolhatja és beillesztheti.
2. A Azure Portal található keresési sávban keresse meg a **vállalati alkalmazások** kifejezést, és válassza ki a **szolgáltatások** kategória alatt megjelenő bejegyzést.
3. A **vállalati alkalmazásokban**keresse meg a **Windows rendszerű virtuális asztal**kifejezést. Ekkor megjelenik a két, az előző szakaszban megadott beleegyezett alkalmazás. A két alkalmazás közül válassza a **Windows virtuális asztal**lehetőséget.
   ![Képernyőkép a keresési eredményekről, amikor a "Windowsos virtuális asztal" kifejezést keres a "vállalati alkalmazások" kifejezésben. A "Windows virtuális asztal" nevű alkalmazás ki van emelve.](media/tenant-enterprise-app.png)
4. Válassza a **Felhasználók és csoportok** elemet. Láthatja, hogy az alkalmazáshoz beleegyezett jogosultsággal rendelkező rendszergazda már szerepel a hozzárendelt **alapértelmezett hozzáférési** szerepkörben. Ez nem elég a Windows rendszerű virtuális asztali bérlő létrehozásához. Kövesse az alábbi utasításokat a **TenantCreator** szerepkör felhasználóhoz való hozzáadásához.
   ![Képernyőkép a "Windows Virtual Desktop" vállalati alkalmazás kezeléséhez hozzárendelt felhasználókról és csoportokról. A képernyőképen csak egy hozzárendelés látható, amely az "alapértelmezett hozzáférés" értékre mutat.](media/tenant-default-access.png)
5. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.
6. Keressen egy olyan felhasználói fiókot, amely létrehozza a Windows rendszerű virtuális asztali bérlőt. Az egyszerűség kedvéért ez lehet a globális rendszergazdai fiók.

   ![Képernyőkép a "TenantCreator" felvenni kívánt felhasználó kiválasztásáról.](media/tenant-assign-user.png)

   > [!NOTE]
   > Ki kell választania egy felhasználót (vagy egy csoportot, amely tartalmazza a felhasználót) Ebből az Azure Active Directory-példányból származik. Nem választhat vendég (B2B) felhasználót vagy egyszerű szolgáltatásnevet.

7. Válassza ki a felhasználói fiókot, kattintson a **kiválasztás** gombra, majd válassza a **hozzárendelés**lehetőséget.
8. A **Windows rendszerű virtuális asztal – felhasználók és csoportok** lapon ellenőrizze, hogy megjelenik-e egy új bejegyzés a **TenantCreator** szerepkörrel, amely ahhoz a felhasználóhoz van rendelve, aki létrehozza a Windows rendszerű virtuális asztali bérlőt.
   ![Képernyőkép a "Windows Virtual Desktop" vállalati alkalmazás kezeléséhez hozzárendelt felhasználókról és csoportokról. A képernyőkép most már tartalmaz egy, a "TenantCreator" szerepkörhöz rendelt felhasználó második bejegyzését.](media/tenant-tenant-creator-added.png)

Mielőtt továbblépne a Windows rendszerű virtuális asztali bérlő létrehozásához, két információra van szüksége:
- Az Azure Active Directory bérlői AZONOSÍTÓját (vagy a **CÍMTÁR azonosítóját**)
- Az Azure-előfizetés azonosítója

A Azure Active Directory bérlői AZONOSÍTÓjának (vagy a **könyvtár azonosítójának**) megkeresése:
1. Ugyanebben a Azure Portal-munkamenetben keresse meg **Azure Active Directory** a keresősáv alatt, és válassza ki a **szolgáltatások** kategória alatt megjelenő bejegyzést.
   ![A Azure Portal "Azure Active Directory" keresési eredményeinek képernyőképe. A "szolgáltatások" alatt a keresési eredmények ki vannak emelve.](media/tenant-search-azure-active-directory.png)
2. Görgessen lefelé, amíg meg nem találja a **tulajdonságokat**, majd válassza ki.
3. Keresse meg a **CÍMTÁR azonosítóját**, majd válassza a vágólap ikont. Illessze be egy praktikus helyre, hogy később a **AadTenantId** értékeként is használhassa.
   ![Képernyőkép a Azure Active Directory tulajdonságairól. Az egérmutatót a vágólap ikonja fölé helyezi a másoláshoz és beillesztéshez.](media/tenant-directory-id.png)

Az Azure-előfizetés AZONOSÍTÓjának megkeresése:
1. Ugyanebben a Azure Portal-munkamenetben keresse meg az előfizetéseket a keresősáv alatt, és válassza ki a **szolgáltatások** kategória alatt megjelenő bejegyzést.
   ![A Azure Portal "Azure Active Directory" keresési eredményeinek képernyőképe. A "szolgáltatások" alatt a keresési eredmények ki vannak emelve.](media/tenant-search-subscription.png)
2. Válassza ki azt az Azure-előfizetést, amelyet a Windows rendszerű virtuális asztali szolgáltatás értesítéseinek fogadásához használni kíván.
3. Keresse meg az **előfizetés azonosítóját**, majd vigye a kurzort az érték fölé, amíg meg nem jelenik a vágólap ikonja. Válassza a vágólap ikont, és illessze be egy praktikus helyre, hogy később a **AzureSubscriptionId** értékeként is használhassa.
   ![Képernyőkép az Azure-előfizetés tulajdonságairól. Az egérmutatót a vágólap ikon fölé helyezi a másoláshoz és beillesztéshez az "előfizetés azonosítója" elemnél.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Windows rendszerű virtuális asztali előnézet bérlő létrehozása

Most, hogy megadta a Windows rendszerű virtuális asztali szolgáltatásnak a Azure Active Directory lekérdezésére vonatkozó engedélyeit, és hozzárendelte a TenantCreator szerepkört egy felhasználói fiókhoz, létrehozhat egy Windows rendszerű virtuális asztali bérlőt.

Először [töltse le és importálja a Windows rendszerű virtuális asztali modult](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a PowerShell-munkamenetben való használathoz, ha még nem tette meg.

Jelentkezzen be a Windows rendszerű virtuális asztalra a TenantCreator felhasználói fiók használatával a következő parancsmaggal:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután hozzon létre egy új, a Azure Active Directory bérlőhöz társított Windowsos virtuális asztali bérlőt:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Cserélje le a zárójelben lévő értékeket a szervezete és a bérlője szempontjából releváns értékekre. Az új Windowsos virtuális asztali bérlőhöz választott névnek globálisan egyedinek kell lennie. Tegyük fel például, hogy a contoso szervezet Windows rendszerű virtuális asztali TenantCreator van. A futtatni kívánt parancsmag a következőképpen fog kinézni:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>További lépések

A bérlő létrehozása után létre kell hoznia egy egyszerű szolgáltatásnevet a Azure Active Directoryban, és hozzá kell rendelnie egy szerepkört a Windows rendszerű virtuális asztalon belül. Az egyszerű szolgáltatásnév lehetővé teszi a Windows rendszerű virtuális asztali Azure Marketplace-ajánlat sikeres üzembe helyezését egy gazda készlet létrehozásához. Ha többet szeretne megtudni a gazdagép-készletekről, folytassa a gazdagépek Windows rendszerű virtuális asztali gépen való létrehozásának oktatóanyagával.

> [!div class="nextstepaction"]
> [Szolgáltatásnevek létrehozása és szerepkörök hozzárendelése a PowerShell-lel](./create-service-principal-role-powershell.md)
