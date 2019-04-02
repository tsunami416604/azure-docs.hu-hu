---
title: Minta – ISO 27001, a megosztott szolgáltatások tervezetet – a telepítés lépéseit
description: Telepítés lépéseit, az ISO 27001, a megosztott szolgáltatások tervezet minta.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f49951d0a2ec738df9946edc7f44820c2cde975f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804332"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001, a megosztott szolgáltatások tervezet-minta üzembe helyezése

Az Azure tervezetek ISO 27001 megosztott szolgáltatások tervezet minta üzembe helyezéséhez az alábbi lépéseket kell tenni:

> [!div class="checklist"]
> - Hozzon létre egy új tervezet arról a minta
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először meg a tervezet minta létrehoz egy új tervezet alapszintű, a minta az környezet.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **ISO 27001: Megosztott szolgáltatások** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a az ISO 27001, a megosztott szolgáltatások tervezet minta példányának nevét.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Az összetevők számos rendelkezik, paraméterek, amelyeket később fogunk meghatározni. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet minta másolatát is beállítható, hogy a környezet és az igényeinek, azonban, hogy a módosítás lehet, hogy esniük, az ISO 27001 szabványnak.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. A jobb oldalon az új lap, adja meg egy **verzió** a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója közzé az ISO 27001 tervezet mintából." Válassza ki **közzététel** az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolási hozzárendelése

Miután a tervezet minta másolata sikeresen megtörtént **közzétett**, előfizetésre történő mentése felügyeleti csoporton belül is hozzárendelhető. Ez a lépés nem, melyekben a paraméterek vannak-e adva az, hogy az egyes telepítések a példány a tervezet minta egyedi.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több az előfizetéseket, amelyek a felügyeleti csoportban, a tervezet minta másolatának mentése. Ha egynél több előfizetéssel, hozzárendelést a megadott paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: Az Ön a tervrajz neve alapján előre megadott név.
       Szükség szerint változtassa meg, vagy hagyja üresen, mivel.
     - **Hely**: Válassza ki a régiót, a felügyelt identitás kell létrehozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Tervrajz-definíció verziója**: Válasszon ki egy **közzétett** a tervezet minta másolatának verzióját.

   - Hozzárendelés zárolása

     Válassza ki a tervezet zárolás, a környezet beállítása. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás lehetőséget.

   - Tervparaméterek

     Ebben a szakaszban definiált paraméterek segítségével számos, az összetevőket a tervezetdefiníciót a konzisztenciát.

     - **Szervezet neve**: Adjon meg egy rövid nevet a szervezet számára. Ez a tulajdonság erőforrások elnevezési elsősorban.
     - **Megosztott szolgáltatások alhálózati cím előtagja**: Adja meg a CIDR-jelölés érték hálózati az üzembe helyezett erőforrások együtt.
     - **Megosztott szolgáltatások hely**: Meghatározza, hogy milyen helyet az összetevők telepítve vannak. Nem minden szolgáltatás az összes hely érhető el. Ilyen szolgáltatások üzembe helyezése összetevők lehetőséget nyújtanak olyan paramétert a lehívandó összetevő telepítése a helyen.
     - **Engedélyezett helyének (szabályzat: Kezdeményezés tervezetet ISO 27001)**: Érték, amely azt jelzi, hogy az engedélyezett helyek, az erőforráscsoport és erőforrások.
     - **Analytics-munkaterületen keresse meg a Virtuálisgép-ügynökök (szabályzat: Kezdeményezés tervezetet ISO 27001)**: Itt adhatja meg a munkaterület erőforrás-Azonosítóját. Ezt a paramétert használ egy `concat` függvény létrehozására, az erőforrás-azonosító.

   - Összetevő paraméterei

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. A teljes listát vagy összetevő paraméterek és ezek leírását: [összetevő paramétereket tartalmazó](#artifact-parameters-table).

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján. A tervezet-hozzárendelést jön létre, és összetevő telepítési kezdődik. Üzembe helyezés eltarthat nagyjából egy óra. Üzembe helyezés állapotának ellenőrzéséhez nyissa meg a tervezet-hozzárendelést.

> [!WARNING]
> Az Azure-tervek szolgáltatás és a beépített tervezet minták **díjmentes**. Az Azure-erőforrások [termék díjszabása](https://azure.microsoft.com/en-us/pricing/). Használja a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) a tervezet minta által üzembe helyezett erőforrások futtatásával járó költségeket megbecsülheti.

## <a name="artifact-parameters-table"></a>Összetevő paraméterek táblában

A következő táblázat felsorolja a tervezet összetevő paraméterek:

|Elemnév|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|[Előzetes verzió]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hogy támogatott Linux operációs rendszer hatókör hozzáadása Virtuálisgép-rendszerképek listája|(Nem kötelező) Alapértelmezett érték _["none"]_.|
|[Előzetes verzió]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hogy támogatott Linux operációs rendszer hatókör hozzáadása Virtuálisgép-rendszerképek listája|(Nem kötelező) Alapértelmezett érték _["none"]_.|
|[Előzetes verzió]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hatókör hozzáadása a Windows operációs rendszer rendelkezik támogatott Virtuálisgép-rendszerképek listája|(Nem kötelező) Alapértelmezett érték _["none"]_.|
|[Előzetes verzió]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hatókör hozzáadása a Windows operációs rendszer rendelkezik támogatott Virtuálisgép-rendszerképek listája|(Nem kötelező) Alapértelmezett érték _["none"]_.|
|Engedélyezett erőforrástípusok|Szabályzat-hozzárendelés|Engedélyezett erőforrástípusok|Engedélyezett üzembe helyezhető erőforrástípusok listája. Ebben a listában megosztott szolgáltatásaiban üzembe helyezett összes erőforrástípus tevődik össze.|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett termékváltozatok tárhelyet|Diagnosztikai lista engedélyezett termékváltozatok tárolási naplózza. Alapértelmezett érték _["Standard_LRS"]_.|
|Engedélyezett VM-termékváltozatok|Szabályzat-hozzárendelés|VM-termékváltozatok listájának engedélyezett üzembe helyezni. Alapértelmezett érték _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|ISO 27001-alapú tervkezdeményezés|Szabályzat-hozzárendelés|Erőforrástípusok diagnosztikai naplók|Ha diagnosztikai naplót a beállítás nincs engedélyezve a naplózandó erőforrástípusok listája. Elfogadható értékek található [Azure Monitor-diagnosztikai naplók sémák](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics-erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-sharedsvsc-log-rg` , hogy az erőforráscsoport egyedi.|
|Log Analytics-erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Log Analytics-sablon|Resource Manager-sablon|Szolgáltatásszint|A Log Analytics-munkaterület szintjének beállítása. Alapértelmezett érték _PerNode_.|
|Log Analytics-sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Log Analytics-sablon|Resource Manager-sablon|Hely|A Log Analytics-munkaterület létrehozásához használt régió. Alapértelmezett érték _USA 2. nyugati_.|
|Hálózati erőforrás-csoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-sharedsvcs-net-rg` , hogy az erőforráscsoport egyedi.|
|Hálózati erőforrás-csoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Azure Firewall-sablon|Resource Manager-sablon|Azure Firewall magánhálózati IP-címe|Konfigurálja a magánhálózati IP-címét a [Azure tűzfal](../../../../firewall/overview.md). Ez az érték a megosztott szolgáltatások alhálózata alapértelmezett útválasztási tábla is szolgál. A meghatározott CIDR jelölésrendszer szerint kell **Azure tűzfal alhálózati cím előtagja**. Alapértelmezett érték _címe: 10.0.4.4_.|
|Azure Firewall-sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Hálózati biztonsági csoport – sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Virtuális hálózat címelőtagja|A virtuális hálózat CIDR-jelölésrendszerben. Alapértelmezett érték _10.0.0.0/16_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Virtuális hálózat DDoS elleni védelmének engedélyezése|Konfigurálja a virtuális hálózat DDoS-védelem. Alapértelmezett érték _igaz_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Megosztott szolgáltatások alhálózati cím előtagja|A megosztott szolgáltatások alhálózata CIDR-jelölésrendszerben. Alapértelmezett érték _10.0.0.0/24_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|DMZ alhálózati címelőtagja|A DMZ-alhálózat CIDR-jelölésrendszerben. Alapértelmezett érték _10.0.1.0/24_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Application Gateway-átjáró alhálózati címelőtagja|Az application gateway alhálózatának CIDR-jelölésrendszerben. Alapértelmezett érték _10.0.2.0/24_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Virtuális hálózati átjáró alhálózati címelőtagja|A virtuális hálózati átjáró-alhálózat CIDR-jelölésrendszerben. Alapértelmezett érték _10.0.3.0/24_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Azure Firewall alhálózati címelőtagja|A CIDR-jelölés a [Azure tűzfal](../../../../firewall/overview.md) alhálózat. Tartalmaznia kell a **Azure tűzfal magánhálózati IP-címe** paraméter.|
|A Key Vault-erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-sharedsvcs-kv-rg` , hogy az erőforráscsoport egyedi.|
|A Key Vault-erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Kulcstartósablon|Resource Manager-sablon|Jumpbox-rendszergazda felhasználóneve|A felhasználónév a jumpbox számára. Meg kell egyeznie az azonos tulajdonság értéke **Jumpbox sablon**. Alapértelmezett érték _jb rendszergazda_.|
|Kulcstartósablon|Resource Manager-sablon|Jumpbox-rendszergazda SSH-kulcsa vagy jelszava|Kulcs vagy a jumpbox fiókjához tartozó jelszót. Meg kell egyeznie az azonos tulajdonság értéke **Jumpbox sablon**. Nem alapértelmezett értékét, és nem lehet üres.|
|Kulcstartósablon|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|A felhasználónév az Active Directory virtuális gép eléréséhez, és más virtuális gépek tartományhoz csatlakoztatására. Meg kell egyeznie **tartományi rendszergazda felhasználó** tulajdonságértéket **Active Directory Domain Services sablon**. Alapértelmezett érték _tartományi rendszergazda_.|
|Kulcstartósablon|Resource Manager-sablon|Tartományi rendszergazda jelszava|Tartományi rendszergazda felhasználó jelszavát. Nem alapértelmezett értékét, és nem lehet üres.|
|Kulcstartósablon|Resource Manager-sablon|AAD-objektum-azonosító|A fiókot, amelyet hozzá kell férnie a Key Vault-példány AAD-objektum azonosítója. Nem alapértelmezett értékét, és nem lehet üres. Keresse meg ezt az értéket az Azure Portalról, keresse meg és válassza ki a "Felhasználók" alatt _szolgáltatások_. Használja a _neve_ be a fiók neve szűrését, és válassza ki azt a fiókot. Az a _felhasználói profil_ lapra, jelölje be a "Kattintson a másoláshoz" ikon mellett a _Objektumazonosító_.  |
|Kulcstartósablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Kulcstartósablon|Resource Manager-sablon|Key Vault-termékváltozat|Adja meg a létrehozott Key Vault-Termékváltozat. Alapértelmezett érték _prémium_.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-sharedsvcs-jb-rg` , hogy az erőforráscsoport egyedi.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox-rendszergazda felhasználóneve|A jumpbox virtuális gép eléréséhez használt felhasználónév. Meg kell egyeznie az azonos tulajdonság értéke **Key Vault sablon**. Alapértelmezett érték _jb rendszergazda_.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-Azonosítóját. Használat "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv", és cserélje le `{subscriptionId}` meg előfizetési azonosítóját és `{orgName}` együtt a  **Szervezet neve** tervezetet paraméter.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszó (a kulcstartó titkos kód nevét)|A jumpbox rendszergazda felhasználóneve Egyeznie kell azzal az értékkel **Key Vault sablon** tulajdonság **Jumpbox rendszergazdai felhasználónév**.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox operációs rendszere|A jumpbox virtuális gép operációs rendszere határozza meg. Alapértelmezett érték _Windows_.|
|Az Active Directory Domain Services-erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-sharedsvcs-adds-rg` , hogy az erőforráscsoport egyedi.|
|Az Active Directory Domain Services-erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|Az ADDS jumpbox felhasználónév. Meg kell egyeznie az azonos tulajdonság értéke **Key Vault sablon**. Alapértelmezett érték _ad-rendszergazda-user_.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartományi rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-Azonosítóját. Használat "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv", és cserélje le `{subscriptionId}` meg előfizetési azonosítóját és `{orgName}` együtt a  **Szervezet neve** tervezetet paraméter.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartományi rendszergazdai jelszó (a kulcstartó titkos kód nevét)|A tartományi rendszergazda felhasználóneve Egyeznie kell azzal az értékkel **Key Vault sablon** tulajdonság **tartományi rendszergazdai felhasználónév**.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartománynév|Az Active Directory, a példaszkript által létrehozott neve. Alapértelmezett érték _contoso.com_.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartomány-rendszergazdai felhasználó|A felhasználónév a AD rendszergazdai fiókkal és az eszközök az AD-tartományhoz történő csatlakoztatásához. Meg kell egyeznie **AD rendszergazdai felhasználónév** tulajdonságértéket **Key Vault sablon**. Alapértelmezett érték _tartományi rendszergazda_.|
|Active Directory tartományi szolgáltatások sablonja|Resource Manager-sablon|Tartományi rendszergazda jelszava|Adja meg a jelszót tároló a Key Vault részleteit. Nem alapértelmezett értékét, és nem lehet üres.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001, a megosztott szolgáltatások tervezet-minta üzembe helyezése a lépéseket, keresse fel az architektúra és a vezérlő leképezés a következő cikkeket:

> [!div class="nextstepaction"]
> [ISO 27001, a megosztott szolgáltatások tervezet - áttekintés](./index.md)
> [ISO 27001, a megosztott szolgáltatások tervezet - vezérlő leképezés](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.