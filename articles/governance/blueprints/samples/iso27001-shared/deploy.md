---
title: Az ISO 27001 megosztott szolgáltatások tervezetének mintatelepítése
description: Az ISO 27001 megosztott szolgáltatások tervezetminta lépéseit, beleértve a tervezet összetevő paraméterének részleteit.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920690"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 megosztott szolgáltatások tervezetminta telepítése

Az Azure Blueprints ISO 27001 megosztott szolgáltatások mintaminta üzembe helyezéséhez a következő lépéseket kell elvégezni:

> [!div class="checklist"]
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg az **ISO 27001: Megosztott szolgáltatások** tervezetminta mintát _az Egyéb minták csoportban,_ és válassza **a Használja ezt a mintát**lehetőséget.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve**: Adja meg az ISO 27001 megosztott szolgáltatások tervezetminta példányának nevét.
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Sok összetevő nek vannak paraméterei, amelyeket később definiálunk. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezetminta másolata testreszabható a környezetés az igények igényei szerint, de a módosítás elmozdíthatja az ISO 27001 szabványtól.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új lapon adja meg a tervezetminta egy **verzióját.** Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **meg módosítási megjegyzéseket,** például "Az ISO 27001 tervezetmintából közzétett első verzió". Ezután válassza a **Közzététel** lehetőséget a lap alján.

## <a name="assign-the-sample-copy"></a>A mintapéldány hozzárendelése

Miután a tervezetminta példánya sikeresen **közzélett,,** hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve mentették. Ez a lépés az, ahol paraméterek et biztosítanak, hogy a tervezet minta másolatának minden egyes központi telepítése egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza **a Blueprint hozzárendelése** lehetőséget a tervezetdefiníciós lap tetején.

1. Adja meg a tervezet hozzárendelésének paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések:** Válasszon ki egy vagy több előfizetést, amely abban a felügyeleti csoportban van, amelybe a tervezetminta másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez egy-egy hozzárendelés jön létre a megadott paraméterek használatával.
     - **Hozzárendelés neve**: A név előre ki van töltve a tervrajz neve alapján.
       Szükség szerint változtassa meg, vagy hagyja úgy, ahogy van.
     - **Hely**: Válassza ki azt a régiót, amelyben a felügyelt identitást létre szeretné hozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Blueprint-definíciós verzió:** Válasszon egy **közzétett** változata a tervezet minta példánya.

   - Hozzárendelés zárolása

     Válassza ki a környezetéhez való tervezetzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszer hez rendelt_ felügyelt identitás beállítást.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervezet definíciójában szereplő számos műtermék használja a konzisztencia biztosításához.

     - **Szervezet neve**: Adjon meg egy rövid nevet a szervezetnek. Ez a tulajdonság elsősorban erőforrások elnevezésére szolgál.
     - **Megosztott szolgáltatások alhálózati címelőtagja**: Adja meg a CIDR-jelölési értéket az üzembe helyezett erőforrások közös hálózatba kötéséhez.
     - **Megosztott szolgáltatások helye:** Meghatározza, hogy a műtermékek melyik helyre vannak telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméterbeállítást biztosítanak a hely üzembe helyezéséhez.
     - **Engedélyezett hely (Házirend: Blueprint kezdeményezés az ISO 27001-hez)**: Az erőforráscsoportok és erőforrások engedélyezett helyeit jelző érték.
     - **Log Analytics-munkaterület virtuálisgép-ügynökökhöz (Szabályzat: Az ISO 27001 tervezetkezdeményezése)**: Egy munkaterület erőforrás-azonosítóját adja meg. Ez a `concat` paraméter egy függvényt használ az erőforrás-azonosító létrehozásához.

   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. A teljes lista- vagy műtermék-paramétereket és azok leírását a [Műtermék paraméterek táblázatban láthatja.](#artifact-parameters-table)

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján. A tervrajz-hozzárendelés jön létre, és a műtermék üzembe helyezése kezdődik. A telepítés nagyjából egy órát vesz igénybe. A telepítés állapotának ellenőrzéséhez nyissa meg a tervhozzárendelést.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervezetminták **ingyenesek.** Az Azure-erőforrások [ára termékenként.](https://azure.microsoft.com/pricing/) A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a tervezet minta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Műtermék-paraméterek táblája

Az alábbi táblázat a tervezet összetevő paramétereinek listáját tartalmazza:

|Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert|(Nem kötelező) Az alapértelmezett érték _["nincs"]_.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert|(Nem kötelező) Az alapértelmezett érték _["nincs"]_.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Windows operációs rendszert|(Nem kötelező) Az alapértelmezett érték _["nincs"]_.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Windows operációs rendszert|(Nem kötelező) Az alapértelmezett érték _["nincs"]_.|
|Engedélyezett erőforrástípusok|Szabályzat-hozzárendelés|Engedélyezett erőforrástípusok|A telepíthető erőforrástípusok listája. Ez a lista a megosztott szolgáltatásokban telepített összes erőforrástípusból áll.|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett tárolási sk-k|Diagnosztikai naplók tárfiók skus engedélyezett listája. Az alapértelmezett érték _["Standard_LRS"]_.|
|Engedélyezett virtuálisgép-skus|Szabályzat-hozzárendelés|Az üzembe helyezhető virtuálisgép-skusok listája. Az alapértelmezett érték _a ["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Az ISO 27001 tervezetkezdeményezése|Szabályzat-hozzárendelés|A diagnosztikai naplók naplózására szolgáló erőforrástípusok|Naplózandó erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Az elfogadható értékek az [Azure Monitor diagnosztikai naplóinak sémájában](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)találhatók.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-sharedsvsc-log-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Log Analytics-sablon|Resource Manager-sablon|Szolgáltatásszint|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték _pernode_.|
|Log Analytics-sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Log Analytics-sablon|Resource Manager-sablon|Hely|A Log Analytics-munkaterület létrehozásához használt terület. Az alapértelmezett érték _US2 nyugati helyi_érték.|
|Hálózati erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-sharedsvcs-net-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Hálózati erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Azure tűzfalsablon|Resource Manager-sablon|Azure tűzfal privát IP-címe|Az [Azure-tűzfal](../../../../firewall/overview.md)privát IP-címének konfigurálása. Ez az érték a megosztott szolgáltatások alhálózatának alapértelmezett útvonaltáblája ként is használatos. Az **Azure Firewall alhálózati címelőtagban**definiált CIDR-jelölés része kell, hogy legyen. Az alapértelmezett érték _10.0.4.4_.|
|Azure tűzfalsablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Hálózati biztonsági csoport sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Virtuális hálózat címelőtagja|A virtuális hálózat CIDR jelölése. Az alapértelmezett érték _10.0.0.0/16_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Virtuális hálózati DDoS-védelem engedélyezése|A virtuális hálózat DDoS-védelmének konfigurálása. Az alapértelmezett érték _igaz_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Megosztott szolgáltatások alhálózati cím előtagja|A megosztott szolgáltatások alhálózatának CIDR-jelölése. Az alapértelmezett érték _10.0.0.0/24_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|DMZ alhálózati cím előtagja|A DMZ alhálózat CIDR-jelölése. Az alapértelmezett érték _10.0.1.0/24_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Alkalmazásátjáró alhálózati címelőtagja|Az alkalmazásátjáró alhálózatának CIDR-jelölése. Az alapértelmezett érték _10.0.2.0/24_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Virtuális hálózati átjáró alhálózati altagja előtag|A virtuális hálózati átjáró alhálózatCIDR-jelölése. Az alapértelmezett érték _10.0.3.0/24_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Az Azure Firewall alhálózati címelőtagja|Az [Azure tűzfal](../../../../firewall/overview.md) alhálózatának CIDR-jelölése. Tartalmaznia kell az **Azure tűzfal privát IP-paraméter.**|
|Key Vault erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-sharedsvcs-kv-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Key Vault erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox felügyeleti felhasználónév|A jumpbox felhasználóneve. A **Jumpbox sablonban**azonos tulajdonságértéknek kell lennie. Az alapértelmezett érték _jb-admin-user_.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox admin ssh kulcs vagy jelszó|A jumpboxon lévő fiók kulcsa vagy jelszava. A **Jumpbox sablonban**azonos tulajdonságértéknek kell lennie. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazdai felhasználónév|Az Active Directory virtuális gép eléréséhez és más virtuális gépek tartományhoz való csatlakoztatásához használt felhasználónév. Meg kell egyeznie **a Tartományfelügyeleti felhasználói** tulajdonság értékével az **Active Directory tartományi szolgáltatások sablonban.** Az alapértelmezett érték _a tartomány-rendszergazda-felhasználó_.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó|Tartományi rendszergazda felhasználó jelszavát. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|AAD-objektum azonosítója|A Key Vault-példányhoz hozzáférést igénylő fiók AAD-objektumazonosítója. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné találni ezt az értéket az Azure Portalon, keresse meg és válassza a "Felhasználók" lehetőséget a _Szolgáltatások_csoportban. A _Név_ mezővel szűrheti a fiók nevét, és kiválaszthatja azt a fiókot. A _Felhasználói profil_ lapon kattintson a "Másoláshoz" ikonra az _Objektumazonosító_mellett .  |
|Key Vault sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault Termékváltozat|A létrehozott Key Vault termékváltozatát adja meg. Az alapértelmezett érték _prémium_.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-sharedsvcs-jb-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Jumpbox sablon|Resource Manager-sablon|Jumpbox felügyeleti felhasználónév|A jumpbox virtuális gépek eléréséhez használt felhasználónév. A **Key Vault-sablonban**azonos tulajdonságértéknek kell lennie. Az alapértelmezett érték _jb-admin-user_.|
|Jumpbox sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszó (Key Vault erőforrásazonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" nevet, és cserélje le `{subscriptionId}` az előfizetés-azonosítóra és `{orgName}` a szervezet **névtervezet** paraméterére.|
|Jumpbox sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszó (Key Vault titkos neve)|A jumpbox adminisztrátor felhasználóneve. Meg kell egyeznie a **Key Vault sablontulajdonságának** **Jumpbox admin felhasználónevének értékével.**|
|Jumpbox sablon|Resource Manager-sablon|Jumpbox operációs rendszer|A jumpbox vm operációs rendszerét határozza meg. Az alapértelmezett érték a _Windows_.|
|Active Directory tartományi szolgáltatások erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-sharedsvcs-adds-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Active Directory tartományi szolgáltatások erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai felhasználónév|Az ADDS jumpbox felhasználóneve. A **Key Vault-sablonban**azonos tulajdonságértéknek kell lennie. Az alapértelmezett érték _a hozzáadás-rendszergazda-felhasználó_.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó (key vault-erőforrásazonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" nevet, és cserélje le `{subscriptionId}` az előfizetés-azonosítóra és `{orgName}` a szervezet **névtervezet** paraméterére.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó (kulcstároló titkos neve)|A tartomány adminisztrátorának felhasználóneve. Meg kell egyeznie a **Key Vault sablontulajdonságának** **Értéke Tartományrendszergazda felhasználóneve**.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték _a contoso.com._|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai felhasználó|A rendszergazda AD-fiókjának felhasználóneve és az eszközök AD-tartományhoz való csatlakoztatásához. Meg kell egyeznie **az AD rendszergazdai felhasználónév** tulajdonságértékével a **Key Vault sablonban.** Az alapértelmezett érték _a tartomány-rendszergazda-felhasználó_.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó|Állítsa be a Key Vault adatait a jelszó tárolásához. Nincs alapértelmezett érték, és nem hagyható üresen.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 Shared Services tervezetminta telepítésének lépéseit, az alábbi cikkekben megismerkedhet az architektúrával és a vezérlésleképezéssel:

> [!div class="nextstepaction"]
> [ISO 27001 Megosztott szolgáltatások tervezete – Áttekintés Az](./index.md)
> [ISO 27001 megosztott szolgáltatások tervrajza – Vezérlőleképezés](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)
