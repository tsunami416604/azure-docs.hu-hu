---
title: "Virtuális hálózati szolgáltatási végpont és az Azure SQL Database szabályok |} Microsoft Docs"
description: "Egy alhálózat megjelölése egy virtuális hálózati végpontot. Ezután a végpont a hozzáférés-vezérlési lista az Azure SQL Database a virtuális hálózati szabály. SQL-adatbázist, majd fogad kommunikációt az összes virtuális gép és az alhálózat más csomópontok."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 02/20/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: c1bb1698723af60544b89f4b3168c44a32d31afd
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Virtuális hálózati szolgáltatás végpontok és szabályok az Azure SQL Database használata

*Virtuális hálózati szabályok* van egy tűzfal biztonsági szolgáltatás, amely meghatározza, hogy az Azure SQL adatbázis-kiszolgáló elfogadja-e a virtuális hálózatok az adott alhálózat érkező kommunikációt. Ez a cikk azt ismerteti, miért a virtuális hálózati szabály használata egyes esetekben a legjobb lehetőség, a biztonságos használatának engedélyezése az Azure SQL Database-kommunikációt.

A virtuális hálózati szabály létrehozásához először lennie kell egy [virtuális hálózati szolgáltatási végpont] [ vm-virtual-network-service-endpoints-overview-649d] való hivatkozáshoz a szabályhoz.


#### <a name="how-to-create-a-virtual-network-rule"></a>A virtuális hálózati szabály létrehozása

Ha csak szabályt hoz létre egy virtuális hálózat, ugorjon előre a lépéseket és a magyarázat [Ez a cikk későbbi](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminológia és leírása

**Virtuális hálózat:** lehet az Azure-előfizetéshez társított virtuális hálózatokat.

**Alhálózati:** A virtuális hálózatok **alhálózatok**. Bármely Azure virtuális gépek (VM), hogy rendelkezik alhálózatok vannak hozzárendelve. Egy alhálózat több virtuális gép vagy egyéb számítási csomópontok szerepelhet. A számítási csomópontot, amely a virtuális hálózaton kívül nem tud hozzáférni a virtuális hálózat, ha nem állít be, hogy a hozzáférést a biztonsági.

**Virtuális hálózati szolgáltatási végpont:** A [virtuális hálózati szolgáltatási végpont] [ vm-virtual-network-service-endpoints-overview-649d] egy alhálózat, amelyek a következők: egy vagy több hivatalos Azure-szolgáltatás neve. Ebben a cikkben azt is nevét **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve.

**Virtuális hálózati szabály:** az SQL Database-kiszolgálóhoz a virtuális hálózati szabály olyan alhálózatot, amely a hozzáférési lista (ACL) az SQL-adatbázis kiszolgálójának szerepel. Az SQL-adatbázis a hozzáférés-Vezérlési találhatók, az alhálózati tartalmaznia kell a **Microsoft.Sql** típusnév.

A virtuális hálózati szabály van állítva, az SQL adatbázis-kiszolgáló minden csomópont található az alhálózat-kommunikáció fogadására.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>A virtuális hálózati szabály előnyei

Amíg a művelet végrehajtása a virtuális gépeket az alhálózaton az SQL-adatbázis nem tud kommunikálni. Egy műveletet, amely kapcsolatot hoz létre a rendszer egy virtuális hálózati szabály létrehozása. A VNet szabály módszer kiválasztása indoklása használata esetén a versengő biztonsági beállításokat, a tűzfal által kínált hasonlítsa össze, és ezzel szemben döntéseken igényel.

#### <a name="a-allow-access-to-azure-services"></a>A. Azure-szolgáltatásokhoz való hozzáférés engedélyezése

A tűzfal ablaktáblán egy **be-és kikapcsolása** nevű gomb **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**. A **ON** folytatott kommunikáció minden Azure IP-címeket és minden Azure alhálózat-beállítással. Ezeknek az Azure IP-címek és alhálózatok előfordulhat, hogy nem kell tulajdonában. Ez **ON** beállítás valószínűleg több nyitva, mint azt szeretné, hogy az SQL-adatbázis kell lennie. A virtuális hálózati szabály funkciót kínál sok eszközzel kombinálva felhasználóbarát tanúsítványhasználat pontos szabályzása.

#### <a name="b-ip-rules"></a>B. IP-szabályok

Az SQL-adatbázis tűzfala lehetővé teszi az IP-címtartományok, amelyből kommunikációt fogad az SQL-adatbázisba megadását. Erre akkor finom stabil IP-címek, amelyek az Azure magánhálózaton kívülről. Az Azure magánhálózaton belül számos csomópontok be van állítva, de *dinamikus* IP-címeket. Dinamikus IP-címek megváltozhatnak, például újraindításakor a virtuális Gépet. Ha meg szeretné adni egy dinamikus IP-címet egy tűzfalszabályt, éles környezetben folly lenne.

Azt az IP-cím lehetőségét is maradványérték megszerzésével a *statikus* IP-címet a virtuális gép számára. További információkért lásd: [virtuális gépek magánhálózati IP-címek konfigurálása az Azure-portál használatával][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Azonban a statikus IP-megközelítés válhat kezelése bonyolult, és költséges, amikor léptékű hajtja végre. Virtuális hálózati szabályok lettek könnyebb létrehozásához és kezeléséhez.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Még nem rendelkezik SQL-adatbázis egy alhálózaton

Ha az Azure SQL Database-kiszolgálóhoz a virtuális hálózati alhálózat csomópont volt, a virtuális hálózaton belüli összes csomópontján tudott kommunikálni az SQL-adatbázis. Ebben az esetben a virtuális gépek képes kommunikálni az SQL Database minden virtuális hálózati szabályok vagy IP-szabályok anélkül.

Azonban szeptember 2017, az Azure SQL adatbázis-szolgáltatás nincs még az alhálózathoz hozzárendelt szolgáltatások között.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Virtuális hálózati szabályokra vonatkozó adatokat

Ez a szakasz ismerteti a tényezőktől virtuális hálózati szabályokat.

#### <a name="only-one-geographic-region"></a>Csak egy földrajzi terület

Minden egyes virtuális hálózati szolgáltatási végpont csak egy Azure-régiót vonatkozik. A végpont nem engedélyezi az alhálózat keresztüli más régiókban.

Virtuális hálózati szabályok korlátozódik, a régiót, amelyben a mögöttes végpont vonatkozik.

#### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem az adatbázis-szintjét

Teljes Azure SQL Database-kiszolgálóhoz, nem csak egy adott adatbázis a kiszolgálón való minden egyes virtuális hálózati szabály vonatkozik. Ez azt jelenti a virtuális hálózati szabály a kiszolgáló szintjén-, nem az adatbázis szintjén-vonatkozik.

- Ezzel szemben IP-szabályok vagy szinten is alkalmazhatja.

#### <a name="security-administration-roles"></a>Felügyeleti szerepkörök

Nincs a felügyeleti virtuális hálózati szolgáltatás végpontok a biztonsági szerepkörök elkülönítése. Beavatkozásra szükség, a következő szerepkörök:

- **Hálózati rendszergazda:** &nbsp; kapcsolja be a végpont.
- **Adatbázis-rendszergazda:** &nbsp; a hozzáférés-vezérlési lista (ACL) az adott alhálózat hozzáadása az SQL adatbázis-kiszolgáló frissítése.

*Az RBAC alternatív:*

A hálózati rendszergazda és az adatbázis-rendszergazdai szerepkörök rendelkezik, mint a virtuális hálózati szabályok kezeléséhez szükséges további képességeket. Van szükség, azok képességeinek csak egy részét.

Lehetősége van a [szerepköralapú hozzáférés-vezérlést (RBAC)] [ rbac-what-is-813s] , amely csak a szükséges részét képességek rendelkezik egy egyéni szerepkör létrehozása az Azure-ban. Az egyéni biztonsági szerepkört használható helyett használata esetén a hálózati rendszergazda vagy az adatbázis-rendszergazdához. A biztonsági kockázatokat felületének verziója korábbi, ha a felhasználó hozzáadása egy egyéni biztonsági szerepkört, és helyezze a felhasználót a két fő rendszergazdai szerepköröket.

> [!NOTE]
> Bizonyos esetekben az Azure SQL Database és a VNet-alhálózat van különböző előfizetésekhez. Ebben az esetben gondoskodnia kell a következő beállításokat:
> - Az azonos Azure Active Directory-bérlő mindkét előfizetéshez kell tartoznia.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek, például a szolgáltatás végpontok engedélyezésére és VNet-alhálózat hozzáadása az adott kiszolgálóhoz kezdeményezni.

## <a name="limitations"></a>Korlátozások

Az Azure SQL Database a virtuális hálózati szabályok funkció rendelkezik a következő korlátozások vonatkoznak:

- A webes alkalmazás rendelhetők a virtuális hálózatot/alhálózatot magánhálózati IP-címe. Akkor is, ha a végpontok a megadott virtuális hálózat alhálózatból ON vannak kapcsolva, a webalkalmazásról kapcsolódik-e a kiszolgáló lesz az Azure nyilvános IP-forráshoz, nem egy virtuális hálózatot/alhálózatot forrás. Ahhoz, hogy a virtuális hálózat tűzfalszabályok rendelkező kiszolgáló és a webes alkalmazás közötti kapcsolatot, kell **összes Azure-szolgáltatások engedélyezése** a kiszolgálón.

- A tűzfalon az SQL-adatbázis minden egyes virtuális hálózati szabály alhálózat hivatkozik. A hivatkozott alhálózatok ugyanabban a földrajzi régióban az SQL-adatbázist futtató kiszolgálón kell futnia.

- Minden Azure SQL adatbázis-kiszolgáló legfeljebb 128 ACL-bejegyzések a megadott virtuális hálózat lehet.

- Virtuális hálózati szabályok vonatkoznak csak Azure Resource Manager virtuális hálózatok; és nem arra [klasszikus üzembe helyezési modellel] [ arm-deployment-model-568f] hálózatok.

- Virtuális hálózati Szolgáltatásvégpontok indításának ON Azure SQL Database is lehetővé teszi a MySQL és PostGres Azure szolgáltatáshoz a végpontok. Azonban a végpontok ON példányokhoz való csatlakozáshoz a végpontról a MySQL vagy Postgres kísérlet sikertelen lesz.
    - Az alapul szolgáló oka az, hogy a MySQL és PostGres jelenleg nem támogatják az ACLing.

- A tűzfalon az IP-címtartományok alkalmazása a következő hálózati elemek, de a virtuális hálózati szabályok azonban nem:
    - [Webhelyek (közötti S2S) virtuális magánhálózati (VPN)][vpn-gateway-indexmd-608y]
    - A helyszíni keresztül [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Végpontok használatának szempontjai
Végpontok használata az Azure SQL Database, tekintse át a következőket kell figyelembe venni:

- **Az Azure SQL adatbázis nyilvános IP-címek kimenő szükség**: Azure SQL adatbázis IP engedélyezi a csatlakozást a hálózati biztonsági csoportokkal (NSG-k) kell megnyitni. Ehhez a NSG [szolgáltatás címkék](../virtual-network/security-overview.md#service-tags) az Azure SQL Database.
- **PostgreSQL és MySQL az Azure-adatbázis nem támogatottak**: végpontok nem támogatottak az Azure Database PostgreSQL vagy MySQL. Az SQL Database szolgáltatás végpontok engedélyezésére megszakítja a kapcsolatot, hogy ezekre a szolgáltatásokra. A megoldás tudunk a; Lépjen kapcsolatba a  *dmalik@microsoft.com* .

#### <a name="expressroute"></a>ExpressRoute

Ha a hálózat csatlakozik-e az Azure-hálózat használata [ExpressRoute][expressroute-indexmd-744v], a kapcsolatok a Microsoft Edge két nyilvános IP-címek van konfigurálva. A két IP-címek vannak való kapcsolódáshoz használt Microsoft Services, például Azure Storage Azure nyilvános társviszony-létesítés használatával.

Engedélyezi a kommunikációt a kapcsolatcsoport az Azure SQL Database, a nyilvános IP-címeit a Kapcsolatcsoportok IP-hálózati szabályokat kell létrehozni. Ahhoz, hogy a nyilvános IP-címét az ExpressRoute-kapcsolatcsoportot található, nyisson meg egy támogatási jegy ExpressRoute az Azure portál használatával.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>"Az összes Azure-szolgáltatások engedélyezése" eltávolításának következményei

Sok felhasználó el kívánja távolítani **összes Azure-szolgáltatások engedélyezése** az Azure SQL-kiszolgálókról, és cserélje le a virtuális hálózat tűzfalszabály.
Azonban ennek eltávolítása hatással van a következő Azure SQLDB funkciók:

#### <a name="import-export-service"></a>Importálás Export szolgáltatásról
Az Azure SQLDB importálási exportálása szolgáltatás fut, az Azure virtuális gépeken. Virtuális gépeken a virtuális hálózat nem szerepelnek, és ezért első olyan Azure IP-címet, az adatbázishoz való kapcsolódáskor. Az Eltávolítás **összes Azure-szolgáltatások engedélyezése** virtuális gépeken nem tudnak hozzáférni az adatbázisokat.
A probléma megkerüléséhez. Futtassa a BACPAC importálása vagy exportálása a kódban közvetlenül a DACFx API használatával. Győződjön meg arról, hogy ez telepítve van egy virtuális Gépet, amely a VNet-alhálózat, amelynek meg van a tűzfalszabály.

#### <a name="sql-database-query-editor"></a>SQL-adatbázis lekérdezés-szerkesztő
Az Azure SQL adatbázis lekérdezés-szerkesztő a rendszer az Azure virtuális gépeken. Virtuális gépeken nem szerepelnek a virtuális hálózat. Ezért a virtuális gépek kapni olyan Azure IP-címet, az adatbázishoz való kapcsolódáskor. Eltávolítja a **összes Azure-szolgáltatások engedélyezése**, a virtuális gépek nem tudnak hozzáférni az adatbázisokat.

#### <a name="table-auditing"></a>Tábla naplózás
Jelenleg két módja van az SQL-adatbázis naplózásának engedélyezéséhez. Tábla naplózás meghiúsul, miután Szolgáltatásvégpontok engedélyezte az Azure SQL-kiszolgálón. Itt megoldás, hogy a Blobnaplózási funkció.

#### <a name="impact-on-data-sync"></a>Adatszinkronizálás gyakorolt hatás
Az Azure SQLDB rendelkezik az adatszinkronizálás funkciója, amely kapcsolódik az adatbázisokat Azure IP-címek használatával. Végpontok használatakor valószínű, hogy Ön ki fog kapcsolni **összes Azure-szolgáltatások engedélyezése** a logikai kiszolgálóhoz való hozzáférést. Ezzel megszünteti a adatszinkronizálás szolgáltatást.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Virtuális hálózat Szolgáltatásvégpontok használata az Azure storage hatása

Az Azure Storage ugyanaz a funkció, amely lehetővé teszi, hogy korlátozza a tárfiókkal van megvalósítva.
Ha a szolgáltatás használatához egy tárfiókot, az Azure SQL-kiszolgáló által használt választja, a problémák futtathatja. Ezután egy lista és vitafórum által befolyásolt Azure SQLDB funkcióját.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
A PolyBase gyakran használják az adatok betöltése az Azure SQLDW a Storage-fiókok. A Storage-fiók, amely adatokat tölt be korlátozza a hozzáférést csak VNet-alhálózatokat, ha megszakad a fiók és a PolyBase közötti kapcsolatot. Nincs; Ez a megoldás Lépjen kapcsolatba a  *dmalik@microsoft.com*  további információt.

#### <a name="azure-sqldb-blob-auditing"></a>Azure SQLDB Blob Auditing
A saját tárfiók blobnaplózási funkció leküldi naplókat. Ha ezt a tárfiókot használ a BBI szolgáltatás végpontok Azure SQLDB a tárolási fiók kapcsolat megszakad.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>A virtuális hálózat Tűzfalszabályok hozzáadása a kiszolgálóhoz nélkül szolgáltatják a VNET-végpontok

Mennyivel Ez a szolgáltatás tovább lett fejlesztve, mielőtt elvégezhetné szükséges teszi a virtuális hálózat szolgáltatás végpontok a tűzfalon élő VNet szabály sikertelen végrehajtása előtt. A végpontok kapcsolódó egy Azure SQL Database adott VNet-alhálózat. De most frissítésétől január 2018, akkor megkerülheti ennek a követelménynek úgy, hogy a **IgnoreMissingServiceEndpoint** jelzőt.

Csupán beállítása a tűzfalszabályok nem biztonságossá tétele a kiszolgálón. Is engedélyezze a VNet Szolgáltatásvégpontok szolgáltatást a biztonság érvénybe léptetéséhez. Végpontok bekapcsolásakor a VNet-alhálózat állásidő észlel, amíg be nem fejezi be-vagy kikapcsolása történő áttérés a. Ez különösen igaz nagy Vnetek környezetében. Használhatja a **IgnoreMissingServiceEndpoint** jelző csökkentheti vagy elkerülheti a leállás átmenet közben.

Beállíthatja a **IgnoreMissingServiceEndpoint** jelző PowerShell használatával. További információkért lásd: [egy virtuális hálózati szolgáltatási végpont és a szabály létrehozása az Azure SQL Database PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Hibák 40914 és 40615

Kapcsolódási hiba 40914 vonatkozik *virtuális hálózati szabályok*, a tűzfal panelen az Azure portálon meghatározott. Hiba 40615 hasonlít, azzal a különbséggel vonatkozik *IP-cím szabályok* a tűzfalon.

#### <a name="error-40914"></a>Hiba 40914

*Szöveges üzenet:* nem nyitható meg a kiszolgáló "*[kiszolgálónév]*" a bejelentkezés által kért. Ügyfél számára nem engedélyezett a kiszolgálóhoz való hozzáféréshez.

*Hiba leírása:* az ügyfél, amely rendelkezik a virtuális hálózati server végpontok alhálózat van. De az Azure SQL adatbázis-kiszolgáló nem virtuális hálózati szabályt, amely jogot ad az alhálózatnak a az SQL-adatbázissal való kommunikációhoz.

*Névfeloldási hiba:* a tűzfal ablaktáblán az Azure portál, használja a virtuális hálózati szabályok megszabott [adja hozzá a virtuális hálózati szabály](#anchor-how-to-by-using-firewall-portal-59j) az alhálózat.

#### <a name="error-40615"></a>Error 40615

*Szöveges üzenet:* nem nyitható meg a kiszolgáló a bejelentkezés által kért "{0}". Ügyfél IP-cím "{1}" nem engedélyezett a kiszolgálóhoz való hozzáféréshez.

*Hiba leírása:* az ügyfél nem jogosult az Azure SQL Database-kiszolgálóhoz az IP-címről csatlakozni próbál. A kiszolgáló tűzfalának nincs IP cím szabály, amely lehetővé teszi az ügyfél az SQL adatbázishoz a megadott IP-címről érkező kommunikációt.

*Névfeloldási hiba:* meg az ügyfél IP-címet az IP-szabály. Ezt úgy teheti meg a tűzfal ablaktáblán az Azure portálon.


Több SQL adatbázis-hibaüzenetek listáját dokumentált [Itt][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portál virtuális hálózati szabályt hozhat létre.

Ez a szakasz bemutatja, hogyan használhatja a [Azure-portálon] [ http-azure-portal-link-ref-477t] létrehozásához egy *virtuális hálózati szabály* az Azure SQL-adatbázisban. A szabály közli az SQL-adatbázis-kommunikációt fogad az adott alhálózat, amely rendelkezik amelyeken meg van jelölve, hogy egy *virtuális hálózati szolgáltatási végpont*.

#### <a name="powershell-alternative"></a>PowerShell alternatív

Virtuális hálózati szabályokat is létrehozhat egy PowerShell-parancsfájlt. A kritikus fontosságú parancsmag **New-AzureRmSqlServerVirtualNetworkRule**. Ha az érdekelt, lásd: [egy virtuális hálózati szolgáltatási végpont és a szabály létrehozása az Azure SQL Database PowerShell][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="prerequisites"></a>Előfeltételek

Már rendelkeznie kell olyan alhálózatot, amely az adott virtuális hálózati szolgáltatási végpont címkéje *típusnév* az Azure SQL Database megfelelő.

- A megfelelő végpont típusnév **Microsoft.Sql**.
- Ha az alhálózat nem lehet, hogy a típusnév címkézését, [ellenőrizze az alhálózat egy olyan végpont][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Az Azure portál lépései

1. Jelentkezzen be a [Azure-portálon][http-azure-portal-link-ref-477t].

2. Keresse meg a portál **SQL Server-kiszolgálók** &gt; **tűzfal / virtuális hálózatok**.

3. Állítsa be a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** vezérlő OFF.

    > [!IMPORTANT]
    > Ha nem adja meg a vezérlő beállítása ON, az Azure SQL Database-kiszolgálóhoz egyetlen alhálózatának sem érkező kommunikációt fogad el. Hagyja a beállítása ON, előfordulhat, hogy egy biztonsági szempontból túlzott mértékű hozzáférést. A Microsoft Azure-beli virtuális hálózatra végpont szolgáltatás, az SQL-adatbázis, a virtuális hálózati szabály szolgáltatás együttműködve együtt csökkentheti a biztonsági támadási felületét.

4. Kattintson a **+ Hozzáadás létező** szabályozzák, a a **virtuális hálózatok** szakasz.

    ![Kattintson a Hozzáadás létező (alhálózati végpont, SQL szabály).][image-portal-firewall-vnet-add-existing-10-png]

5. Az új **Create/Update** panelen adja meg az Azure-erőforrások nevét a vezérlőelemek a.

    > [!TIP]
    > Meg kell adni a megfelelő **címelőtag** az alhálózathoz. Az érték a portálon találja meg.
    > Keresse meg **összes erőforrás** &gt; **mindenfajta** &gt; **virtuális hálózatok**. A szűrő jeleníti meg a virtuális hálózatok. Kattintson a virtuális hálózat, majd a **alhálózatok**. A **CÍMTARTOMÁNY** oszlopnak van szüksége a címelőtag.

    ![Töltse ki a mezőket az új szabályt.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kattintson a **OK** gombra az ablak alján.

7.  Az eredményül kapott virtuális hálózati szabály jelenik meg a tűzfal ablaktáblán.

    ![Az új szabályt, tekintse meg a tűzfal ablaktáblán.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> A következő állapotok vagy állapota a szabályok vonatkoznak:
> - **Kész:** jelöli, amelyek indította el a művelet sikeresen befejeződött.
> - **Nem sikerült:** jelöli, amelyek indította el a művelet sikertelen volt.
> - **Törölt:** csak a törlési művelet vonatkozik, és jelzi, hogy a szabály törölve lett, és már nem érvényes.
> - **Esetbejegyzések:** annak jelzése, hogy a művelet folyamatban van. A régi szabály vonatkozik, amíg a művelet ebben az állapotban van.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Azure-beli virtuális hálózat Szolgáltatásvégpontok][vm-virtual-network-service-endpoints-overview-649d]
- [Az Azure SQL Database kiszolgáló- és adatbázis tűzfalszabályok][sql-db-firewall-rules-config-715d]

A virtuális hálózati szabály funkció az Azure SQL Database váltak elérhetővé a késői szeptember 2017.

## <a name="next-steps"></a>További lépések

- [A PowerShell szolgáltatás használatával egy virtuális hálózati végpontot, és a virtuális hálózati szabály létrehozása az Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]


<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
