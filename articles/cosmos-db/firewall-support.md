---
title: Az Azure Cosmos DB tűzfaltámogatásáról & IP hozzáférés-vezérlés |} A Microsoft Docs
description: Ismerje meg, IP hozzáférés-vezérlési házirendeket használata az Azure Cosmos DB-adatbázisfiókhoz tűzfaltámogatás.
keywords: IP hozzáférés-vezérlés, a tűzfaltámogatás
services: cosmos-db
author: kanshiG
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: govindk
ms.openlocfilehash: 6d1daededcf8f0efdc6a3a5649aa830110192fef
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381840"
---
# <a name="azure-cosmos-db-firewall-support"></a>Az Azure Cosmos DB-tűzfaltámogatás
Az Azure Cosmos DB-adatbázisfiók tárolt adatok védelméhez, az Azure Cosmos DB rendelkezett támogatási alapú titkos [engedélyezési modellt](https://msdn.microsoft.com/library/azure/dn783368.aspx) , amely már használja egy erős kivonat-alapú üzenet hitelesítési kód (HMAC). Most a titkos kód alapú engedélyezési modell mellett az Azure Cosmos DB IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő vezérelt házirend támogatja. Ez a modell a tűzfalszabályok a hagyományos adatbázisok rendszer hasonló, és a egy további, az Azure Cosmos DB-adatbázisfiók biztonsági szintet. Ebben a modellben az Azure Cosmos DB-adatbázis fiók elérésére csak egy jóváhagyott gépekre és/vagy felhőszolgáltatásokra történő most konfigurálhatja. Azure Cosmos DB-erőforrásokat ezen jóváhagyott készletek gépek és szolgáltatások való hozzáférés továbbra is szükséges a hívó érvényes engedélyezési jogkivonat nyújtjuk.

> [!NOTE]
> Tűzfaltámogatás jelenleg az Azure Cosmos DB SQL API és a Mongo API fiókokhoz érhető el. Tűzfalak konfigurálása más API-k és például az Azure Germany vagy az Azure Government szuverén felhők hamarosan elérhető lesz. Ha azt tervezi, az Azure Cosmos DB-fiókot, amely rendelkezik egy meglévő IP-tűzfalon konfigurálva szolgáltatásvégpontját ACL konfigurálása, vegye figyelembe a tűzfal-konfiguráció, távolítsa el az IP-tűzfalon, és majd konfigurálja a szolgáltatás-végponti ACL. Miután konfigurálta a szolgáltatásvégpont, engedélyezheti újra az IP-tűzfal szükség esetén.

## <a name="ip-access-control-overview"></a>IP hozzáférés-vezérlés áttekintése
Az Azure Cosmos DB-adatbázisfiókok alapértelmezés szerint elérhetők a nyilvános internetről, ha a kérelemhez érvényes engedélyezési jogkivonat tartozik. Az IP-szabályzatalapú hozzáférés-vezérlés beállításához a felhasználónak CIDR formátumban meg kell adnia azokat az IP-címkészletet vagy IP-címtartományokat amelyeket fel kíván venni az adott adatbázisfiók ügyfél IP-címeinek engedélyezési listájára. A konfiguráció alkalmazása után a kiszolgáló blokkol minden olyan kérelmet, amely nem az engedélyezési listán szereplő gépekről származik.  A kapcsolat feldolgozása az IP-alapú hozzáférés-vezérlés folyamata a következő ábra szemlélteti:

![IP-alapú hozzáférés-vezérlés folyamata bemutató ábra.](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Az IP-hozzáférés-vezérlési házirend beállítása
Az Azure Portalon vagy programozott módon keresztül állítható IP hozzáférés-vezérlési házirend [Azure CLI-vel](cli-samples.md), [Azure PowerShell-lel](powershell-samples.md), vagy a [REST API-val](/rest/api/cosmos-db/) a frissítésével**ipRangeFilter** tulajdonság. 

Az IP-hozzáférés-vezérlési házirend beállítása az Azure Portalon, keresse meg az Azure Cosmos DB-fiók lapra, kattintson a **tűzfal és virtuális hálózatok** a navigációs menüben, majd módosítsa a **engedélyezze a hozzáférést** érték a **kiválasztott hálózatok**, és kattintson a **mentése**. 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/firewall-support/azure-portal-firewall.png)

Miután IP hozzáférés-vezérlés egy, a portálon teszi lehetővé IP-címek és tartományok, valamint a kapcsolók más Azure-szolgáltatások és az Azure Portallal való hozzáférés engedélyezéséhez adjon meg. Ezek a kapcsolók További információt a következő szakaszok megtalálható.

> [!NOTE]
> Egy IP hozzáférés-vezérlési szabályzat az Azure Cosmos DB-adatbázisfiók engedélyezésével kívül a beállított gépekről a Azure Cosmos DB-fiókot az összes hozzáférést engedélyezett IP-címtartományok listája le vannak tiltva. Ez a modell alapján keresse az adatsík művelet a portálról is blokkolva lesz a hozzáférés-vezérlés integritását.

## <a name="connections-from-the-azure-portal"></a>Kapcsolatok az Azure Portalról 

Ha programozott módon engedélyezi egy IP-hozzáférés-vezérlési házirend, hozzá kell az Azure portal IP-címét a **ipRangeFilter** tulajdonság megtartásához. A portál IP-címek a következők:

|Régió|IP-cím|
|------|----------|
|Alább megadott kivételével minden régióban|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|USA-beli államigazgatás|52.244.48.71|

Az Azure Portalon való hozzáférés alapértelmezés szerint engedélyezve van, amikor módosítja a tűzfal beállítás **kiválasztott hálózatok** az Azure Portalon. 

![Képernyőfelvétel az Azure portal hozzáférés engedélyezése:](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>A globális Azure-adatközpontok vagy az Azure PaaS-szolgáltatások kapcsolatok

"Az azure PaaS-szolgáltatások, például az Azure Stream analytics, Azure Functions stb. az Azure Cosmos DB összefüggésben használatosak. Ha engedélyezni szeretné más Azure PaaS-szolgáltatások alkalmazások az Azure Cosmos DB-erőforrások eléréséhez, a tűzfal beállítást engedélyezni kell. A tűzfal beállítás engedélyezése, az IP-cím-0.0.0.0 hozzáadása az engedélyezett IP-címek listáját. Az Ip-cím-0.0.0.0 azt jelzi, hogy az összes Azure-adatközpont IP-címtartományból kapcsolat engedélyezve van-e csatlakozni az Azure Cosmos DB-erőforrások."

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

A kapcsolatok az Azure globális adatközpontokon belül való hozzáférés alapértelmezés szerint engedélyezve van, amikor módosítja a tűzfal beállítás **kiválasztott hálózatok** az Azure Portalon. 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Az aktuális IP-kapcsolatok

Fejlesztés egyszerűsítése érdekében, az Azure portal segítségével azonosításához, és adja hozzá az IP-címét az ügyfélszámítógép az engedélyezett listára, hogy a gépen futó alkalmazások hozzáférhessenek-e az Azure Cosmos DB-fiókot. Itt az ügyfél IP-cím észlelve a portál által látható módon. Elképzelhető, hogy az ügyfél IP-cím a gép, de az is előfordulhat, hogy a hálózati átjáró IP-címét. Ne felejtse el az éles környezetben történő előtt távolítsa el.

Ahhoz, hogy az aktuális IP-cím, válassza ki a **saját aktuális IP-cím hozzáadása**, amely az aktuális IP-cím ad hozzá az IP-címek listáját, és kattintson a **mentése**.

![A bemutató Képernyőkép az aktuális IP-tűzfal beállításainak konfigurálása](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>A cloud servicesből kapcsolatok
Az Azure-ban a cloud services módon egy közös üzemeltetéséhez a középső réteg szolgáltatás logikai Azure Cosmos DB használatával. Hozzáférés engedélyezése az Azure Cosmos DB-adatbázisfiók egy felhőalapú szolgáltatásából, a felhőszolgáltatás a nyilvános IP-cím hozzá kell adni a Azure Cosmos DB-adatbázisfiók által társított IP-címeket az engedélyezett listára [az IP-hozzáférés konfigurálása a házirend szabályozza](#configure-ip-policy). Ez biztosítja, hogy a cloud services szerepkör példányainak férhetnek hozzá a Azure Cosmos DB-fiókot. IP-címeket lehet lekérdezni a cloud Services az Azure Portalon, az alábbi képernyőképen látható módon:

![Képernyőfelvétel: a nyilvános IP-cím jelenik meg az Azure Portalon a felhőszolgáltatások](./media/firewall-support/public-ip-addresses.png)

Horizontális felskálázás a felhőszolgáltatás további szerepkör-példány hozzáadásával, ezekhez a példányokhoz automatikusan hozzáférést kap, az Azure Cosmos DB-adatbázisfiók mivel ugyanazon a felhőszolgáltatáson részét képezik.

## <a name="connections-from-virtual-machines"></a>A virtuális gépek kapcsolatok
[Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/) vagy [a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is használható az Azure Cosmos DB használatával, a középső rétegbeli szolgáltatásokat.  Az Azure Cosmos DB adatbázis fiók konfigurálásával lehetővé teheti a virtuális gépek, virtuális gép és/vagy a virtuális gép nyilvános IP-címek hozzáférés méretezési úgy kell konfigurálni az engedélyezett IP-címek az Azure Cosmos DB-adatbázisfiók egyik szerint[IP hozzáférés-vezérlési házirend beállítása](#configure-ip-policy). IP-címeket virtuális gépekhez az Azure Portalon kérheti le az alábbi képernyőképen látható módon.

![Képernyőfelvétel az Azure Portalon megjelenő virtuális gép nyilvános IP-cím:](./media/firewall-support/public-ip-addresses-dns.png)

További virtuálisgép-példányokat ad hozzá a csoporthoz, azok automatikusan rendelkezésre a Azure Cosmos DB-fiókot a hozzáférést.

## <a name="connections-from-the-internet"></a>Az internetről érkező kapcsolatokat
Az Azure Cosmos DB-adatbázisfiók egy számítógépről az interneten való használatakor az ügyfél IP-cím vagy IP-címtartomány a gép hozzá kell adni az Azure Cosmos DB-adatbázis fiókjához tartozó IP-cím az engedélyezett listára. 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Az IP hozzáférés-vezérlés beállítása az Azure Resource Manager-sablon használatával

Adja hozzá a következő JSON-ra a sablonhoz, IP hozzáférés-vezérlés beállítása. Egy olyan fiók Resource Manager-sablon ipRangeFilter attribútum, amely IP-címtartományok kell lennie az engedélyezési listához hozzáadni kívánt lista lesz.

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>Hibaelhárítási IP hozzáférés-vezérlési házirend
### <a name="portal-operations"></a>Webportálos műveletek
Egy IP hozzáférés-vezérlési szabályzat az Azure Cosmos DB-adatbázisfiók engedélyezésével kívül a beállított gépekről a Azure Cosmos DB-fiókot az összes hozzáférést engedélyezett IP-címtartományok listája le vannak tiltva. Ezért ha a portál data síkjával végzett műveletek, például böngészési tárolók és a dokumentumok lekérdezésének engedélyezni szeretné, kell explicit módon lehetővé teszi az Azure portál elérésére a **tűzfal** lapot a portálon. 

### <a name="sdk--rest-api"></a>SDK-t és a Rest API
A biztonsági okokból az SDK-t vagy a REST API-n keresztül hozzáférést az engedélyezett listán nem szereplő gépekről választ küld általános 404-es nem található a nincsenek további részletei. Ellenőrizze az IP-cím engedélyezettek listájához konfigurált az Azure Cosmos DB-adatbázisfiók győződjön meg, hogy a rendszer alkalmazza a megfelelő házirend-konfigurációt a Azure Cosmos DB-fiókot.

## <a name="next-steps"></a>További lépések
Hálózati teljesítménnyel kapcsolatos tippek kapcsolatos információkért lásd: [teljesítménnyel kapcsolatos tippek](performance-tips.md).

