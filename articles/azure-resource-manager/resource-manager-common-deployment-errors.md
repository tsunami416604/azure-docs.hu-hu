---
title: "Az Azure-telepítés gyakori hibák elhárítása |} Microsoft Docs"
description: "Ismerteti, hogyan lehet gyakori hibák megoldásához erőforrások Azure-ban Azure Resource Manager használatával történő központi telepítésekor."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "központi telepítési hiba, az azure-telepítés, telepítse az azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2ebb469289afc36b08c90ae9839f5bdba41cd90b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Hibaelhárítás általános az Azure-telepítés az Azure Resource Manager eszközzel

Ez a cikk ismerteti a gyakori az Azure-telepítés hibák észlelhetnek, és javítsa ki a hibákat az információival. Ha a hiba kódja nem találhatók a központi telepítési hiba, tekintse meg [hibakód keresése](#find-error-code).

## <a name="error-codes"></a>Hibakódok

| Hibakód: | Kezelés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Kövesse a tárfiókok vonatkozó elnevezési korlátozás. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Ellenőrizze a rendelkezésre álló tár fiók tulajdonságait. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AnotherOperationInProgress | Várjon, amíg egyidejű művelet elvégzéséhez. | |
| AuthorizationFailed | A fióknév vagy egyszerű szolgáltatásnév nincs engedélye a központi telepítés befejezéséhez. Ellenőrizze a szerepkör a fiókja tagja, és a hozzáférése a központi telepítés hatókör. | [Azure szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Központi telepítés értékek, amelyek nem egyeznek, mi várható erőforrás-kezelő által küldött. Ellenőrizze a belső hibaállapot-üzeneteket a hibaelhárítás támogatásához. | [Sablonra való hivatkozást](/azure/templates/) és [támogatott helyek](resource-manager-template-location.md) |
| Ütközés | A kért műveletet az erőforrás a jelenlegi állapotban nem engedélyezett. Például a lemezek átméretezése engedélyezett csak akkor, ha a virtuális gép létrehozása, vagy ha a virtuális gép felszabadítása. | |
| DeploymentActive | Várjon, amíg párhuzamos központi telepítést, hogy ez az erőforráscsoport befejezéséhez. | |
| DnsRecordInUse | A DNS-rekord nevének egyedinek kell lennie. Adjon meg egy másik nevet, vagy módosítsa a meglévő bejegyzést. | |
| ImageNotFound | Ellenőrizze a virtuális gép kép beállításait. | [Linux képek hibaelhárítása](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) és [hibáinak elhárítása Windows-lemezképek](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| InUseSubnetCannotBeDeleted | Ez a hiba jelentkezhetnek, ha megpróbált frissíteni egy erőforrást, de törlésével és az erőforrás létrehozása dolgozza fel a kérelmet. Ügyeljen arra, hogy az összes változatlan értékeket megadni. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Szerezze be a hozzáférési tokent a megfelelő bérlő számára. Csak a jogkivonat lekérheti a bérlő, a fiókja tagja. | |
| InvalidContentLink | Valószínűleg próbált összekapcsolása egy beágyazott sablont, amely nem érhető el. Ellenőrizze a beágyazott sablon megadott URI. Ha a sablon egy tárfiókot, ellenőrizze, hogy az URI elérhető. Szükség lehet egy SAS-jogkivonat átadni. | [Csatolt sablonok](resource-group-linked-templates.md) |
| InvalidParameter | Az erőforrás megadott értékek egyike nem egyezik a várt értékkel. Ez a hiba oka lehet számos különböző feltételeket. Például lehet, hogy a jelszó megfelelő, vagy lehet, hogy a blob neve helytelen. Tekintse meg a hibaüzenetet, annak eldöntéséhez, amelynek ki kell javítani szükséges. | |
| InvalidRequestContent | A központi telepítés értékek nem várt vagy a hiányzó értékeket tartalmaznak szükséges értékeket. Ellenőrizze az erőforrás típusára vonatkozó értékeket. | [Hivatkozása](/azure/templates/) |
| InvalidRequestFormat | A hibakeresési naplózást engedélyező, a központi telepítés végrehajtása közben, és ellenőrizze a kérelem tartalma. | [Hibakeresési naplózás](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | A megadott erőforrás-névtér ellenőrzése a **típus** tulajdonság. | [Hivatkozása](/azure/templates/) |
| InvalidResourceReference | Az erőforrás még nem létezik, vagy helytelenül hivatkozott. Ellenőrizze, hogy hozzáadjon egy függőséget szükséges. Ellenőrizze, hogy Ön miként használja a a **hivatkozás** függvény a forgatókönyvhöz szükséges paramétereket tartalmazza. | [-Függőségek feloldása](resource-manager-not-found-errors.md) |
| InvalidResourceType | Ellenőrizze az erőforrás írja be az a **típus** tulajdonság. | [Hivatkozása](/azure/templates/) |
| InvalidTemplate | Ellenőrizze a hibákat a sablon szintaxisát. | [Érvénytelen a sablon feloldása](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Ellenőrizze, hogy ha a fiókja tagja ugyanannak a bérlőnek a telepíti erőforráscsoportként működnek. | |
| LinkedInvalidPropertyId | Az erőforrás-azonosítója egy erőforrás nem megfelelően van feloldása. Ellenőrizze, hogy értékeket ad meg minden szükséges erőforrás-azonosítóhoz, beleértve az előfizetés-azonosító, az erőforráscsoport neve, erőforrástípus, szülő erőforrás neve (ha szükséges) és erőforrás neve. | |
| LocationRequired | Adjon meg egy helyet, az erőforrás. | [Hely beállítása](resource-manager-template-location.md) |
| MissingRegistrationForLocation | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát, és a támogatott helyek. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Az előfizetés regisztrálása az erőforrás-szolgáltató. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Erőforrás-szolgáltató regisztráció állapotának ellenőrzése. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| notFound | Ön próbált meg telepíteni a függő erőforrások és a szülő erőforrás párhuzamosan. Ellenőrizze, hogy hozzáadjon egy függőséget szüksége. | [-Függőségek feloldása](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A központi telepítést, amely meghaladja a kvótát, az előfizetés, erőforráscsoportban vagy régió művelettel próbálkozik. Ha lehetséges javítsa ki a környezetet, hogy a kvóták belül maradnak. Ellenkező esetben fontolja meg a kért módosítva lett a kvótákat. | [Hárítsa el a kvóták](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Győződjön meg arról, hogy a szülő erőforrás létezik-e a gyermek-erőforrás létrehozása előtt. | [Hárítsa el a szülő erőforrás](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | A megadott IP-cím az Azure által igényelt címtartománya tartalmazza. Fenntartott tartomány elkerülése érdekében IP-címének módosítása. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím az alhálózat tartományon kívül esik. Alhálózati tartományba eső IP-címének módosítása. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Néhány tulajdonság üzembe helyezett erőforrás nem módosítható. Egy erőforrás frissítésekor korlátozza a feljogosított tulajdonságainak módosítása. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A előfizetése tartalmaz egy erőforrás-házirendet, amely megakadályozza a központi telepítése során végrehajtani kívánt műveletet. Keresse meg a házirendet, amely blokkolja a műveletet. Ha lehetséges módosíthatja a környezetet, hogy megfeleljen a korlátozások vonatkoznak a szabályzat alól. | [Hárítsa el a házirendek](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Adjon meg egy erőforrás nevét, amely tartalmazza az foglalt név. | [Fenntartott erőforrások neve](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Várjon, amíg befejeződik a törlésre. | |
| ResourceGroupNotFound | Ellenőrizze a központi telepítés a célként megadott erőforráscsoport nevét. Ez már léteznie kell az előfizetéshez. Ellenőrizze az előfizetési kontextust. | [Az Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | A központi telepítés hivatkozik a erőforrása, amely nem oldható fel. Ellenőrizze, hogy Ön miként használja a a **hivatkozás** függvény a forgatókönyvhöz szükséges paramétereket tartalmazza. | [Hivatkozások feloldása](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A központi telepítés próbál létrehozni, amelyek mérete meghaladja a kvótát, az előfizetés, erőforráscsoportban vagy régió erőforrásokat. Ha lehetséges javítsa ki a kvóták belül marad az infrastruktúra. Ellenkező esetben fontolja meg a kért módosítva lett a kvótákat. | [Hárítsa el a kvóták](resource-manager-quota-errors.md) |
| SkuNotAvailable | Válassza ki a Termékváltozat (például a virtuális gép méretét), amely a kijelölt helyen érhető el. | [Hárítsa el a Termékváltozat](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Adjon egyedi nevet a tárfiók. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Adjon egyedi nevet a tárfiók. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Ellenőrizze az előfizetés, erőforráscsoport és a használni kívánt tárfiók nevére. | |
| SubnetsNotInSameVnet | A virtuális gépek csak rendelkezhet egy virtuális hálózatot. Több hálózati adapterrel való telepítésekor, ellenőrizze, hogy az azonos virtuális hálózatban tartoznak. | [Több hálózati adapter](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Hibakód keresése

Ha a telepítés során hibát észlel, erőforrás-kezelő hiba kódot ad vissza. A portál, a PowerShell vagy az Azure parancssori felület a hibaüzenet látható. Lehet, hogy a külső hibaüzenet túl általános hibaelhárítási. Keresse meg a belső állapotüzenetet, amely a hibával kapcsolatos részletes információkat tartalmaz. További információkért lásd: [határozza meg a hiba kódja](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Következő lépések
* Műveletek naplózásával kapcsolatos további tudnivalókért lásd: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* Azokról a műveletekről, a hibák megállapításához központi telepítése során további tudnivalókért lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
