---
title: Gyakori felhőszolgáltatás-kezelési feladatok | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti a felhőszolgáltatásokat az Azure Portalon. Ezek a példák az Azure Portalt használják.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 185bb9d03bec042a5c8e9223616b40eba6629e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247526"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Felhőszolgáltatások kezelése az Azure Portalon
Az Azure Portal **Felhőszolgáltatások** területén a következőket teheti:

* Szolgáltatási szerepkör vagy központi telepítés frissítése.
* Előmozdítsa a szakaszos üzembe helyezést éles környezetben.
* Az erőforrásokat összekapcsolhatja a felhőszolgáltatással, hogy láthassa az erőforrás-függőségeket, és együtt méretezhesse az erőforrásokat.
* Törölje a felhőszolgáltatás vagy a központi telepítés.

A felhőszolgáltatás méretezéséről a [Felhőszolgáltatás automatikus skálázásának konfigurálása a portálon](cloud-services-how-to-scale-portal.md)című témakörben talál további információt.

## <a name="update-a-cloud-service-role-or-deployment"></a>Felhőszolgáltatási szerepkör vagy központi telepítés frissítése
Ha frissítenie kell a felhőszolgáltatás alkalmazáskódját, használja a **Frissítés** a felhőalapú szolgáltatás panelen. Frissítheti egyetlen szerepkört vagy az összes szerepkört. A frissítéshez új szolgáltatáscsomagot vagy szolgáltatáskonfigurációs fájlt tölthet fel.

1. Az [Azure Portalon][Azure portal]válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés megnyitja a felhőszolgáltatás-példány panel.

2. A panelen válassza a **Frissítés**lehetőséget.

    ![Frissítés gomb](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Frissítse a központi telepítést egy új szolgáltatáscsomag-fájllal (.cspkg) és szolgáltatáskonfigurációs fájllal (.cscfg).

    ![UpdateDeployment (Frissítés) telepítése](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Szükség esetén frissítse a tárfiókot és a központi telepítési címkét.

5. Ha bármelyik szerepkör csak egy szerepkörpéldánysal rendelkezik, jelölje be a **telepítés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** jelölőnégyzetet a frissítés folytatásához.

    Az Azure csak 99,95%-os rendelkezésre állást garantál a felhőszolgáltatás frissítése során, ha minden szerepkör legalább két szerepkörpéldányt (virtuális gépet) rendelkezik. Két szerepkörpéldány esetén az egyik virtuális gép feldolgozza az ügyfélkérelmeket, míg a másik frissül.

6. Jelölje be a **Központi telepítés indítása** jelölőnégyzetet, ha a csomag feltöltése után szeretné alkalmazni a frissítést.

7. A szolgáltatás frissítésének megkezdéséhez válassza az **OK gombot.**

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Üzemelő példányok felcserélése az éles környezetszakaszban történő üzembe helyezés elősegítése érdekében
Ha úgy dönt, hogy egy felhőszolgáltatás új kiadását telepíti, szakaszt, és tesztelje az új kiadást a felhőszolgáltatás átmeneti környezetében. **A Swap** segítségével váltson az URL-címek, amelyek a két központi telepítés címzettje, és előegy új kiadás éles környezetben.

A **felhőszolgáltatások** lapról vagy az irányítópultról cserélhet központi telepítéseket.

1. Az [Azure Portalon][Azure portal]válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés megnyitja a felhőszolgáltatás-példány panel.

2. A panelen válassza a **Csere**lehetőséget.

    ![A Felhőszolgáltatások felcserélése gomb](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Megnyílik a következő megerősítő üzenet:

    ![Felhőszolgáltatások cseréje](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Miután ellenőrizte a központi telepítési adatokat, válassza az **OK lehetőséget** a központi telepítések cseréjéhez.

    A központi telepítési csere gyorsan történik, mert az egyetlen dolog, ami módosítja a virtuális IP-címek (VIP) a központi telepítések.

    A számítási költségek csökkentése érdekében törölheti az átmeneti központi telepítést, miután meggyőződött arról, hogy az éles üzemelő példány a várt módon működik.

### <a name="common-questions-about-swapping-deployments"></a>Gyakori kérdések a központi telepítések cseréjével kapcsolatban

**Milyen előfeltételei vannak a központi telepítések cseréjének?**

A sikeres központi telepítési cserekét fő előfeltétele van:

- Ha statikus IP-címet szeretne használni az éles tárolóhelyhez, le kell foglalnia egyet az átmeneti tárolóhelyhez is. Ellenkező esetben a csere sikertelen lesz.

- A csere végrehajtása előtt a szerepkörök összes példányának futnia kell. A példányok állapotát az Azure Portal **Áttekintés** panelen ellenőrizheti. Másik lehetőségként használhatja a [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) parancsot a Windows PowerShellben.

Vegye figyelembe, hogy a vendég operációs rendszer frissítései és a szolgáltatás-javító műveletek is okozhat központi telepítési cserecsere sikertelen lehet. További információt a [Felhőszolgáltatások telepítési problémáinak elhárítása című témakörben talál.](cloud-services-troubleshoot-deployment-problems.md)

**A swap-ügylet állásidőt okoz az alkalmazásomhoz? Hogyan kezeljem?**

Az előző szakaszban leírtak szerint a központi telepítési csere általában gyors, mert ez csak egy konfigurációs változás az Azure terheléselosztó. Bizonyos esetekben 10 vagy több másodpercet is igénybe vehet, és átmeneti csatlakozási hibákat okozhat. Az ügyfelekre gyakorolt hatás korlátozása érdekében fontolja meg [az ügyfélújrapróbálkozási logika megvalósítását.](../best-practices-retry-general.md)

## <a name="delete-deployments-and-a-cloud-service"></a>Üzemelő példányok és felhőszolgáltatás törlése
A felhőszolgáltatások törlése előtt minden egyes meglévő központi telepítést törölnie kell.

A számítási költségek csökkentése érdekében törölheti az átmeneti központi telepítést, miután meggyőződött arról, hogy az éles üzemelő példány a várt módon működik. A leállított üzembe helyezett szerepkörpéldányok számítási költségeinek számlázása.

Az alábbi eljárással törölheti a központi telepítést vagy a felhőszolgáltatást.

1. Az [Azure Portalon][Azure portal]válassza ki a törölni kívánt felhőszolgáltatást. Ez a lépés megnyitja a felhőszolgáltatás-példány panel.

2. A panelen válassza a **Törlés**gombot.

    ![A Felhőszolgáltatások törlése gomb](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. A teljes felhőszolgáltatás törléséhez jelölje be a **felhőszolgáltatás és annak központi telepítései jelölőnégyzetet.** Vagy választhatja az **Éles üzemelő telepítés** vagy az Átmeneti központi **telepítés** jelölőnégyzetet.

    ![Felhőszolgáltatások törlése](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Alul válassza a **Törlés** lehetőséget.

5. A felhőszolgáltatás törléséhez válassza a **Felhőszolgáltatás törlése**lehetőséget. Ezután a megerősítési gyorsan válassza az **Igen**lehetőséget.

> [!NOTE]
> Ha egy felhőszolgáltatás törlődik, és részletes figyelés van konfigurálva, törölnie kell az adatokat manuálisan a tárfiókból. A metrikatáblázatok helyéről a Bevezetés a [felhőszolgáltatás figyelése című témakörbe.](cloud-services-how-to-monitor.md)


## <a name="find-more-information-about-failed-deployments"></a>További információ a sikertelen telepítésekről
Az **Áttekintő** panel tetején egy állapotsor található. A sáv kiválasztásakor egy új panel nyílik meg, és megjeleníti a hibainformációkat. Ha a központi telepítés nem tartalmaz semmilyen hibát, az információs panel üres.

![Felhőszolgáltatások – áttekintés](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* További információ a [felhőszolgáltatások üzembe helyezéséről.](cloud-services-how-to-create-deploy-portal.md)
* Egyéni [tartománynév konfigurálása](cloud-services-custom-domain-name-portal.md).
* [SSL-tanúsítványok konfigurálása](cloud-services-configure-ssl-certificate-portal.md).



