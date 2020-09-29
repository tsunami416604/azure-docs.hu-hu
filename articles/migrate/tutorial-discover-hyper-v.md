---
title: Hyper-V virtuális gépek felderítése Azure Migrate Server Assessmenttel
description: Ismerje meg, hogyan derítheti fel a helyszíni Hyper-V virtuális gépeket a Azure Migrate Server Assessment Tool eszközzel.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e62effc31ab5dbc687e0509617b89561c5f2a3b6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442317"
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

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy ezek az előfeltételek teljesülnek.


**Követelmény** | **Részletek**
--- | ---
**Hyper-V gazdagép** | Azok a Hyper-V-gazdagépek, amelyeken a virtuális gépek találhatók, önállóak vagy fürtben is.<br/><br/> A gazdagépen a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszernek kell futnia.<br/><br/> Ellenőrizze, hogy a bejövő kapcsolatok engedélyezve vannak-e a WinRM 5985-es portján (HTTP), hogy a készülék csatlakozni tud-e a lekéréses virtuális gépek metaadatainak és teljesítményadatait a CIM (CIM) munkamenet használatával.
**Berendezések üzembe helyezése** | A Hyper-v-gazdagépnek erőforrásokra van szüksége ahhoz, hogy lefoglaljon egy virtuális gépet a készülékhez:<br/><br/> - Windows Server 2016<br/><br/> – 16 GB RAM<br/><br/> – Nyolc vCPU<br/><br/> -Körülbelül 80 GB lemezes tárterület.<br/><br/> – Külső virtuális kapcsoló.<br/><br/> -Internet-hozzáférés a virtuális géphez közvetlenül vagy egy proxyn keresztül.
**Virtuális gépek** | A virtuális gépek Windows vagy Linux operációs rendszert is használhatnak. 

Mielőtt elkezdené, [áttekintheti a](migrate-appliance.md#collected-data---hyper-v) készülék által a felderítés során gyűjtött adatokat.

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:


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

9. Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az **alkalmazás fejlesztői** szerepkörét egy fiókhoz, hogy engedélyezze a HRE-alkalmazás (ok) regisztrációját. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

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

7. Kattintson a **Létrehozás** gombra.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>A készülék beállítása

Ez az oktatóanyag egy Hyper-V virtuális gépen állítja be a készüléket, a következőképpen:

- Adja meg a készülék nevét, és állítson be egy Azure Migrate Project-kulcsot a portálon.
- Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.
> [!NOTE]
> Ha valamilyen okból nem tudja beállítani a készüléket sablon használatával, beállíthatja egy PowerShell-parancsfájl használatával. [További információ](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **felderítési gépek**a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V**lehetőséget.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor**adja meg a Hyper-V virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-vhd"></a>A VHD letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. VHD-fájl, majd kattintson a **Letöltés**gombra. 


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő PowerShell-parancsot a ZIP-fájl kivonatának létrehozásához
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:

        **Forgatókönyv** | **Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Azure Government esetén:

        **Forgatókönyv*** | **Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gépet.

1. Bontsa ki a tömörített VHD-fájlt egy olyan mappába a Hyper-V-gazdagépen, amely a készülék virtuális gépét fogja üzemeltetni. Három mappa van kibontva.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**területen kattintson a **virtuális gép importálása**elemre.
2. A virtuális gép importálása varázslóban > a **Kezdés előtt**kattintson a **tovább**gombra.
3. A **mappában keresse**meg a kibontott virtuális merevlemezt tartalmazó mappát. Ezután kattintson a **Tovább** gombra.
1. A **virtuális gép kiválasztása lapon**kattintson a **tovább**gombra.
2. Az **importálási típus kiválasztása**területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Tovább** gombra.
3. A **cél kiválasztása**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez.
6. Az **Összefoglalás**területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines**indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött VHD helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A Hyper-V kezelőjében > **Virtual Machines**kattintson a jobb gombbal a virtuális gépre > a **kapcsolat**elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson a **proxy beállítása** elemre, és adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
      - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése**lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Kattintson a **Bejelentkezés**elemre. Egy új böngésző lapon nyit meg egy Azure-beli bejelentkezési kérést. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz. 
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](tutorial-prepare-hyper-v.md#prepare-azure) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése**lehetőségre kattintva megtekintheti a regisztráció részleteit.



### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a készülékről:

1. Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:
    - A **helyi számítógép-házirend**  >  **Számítógép konfigurációja**területen kattintson **Felügyeleti sablonok**  >  **rendszer**  >  **hitelesítő adatok delegálása**elemre.
    - Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése**lehetőségre, és válassza az **engedélyezve**lehetőséget.
    - A **Beállítások**területen kattintson a **Megjelenítés**elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
    - A  **hitelesítő adatok delegálása**területen kattintson duplán a **friss hitelesítő adatok delegálása csak NTLM kiszolgálói hitelesítéssel**lehetőségre. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a virtuális gépek felderítését.

1. Az **1. lépés: a Hyper-v gazdagép hitelesítő adatainak**megadása területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** egy olyan Hyper-V-gazdagéphez vagy-fürthöz, amelyet a berendezés a virtuális gépek felderítéséhez használ Kattintson a **Save (Mentés**) gombra.
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A Hyper-V virtuális gépek felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: a Hyper-v-gazdagép/-fürt részleteinek**megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a Hyper-v-gazdagép/ **-fürt IP-címének/teljes tartománynevének** , valamint a gazdagéphez/fürthöz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Emellett lehetőség van a Hyper-V-gazdagép/-fürt adatainak biztosítására is a **CSV importálásával**.


    - Ha az **egyetlen elem hozzáadása**lehetőséget választja, meg kell adnia a hitelesítő adatok és a Hyper-V-gazdagép/fürt **IP-CÍMÉNEK/teljes tartománynevének** rövid nevét, majd kattintson a **Mentés**gombra.
    - Ha a **több elem hozzáadása** _(alapértelmezés szerint)_ lehetőséget választja, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a Hyper-V gazdagép/fürt **IP-címének/teljes tartománynevének** a megadásával. **Ellenőrizze** a hozzáadott rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása**lehetőséget választja, letöltheti a CSV-sablonfájlt, feltöltheti a fájlt a Hyper-V gazdagép/fürt **IP-CÍMÉVEL/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés**gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a kapcsolódást a hozzáadott Hyper-V-gazdagépekhez/-fürtökhöz, és megjeleníti a tábla **érvényesítési állapotát** az egyes gazdagépeken/fürtökön.
    - A sikeresen érvényesített gazdagépek/fürtök esetében további részleteket a saját IP-címére/teljes tartománynevére kattintva tekinthet meg.
    - Ha az érvényesítés sikertelen a gazdagépen, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához kattintson a **Törlés**gombra.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a gazdagépek/fürtök kapcsolatát.
1. Kattintson a **felderítés indítása**lehetőségre a virtuális gép felderítésének kikapcsolásához a sikeresen érvényesített gazdagépekről/fürtökről. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot az egyes gazdagépeken/fürtökön a táblában.

Ez elindítja a felderítést. Gazdagépen körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezését követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultját.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="next-steps"></a>További lépések

- A [Hyper-V virtuális gépek kiértékelése Azure-](tutorial-assess-hyper-v.md) beli virtuális gépekre való Migrálás céljából.
- [Tekintse át a](migrate-appliance.md#collected-data---hyper-v) berendezés által a felderítés során gyűjtött adatokat.
