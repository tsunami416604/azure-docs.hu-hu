---
title: Oktatóanyag az Azure-SSIS-integrációs futásidejű konfigurálásához a virtuális hálózathoz való csatlakozáshoz
description: Ismerje meg, hogyan csatlakozhat egy Azure-SSIS-integrációs futásidőhez egy Azure virtuális hálózathoz.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 7470af23059ccd07e48050e6dc34521e299a986f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418609"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Az Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR) konfigurálása virtuális hálózathoz való csatlakozáshoz

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez az oktatóanyag alapvető lépéseket tartalmaz az Azure-Portal használatával az Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR) virtuális hálózathoz való csatlakozásához.

A lépések a következők:

- Konfiguráljon egy virtuális hálózatot.
- Csatlakozzon az Azure-SSIS IR-hez egy virtuális hálózathoz az Azure Data Factory portálról.

## <a name="prerequisites"></a>Előfeltételek

- **Az Azure-SSIS integrációs futásideje.** Ha nem rendelkezik Azure-SSIS-integrációs futásidejű, [üzembe helyezett egy Azure-SSIS-integrációs futásidejű az Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) megkezdése előtt.

- **Felhasználói engedély**. Az Azure-SSIS IR-t létrehozó felhasználónak legalább az Azure Data Factory-erőforráson [szerepkör-hozzárendeléssel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) kell rendelkeznie az alábbi lehetőségek egyikével:

    - Használja a beépített hálózati közreműködői szerepkört. Ez a szerepkör a _\* Microsoft.Network/permission,amely_ a szükségesnél sokkal nagyobb hatókörrel rendelkezik.
    - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft.Network/virtualNetworks/\*/join/action_ engedélyt tartalmazza. Ha azt is szeretné, hogy a saját nyilvános IP-címeket az Azure-SSIS IR, miközben csatlakozik egy Azure Resource Manager virtuális hálózat, kérjük, a _Microsoft.Network/publicIPAddresses/*/join/action_ engedélyt is a szerepkörben.

- **Virtuális hálózat**.

    - Ha nem rendelkezik virtuális hálózattal, [hozzon létre egy virtuális hálózatot az Azure Portal használatával.](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

    - Győződjön meg arról, hogy a virtuális hálózat erőforráscsoportja hozhat létre és törölhet bizonyos Azure hálózati erőforrásokat.
    
        Az Azure-SSIS ir kell létrehoznia bizonyos hálózati erőforrások at ugyanabban az erőforráscsoportban, mint a virtuális hálózat. Ezek a források a következők:
        - Egy Azure-os terheléselosztó * \<Guid>-azurebatch-cloudserviceloadbalancer névvel*
        - Hálózati biztonsági csoport *\<GUID>-azurebatch-cloudservicenetworksecuritygroup névvel
        - Azure nyilvános IP-címe az -azurebatch-cloudserviceip névvel
    
        Ezek az erőforrások az Azure-SSIS IR indításakor jönnek létre. Az Azure-SSIS ir leállításakor törlődnek. Az Azure-SSIS-ir leállításának letiltása érdekében ne használja fel újra ezeket a hálózati erőforrásokat a többi erőforrásban.

    - Győződjön meg arról, hogy nincs [erőforrás-zárolás](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) az erőforráscsoport/előfizetés, amelyhez a virtuális hálózat tartozik. Ha írásvédett/törlési zárolást állít be, az Azure-SSIS ir indítása és leállítása sikertelen lesz, vagy nem válaszol.

    - Győződjön meg arról, hogy nem rendelkezik olyan Azure-szabályzattal, amely megakadályozza, hogy a következő erőforrások létrejönnek az erőforráscsoport/előfizetés alatt, amelyhez a virtuális hálózat tartozik:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Az alábbiakban **a hálózati konfigurációs** forgatókönyvek nem tartoznak ebbe az oktatóanyagba:
    - Ha saját nyilvános IP-címeket hoz az Azure-SSIS IR-hez.
    - Ha saját DNS-kiszolgálót használ.
    - Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton.
    - Ha az Azure ExpressRoute vagy a felhasználó által meghatározott útvonal (UDR).
    - Ha testre szabott Azure-SSIS IR.If you use customized Azure-SSIS IR.
    
    További információért ellenőrizze a [virtuális hálózat konfigurációját.](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

## <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

Az Azure Portal használatával konfigurálhatja a virtuális hálózatot, mielőtt megpróbálna csatlakozni hozzá egy Azure-SSIS-ir-hez.

1. Indítsa el a Microsoft Edge-et vagy a Google Chrome-ot. Jelenleg csak ezek a webböngészők támogatják a Data Factory felhasználói felületét.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **További szolgáltatások lehetőséget.** Szűrje ki és válassza a **Virtuális hálózatok lehetőséget.**

1. Szűrje ki és válassza ki a virtuális hálózatot a listában.

1. A **Virtuális hálózat** lapon válassza a **Tulajdonságok lehetőséget.**

1. A **RESOURCE ID** másolásgombjának kiválasztásával másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote-ban vagy egy fájlban.

1. A bal oldali menüben válassza az **Alhálózatok**lehetőséget.

    - Győződjön meg arról, hogy a kiválasztott alhálózat elegendő címterülettel rendelkezik az Azure-SSIS ir használatához. Hagyja meg a rendelkezésre álló IP-címeket legalább az infravörös csomópont számának legalább kétszeresére. Az Azure az egyes alhálózatokon belül lefoglal néhány IP-címet. Ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címei a protokollmegfelelőség hez vannak fenntartva, és további három cím használatos az Azure-szolgáltatásokhoz. További információ: [Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Ne válassza ki a GatewaySubnet egy Azure-SSIS IR üzembe helyezéséhez. Virtuális hálózati átjárók számára van elkülönben.
    - Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak el (például SQL Database által felügyelt példány, App Service és így tovább).

1. Ellenőrizze, hogy az Azure Batch-szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja az Azure Batch-szolgáltatót. Ha már rendelkezik Egy Azure Batch-fiókkal az előfizetésében, az előfizetés regisztrálva van az Azure Batch-hez. (Ha létrehozza az Azure-SSIS IR-t a Data Factory portálon, az Azure Batch-szolgáltató automatikusan regisztrálva lesz.)

   1. Az Azure Portalbal a bal oldali menüben válassza az **Előfizetések**lehetőséget.

   1. Válassza ki előfizetését.

   1. A bal oldalon válassza az **Erőforrás-szolgáltatók**lehetőséget, és ellenőrizze, hogy a **Microsoft.Batch** regisztrált szolgáltató-e.

   ![A "Regisztrált" státusz visszaigazolása](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft.Batch a** listában, regisztrálja, [hozzon létre egy üres Azure Batch-fiókot](../batch/batch-account-create-portal.md) az előfizetésben. Később törölheti.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Csatlakozzon az Azure-SSIS ir-hez egy virtuális hálózathoz

Miután konfigurálta az Azure Resource Manager virtuális hálózatát vagy a klasszikus virtuális hálózatot, csatlakozhat az Azure-SSIS ir-hez a virtuális hálózathoz:

1. Indítsa el a Microsoft Edge-et vagy a Google Chrome-ot. Jelenleg csak ezek a webböngészők támogatják a Data Factory felhasználói felületét.

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali menüben az **Adatgyárak**lehetőséget. Ha nem látható **az Adatgyárak** menüben, válassza a **További szolgáltatások**lehetőséget, majd a **INTELLIGENCE + ANALYTICS** szakaszban válassza az **Adatgyárak**lehetőséget.

   ![Adatgyárak listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az adat-gyár az Azure-SSIS IR a listában. Megjelenik az adatgyár kezdőlapja. Válassza a **Szerzői & figyelő** csempét. A Data Factory felhasználói felülete egy külön lapon jelenik meg.

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, válassza a **Kapcsolatok**lehetőséget, és váltson az **Integrációs futtatási lapra.**

   !["Integrációs futásidők" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha az Azure-SSIS-ir fut, az **integrációs futtatóhek** listájában a Műveletek oszlopban válassza a Stop gombot az Azure-SSIS IR.If your Azure-SSIS IR is running, in the Integration Runtimes list, in the **Actions** column, select the **Stop** button for your Azure-SSIS IR. Az Azure-SSIS ir nem szerkeszthető, amíg le nem állítja.

   ![Az infravörös szolgáltatás leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az **integrációs futásidők** listájában a **Műveletek** oszlopban válassza az Azure-SSIS IR **Szerkesztés** gombját.

   ![Az integrációs futásidő szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Az integrációs futásidejű beállítási panelen a **Tovább** gomb kiválasztásával haladjon végig az **Általános beállítások** és az **SQL-beállítások** szakaszon.

1. A **Speciális beállítások** szakaszban:
   1. Jelölje be a **Virtuális hálózat kiválasztása az Azure-SSIS-integrációs futásidejű csatlakozásához, engedélyezze az ADF számára bizonyos hálózati erőforrások létrehozását, és szükség esetén hozza meg saját statikus nyilvános IP-címeit** jelölőnégyzetet.

   1. **Előfizetés esetén**válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

   1. A **Hely**beállításnál az integrációs futásidő ugyanazon helye van kiválasztva.

   1. A **Típus mezőben**válassza ki a virtuális hálózat típusát: klasszikus vagy Az Azure Resource Manager. Azt javasoljuk, hogy válasszon egy Azure Resource Manager virtuális hálózat, mert a klasszikus virtuális hálózatok hamarosan elavult.

   1. A **Virtuálishálózat neve mezőbe**válassza ki a virtuális hálózat nevét. Az Azure SQL Database-kiszolgálóvirtuális hálózati szolgáltatás végpontjaival vagy az SSISDB üzemeltetéséhez saját végponttal rendelkező felügyelt példányhoz használt példánynak kell lennie. Vagy a helyszíni hálózathoz csatlakoztatott nak kell lennie. Ellenkező esetben bármilyen virtuális hálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

   1. Az **Alhálózat neve csoportban**válassza ki a virtuális hálózat alhálózatának nevét. Az SSISDB üzemeltetéséhez az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatásvégpontokkal használt nak kell lennie. Vagy egy másik alhálózat, mint a felügyelt példány a saját végpont ssisdb üzemeltetéséhez használt. Ellenkező esetben bármelyik alhálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

   1. Válassza **a Virtuálishálózat-érvényesítés lehetőséget.** Ha az ellenőrzés sikeres, válassza a **Continue (Folytatás) lehetőséget.**

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Az **Összefoglaló** szakaszban tekintse át az Azure-SSIS ir összes beállítását. Ezután válassza **a Frissítés**lehetőséget.

1. Indítsa el az Azure-SSIS ir-t az Azure-SSIS IR **Műveletek** oszlopában a **Start** gombra kattintva. Körülbelül 20–30 percet vesz igénybe a virtuális hálózathoz csatlakozó Azure-SSIS IR indítása.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure-SSIS IR virtuális hálózathoz való csatlakozásáról.](join-azure-ssis-integration-runtime-virtual-network.md)
