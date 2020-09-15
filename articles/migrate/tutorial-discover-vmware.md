---
title: VMware virtuális gépek felderítése Azure Migrate Server Assessmenttel
description: Ismerje meg, hogyan derítheti fel a helyszíni VMware virtuális gépeket a Azure Migrate Server Assessment Tool eszközzel
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 55a7f15233d32f5ee60a57ad34e8b0177ca29ff3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064345"
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

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


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

Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

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

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>A készülék beállítása

Ez az oktatóanyag egy VMware virtuális gépen állítja be a készüléket.
- Töltse le a készülék sablonját, és importálja vCenter Serverba a készülék virtuális gépe létrehozásához.
- A készülék létrehozása után először be kell állítania azt, és regisztrálnia kell a Azure Migrate projektben.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket a sablonnal, beállíthatja azt egy PowerShell-parancsfájl használatával. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **felderítési gépek**a  >  **gépek virtualizáltak?** területen válassza **az igen, a VMware vSphere hypervisor**lehetőséget.
3. Válassza a **Letöltés** lehetőséget a petesejtek sablon fájljának letöltéséhez.

   ![A PETESEJT-fájl letöltésének kiválasztása](./media/tutorial-discover-vmware/download-ova.png)


### <a name="deploy-the-appliance-vm"></a>A berendezés virtuális gépe üzembe helyezése

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet:

1. Jelentkezzen be a vSphere-ügyfél konzolra a létrehozott vCenter-fiók használatával.
2. A **Virtual Machines** lapon > **műveletek** menüben válassza a **OVF-sablon telepítése**lehetőséget a OVF-sablon telepítése varázsló megnyitásához.
3. A **OVF sablon kiválasztása**lapon válassza a **helyi fájl**lehetőséget, és keresse meg a letöltési sablont. 
   ![OVF-sablon üzembe helyezéséhez használható vSphere](./media/tutorial-discover-vmware/deploy-ovf.png)
3. Adja meg a varázsló beállításai varázslót, amely tartalmazza a központi telepítési helyet, a gazdagépet/fürtöt, amelyen a virtuális gép futni fog, valamint a tárolási/hálózati beállításokat.
4. A **készen áll**, kattintson a **Befejezés**gombra. A rendszer importálja és telepíti a sablont. 
5. Az üzembe helyezés után a készülék virtuális gépe megjelenik a **Virtual Machines** lapon.
6. Válassza ki a virtuális gép > **bekapcsolását**.
7. A VMware távoli konzolon > **licencfeltételek**lapon tekintse át/fogadja el a licencfeltételeket.
8. A **beállítások testreszabása**területen állítson be egy jelszót a felhasználói fiókhoz.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

A készülék virtuális gépekhez való hozzáférésének ellenőrzését.

1. Győződjön meg arról, hogy a virtuális gép tud csatlakozni az Azure-hoz.
    - A nyilvános felhőben a készülék számítógépének képesnek kell lennie csatlakozni ezekhez az [URL-címekhez](migrate-appliance.md#public-cloud-urls).
    - A kormányzati felhőben a virtuális gépnek képesnek kell lennie csatlakozni ezekhez a [kormányzati URL-címekhez](migrate-appliance.md#government-cloud-urls).
2. Győződjön meg arról, hogy ezek a portok nyitva vannak a készülék gépén:

    - Engedélyezze a bejövő kapcsolatokat a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.
    - Engedélyezze a bejövő kapcsolatokat a 44368-as porton a berendezés webalkalmazásának távoli eléréséhez a következő URL-címen: https://<Appliance-IP-vagy-Name>:44368.
    - Engedélyezze a kimenő kapcsolatokat a 443-as porton (HTTPS) a felderítési és a teljesítmény-metaadatok küldéséhez Azure Migrate.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Jelentkezzen be a készülék virtuális gépre. 
    - A készülék webalkalmazása automatikusan megnyílik egy böngészőben.
    - Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjára.
2. A Azure Migrate Appliance-webalkalmazásban > az **Előfeltételek beállításához**tekintse át/fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
3. A készülék ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, és hogy a virtuális gépen lévő idő szinkronban van-e az internetes idő alatt.
    - Ha proxyt használ, kattintson a **proxy beállítása** elemre, és adja meg a proxy címe és portját (a következő formátumban: http://ProxyIPAddress vagy http://ProxyFQDN) . 
    - Hitelesítő adatok megadása, ha a proxy hitelesítést igényel. Csak a HTTP-proxyk használata támogatott.
4. A készülék telepíti a legújabb Azure Migrate frissítéseket, és ellenőrzi, hogy telepítve van-e a VMWare vSphere Virtual Disk Development Kit (VDDK).
5. A VDDK 6,7 telepítéséhez kattintson a **Letöltés** gombra a VMware-ből való letöltéséhez, és bontsa ki a letöltött zip-fájl tartalmát a készülék megadott helyére. Ezután kattintson **az ellenőrzés és telepítés**gombra.

    ![Előfeltételek lap az Internet-hozzáférés ellenőrzéséhez és a berendezések központi telepítésének beállításaihoz](./media/tutorial-discover-vmware/prerequisites.png)
  
3. A VDDK telepítése után tekintse át a beállításokat, majd kattintson a **Folytatás**gombra.

### <a name="register-the-appliance"></a>A berendezés regisztrálása 

1. A **Azure Migrate regisztrálása**lapon válassza a **Bejelentkezés**lehetőséget. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.

    ![A készülék regisztrálásának megkezdéséhez kattintson a Bejelentkezés gombra.](./media/tutorial-discover-vmware/register.png)

1. A **bejelentkezési** oldalon jelentkezzen be az Azure-beli felhasználónevével és jelszavával. A PIN-kóddal való bejelentkezés nem támogatott.

    ![Bejelentkezés gomb a készülék regisztrálásához](./media/tutorial-discover-vmware/sign-in.png)
1. A sikeres bejelentkezést követően térjen vissza az alkalmazáshoz.
1. A **Azure Migrate regisztrálása**területen válassza ki azt az előfizetést, amelyben a Azure Migrate projekt létrejött, majd válassza ki a projektet.
1. Adja meg a berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
3. Válassza a **Regisztráció** lehetőséget. Ezután kattintson a **Continue** (Folytatás) gombra. Egy üzenetben a sikeres regisztráció látható.

    ![Adja meg az előfizetés, a projekt és a berendezés nevét, és regisztrálja a készüléket](./media/tutorial-discover-vmware/success-register.png)

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek felderítéséhez.

### <a name="connect-to-vcenter-server"></a>Kapcsolódás vCenter Serverhoz

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek felderítéséhez.

1. A **vCenter Server megadása lapon**adja meg a vCenter Server nevét (FQDN) vagy IP-címét.
2. Hagyja meg az alapértelmezett portot, vagy határozzon meg egy egyéni portot, amelyen vCenter Server figyeli.
3. A **Felhasználónév** és a **jelszó**mezőben válassza ki a vCenter Server fiók hitelesítő adatait, amelyet a berendezés a vCenter Server virtuális gépek felderítéséhez használ majd.
3. Válassza a **kapcsolat ellenőrzése**lehetőséget, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz. 

    ![vCenter-kiszolgáló részletei és gomb a vCenter Serverhoz való kapcsolódás ellenőrzéséhez](./media/tutorial-discover-vmware/vcenter.png)

1. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása**lehetőségre.
1. Itt adhatja meg az alkalmazás-felderítéshez és a függőségek elemzéséhez használt Windows/Linux rendszerbeli hitelesítő adatokat.
1. A felderítési folyamat elindításához kattintson a Save (Mentés) gombra, **és indítsa el a felderítést**.

    ![Adja meg a hitelesítő adatokat az alkalmazások felderítéséhez és a függőségek elemzéséhez, és indítsa el a folyamatos felderítést. ](./media/tutorial-discover-vmware/start-discovery.png)


A felderítés elindítása után:

- Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon.
- Az alkalmazás felderítése hosszabb időt vesz igénybe. Az idő a felderített virtuális gépek számától függ. Az 500-es virtuális gépek esetében körülbelül egy óra telik, amíg az alkalmazás leltára megjelenik a Azure Migrate-portálon.



## <a name="verify-discovered-vms-in-the-portal"></a>Felderített virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló értékelése**lapon válassza ki a **felderített kiszolgálók**darabszámát megjelenítő ikont.

## <a name="next-steps"></a>Következő lépések

- A [VMWare virtuális gépek felmérése](tutorial-assess-vmware.md) Azure-beli virtuális gépekre való áttelepítéshez.
- [Tekintse át a](migrate-appliance.md#collected-data---vmware) berendezés által a felderítés során gyűjtött adatokat.
