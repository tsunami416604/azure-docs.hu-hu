---
title: Az Azure DNS-alias a rekordok áttekintése
description: A Microsoft Azure DNS-alias rekordok támogatása áttekintése.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957572"
---
# <a name="azure-dns-alias-records-overview"></a>Az Azure DNS-alias a rekordok áttekintése

Az Azure DNS-alias rekordok bizonyságot adhatnak meg, amely lehetővé teszi, hogy hivatkozhat más Azure-erőforrások belül a DNS-zóna DNS rekordhalmazok. Például létrehozhat egy alias a rekordhalmaz hivatkozó Azure nyilvános IP-cím egy A rekordot. Az alias rekordhalmaz dinamikusan mutat egy Azure nyilvános IP-cím szolgáltatáspéldány, mert az alias rekordhalmaz zökkenőmentesen frissíti a saját DNS-feloldása során.

A következő rekordtípusokhoz, az Azure DNS-zóna egy aliast rekordhalmaz támogatott: A, AAAA és a CNAME. 

> [!NOTE]
> Alias-rekordjait a vagy AAAA típusú rekord típust a Traffic Managerhez csak külső végpont esetében támogatottak. Meg kell adnia az IPv4 vagy IPv6-címet (ideális esetben statikus IP-címek) megfelelő külső végpontok Traffic Managerben.

## <a name="capabilities"></a>Funkciók

- **A/AAAA típusú rekordhalmaz DNS nyilvános IP-cím erőforráshoz pontot**. -A vagy AAAA típusú rekordhalmaz létrehozása, és adja meg egy aliast rekordhalmaz átirányítása egy nyilvános IP-cím erőforrás.
- **Mutasson a Traffic Manager-profil a DNS A/AAAA/CNAME-rekordhalmazok**. Mutasson a Traffic Manager-profil CNAME mellett (például: contoso.trafficmanager.net) egy DNS CNAME-rekordhalmazt a most is mutatva, amely rendelkezik a külső végpontokat, egy A vagy AAAA típusú rekordhalmaz a DNS-ben a Traffic Manager-profil zóna.
   > [!NOTE]
   > Alias-rekordjait a vagy AAAA típusú rekord típust a Traffic Managerhez csak külső végpont esetében támogatottak. Meg kell adnia az IPv4 vagy IPv6-címet (ideális esetben statikus IP-címek) megfelelő külső végpontok Traffic Managerben.
- **Egy másik DNS rekordhalmaz ugyanabban a zónában lévő mutasson**. Alias rekordok hivatkozhat, más azonos típusú rekordhalmazok. Rendelkezhet például egy DNS CNAME rekordhalmaz egy alias egy másik CNAME-rekordhalmazt azonos típusú lehet. Ez akkor hasznos, ha azt szeretné, néhány rekordhalmazt kell aliasok és néhány, az aliasokat viselkedése tekintetében.

## <a name="scenarios"></a>Forgatókönyvek
Van néhány olyan gyakori helyzetet Alias rekordokat:

### <a name="prevent-dangling-dns-records"></a>Értékhiányos DNS-rekordok letiltása
A belül az Azure DNS-zóna, alias-rekordok segítségével szorosan nyomon követheti, az Azure-erőforrások egy nyilvános IP-cím és a Traffic Manager-profil például életciklusát. A hagyományos DNS-rekordok kapcsolatos gyakori problémák egyike "értékhiányos rekordokat", különösen A/AAAA vagy CNAME rekordtípusok. 

A hagyományos DNS-zóna rekord ha már nem létezik a cél IP-címet vagy a CNAME, a DNS-zóna rekord nem rendelkezik a tényt, és manuálisan frissíteni kell. Egyes szervezetekben a manuális frissítés nem fordulhat elő, időben, vagy a szerepkörök és engedélyszintek társított elkülönítése miatt problémás lehet.

Például a szerepkör, amely rendelkezik az egy alkalmazáshoz tartozó CNAME vagy IP-cím törlése nem lehet frissíteni a DNS-rekordot, amely ezen célok mutat megfelelő hatáskörrel. Ennek eredményeképpen előfordulhat olyan IP- vagy CNAME törlésekor, és hogy mutat, el kell távolítani, amely kimaradás hibát okozhat a végfelhasználók számára a DNS-rekord közötti késleltetés.

Alias rekordok távolítsa el az ebben a forgatókönyvben a összetettségét, és megakadályozhatja az ilyen értékhiányos hivatkozásokat. DNS-rekord egy aliast rekordként minősített átirányítása nyilvános IP-cím vagy a Traffic Manager-profil, és a rendszer törli a mögöttes erőforrások, a DNS-aliasrekordot egyszerre is eltávolítja. Ez biztosítja, hogy a végfelhasználók számára soha nem kimaradás esetén csökkenhet.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Az alkalmazás IP-címek változásakor automatikusan frissíti a DNS-zónák

Hasonló az előző forgatókönyvben, ha egy alkalmazás áthelyezése vagy a mögöttes virtuális gép újraindul, ha az alias-rekord automatikusan frissül az alapul szolgáló nyilvános IP-erőforráshoz az IP-cím változásakor. Biztonsági kockázatok elkerüléséhez, ha a végfelhasználók számára a rendszer átirányítja egy másik alkalmazás, amely a régi IP-címmel rendelkezik.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Elosztott terhelésű alkalmazások üzemeltetését a zóna legfelső pontján

A DNS protokoll megakadályozza, hogy az A vagy AAAA rekord a zóna legfelső pontján csakis a hozzárendelését (Példa: contoso.com). Ez megadja az alkalmazás tulajdonosok, akik betöltés probléma elosztott terhelésű alkalmazások mögött egy Traffic Manager, mivel nem volt lehetséges, mutasson a Traffic Manager-profil, a zóna felső pontja rekordból. Ennek eredményeképpen alkalmazástulajdonosok kellett használni egy megkerülő megoldás. Ha például átirányítást az alkalmazási rétegben a zóna felső pontja átirányítása egy másik tartomány (contoso.com a www.contoso.com). Ez megadja a hibaérzékeny pont az átirányítási funkciók tekintetében.

Alias rekordokat a probléma már nem létezik. Alkalmazástulajdonos most is a zóna felső pontja rekord mutasson a Traffic Manager-profil, amely rendelkezik a külső végpontokat. Ez a képesség alkalmazástulajdonosok más tartományban a DNS-zóna használt azonos Traffic Manager-profilt is mutat. Például a contoso.com és a www.contoso.com is mutathat ugyanazt a Traffic Manager-profil mindaddig, amíg a Traffic Manager-profil csak külső végpontokkal konfigurálva van.

## <a name="next-steps"></a>További lépések

Több lean típusú alias a rekordok, tekintse meg a feloldását kérte cikkeket:

- [Oktatóanyag: Egy aliasrekordot lehet hivatkozni az Azure nyilvános IP-cím konfigurálása](tutorial-alias-pip.md)
- [Oktatóanyag: Egy aliasrekordot támogatásához apex-tartománynevek a Traffic Manager konfigurálása](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
