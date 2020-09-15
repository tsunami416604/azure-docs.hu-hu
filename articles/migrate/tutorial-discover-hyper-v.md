---
title: Hyper-V virtuális gépek felderítése Azure Migrate Server Assessmenttel
description: Ismerje meg, hogyan derítheti fel a helyszíni Hyper-V virtuális gépeket a Azure Migrate Server Assessment Tool eszközzel.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: fdf96b5767b461953fa88923aaa5050aff4613bc
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064304"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Oktatóanyag: Hyper-V virtuális gépek felderítése kiszolgáló-értékeléssel

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket. 

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a helyszíni Hyper-V virtuális gépeket (VM) a Azure Migrate: Server Assessment Tool eszközzel, egy egyszerű Azure Migrate berendezés használatával. A berendezést Hyper-V virtuális gépként kell üzembe helyezni a gép és a teljesítmény metaadatainak folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure-fiók beállítása
> * Készítse elő a Hyper-V környezetet a felderítéshez.
> * Azure Migrate-projekt létrehozása.
> * Állítsa be az Azure Migrate készüléket.
> * A folyamatos felderítés elindítása.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják.  

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.


**Követelmény** | **Részletek**
--- | ---
**Hyper-V gazdagép** | Azok a Hyper-V-gazdagépek, amelyeken a virtuális gépek találhatók, önállóak vagy fürtben is.<br/><br/> A gazdagépen a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszernek kell futnia.<br/><br/> Ellenőrizze, hogy a bejövő kapcsolatok engedélyezve vannak-e a WinRM 5985-es portján (HTTP), hogy a készülék csatlakozni tud-e a lekéréses virtuális gépek metaadatainak és teljesítményadatait a CIM (CIM) munkamenet használatával.
**Berendezések üzembe helyezése** | vCenter Server erőforrásokra van szükség ahhoz, hogy lefoglaljon egy virtuális gépet a készülékhez:<br/><br/> - Windows Server 2016<br/><br/> -32 GB RAM<br/><br/> – Nyolc vCPU<br/><br/> -Körülbelül 80 GB lemezes tárterület.<br/><br/> – Külső virtuális kapcsoló.<br/><br/> -Internet-hozzáférés a virtuális géphez közvetlenül vagy egy proxyn keresztül.
**Virtuális gépek** | A virtuális gépek Windows vagy Linux operációs rendszert is használhatnak. 

Mielőtt elkezdené, [áttekintheti a](migrate-appliance.md#collected-data---hyper-v) készülék által a felderítés során gyűjtött adatokat.

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:


1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások**területen válassza az **előfizetések**lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési**területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása**párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása**lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-hyper-v/assign-role.png)

7. A portálon keressen felhasználókat, és a **szolgáltatások**területen válassza a **felhasználók**lehetőséget.
8. A **felhasználói beállítások**területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-hyper-v/register-apps.png)

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-gazdagépek előkészítése

Rendszergazdai hozzáféréssel rendelkező fiók beállítása a Hyper-V-gazdagépeken. A készülék ezt a fiókot használja a felderítéshez.

- 1. lehetőség: a Hyper-V gazdagéphez rendszergazdai hozzáféréssel rendelkező fiók előkészítése.
- 2. lehetőség: helyi rendszergazdai fiók vagy tartományi rendszergazdai fiók előkészítése, és a fiók hozzáadása a következő csoportokhoz: távfelügyeleti felhasználók, Hyper-V-rendszergazdák és Teljesítményfigyelő felhasználók.


## <a name="set-up-a-project"></a>Projekt beállítása

új Azure Migrate projekt létrehozása.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen válassza a **projekt létrehozása**lehetőséget.
5. A **projekt létrehozása**lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-hyper-v/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>A készülék beállítása

Ez az oktatóanyag egy Hyper-V virtuális gépen állítja be a készüléket.
- Töltse le a készülék sablonját, és importálja a sablont vCenter Serverba a készülék virtuális gépe létrehozásához.
- A készülék létrehozása után első alkalommal be kell állítania azt, és regisztrálnia kell a Azure Migrate projektben.

> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket sablon használatával, beállíthatja egy PowerShell-parancsfájl használatával. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="download-the-vhd"></a>A VHD letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **felderítési gépek**a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V**lehetőséget.
3. A VHD-fájl letöltéséhez kattintson a **Letöltés** gombra.

   ![A PETESEJT-fájlok letöltésének kiválasztása](./media/tutorial-discover-hyper-v/download-vhd.png)



### <a name="deploy-the-appliance-vm"></a>A berendezés virtuális gépe üzembe helyezése

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet:

1. Töltse le a tömörített VHD-fájlt arra a Hyper-V-gazdagépre, amelyre a készülék virtuális gépe kerül.
2. Bontsa ki a tömörített fájlt.

    - A kinyert helyen a fájl kibontja AzureMigrateAppliance_VersionNumber nevű mappába.
    - Ez a mappa almappát tartalmaz, más néven AzureMigrateAppliance_VersionNumber.
    - Ez az almappa három további almappát tartalmaz: Pillanatképek, virtuális merevlemezek és Virtual Machines.

3. Nyissa meg a Hyper-V kezelőjét. A **műveletek**területen kattintson a **virtuális gép importálása**elemre.

    ![A virtuális merevlemez importálására szolgáló menüelem a Hyper-V kezelőjében](./media/tutorial-discover-hyper-v/deploy-vhd.png)

4. A virtuális gép importálása varázslóban > a **Kezdés előtt**kattintson a **tovább**gombra.
5. A **mappa keresése**területen válassza a **Virtual Machines** mappát. Ezután kattintson a **Tovább** gombra.
6. A **virtuális gép kiválasztása lapon**kattintson a **tovább**gombra.
7. Az **importálási típus kiválasztása**területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Tovább** gombra.
8. A **cél kiválasztása**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
9. A **tárolási mappák**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
10. A **hálózat kiválasztása**területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez. [További](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) információ a virtuális kapcsolók létrehozásáról.
11. Az **Összefoglalás**területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
12. A Hyper-V kezelőjében > **Virtual Machines**indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

A készülék hozzáférését a következőképpen tekintheti meg:

1. Győződjön meg arról, hogy a virtuális gép tud csatlakozni az Azure-hoz.
    - A nyilvános felhőben a készülék számítógépének képesnek kell lennie csatlakozni ezekhez az [URL-címekhez](migrate-appliance.md#public-cloud-urls).
    - A kormányzati felhőben a készüléknek képesnek kell lennie csatlakozni ezekhez a [kormányzati URL-címekhez](migrate-appliance.md#government-cloud-urls).
2. Győződjön meg arról, hogy ezek a portok nyitva vannak a készülék gépén:

    - Engedélyezze a bejövő kapcsolatokat a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.
    - Engedélyezze a bejövő kapcsolatokat a 44368-as porton a készülék webalkalmazásának távoli eléréséhez a következő URL-címen: https:// \<appliance-ip-or-name> : 44368.
    - Engedélyezze a kimenő kapcsolatokat a 443-as porton (HTTPS) a felderítési és a teljesítmény-metaadatok küldéséhez Azure Migrate.


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött VHD helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A Hyper-V kezelőjében > **Virtual Machines**kattintson a jobb gombbal a virtuális gépre > a **kapcsolat**elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson a proxybeállítások elemre, és írja be a proxy címe és a figyelő portját az űrlap vagy a **értékre** http://ProxyIPAddress http://ProxyFQDN .
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések.

### <a name="register-the-appliance"></a>A berendezés regisztrálása 

1. A **Azure Migrate regisztrálása**lapon válassza a **Bejelentkezés**lehetőséget. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.

    ![A készülék regisztrálásának megkezdéséhez kattintson a Bejelentkezés gombra.](./media/tutorial-discover-hyper-v/register.png)

1. A **bejelentkezési** oldalon jelentkezzen be az Azure-beli felhasználónevével és jelszavával. A PIN-kóddal való bejelentkezés nem támogatott.

    ![Bejelentkezés gomb a készülék regisztrálásához](./media/tutorial-discover-hyper-v/sign-in.png)
1. A sikeres bejelentkezést követően térjen vissza az alkalmazáshoz.
1. A **Azure Migrate regisztrálása**területen válassza ki azt az előfizetést, amelyben a Azure Migrate projekt létrejött, majd válassza ki a projektet.
1. Adja meg a berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
3. Válassza a **Regisztráció** lehetőséget. Ezután kattintson a **Continue** (Folytatás) gombra. Egy üzenetben a sikeres regisztráció látható.

    ![Adja meg az előfizetés, a projekt és a berendezés nevét, és regisztrálja a készüléket](./media/tutorial-discover-hyper-v/success-register.png)


### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez engedélyeznie kell, hogy mindegyik gazdagép meghatalmazottként működjön a berendezésben. Ha követte az oktatóanyagokat, ezt az előző oktatóanyagban végezte el, amikor felkészítette a Hyper-V-t az értékeléshez és az áttelepítéshez. [Manuálisan](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)kell beállítania a CredSSP a gazdagépekhez, vagy [egy olyan parancsfájl futtatásával](tutorial-prepare-hyper-v.md#run-the-script) , amely ezt teszi.

A következő módon engedélyezheti a készüléket:

#### <a name="option-1"></a>1\. lehetőség

Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Például: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2\. lehetőség

Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:

1. A **helyi számítógép-házirend**  >  **Számítógép konfigurációja**területen kattintson **Felügyeleti sablonok**  >  **rendszer**  >  **hitelesítő adatok delegálása**elemre.
2. Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése**lehetőségre, és válassza az **engedélyezve**lehetőséget.
3. A **Beállítások**területen kattintson a **Megjelenítés**elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
4. Ezután a **hitelesítő adatok delegálása**lehetőségre duplán kattintva **engedélyezze a friss hitelesítő adatok delegálását csak NTLM kiszolgálói hitelesítéssel**. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.



## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a Hyper-V-gazdagépekhez a virtuális gépek felderítéséhez.


### <a name="connect-to-hyper-v-hosts"></a>Kapcsolódás a Hyper-V-gazdagépekhez

1. A **Felhasználónév** és a **jelszó**mezőben adja meg a fiók hitelesítő adatait, amelyet a berendezés a virtuális gépek felderítéséhez használ majd. Adja meg a hitelesítő adatok rövid nevét, majd kattintson a **részletek mentése**gombra.
2. Kattintson a **gazdagép hozzáadása**lehetőségre, és adja meg a Hyper-V-gazdagép/-fürt részleteit.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után az egyes gazdagépeken vagy fürtökön felderíthető virtuális gépek száma látható.
    - Ha az érvényesítés sikertelen egy gazdagép esetében, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához válassza a > **Törlés**lehetőséget.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Gazdagépen körülbelül 1,5 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.


### <a name="verify-discovered-vms-in-the-portal"></a>Felderített virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló értékelése**lapon válassza ki a **felderített kiszolgálók**darabszámát megjelenítő ikont.

## <a name="next-steps"></a>Következő lépések

- A [Hyper-V virtuális gépek kiértékelése Azure-](tutorial-assess-hyper-v.md) beli virtuális gépekre való Migrálás céljából.
- [Tekintse át a](migrate-appliance.md#collected-data---hyper-v) berendezés által a felderítés során gyűjtött adatokat.