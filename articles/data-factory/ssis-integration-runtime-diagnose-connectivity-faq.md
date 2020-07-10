---
title: A kapcsolat diagnosztizálása a SSIS Integration Runtime-ban – problémamegoldás
description: Ez a cikk hibaelhárítási útmutatást nyújt a kapcsolatok diagnosztizálásához a SSIS Integration Runtime-ban
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172596"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>A kapcsolat diagnosztizálása a SSIS Integration Runtime-ban – problémamegoldás

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha kapcsolódási problémákba ütközik a SSIS-csomagok SSIS integrációs modulban történő végrehajtása során, különösen akkor, ha a SSIS Integration Runtime csatlakozott az Azure Virtual networkhez. Az Azure Data Factory-portál SSIS integrációs moduljának figyelése lapján a kapcsolat diagnosztizálása funkció használatával megpróbálhatja öndiagnosztizálni a problémákat. 

 ![Figyelő oldal – a kapcsolati figyelő diagnosztizálása ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ lap – kapcsolat tesztelése](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Ez a cikk azokat a leggyakoribb hibákat tartalmazza, amelyeket a SSIS integrációs moduljában a csatlakozás tesztelésekor talál. Ismerteti a hibák megoldásának lehetséges okait és műveleteit. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Gyakori hibák, lehetséges okok és javaslati megoldások

### <a name="error-code-invalidinput"></a>Hibakód: InvalidInput.
* **Hibaüzenet**: Ellenőrizze, hogy helyes-e a bevitel.
* **Lehetséges okok:** A bemenet helytelen.
* **Javaslat:** Ellenőrizze a bemenetet.

### <a name="error-code-firewallornetworkissue"></a>Hibakód: FirewallOrNetworkIssue.
* **Hibaüzenet**: Ellenőrizze, hogy a port nyitva van-e a tűzfalon/kiszolgálón/NSG, és hogy a hálózat stabil-e.
* **Lehetséges okok:** 
  * A kiszolgáló nem nyitja meg ezt a portot.
  * A hálózati biztonsági csoport elutasította a kimenő forgalmat ezen a porton
  * A NVA/Azure tűzfal/helyszíni tűzfal nem nyitja meg ezt a portot.
* **Ajánlás** 
  * Nyissa meg ezt a portot a kiszolgálón.
  * Frissítse a hálózati biztonsági csoportot, hogy engedélyezze a kimenő forgalmat ezen a porton.
  * Nyissa meg ezt a portot a NVA/Azure tűzfal/helyszíni tűzfalon.

### <a name="error-code-misconfigureddnssettings"></a>Hibakód: MisconfiguredDnsSettings.
* **Hibaüzenet**: Ha a saját DNS-kiszolgálóját használja a Azure-SSIS IRhoz csatlakoztatott VNet, ellenőrizze, hogy képes-e az állomásnév feloldására.
* **Lehetséges okok:** 
  *  Az egyéni DNS problémája
  *  Nem teljes tartománynevet (FQDN) használ a saját gazdagép neveként
* **Ajánlás** 
  *  Javítsa ki az egyéni DNS-problémát annak biztosítására, hogy az képes legyen az állomásnév feloldására.
  *  Használja a teljes tartománynevet (FQDN), például a <your_private_server>. contoso.com használatát <your_private_server> helyett, mivel a Azure-SSIS IR nem fogja automatikusan hozzáfűzni a saját DNS-utótagját.

### <a name="error-code-servernotallowremoteconenction"></a>Hibakód: ServerNotAllowRemoteConenction.
* **Hibaüzenet**: Ellenőrizze, hogy a kiszolgáló engedélyezi-e a távoli TCP-kapcsolatokat ezen a porton keresztül.
* **Lehetséges okok:** 
  *  A kiszolgáló tűzfala nem engedélyezi a távoli TCP-kapcsolatokat.
  *  A kiszolgáló nem online állapotú.
* **Ajánlás** 
  *  Távoli TCP-kapcsolatok engedélyezése a kiszolgálói tűzfalon.
  *  Indítsa el a kiszolgálót.
   
### <a name="error-code-misconfigurednsgsettings"></a>Hibakód: MisconfiguredNsgSettings.
* **Hibaüzenet**: Győződjön meg róla, hogy a VNet NSG engedélyezi a kimenő forgalmat ezen a porton keresztül. Ha Azure ExpressRoute és UDR használ, ellenőrizze, hogy a port nyitva van-e a tűzfalon/kiszolgálón.
* **Lehetséges okok:** 
  *  A hálózati biztonsági csoport megtagadta a kimenő forgalmat ezen a porton.
  *  A NVA/Azure tűzfal/helyszíni tűzfal nem nyitja meg ezt a portot.
* **Ajánlás** 
  *  Frissítse a hálózati biztonsági csoportot, hogy engedélyezze a kimenő forgalmat ezen a porton.
  *  Nyissa meg ezt a portot a NVA/Azure tűzfal/helyszíni tűzfalon.

### <a name="error-code-genericissues"></a>Hibakód: GenericIssues.
* **Hibaüzenet**: a kapcsolódási teszt általános problémák miatt nem sikerült.
* **Lehetséges okok:** A tesztelési kapcsolatban általános átmeneti probléma merült fel.
* **Javaslat:** Később próbálkozzon újra a tesztelési kapcsolatban. Ha az Újrapróbálkozás nem segít, forduljon a Azure Data Factory támogatási csapatához.


### <a name="error-code-pspingexecutiontimeout"></a>Hibakód: PSPingExecutionTimeout.
* **Hibaüzenet**: a kapcsolat időtúllépésének tesztelése, próbálkozzon újra később.
* **Lehetséges okok:** A tesztelési kapcsolat időkorlátja lejárt.
* **Javaslat:** Később próbálkozzon újra a tesztelési kapcsolatban. Ha az Újrapróbálkozás nem segít, forduljon a Azure Data Factory támogatási csapatához.

### <a name="error-code-networkinstable"></a>Hibakód: NetworkInstable.
* **Hibaüzenet**: a hálózat instabilitása miatt szabálytalanul sikerült tesztelni a kapcsolódást.
* **Lehetséges okok:** Átmeneti hálózati probléma.
* **Javaslat:** Ellenőrizze, hogy a kiszolgáló vagy a tűzfal hálózata stabil-e.

## <a name="next-steps"></a>További lépések

- Telepítse a csomagokat. További információ: [SSIS-projekt üzembe helyezése az Azure-ban a SSMS használatával](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Futtassa a csomagokat. További információ: [SSIS-csomagok futtatása az Azure-ban a SSMS-](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)mel.
- A csomagok beosztása. További információ: [SSIS-csomagok beosztása az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

