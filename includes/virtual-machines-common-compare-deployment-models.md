


## A számítás, a hálózatkezelés és a tárolás Azure Resource Manager-alapú üzemi modellben való integrálásának előnyei

Az Azure Resource Manager-alapú üzemi modell lehetővé teszi az előre elkészített alkalmazássablonok egyszerű használatát vagy egy JSON-sablon összeállítását, és annak segítségével számítási, hálózati és tárolási erőforrások az Azure-on való telepítését és kezelését. Ebben a szakaszban végigvesszük az erőforrások az Azure Resource Manager-alapú üzemi modell használatával való telepítésének előnyeit.

-   Összetett megoldás egyszerűbben – Egy megosztható sablonfájlból kiépíthet és integrálhat olyan összetett alkalmazásokon, illetve együttműködhet rajtuk, amelyek az Azure-erőforrások teljes skáláját használják (Websites, SQL-adatbázisok, Virtual Machines vagy virtuális hálózatok)
-   A megoldás rugalmassága lehetővé teszi az ismétlődő telepítések elvégzését a fejlesztések, a devOps és a rendszergazdák számára, amikor ugyanazt a sablonfájlt használja
-   A virtuálisgép-bővítmények szoros integrációja (Egyéni parancsfájlok, DSC, Chef, Puppet stb.) az Azure Resource Managerrel egy sablonfájlban lehetővé teszi a virtuális gépen belüli beállítási konfiguráció egyszerű összehangolását
-   Címkék meghatározása, és ezen címkék terjesztése a számítási, hálózati és tárolási erőforrásokhoz
-   Egyszerű és pontos szervezeti erőforráshozzáférés-kezelés az Azure szerepköralapú hozzáférés-vezérlésével (RBAC)
-   Egyszerűsített Frissítés az eredeti sablon módosításával, majd újratelepítésével


## Fejlesztések a számítási, hálózati és tárolási API-k területén az Azure Resource Managerben

A fent említett előnyökön túl van néhány jelentős teljesítménybeli fejlesztés is a kiadott API-kban:

-   A virtuális gépek tömeges és párhuzamos telepítésének lehetősége
-   3 tartalék tartomány támogatása a rendelkezésre állási csoportokban
-   Továbbfejlesztett Custom Script bővítmény, amely lehetővé teszi a parancsfájlok megadását bármely nyilvánosan hozzáférhető egyéni URL-ről
- A Virtual Machines integrálása az Azure Key Vaulttal a rendkívül biztonságos tárolás és a bizalmas anyagok [FIPS használatával érvényesített](http://wikipedia.org/wiki/FIPS_140-2) [hardveres biztonsági modulokból](http://wikipedia.org/wiki/Hardware_security_module) való magántelepítése érdekében
-   Alapszintű hálózatkezelési elemeket biztosít az API-kon keresztül, így az ügyfelek bonyolult, hálózati adaptereket, terheléselosztókat és virtuális hálózatokat tartalmazó alkalmazásokat hozhatnak létre
-   A Network Interfaces nevű új objektum lehetővé teszi az összetett hálózati konfiguráció megtartását és újrahasznosítását a virtuális gépeken
-   A terheléselosztók első osztályú erőforrásként lehetővé teszik az IP-címek hozzárendelését
-   A részletes Virtual Network API-k lehetővé teszik az egyéni virtuális hálózatok egyszerűsítését

## Fogalmi változások az új API-k bevezetése kapcsán

Ez a szakasz a legfontosabb fogalmi eltéréseket tárgyalja a jelenleg rendelkezésre álló XML-alapú API-k és a számítás-, hálózat- és tárkezelő Azure Resource Manageren keresztül elérhető JSON-alapú API-k között.

 Elem | Azure szolgáltatásfelügyelet (XML-alapú)    | Számítás-, hálózat- és társzolgáltatók (JSON-alapú)
 ---|---|---
| Felhőszolgáltatás a virtuális gépekhez |  A Cloud Service egy tároló volt a virtuális gépekhez, amely platform és a terheléselosztás Rendelkezésre állását is igényelte. | Az új modell használatával a Cloud Service már nem szükséges objektum egy virtuális gép létrehozásához. |
| Rendelkezésre állási csoportok | A platform felé való rendelkezésre állást azonos „AvailabilitySetName” konfigurálásával lehetett jelezni a virtuális gépeken. A tartalék tartományok maximális száma 2 volt. | A Rendelkezésre állási csoport egy Microsoft.Compute szolgáltató által közzétett erőforrás. A nagy rendelkezésre állást igénylő virtuális gépeket szerepeltetni kell a Rendelkezésre állási csoportban. A tartalék tartományok maximális száma mostantól 3. |
| Affinitáscsoportok | Virtuális hálózatok létrehozásához szükség volt Affinitáscsoportokra. A regionális virtuális hálózatok bevezetésével erre már nem volt szükség. |Egyszerűbben fogalmazva az Azure Resource Manageren keresztül közzétett API-kban nem létezik az Affinitáscsoportok koncepciója. |
| Terheléselosztás    | Egy felhőszolgáltatás létrehozása egy implicit terheléselosztót biztosít a telepített virtuális gépekhez. | A Load Balancer egy Microsoft.Network szolgáltató által közzétett erőforrás. A terheléselosztást igénylő virtuális gépek elsődleges hálózati adapterének hivatkoznia kell a terheléselosztóra. Egy terheléselosztó lehet külső vagy belső. [További információk.](../articles/resource-groups-networking.md) |
|Virtuális IP-cím | A Cloud Services kap egy alapértelmezett VIP-t (Virtuális IP-cím), amikor egy virtuális gépet hozzáadnak egy felhőszolgáltatáshoz. A Virtuális IP-cím az implicit terheléselosztóhoz társított cím.   | A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet Statikus (Fenntartott) vagy Dinamikus. A dinamikus nyilvános IP-címek hozzárendelhetők egy terheléselosztóhoz. A nyilvános IP-címek védelme biztonsági csoportok segítségével biztosítható. |
|Fenntartott IP-címek|   Az Azure-ban fenntarthat egy IP-címet, és társíthatja egy felhőszolgáltatáshoz, hogy biztosítsa az IP-cím állandóságát.   | A nyilvános IP-címek létrehozhatók „Statikus” módban, amely ugyanazokat a képességeket biztosítja, mint a „Fenntartott IP-cím”. A statikus nyilvános IP-címek jelenleg csak terheléselosztóhoz rendelhetők hozzá. |
|Virtuális gépenként megadott nyilvános IP-cím (PIP) | A nyilvános IP-címek közvetlenül is hozzárendelhetők egy virtuális géphez. | A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet Statikus (Fenntartott) vagy Dinamikus. Jelenleg azonban csak dinamikus nyilvános IP-címek rendelhetők hozzá hálózati adapterekhez, hogy virtuális gépenként legyen meghatározva egy nyilvános IP-cím. |
|Végpontok| A virtuális gépen konfigurálni kell a bemeneti végpontokat, hogy bizonyos portok csatlakoztathatóvá váljanak. A virtuális gépekhez való csatlakozás egyik legelterjedtebb módja a bemeneti végpontok beállítása. | A bejövő NAT-szabályok konfigurálhatók a terheléselosztókon, így azonos képességek érhetők el a végpontok engedélyezésére adott portokon a virtuális gépekhez való csatlakozás céljából. |
|DNS-név| Egy felhőszolgáltatás egy implicit globálisan egyedi DNS-nevet kap. Például: `mycoffeeshop.cloudapp.net`. | A DNS-nevek opcionális paraméterek, amelyek egy nyilvános IP-cím erőforráson adhatók meg. Az FQDN formátum a következő lesz: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Hálózati illesztők | Az elsődleges és másodlagos hálózati adapter és tulajdonságai egy virtuális gép hálózati konfigurációjaként voltak megadva. | A hálózati adapter egy Microsoft.Network szolgáltató által közzétett erőforrás. A hálózati adapter életciklusa nincs a virtuális géphez kötve. |

## A virtuális gépekhez elérhető Azure-sablonok – első lépések

Az Azure-sablonokkal való ismerkedés során először használja a platform fejlesztéséhez és telepítéséhez biztosított különböző eszközöket.

### Azure Portal

Az Azure portál továbbra is lehetőséget biztosít a Virtual Machines klasszikus üzembe helyezési modelljének és a Virtual Machines Resource Manager-alapú üzemi modelljének párhuzamos telepítésére. Az Azure portál az egyéni sablonok alapján történő üzembe helyezést is lehetővé teszi.

### Azure PowerShell

Az Azure PowerShell két módon telepíthető – az **AzureServiceManagement** és az **AzureResourceManager** módban.  Az AzureResourceManager mód mostantól tartalmazza a Virtual Machines, a Virtual Networks és a Storage-fiókok felügyeletéhez szükséges parancsmagokat is. További információk [itt](../articles/powershell-azure-resource-manager.md).

### Azure CLI

Az Azure parancssori felület (CLI) két módon telepíthető – az **AzureServiceManagement** és az **AzureResourceManager** módban. Az AzureResourceManager mód mostantól tartalmazza a Virtual Machines, a Virtual Networks és a Storage-fiókok felügyeletéhez szükséges parancsokat is. További információk [itt](../articles/xplat-cli-azure-resource-manager.md).

### Visual Studio

A Visual Studióhoz legutóbb kiadott Azure SDK segítségével közvetlenül a Visual Studióból hozhat létre és telepíthet virtuális gépeket és összetett alkalmazásokat. A Visual Studio segítségével elvégezheti a telepítést sablonok előre összeállított listájáról, vagy kezdhet egy üres sablonnal.

### REST API-k

A Számítás-, hálózat és társzolgáltatók részletes REST API-dokumentációját [itt](https://msdn.microsoft.com/library/azure/dn790568.aspx) találja.

## Gyakori kérdések

**Létrehozhatok virtuális gépet az új Azure Resource Managerrel úgy, hogy azután egy Azure szolgáltatásfelügyeleti API-val létrehozott virtuális hálózatban vagy Storage-fiókban telepítsem azt?**

Ez jelenleg nem támogatott. Az új Azure Resource Manager API-kat nem használhatja arra, hogy virtuális gépet telepítsen egy Szolgáltatásfelügyeleti API-val létrehozott virtuális hálózatba.

**Létrehozhatok virtuális gépet az új Azure Resource Managerrel egy Azure szolgáltatásfelügyeleti API-val létrehozott felhasználói rendszerképből?**

Ez jelenleg nem támogatott. A Szolgáltatásfelügyeleti API használatával létrehozott tárfiókból azonban átmásolhatja a VHD-fájlokat egy új fiókba, amelyet az új Azure Resource Manager API-k használatával hozott létre.

**Milyen változások vonatkoznak az előfizetésemhez tartozó kvótára?**

Az új Azure Resource Manager API-kkal létrehozott Virtual Machines, Virtual Networks és a Storage-fiókok kvótái nem azonosak a már meglévő kvótákkal. Minden előfizetés új kvótákat kap az erőforrások az új API-kkal való létrehozására. A további kvótákról [itt](../articles/azure-subscription-service-limits.md) talál részletes információkat.

**Használhatom továbbra is az automatizált parancsfájljaimat virtuális gépek, virtuális hálózatok, Storage-fiókok stb. kiépítésére az új Azure Resource Manager API-kban?**

Az összes már létrehozott automatizálás és parancsfájl továbbra is működni fog az Azure Szolgáltatásfelügyelet módban létrehozott, már meglévő virtuális gépekhez, virtuális hálózatokhoz stb. Ha viszont az új sémával szeretné használni a parancsfájlokat ugyanazon erőforrásoknak az új Azure Resource Manager módban való létrehozásához, frissítenie kell őket.

**Hol találhatok példákat az Azure Resource Manager-sablonokra?**

Az [Azure Resource Manager gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) között találhat egy átfogó kezdősablonkészletet.


<!--HONumber=Sep16_HO4-->


