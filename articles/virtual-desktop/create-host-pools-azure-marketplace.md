---
title: Windows virtuális asztali gazdagépkészlete Azure Marketplace – Azure
description: Hogyan hozhat létre egy Windows virtuális asztal gazdagép készlet az Azure Marketplace használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238621"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Oktatóanyag: Gazdagépkészlet létrehozása az Azure Piactér használatával

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre gazdakészletet egy Windows virtuális asztal bérlőn belül egy Microsoft Azure Marketplace-ajánlat használatával.

A gazdakészletek egy vagy több azonos virtuális gép gyűjteménye a Windows virtuális asztal bérlői környezetében. Minden gazdagépkészlet tartalmazhat egy alkalmazáscsoportot, amelyet a felhasználók ugyanúgy használhatnak, mint a fizikai asztalon.

Az oktatóanyag feladatai a következők:

> [!div class="checklist"]
>
> * Gazdagépkészlet létrehozása a Windows virtuális asztalon.
> * Hozzon létre egy erőforráscsoportot virtuális gépekkel egy Azure-előfizetésben.
> * Csatlakozzon a virtuális gépekhez az Active Directory tartományhoz.
> * Regisztrálja a virtuális gépeket a Windows virtuális asztallal.

## <a name="prerequisites"></a>Előfeltételek

* Bérlő a virtuális asztalon. Egy korábbi [oktatóanyag](tenant-setup-azure-active-directory.md) létrehoz egy bérlőt.
* [Windows Virtual Desktop PowerShell modul](/powershell/windows-virtual-desktop/overview/).

Miután rendelkezik ezzel a modullal, futtassa a következő parancsmast a fiókba való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Futtassa az Azure Marketplace-ajánlatot egy új gazdagépkészlet kiépítéséhez

Az Azure Marketplace-ajánlat futtatása új gazdagépkészlet kiépítéséhez:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Írja be a **Windows virtuális asztal kifejezést** a Piactér keresési ablakába.
1. Válassza a **Windows virtuális asztal – Állomáskészlet kiépítése**lehetőséget, majd a **Létrehozás**lehetőséget.

Ezután kövesse a következő szakasz utasításait a megfelelő lapok adatainak megadásához.

### <a name="basics"></a>Alapvető beállítások

Az Alapok lapon a **következőket** kell tennie:

1. **Válasszon egy előfizetést**.
1. Az **Erőforráscsoport csoportban**válassza **az Új létrehozása** lehetőséget, és adja meg az új erőforráscsoport nevét.
1. Válasszon egy **régiót**.
1. Adja meg a windowsos virtuális asztal bérlőn belül egyedi gazdagépkészlet nevét.
1. Válassza **az Asztal típust**. Ha a Személyes lehetőséget **választja,** minden, a gazdakészlethez csatlakozó felhasználó véglegesen hozzá van rendelve egy virtuális géphez.
1. Adja meg azon felhasználókat, akik bejelentkezhetnek a Windows virtuális asztali ügyfelekbe, és hozzáférhetnek az asztalhoz. Vesszővel tagolt lista használata. Ha például hozzá szeretne `user1@contoso.com` `user2@contoso.com` rendelni és el szeretne érni, írja be a*`user1@contoso.com,user2@contoso.com`*
1. A **Szolgáltatás metaadat-helyén**válassza ki ugyanazt a helyet, amely az Active Directory-kiszolgálóhoz való kapcsolódással rendelkezik.

   >[!IMPORTANT]
   >Ha egy tiszta Azure Active Directory tartományi szolgáltatások (Azure AD DS) és az Azure Active Directory (Azure AD) megoldás, győződjön meg arról, hogy üzembe helyezi a gazdakészlet ugyanabban a régióban, mint az Azure AD DS a tartomány-csatlakozási és hitelesítő adatok hibáinak elkerülése érdekében.

1. Válassza a **Tovább lehetőséget: Virtuális gépek konfigurálása**.

### <a name="configure-virtual-machines"></a>Virtuális gépek konfigurálása

A **Virtuális gépek konfigurálása** lapon:

1. Fogadja el az alapértelmezett értékeket, vagy testre szabhatja a virtuális gépek számát és méretét.

    >[!NOTE]
    >Ha a keresett virtuálisgép-méret nem jelenik meg a méretválasztóban, annak az az oka, hogy még nem vettük volna be az Azure Marketplace-eszközbe. A méret igényléséhez hozzon létre egy kérelmet, vagy szavazzon egy meglévő kérést a [Windows Virtual Desktop UserVoice fórumon.](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)

1. Adjon meg egy előtagot a virtuális gépek nevéhez. Ha például *előtagot*ad meg, a virtuális gépek neve **0 előtag**, **előtag-1**és így tovább lesz.
1. Válassza a **Tovább: Virtuálisgép beállításai**lehetőséget.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

A **Virtuálisgép beállításai** lapon:

1. **Képforrás**esetén válassza ki a forrást, és adja meg a megfelelő információkat a kereséshez és tárolásához. A blobstorage, a felügyelt lemezkép és a galéria beállításai eltérőek lehetnek.

   Ha úgy dönt, hogy nem használja a felügyelt lemezeket, jelölje ki a *.vhd* fájlt tartalmazó tárfiókot.
1. Adja meg az egyszerű felhasználó nevét és jelszavát. Ennek a fióknak kell lennie annak a tartományi fióknak, amely csatlakozik a virtuális gépekhez az Active Directory-tartományhoz. Ugyanez a felhasználónév és jelszó helyi fiókként lesz létrehozva a virtuális gépeken. Ezeket a helyi fiókokat később is alaphelyzetbe állíthatja.

   >[!NOTE]
   > Ha a virtuális gépeket egy Azure AD DS-környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója az [AAD DC rendszergazdák csoport](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja.
   >
   > A fióknak az Azure AD DS felügyelt tartományának vagy az Azure AD-bérlőnek is része kell lennie. Az Azure AD-bérlőhöz társított külső könyvtárakból származó fiókok nem hitelesíthetők megfelelően a tartományhoz való csatlakozás folyamata során.

1. Válassza ki azt a **virtuális hálózatot,** amely kapcsolatban áll az Active Directory-kiszolgálóval, majd válasszon egy alhálózatot a virtuális gépek üzemeltetéséhez.
1. **Válassza a Tovább lehetőséget: A Windows virtuális asztal adatai**.

### <a name="windows-virtual-desktop-tenant-information"></a>A Windows virtuális asztal bérlői adatai

A **Windows virtuális asztal bérlői adatai** lapon:

1. A **Windows virtuális asztal bérlői csoport nevéhez**adja meg a bérlői csoportot tartalmazó bérlői csoport nevét. Hagyja, hogy az alapértelmezett, kivéve, ha adott bérlői csoport nevét.
1. A **Windows virtuális asztal bérlői neve**esetén adja meg annak a bérlőnek a nevét, amelynek létrehozza ezt a gazdakészletet.
1. Adja meg, hogy milyen típusú hitelesítő adatokat kíván használni a Windows virtuális asztal bérlői biztonsági szolgáltatások tulajdonosaként történő hitelesítéshez. Adja meg az egyszerű felhasználónév vagy az egyszerű szolgáltatás és a jelszót.

   Ha befejezte a [Szolgáltatásegyszerűk létrehozása és szerepkör-hozzárendelések a PowerShell-oktatóanyaggal,](./create-service-principal-role-powershell.md)válassza **az egyszerű szolgáltatás**lehetőséget.

1. Az **egyszerű szolgáltatás**, az Azure **AD-bérlői azonosító,** adja meg a bérlői rendszergazdai fiókot az Azure AD-példány, amely tartalmazza a szolgáltatás egyszerű. Csak a jelszóhitelesítő adatokkal rendelkező szolgáltatásnévi tagok támogatottak.
1. Válassza a **Tovább lehetőséget: Véleményezés + létrehozás**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>A telepítés befejezése és a virtuális gép létrehozása

A **Véleményezés és létrehozás párbeszédpanelen**tekintse át a beállítási információkat. Ha meg kell változtatni valamit, menj vissza, és a változtatásokat. Ha készen áll, válassza a **Létrehozás** lehetőséget a gazdakészlet üzembe helyezéséhez.

Attól függően, hogy hány virtuális gépet hoz létre, ez a folyamat 30 percet vagy többet is igénybe vehet.

>[!IMPORTANT]
> A Windows virtuális asztali környezet azure-beli védelméhez azt javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows virtuális asztal nem igényel nyitott 3389-es bejövő portot a felhasználók számára a gazdakészlet virtuális gépei eléréséhez.
>
> Ha hibaelhárítási célból meg kell nyitnia a 3389-es portot, javasoljuk, hogy just-in-time hozzáférést használjon. További információ: [A felügyeleti portok védelme az időben](../security-center/security-center-just-in-time.md)elérhető hozzáféréssel című témakörben talál.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Nem kötelező) További felhasználók hozzárendelése az asztali alkalmazáscsoporthoz

Miután az Azure Marketplace befejezte a készlet létrehozását, további felhasználókat rendelhet az asztali alkalmazáscsoporthoz. Ha nem szeretne további adhangot, hagyja ki ezt a szakaszt.

Felhasználók hozzárendelése az asztali alkalmazáscsoporthoz:

1. Indítson el egy PowerShell-ablakot.

1. A Windows Virtual Desktop környezetbe való bejelentkezéshez futtassa a következő parancsot:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Felhasználók hozzáadása az asztali alkalmazáscsoporthoz a következő paranccsal:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   A felhasználó upn egyeznie kell a felhasználó identitását az *user1@contoso.com*Azure AD,például. Ha több felhasználót szeretne hozzáadni, futtassa a parancsot minden felhasználóhoz.

Az asztali alkalmazáscsoportba hozzáadott felhasználók bejelentkezhetnek a Windows virtuális asztalra a támogatott Távoli asztali ügyfelekkel, és megnézhetik a munkamenet asztalának erőforrását.

A jelenlegi támogatott ügyfelek:

* [Távoli asztali ügyfél Windows 7 és Windows 10 rendszerhez](connect-windows-7-and-10.md)
* [Windows virtuális asztali webes ügyfél](connect-web.md)

## <a name="next-steps"></a>További lépések

Készített egy gazdakészletet, és felhasználókat rendelt az asztal eléréséhez. A gazdakészletet feltöltheti RemoteApp-programokkal. Ha többet szeretne tudni arról, hogyan kezelheti az alkalmazásokat a Windows Virtuális asztalon, olvassa el az oktatóanyagot:

> [!div class="nextstepaction"]
> [Alkalmazáscsoportok kezelése oktatóanyag](./manage-app-groups.md)
