---
title: Az Azure DNS-alias a rekordok áttekintése
description: A Microsoft Azure DNS-alias rekordok támogatása áttekintése.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092842"
---
# <a name="azure-dns-alias-records-overview"></a>Az Azure DNS-alias a rekordok áttekintése

Az Azure DNS-alias rekordokat a DNS-rekordhalmazok szoftverösszetevője. Ezek hivatkozhat más Azure-erőforrások belül a DNS-zónát. Ha például egy alias a rekordhalmaz helyett egy A rekordot az Azure nyilvános IP-cím hivatkozó is létrehozhat. Az alias bejegyzése set mutat az Azure nyilvános IP-cím dinamikusan szolgáltatáspéldány. Ennek eredményeképpen az alias rekordhalmaz zökkenőmentesen frissíti a saját DNS-feloldása során.

A következő rekordtípusokhoz, az Azure DNS-zóna egy aliast rekordhalmaz támogatott: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Alias-rekordjait a vagy AAAA típusú rekord típust az Azure Traffic Manager csak külső végpont típusok támogatottak. Meg kell adnia az IPv4 vagy IPv6-címet, ha a Traffic Manager külső végpontokat szükséges. Ideális esetben használjon statikus IP-címek a címet.

## <a name="capabilities"></a>Funkciók

- **A DNS A vagy AAAA típusú rekordhalmaz mutasson a nyilvános IP-erőforrást.** Egy A vagy AAAA típusú rekordhalmaz létrehozása, és adja meg egy alias rekordhalmaz átirányítása nyilvános IP-erőforrást.

- **A DNS A/AAAA/CNAME-rekordhalmazok átirányítása a Traffic Manager-profil.** A CNAME REKORDOT a Traffic Manager-profil egy DNS CNAME rekord készletből is mutasson. Ez például akkor contoso.trafficmanager.net. Most is mutathat, amely rendelkezik egy DNS-zónáját A vagy AAAA típusú rekordot a külső végpontok Traffic Manager-profilhoz.

   > [!NOTE]
   > Alias-rekordjait a vagy AAAA típusú rekord típust a Traffic Manager esetében csak a külső végpont típusok támogatottak. Meg kell adnia az IPv4 vagy IPv6-címet, ha a Traffic Manager külső végpontokat szükséges. Ideális esetben használjon statikus IP-címek a címet.
   
- **Egy másik DNS rekordhalmaz ugyanabban a zónában lévő mutasson.** Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Például egy DNS CNAME-rekordhalmazt alias egy másik CNAME rekordhalmazhoz azonos típusú is lehet. Ezzel az elrendezéssel fokozott akkor hasznos, ha azt szeretné, hogy néhány rekordhalmazt kell aliasok és az egyes nem alias.

## <a name="scenarios"></a>Forgatókönyvek
Van néhány olyan gyakori helyzetet Alias rekordokat.

### <a name="prevent-dangling-dns-records"></a>Értékhiányos DNS-rekordok letiltása
 Az Azure DNS-zóna, belül alias rekordok szorosan nyomon követéséhez az Azure-erőforrások életciklus használható. Erőforrások közé tartoznak a nyilvános IP-cím vagy a Traffic Manager-profil. A hagyományos DNS-rekordok egyik általános problémája van értékhiányos rögzíti. Ez a probléma akkor fordul elő, különösen az A/AAAA vagy CNAME rekordtípusok. 

A hagyományos DNS-zóna rekord ha a cél IP-címet vagy CNAME már nem létezik, a DNS-zóna rekord nem ismeri. Ennek eredményeképpen a bejegyzést manuálisan kell frissíteni. Egyes szervezetekben a manuális frissítés nem fordulhat elő, időben. Is lehet a problémás a szerepkörök és engedélyszintek társított elkülönítése miatt.

Például egy szerepkör előfordulhat, hogy megvan a egy CNAME vagy IP-cím, amelyhez tartozik egy alkalmazás törléséhez. De nem rendelkezik elegendő szolgáltató ezen célok mutató DNS-rekordot frissíteni. Késleltetés között történik, ha az IP- vagy CNAME törlődik, és a rá mutató DNS-rekord törlődik. A késés a felhasználók számára egy szolgáltatáskimaradás okozhatja esetleg azt.

Alias rekordok távolítsa el az ebben a forgatókönyvben a összetettségét. Ezek segítségével megakadályozhatja a értékhiányos hivatkozik. Vegyük például, a nyilvános IP-cím vagy a Traffic Manager-profil átirányítása egy aliast rekordként minősített DNS-rekord. Ha a rendszer törli a mögöttes erőforrások, a DNS-aliasrekordot a egyszerre törlődik. Ez a folyamat gondoskodik arról, hogy a felhasználók soha nem szenvedett-e a kimaradás.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Az alkalmazás IP-címek változásakor automatikusan frissíti a DNS-zónák

Ez a forgatókönyv hasonlít az előzőre. Például egy alkalmazás áthelyezése, vagy a mögöttes virtuális gép újraindul. Az alias rekord majd automatikusan frissíti az IP-cím változásának az alapul szolgáló nyilvános IP-erőforráshoz. Biztonsági kockázatok elkerülése érdekében közvetlenül a felhasználók számára, amely a régi IP-címmel rendelkezik egy másik alkalmazás.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Elosztott terhelésű alkalmazások üzemeltetését a zóna legfelső pontján

A DNS protokoll megakadályozza, hogy az A vagy AAAA rekord a zóna legfelső pontján csakis a hozzárendelését. Például a contoso.com. Ez a korlátozás az elosztott terhelésű alkalmazások Forgalomkezelő mögé rendelkező alkalmazástulajdonosok problémát jelent. Nem lehet a zóna felső pontja rekordból a Traffic Manager-profil mutassanak. Ennek eredményeképpen alkalmazástulajdonosok áthidaló kell használnia. Az ügyfélalkalmazási rétegben átirányítási kell átirányítási a zóna felső pontja a egy másik tartományba. Ilyen például, egy irányítja át a contoso.com www.contoso.com. Ezzel az elrendezéssel fokozott megjelenít egy átirányítási függvényéhez meghibásodási pont.

Alias rekordokat a probléma már nem létezik. Alkalmazástulajdonos most már rendelkezik külső végpontok Traffic Manager-profil is mutat a zóna felső pontja rekord. Alkalmazástulajdonos más tartományban a DNS-zóna használt azonos Traffic Manager-profilt is mutat. Például a contoso.com és a www.contoso.com is mutasson a Traffic Manager-profilt. Ez a helyzet, amíg a Traffic Manager-profil csak külső végpontokkal konfigurálva van.

## <a name="next-steps"></a>További lépések

További információt az alias rekordok, tekintse meg a következő cikkeket:

- [Oktatóanyag: Egy aliast rekord tekintse meg az Azure nyilvános IP-cím konfigurálása](tutorial-alias-pip.md)
- [Oktatóanyag: Egy aliasrekordot támogatásához apex-tartománynevek a Traffic Manager konfigurálása](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
