---
title: Az Azure DNS-alias a rekordok áttekintése
description: A Microsoft Azure DNS-alias rekordok támogatása áttekintése.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/20/2019
ms.author: victorh
ms.openlocfilehash: d751d4898be3fd19f9e6f5d03e9313e9d98e9dd2
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446094"
---
# <a name="azure-dns-alias-records-overview"></a>Az Azure DNS-alias a rekordok áttekintése

Az Azure DNS-alias rekordokat a DNS-rekordhalmazok szoftverösszetevője. Ezek hivatkozhat más Azure-erőforrások belül a DNS-zónát. Ha például egy alias a rekordhalmaz helyett egy A rekordot az Azure nyilvános IP-cím hivatkozó is létrehozhat. Az alias bejegyzése set mutat az Azure nyilvános IP-cím dinamikusan szolgáltatáspéldány. Ennek eredményeképpen az alias rekordhalmaz zökkenőmentesen frissíti a saját DNS-feloldása során.

A következő rekordtípusokhoz, az Azure DNS-zóna egy aliast rekordhalmaz támogatott: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Ha szeretne használni a vagy AAAA típusú rekord típust-alias rekord átirányítása egy [Azure Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md) meg kell győződnie arról, hogy a Traffic Manager-profil csak rendelkezik [külső végpontokat](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Külső végpontok Traffic Managerben, meg kell adnia az IPv4 vagy IPv6-címet. Ideális esetben használjon statikus IP-címeket.

## <a name="capabilities"></a>Funkciók

- **A DNS A vagy AAAA típusú rekordhalmaz mutasson a nyilvános IP-erőforrást.** You can create an A/AAAA record set and make it an alias record set to point to a public IP resource. A DNS-rekordhalmaz automatikusan megtörténik, ha a nyilvános IP-cím megváltozik, vagy törölni. DNS értékhiányos rekordokat, amelyek nem megfelelő IP-címek kerülni a rendszer.

- **A DNS A/AAAA/CNAME-rekordhalmazok átirányítása a Traffic Manager-profil.** You can create an A/AAAA or CNAME record set and use alias records to point it to a Traffic Manager profile. Ez különösen hasznos ha kell irányíthatja a forgalmat a zóna legfelső pontján, mint a hagyományos CNAME-rekordokat a zóna felső pontja nem támogatottak. Tegyük fel például, hogy a Traffic Manager-profil myprofile.trafficmanager.net, és a vállalati DNS-zónát a contoso.com. Hozzon létre egy alias rekordhalmaz írja be A/AAAA contoso.com (a zóna felső pontja), és myprofile.trafficmanager.net mutasson.

- **Egy másik DNS rekordhalmaz ugyanabban a zónában lévő mutasson.** Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Például egy DNS CNAME-rekordhalmazt lehet egy alias egy másik CNAME-rekordhalmazt. Ezzel az elrendezéssel fokozott akkor hasznos, ha azt szeretné, hogy néhány rekordhalmazt kell aliasok és az egyes nem alias.

## <a name="scenarios"></a>Forgatókönyvek

Van néhány olyan gyakori helyzetet Alias rekordokat.

### <a name="prevent-dangling-dns-records"></a>Értékhiányos DNS-rekordok letiltása

A hagyományos DNS-rekordok egyik általános problémája van értékhiányos rögzíti. Ha például DNS-rekordok változásainak IP-címek nem lettek frissítve. A probléma akkor fordul elő, különösen az A/AAAA vagy CNAME rekordtípusok.

A hagyományos DNS-zóna rekord ha a cél IP-címet vagy CNAME már nem létezik, a hozzá társított DNS-rekord manuálisan frissíteni kell. Egyes szervezetekben manuális frissítés nem fordulhat elő, a folyamat problémák miatt, vagy a szerepkörök és engedélyszintek társított elkülönítése miatt. Például egy szerepkör előfordulhat, hogy megvan a egy CNAME vagy IP-cím, amelyhez tartozik egy alkalmazás törléséhez. De nem rendelkezik elegendő szolgáltató ezen célok mutató DNS-rekordot frissíteni. Késés tapasztalható a DNS-rekord frissítése a felhasználók számára egy szolgáltatáskimaradás okozhatja esetleg azt.

Alias rekordok értékhiányos hivatkozások megakadályozása szorosan kapcsoló egy Azure-erőforrás a DNS-rekord teljes életciklusát. Például érdemes lehet nyilvános IP-cím vagy a Traffic Manager-profil átirányítása egy aliast rekordként minősített DNS-rekord. Ha a rendszer törli a mögöttes erőforrások, a DNS-aliasrekordot a egyszerre törlődik.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>DNS-rekordkészletet, automatikusan frissíti az alkalmazás IP-címek változásakor

Ez a forgatókönyv hasonlít az előzőre. Például egy alkalmazás áthelyezése, vagy a mögöttes virtuális gép újraindul. Az alias rekord majd automatikusan frissíti az IP-cím változásának az alapul szolgáló nyilvános IP-erőforráshoz. Ezzel elkerülhető a potenciális biztonsági kockázatok irányítani a felhasználók számára, amely a régi nyilvános IP-cím van rendelve egy másik alkalmazás.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Elosztott terhelésű alkalmazások üzemeltetését a zóna legfelső pontján

A DNS protokoll megakadályozza, hogy a CNAME-rekordokat a zóna legfelső pontján hozzárendelését. Például ha a tartománya a contoso.com; hozhat létre CNAME-rekordokat a somelable.contoso.com; azonban nem hozhat létre CNAME contoso.com magát.
Ez a korlátozás problémát jelent, a kérelmek terheléselosztással rendelkező alkalmazástulajdonosok mögött [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Egy CNAME rekord létrehozása a Traffic Manager-profil használatával van szüksége, mivel nincs lehetőség a zóna felső pontja a Traffic Manager-profilt mutassanak.

Ez a probléma alias rekordok használatával kell megoldani. Ellentétben a CNAME-rekordokat alias-rekord zóna felső pontjánál hozható létre, és alkalmazástulajdonosok használhatja a zóna felső pontja rekord átirányítása egy Traffic Manager-profil, amely rendelkezik a külső végpontokat. Alkalmazástulajdonos más tartományban a DNS-zóna használt azonos Traffic Manager-profilt is mutat.

Például a contoso.com és a www.contoso.com is mutasson a Traffic Manager-profilt. További információt az Azure Traffic Manager-profilok alias rekordok használatával, tekintse meg a következő lépések szakasz.

## <a name="next-steps"></a>További lépések

További információt az alias rekordok, tekintse meg a következő cikkeket:

- [Oktatóanyag: Tekintse meg az Azure nyilvános IP-cím egy aliast rekord konfigurálása](tutorial-alias-pip.md)
- [Oktatóanyag: -Aliasrekordot támogatásához apex-tartománynevek a Traffic Manager konfigurálása](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
