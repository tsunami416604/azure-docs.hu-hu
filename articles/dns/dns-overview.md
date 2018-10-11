---
title: Mi az Azure DNS?
description: A Microsoft Azure DNS-szolgáltatásának áttekintése. Saját tartományt üzemeltethet a Microsoft Azure-on.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: e3e04bf7e35b22a56465810f476323ed217e047a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967625"
---
# <a name="what-is-azure-dns"></a>Mi az Azure DNS?

Az Azure DNS egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-t nem használhatja tartománynév vásárlására. Az [Azure Web Apps](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) vagy egy külső tartományregisztráló használatával vásárolhat tartománynevet éves díj ellenében. A tartományokat ezután üzemeltetheti az Azure DNS-ben rekordok kezeléséhez. További információkért tekintse meg a [tartományok az Azure DNS-be való delegálását](dns-domain-delegation.md) ismertető cikket.

Az Azure DNS a következő funkciókat tartalmazza:

## <a name="reliability-and-performance"></a>Megbízhatóság és teljesítmény

Az Azure DNS-beli DNS-tartományok az Azure globális DNS-névkiszolgálói hálózatán üzemelnek. Az Azure DNS anycast hálózatkezelést használ annak érdekében, hogy a DNS-lekérdezésekre a legközelebbi elérhető DNS-kiszolgáló válaszoljon. Ez kiváló teljesítményt és magas szintű rendelkezésre állást biztosít a tartomány számára.

## <a name="security"></a>Biztonság

Az Azure DNS szolgáltatás az Azure Resource Managerre épül. Ennek köszönhetően többek között a következő Resource Manager-funkciókat veheti igénybe:

* [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) – szabályozhatja, hogy ki rendelkezzen hozzáféréssel egy adott művelethez a vállalatban.

* [tevékenységnaplók](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) – nyomon követheti, hogy a vállalat felhasználói hogyan módosították az erőforrásokat, vagy megkeresheti a hibákat a hibaelhárításkor.

* [erőforrás-zárolás](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) – zárolhat egy előfizetést, erőforráscsoportot vagy erőforrást, hogy a szervezet többi felhasználója ne tudja véletlenül törölni vagy módosítani a kritikus fontosságú erőforrásokat.

További információkat a [DNS-zónák és -rekordok védelmével](dns-protect-zones-recordsets.md) kapcsolatos témakörben olvashat. 


## <a name="ease-of-use"></a>Könnyű használat

Az Azure DNS szolgáltatás képes kezelni az Azure-szolgáltatások DNS-rekordjait, és DNS-t biztosít a külső erőforrásoknak. Az Azure DNS integrálva van az Azure Portallal, és ugyanazokat a hitelesítő adatokat, számlázási információkat és támogatási szerződést használja, mint a többi Azure-szolgáltatás. 

A DNS számlázása az Azure-ban üzemeltetett DNS-zónák és a DNS-lekérdezések száma alapján történik. A díjszabással kapcsolatos további információkért tekintse meg [az Azure DNS díjszabásával](https://azure.microsoft.com/pricing/details/dns/) kapcsolatos cikket.

A tartományok és a rekordok az Azure Portal, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI segítségével kezelhetőek. Az automatizált DNS-kezelést igénylő alkalmazások a REST API és SDK-k használatával integrálhatóak a szolgáltatással.

## <a name="customizable-virtual-networks-with-private-domains"></a>Saját tartományokkal rendelkező, testreszabható virtuális hálózatok

Az Azure DNS a privát DNS-tartományok használatát is támogatja, amelyek mostantól nyilvános előzetes verzióban érhetők el. Ez lehetővé teszi, hogy a jelenleg elérhető, Azure által biztosított nevek helyett egyéni tartományneveket használjon a virtuális magánhálózatokon.

További információkat az [Azure DNS privát tartományokhoz való használatát](private-dns-overview.md) ismertető cikkben olvashat.

## <a name="alias-records"></a>Aliasrekordok

Az Azure DNS az aliasrekord-készletek használatát is támogatja. Aliasrekord-készletekkel Azure-erőforrásra is hivatkozhat, például nyilvános Azure IP-címre vagy Traffic Manager-profilra. Ha a mögöttes erőforrás IP-címe módosul, az aliasrekord-készlet a DNS feloldása során zökkenőmentesen frissíti magát. Az aliasrekord-készlet a szolgáltatáspéldányra mutat, a szolgáltatáspéldány pedig egy IP-címmel van társítva. 

Ezenkívül mostantól a legfelső vagy csupasz tartományt (például contoso.com) egy Traffic Manager-profilra irányíthatja egy aliasrekorddal.

További információért lásd: [Az Azure DNS-aliasrekordok áttekintése](dns-alias.md).


## <a name="next-steps"></a>További lépések

* További tudnivalókat a DNS-zónákról és -rekordokról [a DNS-zónák és -rekordok áttekintésében](dns-zones-records.md) olvashat.

* Megismerheti, hogyan hozhat létre zónákat az Azure DNS-ben a [DNS-zónák létrehozását](./dns-getstarted-create-dnszone-portal.md) ismertető témakörből.

* Az Azure DNS-sel kapcsolatos gyakori kérdésekért lásd az [Azure DNS gyakori kérdéseket](dns-faq.md).

