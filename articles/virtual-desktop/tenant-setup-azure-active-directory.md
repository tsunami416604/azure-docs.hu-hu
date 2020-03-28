---
title: Bérlő létrehozása a Windows virtuális asztalon – Azure
description: A Windows virtuális asztal bérlőinek beállítása az Azure Active Directoryban című témakört ismerteti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f9f7f709d31967e892900ccb25657a5963c0379
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370219"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Oktatóanyag: Bérlő létrehozása a Windows virtuális asztalon

A bérlő létrehozása a Windows virtuális asztalon az első lépés az asztali virtualizációs megoldás létrehozása felé. A bérlő egy vagy több gazdakészletből álló csoport. Minden állomáskészlet több munkamenet-állomásból áll, amelyek virtuális gépként futnak az Azure-ban, és regisztrálva vannak a Windows virtuális asztali szolgáltatásban. Minden állomáskészlet egy vagy több alkalmazáscsoportból is áll, amelyek távoli asztali és távoli alkalmazás-erőforrások közzétételére szolgálnak a felhasználók számára. A bérlővel gazdagépkészleteket hozhat létre, alkalmazáscsoportokat hozhat létre, felhasználókat rendelhet hozzá, és kapcsolatokat létesíthet a szolgáltatáson keresztül.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Active Directory-engedélyek megadása a Windows virtuális asztali szolgáltatásszámára.
> * Rendelje hozzá a TenantCreator alkalmazásszerepkört az Azure Active Directory-bérlő egyik felhasználójához.
> * Hozzon létre egy Windows virtuális asztal bérlőt.

## <a name="what-you-need-to-set-up-a-tenant"></a>Mire van szükség a bérlő beállításához?

A Windows virtuális asztal bérlői beállításának megkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi dolgokkal:

* Az [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) bérlői azonosítója a Windows virtuális asztali felhasználók számára.
* Az Azure Active Directory-bérlőn belüli globális rendszergazdai fiók.
   * Ez vonatkozik a felhőszolgáltató (CSP) azokra a szervezetekre is, amelyek windowsos virtuális asztal-bérlőt hoznak létre ügyfeleik számára. Ha egy csp-szervezet, képesnek kell lennie arra, hogy jelentkezzen be az ügyfél Azure Active Directory-példányának globális rendszergazdájaként.
   * A rendszergazdai fiókot az Azure Active Directory-bérlőből kell beszereznie, amelyben a Windows virtuális asztal bérlőjét próbálja létrehozni. Ez a folyamat nem támogatja az Azure Active Directory B2B (vendég) fiókok.
   * A rendszergazdai fióknak munkahelyi vagy iskolai fióknak kell lennie.
* Azure-előfizetés.

A bérlői azonosítóval, a globális rendszergazdai fiókkal és az Azure-előfizetéssel készen kell lennie, hogy az oktatóanyagban leírt folyamat megfelelően működjön.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Engedélyek megadása a Windows virtuális asztalhoz

Ha ehhez az Azure Active Directory-példányhoz már adott engedélyeket a Windows virtuális asztalhoz, hagyja ki ezt a szakaszt.

Engedélyek megadása a Windows virtuális asztali szolgáltatás lehetővé teszi, hogy lekérdezi az Azure Active Directory felügyeleti és végfelhasználói feladatok.

A szolgáltatási engedélyek megadása:

1. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai hozzájárulási folyamatot a [Windows Virtual Desktop server alkalmazással.](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)
   > [!NOTE]
   > Ha egy ügyfél kezelése, és meg kell adnia rendszergazdai jóváhagyást az ügyfél címtár, adja meg a következő URL-t a böngészőben, és cserélje {tenant} az ügyfél Azure AD tartománynevét. Ha például az ügyfél szervezete regisztrálta az Azure AD tartománynevét contoso.onmicrosoft.com, cserélje le {tenant} contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Jelentkezzen be a Windows virtuális asztal hozzájárulási lapjára globális rendszergazdai fiókkal. Ha például a Contoso szervezetnél volt, admin@contoso.com a admin@contoso.onmicrosoft.comfiók lehet vagy a.
3. Válassza ki az **Elfogadás** lehetőséget.
4. Várjon egy percet, hogy az Azure AD rögzíthesse a beleegyezést.
5. Nyisson meg egy böngészőt, és kezdje meg a rendszergazdai hozzájárulási folyamatot a [Windows Virtual Desktop ügyfélalkalmazással.](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)
   >[!NOTE]
   > Ha egy ügyfél kezelése, és meg kell adnia rendszergazdai jóváhagyást az ügyfél címtár, adja meg a következő URL-t a böngészőben, és cserélje {tenant} az ügyfél Azure AD tartománynevét. Ha például az ügyfél szervezete regisztrálta az Azure AD tartománynevét contoso.onmicrosoft.com, cserélje le {tenant} contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Jelentkezzen be a Windows virtuális asztal hozzájárulási lapjára globális rendszergazdaként, ahogy azt a 2.
7. Válassza ki az **Elfogadás** lehetőséget.

## <a name="assign-the-tenantcreator-application-role"></a>A TenantCreator alkalmazásszerepkör hozzárendelése

Egy Azure Active Directory-felhasználó a TenantCreator alkalmazásszerepkör lehetővé teszi, hogy a felhasználó hozzon létre egy Windows virtuális asztal bérlő az Azure Active Directory-példánytársított. A TenantCreator szerepkör hozzárendeléséhez a globális rendszergazdai fiókot kell használnia.

A TenantCreator alkalmazásszerepkör hozzárendelése:

1. Nyissa meg az [Azure Portalon](https://portal.azure.com) a TenantCreator alkalmazásszerepkör kezeléséhez. Keresse meg és válassza a **Vállalati alkalmazások lehetőséget.** Ha több Azure Active Directory-bérlővel dolgozik, ajánlott megnyitni egy privát böngészőmunkamenetet, és másolni és beilleszteni az URL-címeket a címsorba.

   ![Képernyőkép: nagyvállalati alkalmazások keresése az Azure Portalon](media/azure-portal-enterprise-applications.png)
2. A **Vállalati alkalmazások**on belül keresse meg a Windows virtuális **asztal**kifejezést . Az előző szakaszban megjelenik az a két alkalmazás, amelyhez hozzájárult. A két alkalmazás közül válassza a **Windows virtuális asztal lehetőséget.**
   
   ![Képernyőkép a "Windows virtuális asztal" kifejezés "Vállalati alkalmazások" alkalmazásban történő keresésekor a keresési eredményekről. A "Windows virtuális asztal" nevű alkalmazás ki van emelve.](media/tenant-enterprise-app.png)
3. Válassza a **Felhasználók és csoportok** elemet. Előfordulhat, hogy az alkalmazáshoz hozzájárult rendszergazda már szerepel az **alapértelmezett hozzáférési** szerepkörrel. Ez nem elegendő a Windows virtuális asztal bérlőjének létrehozásához. Kövesse ezeket az utasításokat a **TenantCreator** szerepkör felhasználóhoz való hozzáadásához.
   
   ![Képernyőkép a "Windows Virtuális asztal" vállalati alkalmazás kezeléséhez rendelt felhasználókról és csoportokról. A képernyőképen csak egy hozzárendelés látható, amely az "Alapértelmezett hozzáférés" beállításhoz tartozik.](media/tenant-default-access.png)
4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** lapon válassza a Felhasználók **és csoportok** lehetőséget.
5. Keressen egy felhasználói fiókot, amely létrehozza a Windows virtuális asztal bérlőjét. Az egyszerűség kedvéért ez lehet a globális rendszergazdai fiók.
   - Ha a Microsoft Identity Provider-t használja, például contosoadmin@live.com a contosoadmin@outlook.comvagya, előfordulhat, hogy nem tud bejelentkezni a Windows virtuális asztalra. Azt javasoljuk, hogy használjon admin@contoso.com admin@contoso.onmicrosoft.com tartományspecifikus fiókot, mint vagy helyette.

   ![Képernyőkép a "TenantCreator" néven hozzáadni kívánt felhasználó kiválasztásáról.](media/tenant-assign-user.png)
   > [!NOTE]
   > Ki kell választania egy felhasználót (vagy egy felhasználót tartalmazó csoportot), amely ebből az Azure Active Directory-példányból származik. Nem választhat vendég (B2B) felhasználót vagy egyszerű szolgáltatást.

6. Jelölje ki a felhasználói fiókot, válassza a **Kijelölés** gombot, majd a **Hozzárendelés parancsot.**
7. A **Windows virtuális asztal – Felhasználók és csoportok** lapon ellenőrizze, hogy megjelenik-e egy új bejegyzés, amelyhez a Windows virtuális asztal bérlőjét létrehozó felhasználóhoz rendelt **TenantCreator** szerepkör van rendelve.

   ![Képernyőkép a "Windows Virtuális asztal" vállalati alkalmazás kezeléséhez rendelt felhasználókról és csoportokról. A képernyőkép most már tartalmaz egy második bejegyzést a "TenantCreator" szerepkörhöz rendelt felhasználó.](media/tenant-tenant-creator-added.png)

A Windows virtuális asztal bérlőjének létrehozásához két információra van szüksége:

   - Az Azure Active Directory bérlői azonosítója (vagy **címtárazonosítója)**
   - Az Azure-előfizetés azonosítója

Az Azure Active Directory-bérlői azonosító (vagy **címtárazonosító) megkeresése:**
1. Ugyanebben az [Azure Portal-munkamenetben](https://portal.azure.com) keresse meg és válassza az **Azure Active Directoryt.**

   ![Az "Azure Active Directory" keresési eredményeinek képernyőképe az Azure Portalon. A "Szolgáltatások" alatt a keresési eredmény ki van emelve.](media/tenant-search-azure-active-directory.png)
2. Görgessen lefelé, amíg meg nem találja **a Tulajdonságok**elemet, majd jelölje ki.
3. Keresse meg a **Címtárazonosító**t, majd kattintson a vágólap ikonjára. Illessze be egy praktikus helyre, így később **aAdTenantId** értékként használhatja.

   ![Az Azure Active Directory tulajdonságainak képernyőképe. Az egér a "Címtárazonosító" vágólapikonja fölé kerül a másoláshoz és beillesztéshez.](media/tenant-directory-id.png)

Az Azure-előfizetés-azonosító megkeresése:
1. Ugyanebben az [Azure Portal-munkamenetben](https://portal.azure.com) keressen rá az Előfizetések elemre, és válassza az **Előfizetések**lehetőséget.
   
   ![Az "Azure Active Directory" keresési eredményeinek képernyőképe az Azure Portalon. A "Szolgáltatások" alatt a keresési eredmény ki van emelve.](media/tenant-search-subscription.png)
2. Válassza ki a Windows virtuális asztali szolgáltatásértesítéseinek fogadásához használni kívánt Azure-előfizetést.
3. Keresse meg **az Előfizetés-azonosító**t, majd vigye az egérmutatót az érték fölé, amíg meg nem jelenik a vágólap ikonja. Válassza ki a vágólap ikont, és illessze be egy praktikus helyre, hogy később **AzureSubscriptionId-értékként használhassa.**
   
   ![Képernyőkép az Azure-előfizetés tulajdonságairól. Az egér az "Előfizetés-azonosító" vágólapikonja fölé kerül a másoláshoz és beillesztéshez.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows virtuális asztal bérlőjének létrehozása

Most, hogy megadta a Windows virtuális asztal szolgáltatás engedélyeket az Azure Active Directory lekérdezéséhez, és a TenantCreator szerepkört egy felhasználói fiókhoz rendelte, létrehozhat egy Windows virtuális asztal bérlőt.

Először [töltse le és importálja a Windows virtuális asztal modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg.

Jelentkezzen be a Windows virtuális asztalra a TenantCreator felhasználói fiók használatával ezzel a parancsmaggal:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezt követően hozzon létre egy új Windows virtuális asztal bérlőt az Azure Active Directory-bérlőhöz társítva:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Cserélje le a zárójelbe tett értékeket a szervezet és a bérlő szempontjából releváns értékekre. Az új Windows virtuális asztal bérlőhöz választott névnek globálisan egyedinek kell lennie. Tegyük fel például, hogy ön a Contoso szervezet Windows virtuális asztalának bérlője. A parancsmag, amelyet futtatna, így nézne ki:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Érdemes rendszergazdai hozzáférést rendelni egy második felhasználóhoz arra az esetre, ha esetleg kizárva találná magát a fiókjából, vagy szabadságra megy, és szüksége van valakire, aki a távollétében bérlői adminisztrátorként jár el. Rendszergazdai hozzáférés hozzárendelése egy második felhasználóhoz, `<TenantName>` futtassa a következő parancsmasa, és `<Upn>` cserélje le a bérlő nevét és a második felhasználó upn.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a bérlőt, létre kell hoznia egy egyszerű szolgáltatást az Azure Active Directoryban, és hozzá kell rendelnie egy szerepkört a Windows virtuális asztalon belül. Az egyszerű szolgáltatás lehetővé teszi, hogy sikeresen telepítse a Windows Virtual Desktop Azure Marketplace-t egy gazdakészlet létrehozásához. Ha többet szeretne megtudni a gazdakészletekről, folytassa a gazdakészlet létrehozásáról a Windows virtuális asztalon című oktatóanyaggal.

> [!div class="nextstepaction"]
> [Szolgáltatásnevek létrehozása és szerepkörök hozzárendelése a PowerShell-lel](./create-service-principal-role-powershell.md)
