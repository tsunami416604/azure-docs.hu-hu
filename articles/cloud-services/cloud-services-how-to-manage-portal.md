---
title: Gyakori felhőszolgáltatás-felügyeleti feladatok |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a Cloud Services az Azure Portalon. Ezekben a példákban az Azure Portalon.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 3e4520df5c1ca8477abf5a9c4b4e7e124bb38282
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005757"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Cloud Services kezelése az Azure Portalon
Az a **Cloud Services** az Azure portal területén is:

* Egy szolgáltatási szerepkör vagy a központi telepítés frissítése.
* Az éles környezetbe szakaszos telepítés támogatása.
* Erőforrások összekapcsolása a cloud service, hogy tekintse meg az erőforrás-függőségek, és az erőforrások méretezése együtt.
* Törölje a felhőszolgáltatás és a egy központi telepítést.

A cloud Services méretezése kapcsolatos további információkért lásd: [konfigurálása az automatikus skálázást egy felhőszolgáltatáshoz a portálon](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Felhőszolgáltatási szerepkör vagy telepítés frissítése
Ha a felhőszolgáltatás az alkalmazáskódot frissíteni kell, használja a **frissítése** cloud service panelén. Egyetlen szerepkör vagy az összes szerepkör frissítheti. Ha frissíteni szeretné, tölthet fel egy új service-csomag vagy a szolgáltatás konfigurációs fájlja.

1. Az a [az Azure portal][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ebben a lépésben a cloud service példány panel nyílik meg.

2. A panelen válassza ki a **frissítés**.

    ![Frissítés gomb](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Frissíti az üzemelő példányt egy új felhőszolgáltatás csomagfájlját (.cspkg) és a szolgáltatás konfigurációs fájlját (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Szükség esetén frissítse a tárfiók és az üzemelő példány címkéje.

5. Ha szerepköröket csak egy szerepkörpéldány rendelkezik, válassza ki a **telepítse, még akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** jelölőnégyzetet ahhoz, hogy folytatja a frissítést.

    Ha minden egyes szerepkörhöz legalább két szerepkörpéldányt (virtuális gépek) az Azure csak a 99,95 %-os szolgáltatás rendelkezésre állása is garantálja felhőalapú szolgáltatás frissítése során. A két szerepkörpéldányt egy virtuális gép ügyfélkérelmek dolgozza fel, míg a másik frissül.

6. Válassza ki a **üzembe helyezésének megkezdéséhez** melletti jelölőnégyzetet, hogy a frissítés alkalmazására, a csomag a feltöltés befejeződése után.

7. Válassza ki **OK** megkezdéséhez a szolgáltatás frissítése.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Példányok előléptetése éles környezetbe szakaszos telepítés
Ha úgy dönt, hogy üzembe helyezése egy felhőalapú szolgáltatás, a fázis egy új verzióját, és a felhőalapú szolgáltatás átmeneti környezetben az új verziók teszteléséhez. Használat **felcserélése** az URL-címeket, amellyel a két üzembe helyezés foglalkozik, és elősegítik az új kiadás az éles környezetbe váltás.

Telepítések kicserélheti az **Cloud Services** oldalán vagy az irányítópulton.

1. Az a [az Azure portal][Azure portal], válassza ki a frissíteni kívánt felhőszolgáltatást. Ebben a lépésben a cloud service példány panel nyílik meg.

2. A panelen válassza ki a **felcserélése**.

    ![Cloud Services felcserélése gomb](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Megnyílik a következő megerősítő üzenet:

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Miután ellenőrizte az üzembe helyezési adatokat, válassza ki a **OK** a példányok.

    A központi telepítési felcserélés gyorsan mert, hogy módosítja a virtuális IP-címek (VIP) esetében történik a központi telepítéseket.

    Szeretné menteni a számítási költségeket, törölheti az átmeneti üzembe helyezés után, győződjön meg arról, hogy a várt módon működik-e az éles környezet.

### <a name="common-questions-about-swapping-deployments"></a>Központi telepítések felcserélése kapcsolatos gyakori kérdésekre

**Mik azok a telepítések felcserélése előfeltételei?**

Van egy sikeres üzembe helyezés felcserélés két fő előfeltételei:

- Ha szeretne statikus IP-címet használja az éles üzembe helyezési pont, le kell egyet az előkészítési ponton. Ellenkező esetben a felcserélés sikertelen lesz.

- A szerepkörök összes példánya futnia kell a felcserélés végrehajtása előtt. A példányok állapotának ellenőrzéséhez a **áttekintése** panel az Azure Portal. Másik lehetőségként használhatja a [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) parancsot a Windows PowerShellben.

Vegye figyelembe, hogy Vendég operációs rendszer frissítése és a szolgáltatás is javítási műveletek telepítési cseréje sikertelen okozhatnak. További információkért lásd: [felhőalapú szolgáltatás üzembe helyezési problémák elhárítása](cloud-services-troubleshoot-deployment-problems.md).

**Nem a lapozófájl-kapacitás fel az alkalmazásomhoz tartozó állásidő? Hogyan tudok kezelni azt?**

Az előző szakaszban leírtak a központi telepítési lapozófájl-kapacitás használata általában gyors, mert az csak egy konfigurációmódosítás az Azure load balancerben. Bizonyos esetekben is eltarthat, 10 vagy a további másodpercek és eredmény az átmeneti kapcsolati hibákat. Szeretné korlátozni a hatása az ügyfelek számára, vegye fontolóra [ügyfél újrapróbálkozási logikát](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Központi telepítések és a egy felhőalapú szolgáltatás törlése
Egy felhőalapú szolgáltatás törlése előtt törölnie kell minden meglévő telepítés.

Szeretné menteni a számítási költségeket, törölheti az átmeneti üzembe helyezés után, győződjön meg arról, hogy a várt módon működik-e az éles környezet. Telepített szerepkör példányai, amelyek le vannak állítva a számítási költségek számlázzuk ki.

Az alábbi eljárás segítségével törölheti a központi telepítés vagy a felhőszolgáltatás.

1. Az a [az Azure portal][Azure portal], válassza ki a törölni kívánt felhőszolgáltatást. Ebben a lépésben a cloud service példány panel nyílik meg.

2. A panelen válassza ki a **törlése**.

    ![Cloud Services törlése gombra](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. A teljes felhőalapú szolgáltatás törléséhez válassza ki a **Cloud service és a központi telepítései** jelölőnégyzetet. Vagy választhat a **éles környezet** vagy a **átmeneti üzembe helyezés** jelölőnégyzetet.

    ![Cloud Services Delete](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Válassza ki **törlése** alján.

5. A felhőalapú szolgáltatás törléséhez válassza ki **felhőszolgáltatás törlése**. Majd a megerősítési kérést válassza **Igen**.

> [!NOTE]
> Ha törli egy felhőalapú szolgáltatást, és részletes ellenőrzésére van beállítva, törölnie kell az adatokat manuálisan a tárfiókból. Hol található a tabulky metrik kapcsolatos információkért lásd: [Bevezetés a felhőalapú szolgáltatás figyelési](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>További információ a sikertelen üzembe helyezés
A **áttekintése** panelen egy állapotsávval tetején. Amikor kiválasztja a sáv, egy új panel megnyílik, és bármely hibaadatokat jelenít meg. Az üzemelő példány nem tartalmazhat hibaüzeneteket, ha az információk panel érték üres.

![Cloud Services áttekintése](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [egy felhőalapú szolgáltatás üzembe helyezése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* Konfigurálása [SSL-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
