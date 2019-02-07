---
title: Mi az Azure DNS?
description: A Microsoft Azure DNS-szolgáltatásának áttekintése. Saját tartományt üzemeltethet a Microsoft Azure-on.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 51869bcc2ee892bc150102595de09782eb01547c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770520"
---
# <a name="what-is-azure-dns"></a>Mi az Azure DNS?

Az Azure DNS egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-t nem használhatja tartománynév vásárlására. Egy éves díjért vásárolhat egy tartománynév használatával [App Service-tartományok](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) vagy egy külső tartománynév regisztrálójánál. A tartományokat ezután üzemeltetheti az Azure DNS-ben rekordok kezeléséhez. További információ: [Delegate a domain to Azure DNS](dns-domain-delegation.md) (Tartomány delegálása az Azure DNS-be).

Az Azure DNS a következő funkciókat tartalmazza.

## <a name="reliability-and-performance"></a>Megbízhatóság és teljesítmény

Az Azure DNS-beli DNS-tartományok az Azure globális DNS-névkiszolgálói hálózatán üzemelnek. Az Azure DNS anycast hálózatkezelést használ. A tartomány gyors teljesítménye és magas rendelkezésre állása érdekében minden DNS-lekérdezésre a legközelebbi elérhető DNS-kiszolgáló válaszol.

## <a name="security"></a>Biztonság

 Az Azure DNS az Azure Resource Managerre épül, amely többek között a következő szolgáltatásokat nyújtja:

* [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) – szabályozhatja, hogy ki rendelkezzen hozzáféréssel egy adott művelethez a vállalatban.

* [Tevékenységnaplók](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) – nyomon követheti, hogy a vállalat felhasználói hogyan módosították az erőforrásokat, vagy megkeresheti a hibákat a hibaelhárításkor.

* [Erőforrás-zárolás](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) – zárolhat egy előfizetést, erőforráscsoportot vagy erőforrást. A zárolás megakadályozza, hogy a cég vagy intézmény felhasználói véletlenül töröljék vagy módosítsák a kritikus erőforrásokat.

További információkat a [DNS-zónák és -rekordok védelmével](dns-protect-zones-recordsets.md) kapcsolatos témakörben olvashat. 


## <a name="ease-of-use"></a>Könnyű használat

 Az Azure DNS képes kezelni az Azure-szolgáltatások DNS-rekordjait, és DNS-t biztosít a külső erőforrásoknak. Az Azure DNS integrálva van az Azure Portallal, és ugyanazokat a hitelesítő adatokat, számlázási információkat és támogatási szerződést használja, mint a többi Azure-szolgáltatás. 

Az Azure DNS díjszabása az Azure-ban üzemeltetett DNS-zónák és a fogadott DNS-lekérdezések számán alapul. A díjszabással kapcsolatos további információkért tekintse meg [az Azure DNS díjszabásával](https://azure.microsoft.com/pricing/details/dns/) kapcsolatos cikket.

A tartományok és a rekordok az Azure Portal, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI segítségével kezelhetőek. Az automatizált DNS-kezelést igénylő alkalmazások a REST API és SDK-k használatával integrálhatóak a szolgáltatással.

## <a name="customizable-virtual-networks-with-private-domains"></a>Saját tartományokkal rendelkező, testreszabható virtuális hálózatok

Az Azure DNS a privát DNS-tartományok használatát is támogatja egy mostantól nyilvános előzetes verzióban elérhető szolgáltatással. Ez a szolgáltatás lehetővé teszi, hogy a jelenleg elérhető, Azure által biztosított nevek helyett egyéni tartományneveket használjon a virtuális magánhálózatokon.

További információkat az [Azure DNS privát tartományokhoz való használatát](private-dns-overview.md) ismertető cikkben olvashat.

## <a name="alias-records"></a>Aliasrekordok

Az Azure DNS az aliasrekord-készletek használatát is támogatja. Aliasrekord-készletekkel Azure-erőforrásra is hivatkozhat, például nyilvános Azure IP-címre vagy Azure Traffic Manager-profilra. Ha a mögöttes erőforrás IP-címe módosul, az aliasrekord-készlet a DNS feloldása során zökkenőmentesen frissíti magát. Az aliasrekord-készlet a szolgáltatáspéldányra mutat, a szolgáltatáspéldány pedig egy IP-címmel van társítva. 

Ezenkívül mostantól a legfelső vagy csupasz tartományt egy Traffic Manager-profilra irányíthatja egy aliasrekorddal. Például: contoso.com.

További információért lásd: [Az Azure DNS-aliasrekordok áttekintése](dns-alias.md).


## <a name="next-steps"></a>További lépések

* További tudnivalókat a DNS-zónákról és -rekordokról [a DNS-zónák és -rekordok áttekintésében](dns-zones-records.md) olvashat.

* Az Azure DNS-ben a zónák létrehozásáról a [DNS-zónák létrehozását](./dns-getstarted-create-dnszone-portal.md) ismertető témakörben olvashat.

* Az Azure DNS-sel kapcsolatos gyakori kérdésekért lásd az [Azure DNS gyakori kérdéseket](dns-faq.md).

