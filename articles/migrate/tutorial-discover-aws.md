---
title: AWS-példányok felderítése Azure Migrate Server Assessment szolgáltatással
description: Ismerje meg, hogyan derítheti fel az AWS-példányokat Azure Migrate Server Assessment használatával.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 6924182e6425b0927583e8c6498c7073c4ff1806
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064321"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Oktatóanyag: AWS-példányok felderítése kiszolgáló-értékeléssel

Az Azure-ba való Migrálás részeként felderítheti a kiszolgálókat az értékeléshez és az áttelepítéshez.

Ez az oktatóanyag bemutatja, hogyan derítheti fel Amazon Web Services-(AWS-) példányokat a Azure Migrate: Server Assessment Tool használatával, egy egyszerű Azure Migrate berendezéssel. A készüléket fizikai kiszolgálóként kell üzembe helyezni a gépek és a teljesítmény metaadatainak folyamatos felderítése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure-fiókot.
> * AWS-példányok előkészítése a felderítéshez.
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
**Berendezés** | Szüksége van egy EC2 virtuális gépre, amelyen futtatni szeretné a Azure Migrate készüléket. A gépnek a következőket kell tartalmaznia:<br/><br/> – A Windows Server 2016 telepítve van. A készülék futtatása a Windows Server 2019 rendszerű gépen nem támogatott.<br/><br/> -16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárterület, valamint egy külső virtuális kapcsoló.<br/><br/> -Statikus vagy dinamikus IP-cím, internet-hozzáféréssel, közvetlenül vagy proxyn keresztül.
**Windows-példányok** | Engedélyezze a bejövő kapcsolatokat a WinRM 5985-as porton (HTTP), hogy a készülék lekérje a konfiguráció és a teljesítmény metaadatait.
**Linux-példányok** | Bejövő kapcsolatok engedélyezése a 22-es porton (TCP).

## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások**területen válassza az **előfizetések**lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-aws/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési**területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása**párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-aws/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása**lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-aws/assign-role.png)

7. A portálon keressen felhasználókat, és a **szolgáltatások**területen válassza a **felhasználók**lehetőséget.
8. A **felhasználói beállítások**területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>AWS-példányok előkészítése

Állítson be egy fiókot, amelyet a készülék használhat az AWS-példányok eléréséhez.

- Windows-kiszolgálók esetén állítson be egy helyi felhasználói fiókot a felderítésbe felvenni kívánt Windows-kiszolgálókon. Adja hozzá a felhasználói fiókot a következő csoportokhoz:-távfelügyeleti felhasználók – Teljesítményfigyelő felhasználók – Teljesítménynapló felhasználói.
 - Linux-kiszolgálók esetén rendszergazdai fiókra van szüksége a felderíteni kívánt Linux-kiszolgálókon.


## <a name="set-up-a-project"></a>Projekt beállítása

Hozzon létre egy új Azure Migrate projektet.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen válassza a **projekt létrehozása**lehetőséget.
5. A **projekt létrehozása**lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-aws/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>A készülék beállítása

Ha egy AWS-példányon szeretné beállítani a készüléket, egy [megfelelő példányon](#prerequisites)le kell töltenie és futtatnia kell egy telepítési parancsfájlt. A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni az Azure-hoz, először konfigurálja, és regisztrálja a Azure Migrate projekttel.

### <a name="download-the-installer-script"></a>A telepítő parancsfájl letöltése

Töltse le a készülék tömörített fájlját.

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés**gombra.
2. A **felderítési gépeken**a  >  **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb**elemre.
3. A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra.



### <a name="install-the-appliance"></a>A készülék telepítése

A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és egy webalkalmazást telepít az AWS-felderítéshez és-értékeléshez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a **AzureMigrateInstaller.ps1** nevű szkriptet a következő parancs futtatásával:

    - Nyilvános felhő esetén: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Azure Government esetén: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log naplófájlban érheti el a hibaelhárításhoz.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

A készülék hozzáférését a következőképpen tekintheti meg:

1. Győződjön meg arról, hogy a készülék számítógépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.
2. Győződjön meg arról, hogy ezek a portok nyitva vannak a készülék gépén:

    - Engedélyezze a bejövő kapcsolatokat a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.
    - Engedélyezze a bejövő kapcsolatokat a 44368-as porton a berendezés webalkalmazásának távoli eléréséhez a következő URL-címen: https://<Appliance-IP-vagy-Name>:44368.
    - Engedélyezze a kimenő kapcsolatokat a 443-as porton (HTTPS) a felderítési és a teljesítmény-metaadatok küldéséhez Azure Migrate.


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
2. A Azure Migrate Appliance-webalkalmazásban > az **Előfeltételek beállításához**tekintse át/fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
3. A készülék ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, és hogy a virtuális gépen lévő idő szinkronban van-e az internetes idő alatt.
    - Ha proxyt használ, kattintson a **proxy beállítása** elemre, és adja meg a proxy címe és portját (a következő formátumban: http://ProxyIPAddress vagy http://ProxyFQDN) . 
    - Hitelesítő adatok megadása, ha a proxy hitelesítést igényel. Csak a HTTP-proxyk használata támogatott.
4. A készülék telepíti a legújabb Azure Migrate frissítéseket
5. Tekintse át a beállításokat, és kattintson a **Folytatás**gombra.

       

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. A **Azure Migrate regisztrálása**lapon válassza a **Bejelentkezés**lehetőséget. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.

    ![A készülék regisztrálásának megkezdéséhez kattintson a Bejelentkezés gombra.](./media/tutorial-discover-aws/register.png)

1. A **bejelentkezési** oldalon jelentkezzen be az Azure-beli felhasználónevével és jelszavával. A PIN-kóddal való bejelentkezés nem támogatott.

    ![Bejelentkezés gomb a készülék regisztrálásához](./media/tutorial-discover-aws/sign-in.png)
1. A sikeres bejelentkezést követően térjen vissza az alkalmazáshoz.
1. A **Azure Migrate regisztrálása**területen válassza ki azt az előfizetést, amelyben a Azure Migrate projekt létrejött, majd válassza ki a projektet.
1. Adja meg a berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
3. Válassza a **Regisztráció** lehetőséget. Ezután kattintson a **Continue** (Folytatás) gombra. Egy üzenetben a sikeres regisztráció látható.

    ![Adja meg az előfizetés, a projekt és a berendezés nevét, és regisztrálja a készüléket](./media/tutorial-discover-aws/success-register.png)


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készülék csatlakozik az AWS-példányhoz a felderítéshez.

1. Kattintson a **hitelesítő adatok hozzáadása** elemre azon fiók hitelesítő adatainak megadásához, amelyet a berendezés a kiszolgálók felderítéséhez használ majd.  
2. Jelentkezzen be a felhasználónévvel és a jelszóval. A kulccsal való bejelentkezés nem támogatott. Emellett a felhasználónak a legfelső szintű bejelentkezési azonosítónak vagy a helyi rendszergazdai csoportnak kell lennie.
3. Adja meg az **operációs rendszert**, a hitelesítő adatok rövid nevét, valamint a felhasználónevet és a jelszót. Ezután kattintson az **Add** (Hozzáadás) gombra.
Több hitelesítő adatot is hozzáadhat a Windows-és Linux-kiszolgálókhoz.
4. Kattintson a **kiszolgáló hozzáadása**elemre, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (egy sor, soronként egy bejegyzés) a kiszolgálóhoz való kapcsolódáshoz.
5. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához válassza a > **Törlés**lehetőséget.
6. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. A kiszolgálók általában kevesebb, mint két percet vesznek igénybe a metaadatoknak a Azure Portalban való megjelenítéséhez.


## <a name="verify-discovered-vms-in-the-portal"></a>Felderített virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló értékelése**lapon válassza ki a **felderített kiszolgálók**darabszámát megjelenítő ikont.

## <a name="next-steps"></a>Következő lépések

- Az Azure-beli virtuális gépekre való Migrálás [fizikai kiszolgálóinak felmérése](tutorial-migrate-aws-virtual-machines.md) .
- [Tekintse át a](migrate-appliance.md#collected-data---physical) berendezés által a felderítés során gyűjtött adatokat.
