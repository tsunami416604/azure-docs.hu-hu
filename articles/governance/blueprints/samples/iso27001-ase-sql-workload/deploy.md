---
title: Az ISO 27001 ASE/SQL számítási terv minta telepítése
description: Az ISO 27001 App Service-környezet/SQL-adatbázis számítási tervminta lépéseit telepítheti, beleértve a tervezet összetevő paraméterének részleteit.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922576"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Az ISO 27001 App Service-környezet/SQL-adatbázis számítási tervének mintája

Az Azure Blueprints ISO 27001 App Service-környezet/SQL Database számítási terv minta központi telepítéséhez a következő lépéseket kell elvégezni:

> [!div class="checklist"]
> - Az [ISO 27001 megosztott szolgáltatások](../iso27001-shared/index.md) tervezetminta telepítése
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 megosztott szolgáltatások tervezetminta telepítése

A tervezetminta üzembe helyezése előtt az [ISO 27001 megosztott szolgáltatások](../iso27001-shared/index.md) tervezetminta kell telepíteni a cél-előfizetés. Az ISO 27001 megosztott szolgáltatások tervezetminta sikeres telepítése nélkül ez a tervezetminta hiányozni fog az infrastruktúra-függőségekből, és sikertelen lesz az üzembe helyezés során.

> [!IMPORTANT]
> Ezt a tervezetmintát ugyanabban az előfizetésben kell hozzárendelni, mint az [ISO 27001 megosztott szolgáltatások](../iso27001-shared/index.md) mintaminta.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg az **ISO 27001: ASE/SQL workload** tervezet minta _az Egyéb minták csoportban,_ és válassza **a Használja ezt a mintát.**

1. Adja meg a tervminta _alapjait:_

   - **Blueprint name**: Adja meg az ISO 27001 ASE/SQL számítási terv minta példányának nevét.
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
     - **Megosztott szolgáltatáselőfizetés-azonosító:** Előfizetés-azonosító, ahol az [ISO 27001 megosztott szolgáltatások](../iso27001-shared/index.md) tervezetminta van hozzárendelve.
     - **Alapértelmezett alhálózati címelőtag**: A virtuális hálózat alapértelmezett alhálózatának CIDR-jelölése.
       Az alapértelmezett érték _10.1.0.0/16_.
     - **Számítási feladatok helye:** Meghatározza, hogy a műtermékek melyik helyre vannak telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméterbeállítást biztosítanak a hely üzembe helyezéséhez.

   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. A teljes lista- vagy műtermék-paramétereket és azok leírását a [Műtermék paraméterek táblázatban láthatja.](#artifact-parameters-table)

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján. A tervrajz-hozzárendelés jön létre, és a műtermék üzembe helyezése kezdődik. A telepítés nagyjából egy órát vesz igénybe. A telepítés állapotának ellenőrzéséhez nyissa meg a tervhozzárendelést.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervezetminták **ingyenesek.** Az Azure-erőforrások [ára termékenként.](https://azure.microsoft.com/pricing/) A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a tervezet minta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Műtermék-paraméterek táblája

Az alábbi táblázat a tervezet összetevő paramétereinek listáját tartalmazza:

|Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Log Analytics erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-workload-log-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Log Analytics-sablon|Resource Manager-sablon|Szolgáltatásszint|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték _pernode_.|
|Log Analytics-sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Log Analytics-sablon|Resource Manager-sablon|Hely|A Log Analytics-munkaterület létrehozásához használt terület. Az alapértelmezett érték _US2 nyugati helyi_érték.|
|Hálózati erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-workload-net-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Hálózati erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Hálózati biztonsági csoport sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Azure tűzfal privát IP-címe|Az [Azure-tűzfal](../../../../firewall/overview.md)privát IP-címének konfigurálása. Az _ISO 27001: Shared Services_ må±termÃ©k **Azure Firewall alhálózati címelőtag**ban meghatározott CIDR-jelölés része kell, hogy legyen. Az alapértelmezett érték _10.0.4.4_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Megosztott szolgáltatások Előfizetés azonosítója|A virtuális hálózat és a megosztott szolgáltatások közötti társviszony-létesítés engedélyezéséhez használt érték.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Virtuális hálózat címelőtagja|A virtuális hálózat CIDR jelölése. Az alapértelmezett érték _10.1.0.0/16_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|Alapértelmezett alhálózati címelőtag|A virtuális hálózat alapértelmezett alhálózatának CIDR jelölése. Az alapértelmezett érték _10.1.0.0/16_.|
|Virtuális hálózat és útvonaltábla sablon|Resource Manager-sablon|IP-cím hozzáadása|Az első HOZZÁTESZI virtuális gép IP-címe. Ez az érték egyéni vnet DNS-ként használatos.|
|Key Vault erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-workload-kv-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Key Vault erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Key Vault sablon|Resource Manager-sablon|AAD-objektum azonosítója|A Key Vault-példányhoz hozzáférést igénylő fiók AAD-objektumazonosítója. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné találni ezt az értéket az Azure Portalon, keresse meg és válassza a "Felhasználók" lehetőséget a _Szolgáltatások_csoportban. A _Név_ mezővel szűrheti a fiók nevét, és kiválaszthatja azt a fiókot. A _Felhasználói profil_ lapon kattintson a "Másoláshoz" ikonra az _Objektumazonosító_mellett .|
|Key Vault sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault Termékváltozat|A létrehozott Key Vault termékváltozatát adja meg. Az alapértelmezett érték _prémium_.|
|Key Vault sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai felhasználóneve|Az Azure SQL Server eléréséhez használt felhasználónév. Az Azure SQL **Database-sablonban**azonos tulajdonságértéknek kell lennie. Az alapértelmezett érték az _sql-admin-user_.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-workload-azsql-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|Azure SQL Database-sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai felhasználóneve|Az Azure SQL Server felhasználóneve. A **Key Vault-sablonban**azonos tulajdonságértéknek kell lennie. Az alapértelmezett érték az _sql-admin-user_.|
|Azure SQL Database-sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault erőforrásazonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" címet, és cserélje le `{subscriptionId}` az előfizetés-azonosítóval és `{orgName}` a szervezet **neve** tervezet paraméterrel.|
|Azure SQL Database-sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault titkos neve)|Az SQL Server rendszergazdájának felhasználóneve. Meg kell egyeznie a **Key Vault sablontulajdonságának** **Azure SQL Server felügyeleti felhasználónevének.**|
|Azure SQL Database-sablon|Resource Manager-sablon|Bejelentkezésmegőrzés napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|
|Azure SQL Database-sablon|Resource Manager-sablon|AAD felügyeleti objektum azonosítója|Annak a felhasználónak az AAD-objektumazonosítója, aki Active Directory-rendszergazdaként lesz hozzárendelve. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné találni ezt az értéket az Azure Portalon, keresse meg és válassza a "Felhasználók" lehetőséget a _Szolgáltatások_csoportban. A _Név_ mezővel szűrheti a fiók nevét, és kiválaszthatja azt a fiókot. A _Felhasználói profil_ lapon kattintson a "Másoláshoz" ikonra az _Objektumazonosító_mellett .|
|Azure SQL Database-sablon|Resource Manager-sablon|AAD admin bejelentkezés|Jelenleg a Microsoft-fiókok (például live.com vagy outlook.com) nem állíthatók be rendszergazdaként. Csak a szervezeten belüli felhasználók és biztonsági csoportok állíthatók be rendszergazdaként. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné találni ezt az értéket az Azure Portalon, keresse meg és válassza a "Felhasználók" lehetőséget a _Szolgáltatások_csoportban. A _Név_ mezővel szűrheti a fiók nevét, és kiválaszthatja azt a fiókot. A _Felhasználói profil_ lapon másolja a _Felhasználónevet_.|
|App Service-környezet erőforráscsoport|Erőforráscsoport|Név|**Zárolt** – Összefűzi a `-workload-ase-rg` **szervezet nevét,** hogy az erőforráscsoport egyedi legyen.|
|App Service-környezet erőforráscsoport|Erőforráscsoport|Hely|**Zárolva** – a tervezet paraméterét használja.|
|App Service-környezet sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték _a contoso.com._|
|App Service-környezet sablon|Resource Manager-sablon|ASE-hely|Az App Service-környezet helye. Az alapértelmezett érték _US2 nyugati helyi_érték.|
|App Service-környezet sablon|Resource Manager-sablon|Az Alkalmazásátjáró naplójának megőrzése napokban|Adatmegőrzés napokon belül. Az alapértelmezett érték _365_.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 App Service-környezet/SQL-adatbázis munkaterhelés-tervezetmintájának üzembe helyezésének lépéseit, az alábbi cikkekben megismerkedhet az architektúrával és a vezérlésleképezéssel:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database workload blueprint – Áttekintés](./index.md)
> [Az ISO 27001 App Service Környezet/SQL Database munkaterhelési terv - Vezérlő leképezés](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)