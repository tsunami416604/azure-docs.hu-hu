---
title: "Cloud service általános felügyeleti feladatai |} Microsoft Docs"
description: "Útmutató az Azure portálon felhőszolgáltatások kezelése. Ezekben a példákban az Azure-portálon."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: a06becda42541b22d3e090087dd96cd08c980f44
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-manage-cloud-services"></a>Cloud Services kezelése
Az a **Felhőszolgáltatások** területén az Azure portál, lehet frissíteni a szerepkör-szolgáltatás vagy a központi telepítés, üzemi szakaszos telepítés előléptetéséhez, erőforrások csatolása a felhőalapú szolgáltatás, így megtekintheti az erőforrás-függőségek és a skála a erőforrások együtt, és egy felhőalapú szolgáltatás, vagy a központi telepítés törlése.

További információk a felhőalapú szolgáltatás méretezése [Itt](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Hogyan: Felhő szerepkör-szolgáltatás vagy a központi telepítés
Ha az alkalmazás kódjának a felhőszolgáltatás frissíteni kell, **frissítése** a felhőalapú szolgáltatás panelen. Egyetlen szerepkör vagy az összes szerepkör frissítheti. Frissítéséhez feltöltheti egy új service-csomag vagy a szolgáltatás konfigurációs fájljában.

1. Az a [Azure-portálon][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.
2. A panelen kattintson a **frissítés** gombra.

    ![Frissítés gomb](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Az üzemelő példány frissítése egy új felhőszolgáltatás csomagfájlját (.cspkg) és a szolgáltatás konfigurációs fájlját (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionálisan** frissíteni az üzemelő példány címkéje és a storage-fiók.
5. Ha a szerepkörök csak egy szerepkör példánya, válassza ki a **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** ahhoz, hogy a frissítés gombra.

    Azure is csak 99,95 % szolgáltatás rendelkezésre állásának biztosítása a felhőalapú szolgáltatás frissítése közben. Ha szerepkörönként legalább két szerepkörpéldányokat (virtuális gépek). Két szerepkör osztályt egy virtuális gép ügyfél kérelmeket dolgozza fel a másik van frissítése közben.

6. Ellenőrizze **indítsa el a központi telepítés** szeretné, hogy a frissítés telepítése a csomag a feltöltés befejeződése után.
7. Kattintson a **OK** megkezdéséhez, a szolgáltatás frissítése.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Hogyan: felcserélése előléptetése üzemi szakaszos telepítés központi telepítések
Ha úgy dönt, hogy egy felhőalapú szolgáltatás, a szakasz az új verziót telepítse és tesztelje a az új verziót a felhőalapú szolgáltatás tesztelési környezetben. Használjon **felcserélése** Váltás az URL-címeket, amellyel a két központi telepítések tárgyalja, és előléptetése üzemi új verziót.

A központi telepítések kicserélheti a **Felhőszolgáltatások** lap vagy az irányítópulton.

1. Az a [Azure-portálon][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.
2. A panelen kattintson a **felcserélése** gombra.

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. A következő megerősítő kérdés megjelenik.

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Miután meggyőződött a központi telepítési információkat, kattintson a **OK** felcserélni a központi telepítéseket.

    A központi telepítés felcserélés gyorsan mert, hogy módosítja a virtuális IP-címek (VIP) a telepítések.

    Számítási költségek csökkentése érdekében, hogy a várt módon működik-e az éles telepítési ellenőrzése után törölheti a átmeneti központi telepítési.

### <a name="common-questions-about-swapping-deployments"></a>Központi telepítések csere kapcsolatos gyakori kérdésekre

**Mik azok a telepítések csere előfeltételei**

Nincsenek a sikeres telepítés swap két fő előfeltételei:

- Ha szeretné használni az éles tárolóhelyre egy statikus IP-címet, kell lefoglalni egy a, valamint az átmeneti helyet. Ellenkező esetben a felcserélés meghiúsul.

- A szerepkörök az összes példányát futnia kell a felcserélés elvégzése előtt. Az Azure-portálon a áttekintése panel példányának állapotát ellenőrizheti. Másik lehetőségként használhatja a [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) Windows PowerShell parancsot.

Vegye figyelembe, hogy a vendég operációs rendszer frissítése és a szolgáltatás javító műveleteket is okozhat, központi telepítési címek cseréje sikertelen lesz. További információkért lásd: [felhőalapú szolgáltatás központi telepítési problémák elhárítása](cloud-services-troubleshoot-deployment-problems.md).

**A lapozófájl-kapacitás negatívan befolyásolja az alkalmazáshoz állásidő? Hogyan tudom kezelje azt?**

Az utolsó szakaszban leírt módon egy központi telepítési felcserélés, általában gyors, mert csak egy konfigurációs változásokat az Azure load balancer. Néhány esetben azonban képes tíz vagy több másodpercre és átmeneti kapcsolati hibákat eredményezhet. Hatása az ügyfelek számára korlátozása érdekében vegye fontolóra [ügyfél újrapróbálkozási logika](../best-practices-retry-general.md).

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Útmutató: egy felhőalapú szolgáltatás és a központi telepítései törlése
Egy felhőalapú szolgáltatás törlése előtt törölni kell minden meglévő telepítés.

Számítási költségek csökkentése érdekében, hogy a várt módon működik-e az éles telepítési ellenőrzése után törölheti a átmeneti központi telepítési. Le lett állítva telepített szerepkör-példányok számítási költségek kell fizetni.

Az alábbi eljárás segítségével törölheti a központi telepítés vagy a felhőalapú szolgáltatás.

1. Az a [Azure-portálon][Azure portal], válassza ki a törölni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.
2. A panelen kattintson a **törlése** gombra.

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. A teljes körű felhőalapú szolgáltatás törölheti ellenőrzésével **felhőalapú szolgáltatás, és a központi telepítései** vagy válassza a **éles környezet** vagy a **történő üzembe helyezése**.

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Kattintson a **törlése** panel alján.
5. A felhőalapú szolgáltatás törléséhez kattintson **törlése a felhőalapú szolgáltatás**. A megerősítést kérő kattintson **Igen**.

> [!NOTE]
> A felhőszolgáltatást törölték, és részletes figyelési van beállítva, törölnie kell az adatokat kézzel a tárfiók. Hol található a metrikák táblák kapcsolatos információkért lásd: [ez](cloud-services-how-to-monitor.md) cikk.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Hogyan: sikertelen központi telepítéssel kapcsolatban további információt
A **áttekintése** panel rendelkezik állapotjelző tetején. A elemre, ha egy új panel nyílik meg, és bármely hibaadatokat jelenít meg. Ha az üzemelő példány nem tartalmaz ki a hibákat, az információ panel érték üres.

![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
