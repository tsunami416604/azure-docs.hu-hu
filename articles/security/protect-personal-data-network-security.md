---
title: "Azure hálózati funkciókat a személyes adatok védelme |} Microsoft Docs"
description: "Az Azure hálózati biztonsági funkciókat használ, a személyes adatok védelme"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Hálózati biztonsági funkciók személyes adatok védelme: Azure Application Gateway és a hálózati biztonsági csoportok

Ez a cikk információkat tartalmazza, és eljárások, amelyek segítséget Azure Application Gateway és a hálózati biztonsági csoportok használata a személyes adatok védelmét.

Egy többrétegű biztonsági stratégia személyes adatok védelme fontos eleme a például az SQL-injektálás vagy többhelyes scripting közös biztonsági rések elleni védelmet. Nem kívánt hálózati forgalom kívül az Azure virtuális hálózat segíti a bizalmas adatok esetleges biztonsági sérülésekkel szembeni védelmére, és a Microsoft Azure lehetővé teszi az eszközök ellen, az adatok védelme érdekében.

## <a name="scenario"></a>Forgatókönyv

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a műveleteket a mediterrán, Adriai, és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani. Az adott erőfeszítéseket abból a célból több kisebb körutazás sorok Olaszország, Németországban, Dánia és az Egyesült szerzett

A vállalat a Microsoft Azure használatával a felhőben tárolt vállalati adatok és alkalmazások futtatásához a virtuális gépeken futó feldolgozni, és elérje ezeket az adatokat. Ezen adatok tartalmazzák a személyes azonosításra alkalmas adatokat, például neveket, címeket, telefonszámokat, és a globális felhasználói bázis hitelkártya adatait. Az összes hely például a címet, telefonszámot, azonosító számokat és egyéb információkat a vállalat alkalmazottai hagyományos emberi erőforrások adatokat is tartalmaz. A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagok nyomon követéséhez a kapcsolatokat az aktuális és korábbi ügyfelek személyes információkat is tartalmaz.

Vállalati alkalmazottak hozzáférjenek a hálózathoz, a távoli iroda a vállalat és a világ utazás ügynökök lehet elérni egyes vállalati erőforrásokat és webalkalmazásokat az Azure virtuális gépeken futtatott használhatja őket.

## <a name="problem-statement"></a>Probléma leírása

A vállalat kell védelmében ügyfelek és az alkalmazottak személyes adatokat a támadóktól, akik szoftver réseit rosszindulatú kód, amely teheti ki a személyes adatok tárolása, vagy a vállalat felhőalapú alkalmazások által használt.

## <a name="company-goal"></a>Vállalati cél

A vállalat célja, hogy jogosulatlan személyek nem férhetnek hozzá vállalati Azure virtuális hálózatok és az alkalmazások és adatok, amelyek közös biztonsági réseket kihasználva nem találhatók. 

## <a name="solutions"></a>Megoldások

A Microsoft Azure biztonsági mechanizmusai nem kívánt forgalmat Azure virtuális hálózatok belépésének megelőzése érdekében. Bejövő és kimenő forgalom irányítását hagyományosan tűzfalak végzi el. Az Azure az Alkalmazásátjáró a a webalkalmazási tűzfal és a hálózati biztonsági csoportok (NSG), egy egyszerű az elosztott tűzfalat összekötőként is használhatja. Ezek az eszközök lehetővé teszik észleli és blokkolja a nem kívánt hálózati forgalom.

### <a name="application-gatewayweb-application-firewall"></a>Átjáró vagy webes alkalmazás tűzfal

A [webalkalmazási tűzfal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) komponense a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) webes alkalmazásokhoz, amelyek egyre támadásokat, amelyek közös ismert réseit céljainak védelme. Egy központi WAF webes támadások ellen, mind biztonságkezelés leegyszerűsíti az alkalmazások módosítása nélkül.

Azure WAF címek különböző, beleértve az SQL-injektálás, helyközi scripting, HTTP protokollal kapcsolatos szabálysértés és rendellenességeket, botok, webbejárók, képolvasók, közös alkalmazás hibás konfigurációja, HTTP szolgáltatásmegtagadásos támadás kategóriák, és más közös támadásokkal, például a parancs injektálási, HTTP kérelem való, a HTTP-válasz felosztásával, és a távoli fájl befoglalási támadásokat. 

Hozzon létre egy alkalmazás WAF, vagy egy meglévő Alkalmazásátjáró WAF hozzáadása. Mindkét esetben az Azure Application Gateway saját alhálózatba van szükség.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Hogyan hozható létre olyan átjárót a WAF? 

Hozzon létre egy új alkalmazás WAF engedélyezve van, tegye a következőket:

1. Jelentkezzen be az Azure portálra, majd a a **Kedvencek** ablaktáblán megjelenő, a portál **új**

2. Az **Új** panelen kattintson a **Hálózat** elemre.

3. Kattintson a **Alkalmazásátjáró**.

4. Keresse meg az Azure-portálon **kattintson az új \> hálózati \> Alkalmazásátjáró.**

   ![alkalmazásátjárót létrehozása](media/protect-netsec/app-gateway-01.png)

5. Az a **alapjai** panelen megjelenő, adja meg az értékeket a következő mezőket: név, a réteg (Standard vagy WAF), Termékváltozat-méretét (kicsi, közepes vagy nagy) példány száma (a magas rendelkezésre állás 2), előfizetés, erőforráscsoportot és helyet.

6. Az a **beállítások** panel alatt megjelenő **virtuális hálózati**, kattintson a **virtuális hálózatot választ**. Ez a lépés megnyílik, adja meg a virtuális hálózat kiválasztása panel.

7. Kattintson a **hozzon létre új** megnyitásához a **virtuális hálózat létrehozása** panelen.

8. Adja meg a következő értékeket: név, címtartomány, az alhálózat neve, az alhálózati címtartományt. Kattintson az **OK** gombra.

9. Az a **beállítások** részen **előtérbeli IP-konfiguráció**, válassza ki az IP-cím típusa.

10. Kattintson a **egy nyilvános IP-cím kiválasztása** majd **hozzon létre új.**

11. Fogadja el az alapértelmezett értéket, és kattintson a **OK gombra.**

12. Az a **beállítások** részen **figyelő konfigurációs**, jelölje be a HTTP vagy a HTTPS használatára **protokoll**. A HTTPS protokoll használatát, egy tanúsítványra szükség.

13. A WAF adott beállításainak a konfigurálásához: **állapot tűzfal** (**engedélyezve**) és **tűzfal módban** (**megelőzési**). Ha úgy dönt, **észlelési** módot, forgalmat csak naplózza.

14. Tekintse át a **összegzés** lapot, és kattintson **OK**. Az Alkalmazásátjáró most sorba és létre.

Az Alkalmazásátjáró létrehozását követően navigáljon a portálon, és folytassa az alkalmazás-átjáró konfigurálását.

![az Alkalmazásátjáró létrehozása](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Hogyan WAF hozzáadása egy meglévő alkalmazást?

Meglévő Alkalmazásátjáró megelőzési módban WAF támogatásához frissítéséhez tegye a következőket:

1. Az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre.

2. Kattintson a meglévő alkalmazás-átjáró a **összes erőforrás** panelen. 
>[!NOTE]
Megjegyzés: Ha már kijelölt előfizetésben több erőforrást tartalmaz, megadhatja a nevét a szűrő név szerint... mezőbe.
3. Kattintson a **webalkalmazási tűzfal** és az alkalmazás-átjáró beállításainak frissítése: **váltson WAF csomagra** (be van jelölve), **állapot tűzfal** (engedélyezve), **tűzfal módban** (megelőzési). Szükség konfigurálja a szabálykészletet, és a letiltott szabályok konfigurálása.

Részletesebb információk létrehozásával egy új Alkalmazásátjáró WAF és WAF hozzáadása egy meglévő Alkalmazásátjáró: [hozzon létre egy alkalmazás webalkalmazási tűzfal a portál használatával.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

A [hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) felsorolja azokat a szabályokat, amelyek engedélyezik vagy megtagadják a csatlakoztatott hálózati forgalom [Azure virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/) (VNet). Lehet, hogy az NSG-k társított alhálózat vagy az egyes virtuális gépeken. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. A forgalom tovább korlátozható egy NGS-t virtuális géphez vagy hálózati adapterhez társításával.

Az NSG-k négy tulajdonságokat tartalmazzák: név, régió, erőforráscsoport és szabályok.
>[!Note]
Az NSG-k egy adott erőforráscsoportban találhatók, azonban bármely erőforráscsoport erőforrásaihoz társíthatók, feltéve, hogy az erőforrás ugyanahhoz az Azure-régióhoz tartozik, mint az NSG.

NSG-szabályok kilenc tulajdonságokat tartalmazzák: név, a protokoll (TCP, UDP vagy \*, mely tartalmazza az ICMP, valamint az UDP és a TCP), forrás-porttartomány, Célporttartomány, forráscímelőtag, célcím-előtag, irányát (bejövő vagy kimenő), a prioritás (100 és 4096 közötti szám) és a hozzáférés típusa (engedélyezése vagy megtagadása). Minden NSG tartalmaz egy alapértelmezett szabálykészletet, hogy törölték, vagy létrehozott szabályok felülbírálják.

#### <a name="how-do-i-implement-nsgs"></a>Hogyan valósítja meg az NSG-ket?

Az NSG-k végrehajtására is szükség van, és számos tervezési szempontot kell figyelembe venni. Ezek közé tartoznak az NSG-k száma / előfizetést és NSG; szabálynál vonatkozó korlátozások VNet és alhálózat kialakítása, különleges szabályokat, az ICMP-forgalmat, elszigetelési meghatározási alhálózatok, terheléselosztó és több.

Planning and implementing NSG-ket, és egy minta környezet-forgatókönyv és a További útmutatóért lásd: [hálózati forgalmat hálózati biztonsági csoportokkal.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Hogyan hozható létre szabályokat egy NSG?

Egy meglévő NSG bejövő szabályok létrehozásához tegye a következőket:

1. Kattintson a **Tallózás**, majd **hálózati biztonsági csoportok**.

2. Az NSG-ket, kattintson a **NSG-előtérbeli**, majd **bejövő biztonsági szabályok.**

3. Bejövő biztonsági szabályok, kattintson a **hozzáadása.**

4. Adja meg az értékeket a következő mezőket: nevét, a prioritás, a forrás, a protokoll, a forrás között, célt cél porttartomány és művelet.

Az új szabály megjelenik az NSG néhány másodperc múlva.

![hálózati biztonsági szabályok](media/protect-netsec/inbound-security.png)

További információkat az NSG-k létrehozásához az alhálózatok, létrehozhat szabályokat, és társít egy NSG előtér- és alhálózattal, a következő témakörben: [az Azure portál használatával a hálózati biztonsági csoportok létrehozása.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Következő lépések

[Az Azure hálózati biztonság](https://azure.microsoft.com/blog/azure-network-security/)

[Azure-hálózat ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[A hálózati biztonsági csoportok adatainak beolvasása](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
