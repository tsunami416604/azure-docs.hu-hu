---
title: Mi az a Dedicated HSM? - Az Azure dedikált HSM | Microsoft dokumentumok
description: Az Azure dedikált HSM-jének áttekintése olyan kulcsfontosságú tárolási lehetőségeket kínál az Azure-ban, amelyek megfelelnek a FIPS 140-2 Level 3 minősítésnek
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337175"
---
# <a name="troubleshooting"></a>Hibaelhárítás

Az Azure dedikált HSM-szolgáltatás két különböző aspektusa van. Először is, a hsm-eszközök regisztrációja és üzembe helyezése az Azure-ban az alapul szolgáló hálózati összetevőkkel. Másodszor, a HSM-eszközök konfigurálása egy adott számítási feladattal vagy alkalmazással való használatra/integrációra előkészítve. Bár a Thales Luna Network HSM-eszközök ugyanazok az Azure-ban, mint közvetlenül a Thales-től vásárolna, az a tény, hogy az Azure-ban erőforrásnak minősülnek, néhány egyedi szempontot hoz létre. Ezeket a szempontokat és az ebből eredő hibaelhárítási elemzéseket és bevált gyakorlatokat itt dokumentáljuk a nagy láthatóság és a kritikus információkhoz való hozzáférés biztosítása érdekében. A szolgáltatás használatba kerülése után végleges információk érhetők el a Microsoft vagy a Thales közvetlen ülésére irányuló támogatási kérelmeken keresztül. 

> [!NOTE]
> Meg kell jegyezni, hogy mielőtt bármilyen konfigurációt egy újonnan telepített HSM-eszközön végezne, frissíteni kell a megfelelő javításokkal. Egy speciális javítás [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) a Thales támogatási portálon, amely az újraindítási lefagyási problémát szünteti meg.

## <a name="hsm-registration"></a>HSM regisztráció

A dedikált HSM nem szabadon használható, mivel hardvererőforrásokat biztosít a felhőben, és ezért értékes erőforrás, amelyet védeni kell. Ezért használjuk a whitelisting HSMrequest@microsoft.comfolyamat e-mailben segítségével. 

### <a name="getting-access-to-dedicated-hsm"></a>Hozzáférés a dedikált HSM-hez

Ha úgy gondolja, hogy a Dedikált HSM HSMrequest@microsoft.com megfelel a legfontosabb tárolási követelményeknek, akkor e-mailben kérhet hozzáférést. Vázolja az alkalmazást, a kívánt régiókat és a keresett HSM-ek mennyiségét. Ha például egy Microsoft-képviselővel, például egy fiókkezelővel vagy egy felhőalapú megoldástervezővel dolgozik, akkor minden kérelembe belekell foglalnia őket.

## <a name="hsm-provisioning"></a>HSM-kiépítés

Egy HSM-eszköz kiépítése az Azure-ban cli vagy PowerShell keresztül végezhető el. A szolgáltatásra való regisztráció során egy minta ARM sablont biztosítunk, és segítséget kap a kezdeti testreszabáshoz. 

### <a name="hsm-deployment-failure-information"></a>HSM központi telepítési hiba információi

Dedikált HSM támogatja a CLI és a PowerShell üzembe helyezési így portálalapú hibainformációk korlátozott, és nem részletes. Jobb információ az Erőforrás-kezelő használatával érhető el. A portál kezdőlapján van egy ikon, és részletesebb hibainformáció áll rendelkezésre. Ez az információ sokat segít, ha beillesztette a telepítési problémákkal kapcsolatos támogatási kérelem létrehozásakor.

![Hiba információ](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM-alhálózati delegálás
Az üzembe helyezési hibák első számú oka az, hogy elfelejti beállítani a megfelelő delegálást az ügyfél által definiált alhálózathoz, amelyen a HSM-ek ki lesznek építve. A delegálás beállítása a virtuális hálózat és az alhálózat központi telepítésének előfeltételei, és további részletek találhatók az oktatóanyagokban.

![Alhálózati delegálás](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>HSM telepítési verseny állapota

A központi telepítéshez biztosított szabványos ARM-sablon HSM- és ExpressRoute-átjáróval kapcsolatos erőforrásokkal rendelkezik. A hálózati erőforrások a HSM sikeres üzembe helyezésétől függenek, és az időzítés döntő fontosságú lehet.  Alkalmanként láttuk a függőségi problémákkal kapcsolatos telepítési hibákat, és a központi telepítés újrafuttatása gyakran megoldja a problémát. Ha nem, az erőforrások törlése, majd az újratelepítés gyakran sikeres. Miután megpróbálta ezt, és továbbra is a probléma keresése, elő egy támogatási kérelmet az Azure Portalon kiválasztja a probléma típusát "Problémák konfigurálása az Azure-beállítás".

### <a name="hsm-deployment-using-terraform"></a>HSM-telepítés terraform használatával

Néhány ügyfél a Terraform-ot használta automatizálási környezetként az ARM sablonok helyett, ahogy azt a szolgáltatásra való regisztráció során biztosították. A HSM-ek nem telepíthetők így, de a függő hálózati erőforrások. Terraform egy modult, hogy hívja ki, hogy egy minimális ARM sablont, hogy jut a HSM telepítés.  Ebben a helyzetben ügyelni kell arra, hogy a hálózati erőforrások, például a szükséges ExpressRoute-átjáró teljes mértékben üzembe helyezése a HSM-ek telepítése előtt. A következő CLI parancs használható a befejezett üzembe helyezés tesztelésére és szükség szerint integrálható. Cserélje ki a szögletes zárójel helytartóit az adott elnevezéshez. Meg kell keresni az eredményt a "provisioningState sikeres"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Telepítési hiba kvóta alapján
A központi telepítések sikertelenek lehetnek, ha meghaladja a 2 HSM-et bélyegzőnként és 4 HSM-et régiónként. A helyzet elkerülése érdekében győződjön meg arról, hogy törölte az erőforrásokat a korábban sikertelen telepítések újra üzembe helyezése előtt. Az erőforrások ellenőrzéséhez tekintse meg az alábbi "Hogyan jelenek meg a HSM-ek" című elemet. Ha úgy gondolja, hogy meg kell haladnia ezt a kvótát, amely elsősorban ott van biztosítékként, akkor kérjük, írjon e-mailt HSMrequest@microsoft.com a részletekkel.

### <a name="deployment-failure-based-on-capacity"></a>Telepítési hiba kapacitás alapján
Ha egy adott bélyegző vagy régió megtelik, azaz szinte az összes ingyenes HSM van kiépítve, ez üzembe helyezési hibákhoz vezethet. Minden bélyegző 11 HSM-et bocsát az ügyfelek rendelkezésére, ami régiónként 22-t jelent. Minden bélyegzőben 3 pótalkatrész és 1 teszteszköz található. Ha úgy gondolja, hogy elérte a HSMrequest@microsoft.com korlátot, akkor e-mailben tájékoztatást kaphat az egyes bélyegzők kitöltéséről.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hogyan láthatom a HSM-eket kiépítéskor?
Mivel a dedikált HSM egy engedélyezési szolgáltatás, az Azure Portalon "rejtett típusnak" minősül. A HSM-erőforrások megtekintéséhez be kell jelentkeznie a "Rejtett típusok megjelenítése" jelölőnégyzetbe az alábbi módon. A hálózati hálózati adapter erőforrás mindig követi a HSM, és egy jó hely, hogy megtudja, az IP-címét a HSM használata előtt SSH csatlakozni.

![Alhálózati delegálás](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Hálózati erőforrások

A dedikált HSM telepítése hálózati erőforrásoktól és néhány ebből eredő korlátozástól függ.

### <a name="provisioning-expressroute"></a>Az ExpressRoute kiépítése

A dedikált HSM az ExpressRoute-átjárót "alagútként" használja az ügyfél privát IP-címtere és az Azure-adatközpontban lévő fizikai HSM közötti kommunikációhoz.  Figyelembe véve, hogy a virtuális hálózatonként egy átjáró korlátozása van érvényben, a helyszíni erőforrásokhoz az ExpressRoute-on keresztül kapcsolatot igénylő ügyfeleknek egy másik virtuális hálózatot kell használniuk az adott kapcsolathoz.  

### <a name="hsm-private-ip-address"></a>HSM privát IP-cím

A dedikált HSM-hez biztosított mintasablonok feltételezik, hogy a HSM IP automatikusan egy adott alhálózati tartományból származik. A HSM explicit IP-címét az ARM sablonban egy "NetworkInterfaces" attribútumon keresztül adhatja meg. 

![Alhálózati delegálás](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM inicializálása

Az inicializálás előkészíti az új HSM-et a használatra, vagy egy meglévő HSM-et újrahasználatra. A HSM inicializálásának be kell fejeződnie ahhoz, hogy objektumokat generálhat vagy tároljon, lehetővé tegye az ügyfelek számára a csatlakozást vagy a kriptográfiai műveleteket.

### <a name="lost-credentials"></a>Elveszett hitelesítő adatok

A Shell rendszergazdai jelszavának elvesztése a HSM kulcsanyag elvesztését eredményezi. A HSM alaphelyzetbe állításához támogatási kérelmet kell benyújtani.
A HSM inicializálásakor biztonságosan tárolja a hitelesítő adatokat. A rendszerhéj és a HSM hitelesítő adatait a vállalat irányelveinek megfelelően kell tartani.

### <a name="failed-logins"></a>Sikertelen bejelentkezések

Helytelen hitelesítő adatok biztosítása a HSM-ek számára romboló következményekkel járhat. A hsm-szerepkörök alapértelmezett viselkedései az alábbi műveleteket követik.

| Szerepkör | Küszöbérték (próbálkozások száma) | A túl sok rossz bejelentkezési kísérlet eredménye | Helyreállítás |
|--|--|--|--|
| HSM SO | 3 |  HSM nullázva (az összes HSM-objektum identitások, és az összes partíciót elment)  |  A HSM-et újra kell inicializálni. A tartalom visszaállítható a biztonsági mentés(ek)ből. | 
| Partíció SO | 10 |  A partíció nullázva van. |  A partíciót újra inicializálni kell. A tartalom visszaállítható a biztonsági mentésből. |  
| Naplózás | 10 | Lockout | 10 perc elteltével automatikusan feloldva. |  
| Crypto tisztviselő | 10 (csökkenthető) | Ha HSM-házirend 15: Engedélyezése SO visszaállítása partíció PIN-kód van beállítva 1 (engedélyezve van), a CO és a CU szerepkörök vannak zárva.<br>Ha HSM-házirend 15: Engedélyezése SO visszaállítása partíció PIN-kód beállítása 0 (le tiltva), a CO és a CU szerepkörök véglegesen zárolva vannak, és a partíció tartalma már nem érhető el. Ez az alapértelmezett beállítás. | CO szerepkör fel kell oldani, és a hitelesítő `role resetpw -name co`adatok alaphelyzetbe a partíció SO, a .<br>A partíciót újra inicializálva kell, és a kulcsanyagot vissza kell állítani egy biztonsági másolat készítő eszközről. |  

## <a name="hsm-configuration"></a>HSM konfiguráció 

A következő elemek olyan helyzetek, amikor a konfigurációs hibák gyakoriak, vagy olyan hatással vannak, amely érdemes a kihívásra:

### <a name="hsm-documentation-and-software"></a>HSM dokumentáció és szoftver
A Thales SafeNet Luna 7 HSM eszközök szoftverei és dokumentációi nem érhetők el a Microsofttól, és közvetlenül a Thales-től kell letölteni. A regisztráció a regisztrációs folyamat során kapott Thales ügyfélazonosító használatával szükséges. Az eszközök, a Microsoft által biztosított, szoftver es 7.2-es verzióés firmware verzió 7.0.3. 2020 elején a Thales nyilvánosságra hozta a dokumentációt, és [itt](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)található .  

### <a name="hsm-networking-configuration"></a>HSM hálózati konfiguráció

Legyen óvatos, amikor konfigurálja a hálózat a HSM.L careful ing when configuring the networking within the HSM.  A HSM az ExpressRoute-átjárón keresztül közvetlenül a HSM-hez való kapcsolattal rendelkezik egy ügyfél privát IP-címteréből.  Ez a kommunikációs csatorna csak az ügyfelek kommunikációjára szolgál, és a Microsoft nem rendelkezik hozzáféréssel. Ha a HSM úgy van konfigurálva, hogy ez a hálózati elérési út hatással van, az azt jelenti, hogy a HSM-mel folytatott összes kommunikáció törlődik.  Ebben az esetben az egyetlen lehetőség az, hogy a Microsoft támogatási kérelmet az Azure Portalon keresztül, hogy az eszköz alaphelyzetbe állítása. Ez az alaphelyzetbe állítási eljárás visszaállítja a HSM-et a kezdeti állapotába, és az összes konfiguráció és kulcsanyag elvész.  A konfigurációt újra létre kell hozni, és amikor az eszköz csatlakozik a HA-csoporthoz, a kulcsanyag replikálása lesz.  

### <a name="hsm-device-reboot"></a>HSM-eszköz újraindítása

Egyes konfigurációs módosítások megkövetelik, hogy a HSM tápellátását ciklusban vagy újraindítsák. A HSM Azure-beli Microsoft-tesztelése megállapította, hogy bizonyos esetekben az újraindítás lefagyhat. Ennek az a következménye, hogy egy támogatási kérelmet létre kell hozni az Azure Portalon, amely hard-rebootot kér, és amely akár 48 órát is igénybe vehet, tekintve, hogy egy azure-adatközpontban manuális folyamatról van szó.  A helyzet elkerülése érdekében győződjön meg arról, hogy közvetlenül a Thales-től elérhető újraindítási javítást telepítette. Lásd [kb0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) a Thales Luna Network HSM 7.2 Letöltések egy ajánlott javítás egy újraindítás lefagy kérdés (Megjegyzés: akkor regisztrálnia kell a Thales támogatási portál letölthető).

### <a name="ntls-certificates-out-of-sync"></a>Az NTLS-tanúsítványok nincsenek szinkronban
Az ügyfél elveszítheti a hsm-hez való kapcsolódást, ha egy tanúsítvány lejár, vagy a konfigurációs frissítések en keresztül felülírták. A tanúsítványcsere-ügyfél konfigurációját minden HSM-mel újra alkalmazni kell.
Példa NTLS naplózásra érvénytelen tanúsítvánnyal:

> NTLS[8508]: info : 0 : Bejövő csatlakozási kérelem... : 192.168.50.2/59415 NTLS[8508]: Az SSLAccept hibaüzenete: hiba:14094418:SSL rutinok:ssl3_read_bytes:tlsv1 riasztás ismeretlen ca NTLS[8508]: Hiba az SL accept során (RC_SSL_ERROR) NTLS[8508]: info : 0xc0000711 : Nem sikerült biztonságos csatornát létrehozni az ügyféllel: 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS-ügyfél "Ismeretlen állomásnév" Csatlakozási példány eltávolítva : 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Sikertelen TCP-kommunikáció

A Luna client telepítésének a HSM-hez való kommunikációjához legalább 1792-es TCP-portszükséges. Tekintsük ezt úgy, hogy a környezetben minden hálózati konfiguráció módosul.

### <a name="failed-ha-group-member-doesnt-recover"></a>A SIKERTELEN HA-csoporttag nem állítható helyre

Ha egy sikertelen HA-csoport tag nem helyreállítani, azt manuálisan kell helyreállítani a Luna ügyféla parancs hagroup helyreállítása.
Az automatikus helyreállítás engedélyezéséhez újrapróbálkozások számát kell konfigurálni a HA-csoporthoz. Alapértelmezés szerint egy HA-csoport nem kísérli meg a HA-tag helyreállítását a csoportba, amikor helyreáll.

### <a name="ha-group-doesnt-sync"></a>Ha csoport nem szinkronizál

Abban az esetben, ha a tagpartíciók nem rendelkeznek ugyanazzal a klónozó tartománnyal, a ha szinkronizálási parancs a következőt jeleníti meg: Figyelmeztetés: A szinkronizálás sikertelen lehet.  A 0 és 1 bővítőhely tagjai ütköznek a személyes kulcsklónozáshoz.
A MEGFELELŐ klónozási tartománnyal rendelkező új partíciót hozzá kell adni a HA-csoporthoz, majd el kell távolítani a helytelenül konfigurált partíciót.

## <a name="hsm-deprovisioning"></a>HSM-megszüntetés 

Csak akkor, ha teljesen kész a HSM lehet megszüntetni, majd a Microsoft alaphelyzetbe állítja, és visszaadja azt egy ingyenes készlet. 

### <a name="how-to-delete-an-hsm-resource"></a>HSM-erőforrás törlése

A HSM Azure-erőforrása nem törölhető, kivéve, ha a HSM "nulláztatott" állapotban van.  Ezért minden kulcsfontosságú anyagot törölni kell, mielőtt erőforrásként próbálna törölni. A leggyorsabb módja annak, hogy nullázás, hogy a HSM admin jelszó rossz 3-szor (megjegyzés: ez utal a HSM admin, és nem a készülék szintű admin). A Luna rendszerhéj `hsm -factoryreset` nak van egy nullázó parancsa, de csak konzolon keresztül hajtható végre a soros porton, és az ügyfelek nem férnek hozzá ehhez.

## <a name="next-steps"></a>További lépések

Ez a cikk betekintést nyújtott a HSM telepítési életciklusának olyan területeibe, amelyek problémákat okozhatnak, vagy hibaelhárítást vagy gondos megfontolást igényelhetnek. Remélhetőleg ez a cikk segít elkerülni a szükségtelen késedelmeket és frusztrációt, és ha releváns kiegészítéseket vagy változtatásokat, majd emelje fel a támogatási kérelmet a Microsoft, és tudassa velünk. 
