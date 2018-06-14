---
title: Cloud service általános felügyeleti feladatai |} Microsoft Docs
description: Útmutató a Felhőszolgáltatások kezelése az Azure portálon. Ezekben a példákban az Azure-portálon.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: e60bf5c82e68d49abaa44d80ac9fafba2d8265da
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121195"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Cloud Services kezelése az Azure-portálon
Az a **Felhőszolgáltatások** is az Azure-portál területen:

* Egy szerepkör-szolgáltatás vagy a központi telepítés frissítéséhez.
* Szakaszos telepítés üzemi lépteti elő.
* Erőforrások csatolása a felhőalapú szolgáltatás, hogy tekintse meg az erőforrás-függőségek, és az erőforrások együtt méretezni.
* Egy felhőalapú szolgáltatás, vagy a központi telepítés törlése.

A felhőalapú szolgáltatás méretezése kapcsolatos további információkért lásd: [konfigurálása az automatikus skálázás egy felhőalapú szolgáltatás, a portál](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Felhő szerepkör-szolgáltatás vagy a központi telepítés
Ha az alkalmazás kódjának a felhőszolgáltatás frissíteni kell, **frissítése** a felhőalapú szolgáltatás panelen. Egyetlen szerepkör vagy az összes szerepkör frissítheti. Frissítéséhez feltöltheti egy új service-csomag vagy a szolgáltatás konfigurációs fájljában.

1. Az a [Azure-portálon][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.

2. A panelen válassza ki a **frissítés**.

    ![Frissítés gomb](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Az üzemelő példány frissítése egy új felhőszolgáltatás csomagfájlját (.cspkg) és a szolgáltatás konfigurációs fájlját (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Szükség esetén frissítse a tárfiók és a telepített környezet címkéje.

5. Ha a szerepkörök csak egy szerepkör példánya, válassza ki a **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** jelölőnégyzetet ahhoz, hogy a frissítés gombra.

    Azure is garantálható, hogy csak a 99,95 % szolgáltatás rendelkezésre állása egy felhőalapú szolgáltatás frissítése közben. Ha szerepkörönként legalább két szerepkörpéldányokat (virtuális gépek). Két szerepkör osztályt egy virtuális gép ügyfél kérelmeket dolgozza fel a másik van frissítése közben.

6. Válassza ki a **indítsa el a központi telepítés** melletti jelölőnégyzetet, hogy a frissítés alkalmazásához a csomag a feltöltés befejeződése után.

7. Válassza ki **OK** megkezdéséhez, a szolgáltatás frissítése.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>A felcserélendő előléptetése üzemi szakaszos telepítés központi telepítések
Ha úgy dönt, hogy egy felhőalapú szolgáltatás, a szakasz az új verziót telepítse és tesztelje a az új verziót a felhőalapú szolgáltatás tesztelési környezetben. Használjon **felcserélése** Váltás az URL-címeket, amellyel a két központi telepítések tárgyalja, és előléptetése üzemi új verziót.

A központi telepítések kicserélheti a **Felhőszolgáltatások** lap vagy az irányítópulton.

1. Az a [Azure-portálon][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.

2. A panelen válassza ki a **felcserélése**.

    ![Cloud Services felcserélése gomb](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Ekkor megjelenik a következő megerősítő kérdés:

    ![Cloud Services lapozófájl-kapacitás](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Miután meggyőződött a központi telepítési információk, válassza ki a **OK** felcserélni a központi telepítéseket.

    A központi telepítés felcserélés gyorsan mert, hogy módosítja a virtuális IP-címek (VIP) a telepítések.

    Számítási költségek csökkentése érdekében, hogy a várt módon működik-e az éles telepítési ellenőrzése után törölheti a átmeneti központi telepítési.

### <a name="common-questions-about-swapping-deployments"></a>Központi telepítések csere kapcsolatos gyakori kérdésekre

**Mik azok a telepítések csere előfeltételei**

Nincsenek a sikeres telepítés swap két fő előfeltételei:

- Ha szeretne egy statikus IP-címet használja az éles webalkalmazásra, kell lefoglalni egy a, valamint az átmeneti helyet. Ellenkező esetben a felcserélés meghiúsul.

- A szerepkörök az összes példányát futnia kell a felcserélés elvégzése előtt. Akkor is ellenőrizhesse a példányok állapotát a **áttekintése** panel az Azure portálról. Másik lehetőségként használhatja a [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) Windows PowerShell parancsot.

Vegye figyelembe, hogy Vendég operációs rendszer frissítése és a szolgáltatás műveletek is javítás sikertelen telepítés swap okozhat. További információkért lásd: [felhőalapú szolgáltatás központi telepítési problémák elhárítása](cloud-services-troubleshoot-deployment-problems.md).

**A lapozófájl-kapacitás negatívan befolyásolja az alkalmazáshoz állásidő? Hogyan tudom kezelje azt?**

Az előző szakaszban leírtak kell egy központi telepítési felcserélés, általában gyors, mert az csak az Azure load balancer a módosításait. Bizonyos esetekben is igénybe vehet 10 vagy további másodperc és eredmény átmeneti kapcsolat sikertelen. Hatása az ügyfelek számára korlátozása érdekében vegye fontolóra [ügyfél újrapróbálkozási logika](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Központi telepítések és a felhőszolgáltatás törlése
Egy felhőalapú szolgáltatás törlése előtt törölni kell minden meglévő telepítés.

Számítási költségek csökkentése érdekében, hogy a várt módon működik-e az éles telepítési ellenőrzése után törölheti a átmeneti központi telepítési. Le lett állítva telepített szerepkör-példányok számítási költségek kell fizetni.

Az alábbi eljárás segítségével törölheti a központi telepítés vagy a felhőalapú szolgáltatás.

1. Az a [Azure-portálon][Azure portal], válassza ki a törölni kívánt felhőszolgáltatást. Ez a lépés a felhőalapú szolgáltatás példány paneljének megnyitása.

2. A panelen válassza ki a **törlése**.

    ![Cloud Services törlése gombra](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. A teljes körű felhőalapú szolgáltatás törléséhez válassza ki a **felhőalapú szolgáltatás, és a központi telepítései** jelölőnégyzetet. Vagy dönthet úgy, vagy a **éles környezet** vagy a **történő üzembe helyezése** jelölőnégyzetet.

    ![Cloud Services Delete](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Válassza ki **törlése** alján.

5. A felhőalapú szolgáltatás törléséhez válassza ki **törlése a felhőalapú szolgáltatás**. Ezt követően a megerősítést kérő válassza **Igen**.

> [!NOTE]
> A felhőszolgáltatást törölték, és részletes figyelési van beállítva, törölnie kell az adatokat kézzel a tárfiók. Hol található a metrikák táblák kapcsolatos információkért lásd: [Bevezetés a felhőalapú szolgáltatás megfigyelésének](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Sikertelen központi telepítéssel kapcsolatban további információt
A **áttekintése** panel rendelkezik állapotjelző tetején. A sáv kiválasztásakor egy új panel nyílik meg, és bármely hibaadatokat jelenít meg. Ha az üzemelő példány nem tartalmaz ki a hibákat, az információ panel érték üres.

![A felhőalapú szolgáltatások áttekintése](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>További lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* Konfigurálása [SSL-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
