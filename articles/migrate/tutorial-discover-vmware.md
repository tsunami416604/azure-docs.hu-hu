---
title: VMware virtuális gépek felderítése Azure Migrate Server Assessmenttel
description: Ismerje meg, hogyan derítheti fel a helyszíni VMware virtuális gépeket a Azure Migrate Server Assessment Tool eszközzel
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 0e06d82c30743a4084cfc5ff856b4a9c8d548146
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566938"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Oktatóanyag: VMware virtuális gépek felderítése kiszolgáló értékelésével

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket.

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a helyszíni VMware virtuális gépeket (VM) a Azure Migrate: Server Assessment Tool eszközzel, egy egyszerű Azure Migrate berendezés használatával. A készüléket VMware virtuális gépként helyezheti üzembe, így folyamatosan felderítheti a virtuális gépeket és a teljesítményük metaadatait, a virtuális gépeken futó alkalmazásokat és a virtuálisgép-függőségeket.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * Készítse elő a VMware-környezetet a felderítéshez.
> * Azure Migrate-projekt létrehozása.
> * Állítsa be az Azure Migrate készüléket.
> * A folyamatos felderítés elindítása.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.


**Követelmény** | **Részletek**
--- | ---
**vCenter Server/ESXi-gazdagép** | A 5,5, 6,0, 6,5 vagy 6,7 verziót futtató vCenter Serverra van szüksége.<br/><br/> A virtuális gépeket egy 5,5-es vagy újabb verziót futtató ESXi-gazdagépen kell üzemeltetni.<br/><br/> A vCenter Server engedélyezze a bejövő kapcsolatokat a 443-as TCP-porton, hogy a készülék képes legyen a konfiguráció és a teljesítmény metaadatainak gyűjtésére.<br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter Server egy másik portot figyel, akkor módosíthatja a portot, amikor megadja a vCenter Server részleteit a készülék Configuration Managerben.<br/><br/> A virtuális gépeket üzemeltető ESXi-kiszolgálón győződjön meg arról, hogy a 443-es TCP-porton engedélyezve van-e a bejövő hozzáférés a virtuális gépekre és a virtuálisgép-függőségekre telepített alkalmazások felderítéséhez.
**Berendezés** | vCenter Server erőforrásokra van szükség a virtuális gép lefoglalásához a Azure Migrate berendezéshez:<br/><br/> -32 GB RAM, 8 vCPU és körülbelül 80 GB lemezes tárterület.<br/><br/> -Külső virtuális kapcsoló és internet-hozzáférés a készülék virtuális gépén közvetlenül vagy proxyn keresztül.
**Virtuális gépek** | A konfigurációs és a teljesítménybeli metaadatok felderítéséhez, valamint a virtuális gépekre telepített alkalmazások felderítéséhez a Windows és a Linux operációs rendszer összes verziója támogatott. <br/><br/> [Tekintse meg az ügynök](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) nélküli függőségek elemzéséhez támogatott operációsrendszer-verziókat.<br/><br/> A telepített alkalmazások és virtuálisgép-függőségek felderítéséhez telepíteni kell a VMware-eszközöket (a 10.2.0 később), és a virtuális gépeken és a Windows rendszerű virtuális gépeken telepítve kell lenniük a PowerShell 2,0-es vagy újabb verziójával.


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek az Azure-előfizetésben
- Engedélyek a Azure Active Directory-(HRE-) alkalmazások regisztrálásához
- Tulajdonos vagy közreműködő, valamint felhasználói hozzáférés rendszergazdai engedélyei az Azure-előfizetésben az ügynök nélküli VMware-áttelepítés során használt Key Vault létrehozásához

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-vmware/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-vmware/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-vmware/assign-role.png)

1. A készülék regisztrálásához az Azure-fióknak rendelkeznie kell a **HRE-alkalmazások regisztrálásához szükséges engedélyekkel.**
1. A Azure Portal területen navigáljon a  >  **felhasználók**  >  **felhasználói beállításainak** Azure Active Directory.
1. A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-vmware/register-apps.png)

9. Ha a "Alkalmazásregisztrációk" beállítások "nem" értékre van állítva, kérje meg a bérlőt/globális rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás regisztrálását. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>A VMware előkészítése

VCenter Server esetén győződjön meg arról, hogy a fiókja rendelkezik engedéllyel a virtuális gép létrehozásához PETESEJT-fájl használatával. Erre akkor van szükség, amikor a Azure Migrate készüléket VMware virtuális gépként telepíti, és egy PETESEJT-fájlt használ.

A kiszolgáló értékeléséhez vCenter Server írásvédett fiókra van szükség a VMware virtuális gépek felderítéséhez és értékeléséhez. Ha a telepített alkalmazásokat és a virtuális gépek függőségeit is szeretné felderíteni, a fióknak **Virtual Machines > vendég műveleteihez** engedélyezett jogosultságokkal kell rendelkeznie.

### <a name="create-an-account-to-access-vcenter"></a>Fiók létrehozása a vCenter való hozzáféréshez

A vSphere webes ügyfélprogramban az alábbiak szerint állítson be egy fiókot:

1. Rendszergazdai jogosultságokkal rendelkező fiók használata a vSphere Web Client > válassza az **Adminisztráció** lehetőséget.
2. **Hozzáférés**, válassza az **SSO-felhasználók és-csoportok** lehetőséget.
3. A **felhasználók** területen adjon hozzá egy új felhasználót.
4. Az **új felhasználó** mezőbe írja be a fiók adatait. Ezután kattintson az **OK** gombra.
5. A **globális engedélyek** területen válassza ki a felhasználói fiókot, és rendelje hozzá a **csak olvasási** szerepkört a fiókhoz. Ezután kattintson az **OK** gombra.
6. Ha a telepített alkalmazásokat és a virtuális gépek függőségeit is szeretné felderíteni, lépjen a **szerepkörök** > válassza ki a **csak olvasási** szerepkört, és a **jogosultságok** területen válassza a **vendég műveletek** lehetőséget. A jogosultságokat a vCenter Server alatt lévő összes objektumra propagálhatja a "propagálás gyermekeknek" jelölőnégyzet bejelölésével.
 
    ![Jelölőnégyzet a vendég műveleteknek a csak olvasási szerepkörön való engedélyezéséhez](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Hozzon létre egy fiókot a virtuális gépek eléréséhez

A telepített alkalmazások és a virtuálisgép-függőségek felderítéséhez szükség van egy olyan felhasználói fiókra, amely rendelkezik a szükséges jogosultságokkal a virtuális gépeken. A felhasználói fiókot megadhatja a készülék Configuration Managerben. A készülék nem telepít ügynököt a virtuális gépeken.

1. Windows rendszerű virtuális gépek esetén hozzon létre egy fiókot (helyi vagy tartományi) rendszergazdai engedélyekkel a virtuális gépeken.
2. Linux rendszerű virtuális gépek esetén hozzon létre egy rendszergazdai jogosultságokkal rendelkező fiókot. Másik lehetőségként létrehozhat egy fiókot ezekkel az engedélyekkel a/bin/netstat-és/bin/ls-fájlokon: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.

> [!NOTE]
> Jelenleg Azure Migrate egy felhasználói fiókot támogat a Windows rendszerű virtuális gépekhez és egy felhasználói fiókot a Linux rendszerű virtuális gépekhez, amelyek a készüléken elérhetők a telepített alkalmazások és a virtuálisgép-függőségek felderítéséhez.


## <a name="set-up-a-project"></a>Projekt beállítása

Hozzon létre egy új Azure Migrate projektet.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **projekt létrehozása** lehetőséget.
5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-vmware/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg a Azure Migrate-projekt üzembe helyezése megtörténik. A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> Ha már létrehozott egy projektet, ugyanezzel a projekttel regisztrálhat további készülékeket, és több virtuális gépet is felderítheti. további[információ](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>A készülék beállítása

Azure Migrate: a kiszolgáló értékelése egy könnyű Azure Migrate berendezést használ. A készülék virtuálisgép-felderítést végez, és a virtuális gép konfigurációját és a teljesítmény metaadatait a Azure Migrateba küldi. A készülék egy olyan PETESEJT-sablon telepítésével állítható be, amely letölthető a Azure Migrate projektből.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket a sablon használatával, beállíthatja egy PowerShell-parancsfájl használatával egy meglévő Windows Server 2016-kiszolgálón. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Üzembe helyezés PETESEJTekkel

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:
1. Adja meg a készülék nevét, és állítson elő egy Azure Migrate Project-kulcsot a portálon
1. Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba. Ellenőrizze, hogy a petesejtek biztonságosak-e.
1. Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
1. Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. a Azure Migrate projekt kulcsának előállítása

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **felderítési gépeken** a  >  **gépek virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor** adja meg a VMWare virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="2-download-the-ova-template"></a>2. a petesejtek sablon letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. A petesejtek fájlt, majd kattintson a **Letöltés** gombra.

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a PETESEJT-fájl kivonatának létrehozásához:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government esetén:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-vm"></a>3. a berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet.

1. A vSphere-ügyfél konzolján kattintson a **fájl**  >  **telepítése OVF sablon** elemre.
2. A OVF-sablon központi telepítése varázslóban > **forrás** mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely** mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
5. A **gazdagép/fürt** területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **tárterület** területen határozza meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés** területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.


### <a name="4-configure-the-appliance"></a>4. a berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött petesejtek helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a virtuális gépre, majd válassza a **konzol megnyitása** lehetőséget.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása** lapon tegye a következőket:
   - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - A proxy **beállítása** elemre kattintva adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a **telepítési utasításokban** megadott módon.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során. 
1. Ha szeretné, bármikor újrafuttathatja az **előfeltételeket** a berendezés konfigurálása során annak ellenőrzéséhez, hogy a berendezés teljesíti-e az összes előfeltételt.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése** lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Szüksége lesz egy eszköz kódjára az Azure-beli hitelesítéshez. A **Bejelentkezés** gombra kattintva megnyílik egy modális az eszköz kódjával az alább látható módon.

    ![Az eszköz kódját ábrázoló modális](./media/tutorial-discover-vmware/device-code.png)

1. Kattintson a **kód másolása & a bejelentkezés** elemre az eszköz kódjának másolásához és egy új böngésző lapon található Azure-beli bejelentkezési kérés megnyitásához. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon illessze be az eszköz kódját, és jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. Ha a bejelentkezés lapot véletlenül a bejelentkezés nélkül zárta be, frissítenie kell a készülék Configuration Manager böngésző lapját, hogy ismét engedélyezze a bejelentkezés gombot.
1. Miután sikeresen bejelentkezett, lépjen vissza az előző lapra a készülék Configuration Managerrel.
1. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő engedélyekkel a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése** lehetőségre kattintva megtekintheti a regisztráció részleteit.



## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

1. Az **1. lépés: vCenter Server hitelesítő adatok** megadása lapon kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** ahhoz a vCenter Server-fiókhoz, amelyet a berendezés a vCenter Server példányban található virtuális gépek felderítéséhez használ majd.
    - Be kell állítania egy fiókot a szükséges engedélyekkel az előző oktatóanyagban.
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyéni virtuális gépekre) szeretné alkalmazni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.
1. A **2. lépés: adja meg a vCenter Server részleteket** lehetőségnél kattintson a **felderítési forrás hozzáadása** elemre, hogy a legördülő listából válassza ki a hitelesítő adatok rövid nevét, adja meg a vCenter Server példány **IP-címét/teljes tartománynevét** . A **portot** meghagyhatja az alapértelmezett értékre (443), vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli, és kattintson a **Save (Mentés**) gombra.
1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a vCenter Serverhoz való kapcsolódást a megadott hitelesítő adatokkal, és megjeleníti az **ellenőrzési állapotot** a táblában a vCenter Server IP-cím/FQDN használatával.
1. A felderítés elindítása előtt bármikor **újraérvényesítheti** vCenter Server a kapcsolatot.
1. A **3. lépés: adja meg a virtuális gép hitelesítő adatait a telepített alkalmazások felderítéséhez, valamint az ügynök nélküli függőségek leképezésének végrehajtásához** kattintson a **hitelesítő adatok hozzáadása** lehetőségre, és adja meg azt az operációs rendszert, amelyhez a hitelesítő adatokat megadja, a hitelesítő adatok rövid nevét, valamint a **felhasználónevet** és **jelszót** Ezután kattintson a **Save (Mentés**) gombra.

    - Itt opcionálisan adhat meg hitelesítő adatokat, ha létrehozott egy fiókot az alkalmazás- [felderítéshez](how-to-discover-applications.md)vagy az [ügynök nélküli függőségek elemzéséhez](how-to-create-group-machine-dependencies-agentless.md).
    - Ha nem szeretné használni ezeket a funkciókat, kattintson a csúszkára a lépés kihagyásához. Később bármikor visszafordíthatja a szándékot.
    - Tekintse át az [alkalmazások felderítéséhez](migrate-support-matrix-vmware.md#application-discovery-requirements), illetve az [ügynök nélküli függőségek elemzéséhez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)szükséges engedélyeket.

5. A virtuális gép felderítésének indításához kattintson a **felderítés indítása** lehetőségre. A felderítés sikeres elindítását követően megtekintheti a felderítési állapotot a tábla vCenter Server IP-címe/teljes tartománynevével.

A felderítés a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon.
- A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. Az 500-es virtuális gépek esetében körülbelül egy óra telik, amíg az alkalmazás leltára megjelenik a Azure Migrate-portálon.
- A virtuális gépek felderítésének befejezése után a portálon engedélyezheti az ügynök nélküli függőségek elemzését a kívánt virtuális gépeken.


## <a name="next-steps"></a>További lépések

- A [VMWare virtuális gépek felmérése](./tutorial-assess-vmware-azure-vm.md) Azure-beli virtuális gépekre való áttelepítéshez.
- [Tekintse át a](migrate-appliance.md#collected-data---vmware) berendezés által a felderítés során gyűjtött adatokat.