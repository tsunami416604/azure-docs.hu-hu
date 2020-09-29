---
title: VMware virtuális gépek felderítése Azure Migrate Server Assessmenttel
description: Ismerje meg, hogyan derítheti fel a helyszíni VMware virtuális gépeket a Azure Migrate Server Assessment Tool eszközzel
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: f39ad3cbc357575f735b963346c8a8b0cc95e7c8
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442229"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Oktatóanyag: VMware virtuális gépek felderítése kiszolgáló értékelésével

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket. 

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a helyszíni VMware virtuális gépeket (VM) a Azure Migrate: Server Assessment Tool eszközzel, egy egyszerű Azure Migrate berendezés használatával. A készüléket VMware virtuális gépként kell üzembe helyeznie a virtuális gépek és a teljesítmény metaadatainak, valamint a virtuális gépeken futó alkalmazások és a virtuálisgép-függőségek folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * Készítse elő a VMware-környezetet a felderítéshez.
> * Azure Migrate-projekt létrehozása.
> * Állítsa be az Azure Migrate készüléket.
> * A folyamatos felderítés elindítása.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges.  

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/), mielőtt hozzákezd.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.


**Követelmény** | **Részletek**
--- | ---
**vCenter Server/ESXi-gazdagép** | A 5,5, 6,0, 6,5 vagy 6,7 verziót futtató vCenter Serverra van szüksége.<br/><br/> A virtuális gépeket egy 5,5-es vagy újabb verziót futtató ESXi-gazdagépen kell üzemeltetni.<br/><br/> A vCenter Serveron engedélyezze a bejövő kapcsolatokat a 443-as TCP-porton, hogy a készülék gyűjtsön adatokat az értékeléshez.<br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor módosíthatja a portot, amikor a készülékről a-kiszolgálóra csatlakozik a felderítés elindításához.<br/><br/> Győződjön meg arról, hogy a virtuális gépeket üzemeltető ESXi-kiszolgálón engedélyezve van-e a bejövő hozzáférés az 443-es TCP-porton az alkalmazások felderítéséhez.
**Berendezés** | vCenter Server erőforrásokra van szükség a virtuális gép lefoglalásához a Azure Migrate berendezéshez:<br/><br/> - Windows Server 2016<br/><br/> -32 GB RAM, nyolc vCPU és körülbelül 80 GB lemezes tárterület.<br/><br/> – Egy külső virtuális kapcsoló és internet-hozzáférés a virtuális géphez közvetlenül vagy egy proxyn keresztül.
**Virtuális gépek** | Az oktatóanyag használatához a Windows-alapú virtuális gépeken Windows Server 2016, 2012 R2, 2012 vagy 2008 R2 rendszernek kell futnia.<br/><br/> A linuxos virtuális gépeknek Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 vagy CentOS 5/6/7 rendszereken kell futniuk.<br/><br/> A virtuális gépeknek VMware-eszközökre (a 10.2.0 későbbi verzióra) van szükségük, amely telepítve van és fut.<br/><br/> Windows rendszerű virtuális gépeken telepíteni kell a Windows PowerShell 2,0-es vagy újabb verzióját.


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások**területen válassza az **előfizetések**lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-vmware/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési**területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása**párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-vmware/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása**lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-vmware/assign-role.png)

7. A portálon keressen felhasználókat, és a **szolgáltatások**területen válassza a **felhasználók**lehetőséget.
8. A **felhasználói beállítások**területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-vmware/register-apps.png)

9. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás (ok) regisztrációját. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>A VMware előkészítése

A vCenter Server hozzon létre egy fiókot, amelyet a készülék használhat a vCenter Server eléréséhez, és győződjön meg arról, hogy a szükséges portok nyitva vannak. Szüksége lesz egy fiókra is, amelyet a készülék használhat a virtuális gépek eléréséhez. 

### <a name="create-an-account-to-access-vcenter"></a>Fiók létrehozása a vCenter való hozzáféréshez

A vSphere webes ügyfélprogramban az alábbiak szerint állítson be egy fiókot:

1. Rendszergazdai jogosultságokkal rendelkező fiók használata a vSphere Web Client > válassza az **Adminisztráció**lehetőséget.
2. **Hozzáférés**, válassza az **SSO-felhasználók és-csoportok**lehetőséget.
3. A **felhasználók**területen adjon hozzá egy új felhasználót.
4. Az **új felhasználó**mezőbe írja be a fiók adatait. Ezután kattintson az **OK** gombra.
5. A **globális engedélyek**területen válassza ki a felhasználói fiókot, és rendelje hozzá a **csak olvasási** szerepkört a fiókhoz. Ezután kattintson az **OK** gombra.
6. A **szerepkörök** > válassza ki a **csak olvasási** szerepkört, és a **jogosultságok**területen válassza a **vendég műveletek**lehetőséget. Ezek a jogosultságok a virtuális gépeken futó alkalmazások felderítéséhez, valamint a virtuálisgép-függőségek elemzéséhez szükségesek.
 
    ![Jelölőnégyzet a vendég műveleteknek a csak olvasási szerepkörön való engedélyezéséhez](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Hozzon létre egy fiókot a virtuális gépek eléréséhez

A készülék hozzáférést biztosít a virtuális gépekhez az alkalmazások felderítéséhez és a virtuálisgép-függőségek elemzéséhez. A készülék nem telepít ügynököket a virtuális gépeken.

1. Hozzon létre egy helyi rendszergazdai fiókot, amelyet a berendezés a Windows rendszerű virtuális gépeken futó alkalmazások és függőségek felderítésére használhat.
2. Linux rendszerű gépek esetén hozzon létre egy rendszergazdai jogosultságokkal rendelkező felhasználói fiókot, vagy egy olyan felhasználói fiókot, amely rendelkezik ezekkel az engedélyekkel a/bin/netstat-és/bin/ls-fájlokon: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate támogatja az alkalmazás-felderítés egyik hitelesítő adatait az összes Windows-kiszolgálón, valamint egy hitelesítő adatot az App-Discovery számára az összes Linux-gépen.


## <a name="set-up-a-project"></a>Projekt beállítása

Hozzon létre egy új Azure Migrate projektet.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen válassza a **projekt létrehozása**lehetőséget.
5. A **projekt létrehozása**lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-vmware/new-project.png)

7. Kattintson a **Létrehozás** gombra.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>A készülék beállítása

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:
- Adja meg a készülék nevét, és állítson elő egy Azure Migrate Project-kulcsot a portálon
- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket a sablonnal, beállíthatja azt egy PowerShell-parancsfájl használatával. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Üzembe helyezés PETESEJTekkel

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:
- Adja meg a készülék nevét, és állítson elő egy Azure Migrate Project-kulcsot a portálon
- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.

### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **felderítési gépeken**a  >  **gépek virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor**adja meg a VMWare virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. A petesejtek fájlt, majd kattintson a **Letöltés**gombra. 


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
        VMware (11,9 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140333) | bd5c19eec93a62d52cc507a6b7b408d07f33f92b7d39b8a1e3dfec4ec62830d7

    - Azure Government esetén:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet.

1. A vSphere-ügyfél konzolján kattintson a **fájl**  >  **telepítése OVF sablon**elemre.
2. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
5. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött petesejtek helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a virtuális gépre, majd válassza a **konzol megnyitása**lehetőséget.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
   - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - A proxy **beállítása** elemre kattintva adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a **telepítési utasításokban**megadott módon.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során. 
1. Ha szeretné, bármikor újrafuttathatja az **előfeltételeket** a berendezés konfigurálása során annak ellenőrzéséhez, hogy a berendezés teljesíti-e az összes előfeltételt.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése**lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Kattintson a **Bejelentkezés**elemre. Egy új böngésző lapon nyit meg egy Azure-beli bejelentkezési kérést. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz. 
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](tutorial-prepare-vmware.md#prepare-azure) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése**lehetőségre kattintva megtekintheti a regisztráció részleteit.



## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

1. Az **1. lépés: vCenter Server hitelesítő adatok**megadása lapon kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** ahhoz a vCenter Server-fiókhoz, amelyet a berendezés a vCenter Server példányban található virtuális gépek felderítéséhez használ majd.
    - Be kell állítania egy fiókot a szükséges engedélyekkel az [előző oktatóanyagban](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyéni virtuális gépekre) szeretné alkalmazni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.
1. A **2. lépés: adja meg a vCenter Server részleteket**lehetőségnél kattintson a **felderítési forrás hozzáadása** elemre, hogy a legördülő listából válassza ki a hitelesítő adatok rövid nevét, adja meg a vCenter Server példány **IP-címét/teljes tartománynevét** . A **portot** meghagyhatja az alapértelmezett értékre (443), vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli, és kattintson a **Save (Mentés**) gombra.
1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a vCenter Serverhoz való kapcsolódást a megadott hitelesítő adatokkal, és megjeleníti az **ellenőrzési állapotot** a táblában a vCenter Server IP-cím/FQDN használatával.
1. A felderítés elindítása előtt bármikor **újraérvényesítheti** vCenter Server a kapcsolatot.
1. A **3. lépés: adja meg a virtuális gép hitelesítő adatait a telepített alkalmazások felderítéséhez, valamint az ügynök nélküli függőségek leképezésének végrehajtásához**kattintson a **hitelesítő adatok hozzáadása**lehetőségre, és adja meg azt az operációs rendszert, amelyhez a hitelesítő adatokat megadja, a hitelesítő adatok rövid nevét, valamint a **felhasználónevet** és **jelszót** Ezután kattintson a **Save (Mentés**) gombra.

    - Itt opcionálisan adhat meg hitelesítő adatokat, ha létrehozott egy fiókot az [alkalmazás-felderítési szolgáltatáshoz](how-to-discover-applications.md), vagy az [ügynök nélküli függőségek elemzése funkciót](how-to-create-group-machine-dependencies-agentless.md).
    - Ha nem szeretné használni ezeket a funkciókat, kattintson a csúszkára a lépés kihagyásához. Később bármikor visszafordíthatja a szándékot.
    - Tekintse át az alkalmazás- [felderítéshez](migrate-support-matrix-vmware.md#application-discovery-requirements)szükséges hitelesítő adatokat, vagy az [ügynök nélküli függőségek elemzéséhez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. A virtuális gép felderítésének indításához kattintson a **felderítés indítása**lehetőségre. A felderítés sikeres elindítását követően megtekintheti a felderítési állapotot a tábla vCenter Server IP-címe/teljes tartománynevével.

A felderítés a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon.
- A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. Az 500-es virtuális gépek esetében körülbelül egy óra telik, amíg az alkalmazás leltára megjelenik a Azure Migrate-portálon.


## <a name="next-steps"></a>További lépések

- A [VMWare virtuális gépek felmérése](tutorial-assess-vmware.md) Azure-beli virtuális gépekre való áttelepítéshez.
- [Tekintse át a](migrate-appliance.md#collected-data---vmware) berendezés által a felderítés során gyűjtött adatokat.
