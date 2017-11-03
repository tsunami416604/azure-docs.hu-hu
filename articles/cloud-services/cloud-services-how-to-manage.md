---
title: "Általános felhő szolgáltatás kezelési feladatai (klasszikus) |} Microsoft Docs"
description: "Útmutató: a klasszikus Azure portálon felhőszolgáltatások kezelése."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 41a30e50-067c-485b-96fd-434a6d057abc
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 2ee76dfcb579e53975b1f61a6590f8d78dc0961b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-cloud-services"></a>Cloud Services kezelése
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-manage-portal.md)
> * [klasszikus Azure portál](cloud-services-how-to-manage.md)
>
>

Az a **Felhőszolgáltatások** a klasszikus Azure portál területén portál, a szerepkör-szolgáltatás vagy a központi telepítés frissítése, üzemi szakaszos telepítés előléptetéséhez is erőforrások csatolása a felhőalapú szolgáltatás, hogy tekintse meg az erőforrás-függőségek és az erőforrások együtt méretezhető, és egy felhőalapú szolgáltatás, vagy a központi telepítés törlése.

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Hogyan: Felhő szerepkör-szolgáltatás vagy a központi telepítés
Az alkalmazás kódjának a felhőszolgáltatás frissíteni kell, ha **frissítése** az irányítópulton **Felhőszolgáltatások** lapon vagy **példányok** lap. Egyetlen szerepkör vagy az összes szerepkör frissítheti. Szüksége lesz egy új service-csomag és a szolgáltatás konfigurációs fájl feltöltése.

1. A a [a klasszikus Azure portálon](https://manage.windowsazure.com/), az irányítópult **Felhőszolgáltatások** lapon vagy **példányok** lapján kattintson **frissítés**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. A **üzemelő példány címkéje**, adjon meg egy nevet a központi telepítés (például mycloudservice4) azonosításához. Az üzemelő példány címkéje alatt található **gyors üzembe helyezési** az irányítópulton.
3. A **csomag**, használjon **Tallózás** feltölteni a felhőszolgáltatás csomagfájlját (.cspkg).
4. A **konfigurációs**, használjon **Tallózás** töltse fel a szolgáltatás konfigurációs (.cscfg) fájljában.
5. A **szerepkör**, jelölje be **összes** Ha frissíti a felhőszolgáltatásban található összes szerepkör. Egyetlen szerepkör frissítést végez, hogy válassza ki a frissíteni kívánt szerepkört. Akkor is, ha egy adott szerepkör frissítése választja, a szolgáltatás konfigurációs fájljában lévő frissítések az összes szerepkör is vonatkozik.
6. Ha a frissítés módosítja, a szerepkörök számának vagy a szerepkör méretét, jelölje be a **frissítés engedélyezése, ha a szerepkör méretét vagy a szerepkörök számának változik** jelölőnégyzetet ahhoz, hogy folytatja a frissítést.

    Vegye figyelembe, hogy a szerepkört (Ez azt jelenti, hogy a virtuális gép, amelyen a szerepkör példánya méret) vagy a szerepkörök számának méretének módosítása esetén minden egyes szerepkörpéldányt (virtuális gép) visszaállított kell lennie, és bármely helyi adatok elvesznek.

7. Ha bármely szolgáltatás szerepkörök csak egy szerepkör példánya, válassza ki a **frissítése akkor is, ha egy vagy több szerepkör egyetlen példányt jelölőnégyzet tartalmaz** ahhoz, hogy a frissítés gombra.

    Azure is csak 99,95 % szolgáltatás rendelkezésre állásának biztosítása a felhőalapú szolgáltatás frissítése közben. Ha szerepkörönként legalább két szerepkörpéldányokat (virtuális gépek). Amely lehetővé teszi, hogy egy virtuális gép ügyfél kérelmek feldolgozásához, a másik frissítése közben.

8. Kattintson a **OK** (jelölő) kattintva megkezdheti a szolgáltatás frissítése.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Hogyan: felcserélése előléptetése üzemi szakaszos telepítés központi telepítések
Használjon **felcserélése** előléptetése üzemi felhőszolgáltatás átmeneti központi telepítését. Ha úgy dönt, hogy egy felhőalapú szolgáltatás új verziót telepíteni, tesztelése, és új kiadási tesztelése a felhőalapú szolgáltatás tesztelési környezetben, amíg az ügyfelek az aktuális kiadásban használ éles környezetben. Ha készen áll a előléptetése üzemi új kiadását, **felcserélése** Váltás az URL-címeket, amely két központi telepítését tárgyalja.

A központi telepítések kicserélheti a **Felhőszolgáltatások** lap vagy az irányítópulton.

1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**.
2. A felhőalapú szolgáltatások, kattintson rá kattintva jelölje ki a felhőalapú szolgáltatáshoz.
3. Kattintson a **felcserélése**.

    A következő megerősítő kérdés megjelenik.

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Miután meggyőződött a központi telepítési információkat, kattintson a **Igen** felcserélni a központi telepítéseket.

    A központi telepítés felcserélés gyorsan mert, hogy módosítja a virtuális IP-címek (VIP) a telepítések.

    Számítási költségek csökkentése érdekében törölheti a központi telepítést az átmeneti, ha biztos benne, hogy az új éles környezet az elvárt módon működnek.

### <a name="common-questions-about-swapping-deployments"></a>Központi telepítések csere kapcsolatos gyakori kérdésekre

**Mik azok a telepítések csere előfeltételei**

Nincsenek a sikeres telepítés swap két fő előfeltételei:

- Ha szeretné használni az éles tárolóhelyre egy statikus IP-címet, kell lefoglalni egy a, valamint az átmeneti helyet. Ellenkező esetben a felcserélés sikertelen lesz.

- A szerepkörök az összes példányát futnia kell a felcserélés elvégzése előtt. Ellenőrizheti a klasszikus Azure portálon vagy a példány állapotának [a Get-AzureRole parancsot a Windows PowerShell](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0).

Vegye figyelembe, hogy Vendég operációs rendszer frissítése és a szolgáltatás javító műveleteket is eredményezheti, hogy telepítési címek cseréje sikertelen lesz. Lásd: [felhőalapú szolgáltatás központi telepítési problémák elhárítása](cloud-services-troubleshoot-deployment-problems.md) további részleteket.

**A lapozófájl-kapacitás negatívan befolyásolja az alkalmazáshoz állásidő? Hogyan tudom kezelje azt?**

Az utolsó szakaszban leírtak telepítési egy felcserélés általában nagyon gyorsan mert csak egy konfigurációs változásokat az Azure load balancer. Néhány esetben azonban képes tíz vagy több másodpercre és átmeneti kapcsolati hibákat eredményezhet. Hatása az ügyfelek számára korlátozása érdekében vegye fontolóra [ügyfél újrapróbálkozási logika](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Hogyan: erőforrás összekapcsolása egy felhőalapú szolgáltatás
A felhőszolgáltatások és más erőforrások közötti függőségek megjelenítéséhez a felhőszolgáltatások összekapcsolhatók az Azure SQL Database példányaival, illetve tárfiókokkal. Hivatkozásra, és az erőforrások leválasztása a **kapcsolt erőforrásokban** lapon, és ezután a figyelheti a felhőalapú szolgáltatás irányítópultján. Ha kapcsolt tárfiókra monitoring bekapcsolva, a felhőalapú szolgáltatás irányítópultján kérelmek teljes száma a figyelheti meg.

Használjon **hivatkozás** egy új vagy meglévő SQL-adatbázis példányt, vagy a storage-fiók összekötése a felhőalapú szolgáltatás számára. Az adatbázis és a felhő szerepkör-szolgáltatás, amely a használja majd méretezheti a **méretezési** lap. (A storage-fiókok arányosan automatikusan a használati növekedése.) További információkért lásd: [egy felhőalapú szolgáltatás és a kapcsolt erőforrásokban méretezése](cloud-services-how-to-scale.md).

Ezen kívül figyeléséhez, kezeléséhez, és az adatbázis méretezése a **adatbázisok** csomópont a klasszikus Azure portálon.

"Linking" erőforrás abban az értelemben nem az alkalmazás csatlakoztatása az erőforrás. Ha létrehoz egy új adatbázis használatával **hivatkozás**, adja hozzá a kapcsolati karakterláncokkal az alkalmazás kódjában, és frissítse a felhőszolgáltatás lesz szüksége. Is szüksége lehet felvenni a kapcsolati karakterláncokat, ha az alkalmazás erőforrást használ, a kapcsolt tárfiókra.

Az alábbi eljárás ismerteti egy új SQL Database-példányt, a kiszolgálón telepítve egy új SQL-adatbázis, a felhőszolgáltatásokra hivatkozásra.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Egy SQL-adatbázispéldány összekapcsolása egy felhőalapú szolgáltatás
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**. Kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
2. Kattintson a **kapcsolódó erőforrások**.

    A **kapcsolt erőforrásokban** lap megnyitásakor.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Jelölje be az **erőforrás hivatkozás** vagy **hivatkozás**.

    A **hivatkozás erőforrás** varázsló elindul.

    ![Hivatkozás 1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Kattintson a **hozzon létre egy új erőforrást** vagy **csatolni a meglévő erőforrás**.
5. Válassza ki a csatolni kívánt erőforrás típusát. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **SQL-adatbázis**. (Csak a klasszikus Azure portálon támogatja, a storage-fiók összekapcsolása egy felhőalapú szolgáltatás.)
6. Az adatbázis-konfiguráció befejezéséhez kövesse az utasításokat a Súgó a **SQL-adatbázisok** területén a klasszikus Azure portálon.

    Követheti, hogy az üzenet területen hivatkozási műveletnek az előrehaladását.

    Ha linking befejeződött, a felhőalapú szolgáltatás irányítópultján csatolt erőforrás állapotának figyelheti meg. Csatolt SQL-adatbázis méretezésével kapcsolatos információkért lásd: [egy felhőalapú szolgáltatás és a kapcsolt erőforrásokban méretezése](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>A csatolt erőforrás leválasztása
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**. Kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
2. Kattintson a **kapcsolt erőforrásokban**, majd válassza ki az erőforrás.
3. Kattintson a **leválasztása**. Kattintson a **Igen** a megerősítést kérő.

    SQL-adatbázis leválasztása nincs hatása az adatbázis vagy az alkalmazás-kapcsolatok az adatbázisba. Addig kezelheti az adatbázishoz a **SQL-adatbázisok** területén a klasszikus Azure portálon.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Útmutató: egy felhőalapú szolgáltatás és a központi telepítései törlése
Egy felhőalapú szolgáltatás törlése előtt törölni kell minden meglévő telepítés.

Számítási költségek csökkentése érdekében, hogy a várt módon működik-e az éles telepítési ellenőrzése után törölheti a tesztelési környezet. Akkor is, ha egy felhőalapú szolgáltatás nem fut áll szerepkörpéldányokat számlázott számítási költségeit.

Az alábbi eljárás segítségével törölheti a központi telepítés vagy a felhőalapú szolgáltatás.

1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**.
2. Válassza ki a felhőszolgáltatást, és kattintson **törlése**. (Kiválasztásához felhőszolgáltatásban az irányítópult megnyitása nélkül kattintson bárhol cloud service bejegyzésben nevén kívül.)

    Ha egy telepítés átmeneti és üzemi, látni fogja a az ablak alján alábbihoz hasonló lehetőségeket gombot. A felhőalapú szolgáltatás törlése előtt törölni kell a meglévő telepítések.

    ![Menü törlése](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3. A központi telepítés törléséhez kattintson **éles környezet törlése** vagy **átmeneti központi telepítés törlése**. A megerősítést kérő kattintson **Igen**.
4. Ha le szeretné törölni a felhőszolgáltatást, ismételje meg a 3, ha szükséges, törölje a központi telepítést.
5. A felhőalapú szolgáltatás törléséhez kattintson **törlése a felhőalapú szolgáltatás**. A megerősítést kérő kattintson **Igen**.

> [!NOTE]
> Ha részletes figyelését a felhőalapú szolgáltatás van konfigurálva, Azure nem törli a figyelési adatokat importáljon a tárfiókba törölje a felhőszolgáltatást. Szüksége lesz az adatok manuális törléséhez. Hol található a metrikák táblák kapcsolatos információkért lásd: "hogyan: részletes nyomon követési adatok a klasszikus Azure portálon kívül hozzáférés" a [figyelő felhőalapú szolgáltatások](cloud-services-how-to-monitor.md).
>
>

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate.md).
