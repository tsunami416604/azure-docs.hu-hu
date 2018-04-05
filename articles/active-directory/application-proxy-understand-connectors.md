---
title: Az Azure AD-alkalmazásproxy összekötők megértése |} Microsoft Docs
description: Alapvető tudnivalók az Azure AD-alkalmazásproxy összekötők ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fe8d5c40249431be60dc8844adf7efa1b8e87c5f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD-alkalmazásproxy összekötők ismertetése

Összekötők a legtöbb esetben mi az Azure AD-alkalmazásproxy lehetővé teszik. Egyszerű, telepíteni és fenntartani egyszerű és hatékony super. A cikkből megtudhatja, milyen összekötők, hogyan működnek, és hogyan optimalizálható a központi telepítés egyes javaslatokat. 

## <a name="what-is-an-application-proxy-connector"></a>Mi az az alkalmazásproxy-összekötő?

Összekötők olyan egyszerűsített helyszíni elhelyezkedik, és lehetővé teszi a kimenő kapcsolat az alkalmazásproxy-szolgáltatás. Összekötők, amely hozzáfér a háttéralkalmazás Windows Server rendszerre telepíthető. Összekötők minden csoporttal adatforgalmának adott alkalmazásokhoz összekötő csoportokba rendezhetők. Összekötők terheléselosztásához automatikusan, és segít a hálózati struktúrát optimalizálása érdekében. 

## <a name="requirements-and-deployment"></a>Követelmények és üzembe helyezés

Alkalmazásproxy sikeres telepítéséhez legalább egy összekötő van szüksége, de azt javasoljuk, hogy két vagy több, a nagyobb rugalmasságot. Telepítse az összekötőt a Windows Server 2012 R2 vagy 2016-gépnek. Az összekötő képesnek kell lennie az alkalmazásproxy-szolgáltatás, valamint a helyszíni alkalmazások közzétett folytatott kommunikációhoz. 

Az összekötő kiszolgálója hálózati követelményeivel kapcsolatos további információkért lásd: [az alkalmazásproxy első lépései, és telepítse az összekötőt](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Karbantartás
Az összekötők és a szolgáltatás a magas rendelkezésre állású feladatokat kezeli. Akkor is lehet hozzáadásakor vagy eltávolításakor dinamikusan. Minden alkalommal, amikor egy új kérelem érkezik továbbításuk a jelenleg rendelkezésre álló összekötők egyikéhez. Ha egy összekötő átmenetileg nem érhető el, a forgalom nem válaszol.

Az összekötők állapot nélküli, és nincs konfigurációs adatokat a számítógépen. Adatok kizárólag tárolják a beállítások csatlakozni a szolgáltatáshoz és a hitelesítési tanúsítványt. A szolgáltatáshoz való csatlakozáskor szükséges konfigurációs adatok lekérésére, és frissíti minden néhány percig.

Összekötők is annak megállapításához, hogy a kiszolgáló kérdezze le az összekötő újabb verziója. Ha talál olyat, frissítse az összekötők magukat.

Az összekötők a gépről, akkor futnak, az Eseménynapló és a teljesítményszámlálók segítségével figyelheti. Vagy azok állapotát az Azure-portálon alkalmazásproxy lapján tekintheti meg:

 ![AzureAD Application Proxy összekötők](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Ne kelljen manuálisan törölje az összekötőt, nem használt. Amikor egy összekötő fut, addig marad aktív, csatlakozik a szolgáltatáshoz. Nem használt összekötők címkével rendelkeznek, _inaktív_ és inaktivitás 10 nap után törlődnek. Ha az eltávolítani kívánt összekötőt, azonban eltávolítása az összekötő szolgáltatást és a frissítési szolgáltatást is a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD automatikus frissítések biztosít minden olyan összekötőt, amely központilag. Mindaddig, amíg a Alkalmazásproxyösszekötő szolgáltatás fut, az összekötők automatikusan frissíteni. Ha az összekötő frissítési szolgáltatást nem látja a kiszolgálón, akkor [telepítse újra az összekötő](active-directory-application-proxy-enable.md) le a frissítéseket. 

Várjon, amíg az összekötő tudomást automatikus frissítése nem szeretnénk, ha a kézi frissítés végezheti el. Lépjen a [összekötő letöltési oldala](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) a kiszolgálóra, amelyen az összekötő található, és válassza a **letöltése**. Ez a folyamat a helyi összekötő frissítés másolattól. 

A bérlők számára a több összekötők az automatikus frissítések célként egy összekötőt minden csoport a környezetében az állásidő érdekében egyszerre. 

Állásidő problémákat tapasztalhat, amikor frissíti az összekötőt, ha:  
- Csak akkor kell egy összekötőt. Üzemszünet elkerüléséhez, és magas rendelkezésre állás javítása, javasoljuk, hogy telepítse a második összekötő és [hozzon létre egy összekötő csoportot](active-directory-application-proxy-connectors-azure-portal.md).  
- Összekötő lett közepén tranzakció, ha a frissítés megkezdése. Bár a kezdeti tranzakció nem vesztek el, a böngésző automatikus kell újra a műveletet, vagy a lap frissítésével. Amikor a kérést a rendszer újraküldi, a továbbítódik a biztonságimásolat-összekötőhöz.

## <a name="creating-connector-groups"></a>Összekötő csoportok létrehozása

Összekötő csoportok engedélyezése rendelheti hozzá az adott összekötők bizonyos alkalmazások kiszolgálására. Összekötőket csoportosíthatja, és rendeljen az egyes alkalmazásokat, egy csoportot. 

Összekötő csoportok megkönnyítik a nagy méretű központi telepítések felügyeletéhez szükséges. Akkor is tovább fejlesztheti várakozási ideje a bérlők számára, amelyek rendelkeznek a különböző régiókban található, mert csak a helyi alkalmazások kiszolgálására helyalapú összekötő csoportokat hozhat létre. 

Összekötő csoportokkal kapcsolatos további tudnivalókért lásd: [külön hálózatok és helyek összekötő csoportok használata alkalmazások közzétételére](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Kapacitástervezés 

Összekötők lesz automatikusan terheléselosztásához összekötő csoporton belül, amíg fontos is győződjön meg arról, hogy elegendő kapacitással a várt forgalom mennyisége kezelésére összekötők közötti tervezett. Általában a több felhasználó van, a nagyobb gép szüksége lesz. Az alábbiakban egy táblázatot, amely megadja a kötet vázlat különböző gépek kezelheti. Ne feledje, az összes alapul a várt tranzakciók másodpercenkénti második (TP-k) helyett felhasználó óta használati minták eltérőek, ezért nem használható, terhelés előrejelzése céljából.  Ne feledje, hogy néhány különbség a visszajelzések és a háttérkiszolgáló alkalmazás válaszideje mérete alapján lesz - válasz nagyobb méretek és a lassabb válaszidejét eredményezi egy alacsonyabb maximális TP-k.

|Processzormagok|RAM|Késés (MS) várt-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Ezen a számítógépen rendelkezett 800 a kapcsolathoz megadott korlátot. Az összes többi gép használtuk az alapértelmezett 200 kapcsolathoz megadott korlátot.
 
>[!NOTE]
>Nincs mekkora a legnagyobb TP-k 4, 8 és 16 mag gépek közötti különbség. A fő különbség a között, amelyek a várt késés van.  

## <a name="security-and-networking"></a>Biztonság és a hálózatkezelés

Összekötők bárhol a hálózaton, amellyel kérelmeket küldeni az alkalmazásproxy-szolgáltatás telepíthető. Fontos, hogy az összekötő is futtató számítógép rendelkezik-e a alkalmazásokhoz való hozzáférés. Összekötők telepíthető a vállalati hálózaton belül, vagy a felhőben futó virtuális gépen. Összekötők futtathatja a demilitarizált zónában (DMZ), de nincs szükség, mert az összes akkor kimenő forgalomról beszélünk, a hálózat biztonságos marad.

Összekötők csak a kimenő kérések küldése. A kimenő adatforgalom az alkalmazásproxy-szolgáltatás és a közzétett alkalmazásokhoz. Bejövő portok megnyitásához, mert a forgalom mindkét irányba után a munkamenet nem rendelkezik. Az összekötőket közötti terheléselosztás telepítéséhez és a tűzfalon keresztüli bejövő hozzáférésének beállítása nincs. 

Kimenő tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: [együttműködnek a meglévő helyszíni proxykiszolgálókat](application-proxy-working-with-proxy-servers.md).

Használja a [az Azure AD Application Proxy Connector portok teszt eszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) ellenőrzése, hogy az összekötő el lehet-e érni az alkalmazásproxy-szolgáltatás. Minimális győződjön meg arról, hogy a központi US régió és az Önhöz legközelebbi régiót összes zöld jelölők. Túl további zöld jelölők azt jelenti, hogy nagyobb rugalmasság. 

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy-skálájának átlátszó, de méretezési tényező az összekötők. Elegendő összekötők csúcs forgalmat fog kezelni kell. Azonban nem kell konfigurálni a terheléselosztást, mivel az összekötő csoportban lévő összes összekötőt automatikus terheléselosztása érdekében.

Összekötők a legtöbb esetben az állapot nélküli, mivel azok nem érinti a felhasználók vagy a munkamenetek számát. Ehelyett válaszoljanak a kérések számát és a terhelés méretének. A szokásos webes forgalom egy átlagos gép kezelni tud a néhány ezer kérések száma másodpercenként. A megadott kapacitás attól függ, hogy a pontos gép jellemzőit. 

Az összekötők teljesítménye CPU és a hálózat van kötve. Processzorteljesítmény szükséges SSL-titkosítás és visszafejtés, miközben a hálózat fontos, hogy az alkalmazások és az online szolgáltatás gyors kapcsolat beolvasása az Azure-ban.

Ezzel szemben memória beállítás értéke kisebb a csatlakozók kapcsolatos problémát. Az online szolgáltatás gondoskodik a feldolgozás jelentős részét, és az összes nem hitelesített forgalom. Minden, a felhőben végezheti el a felhőben történik. 

A terheléselosztás egy adott összekötő csoport összekötők közötti történik. A ciklikus multiplexelés meghatározni, mely a csoport összekötő szolgál egy adott kérés változata végezzük. Kiválasztása után a az összekötőt, azt, hogy felhasználói és a munkamenet időtartama alatt az alkalmazás között egy munkamenet affinitás karbantartása. Ha bármilyen okból, hogy az összekötő vagy a gép elérhetetlenné válik, a forgalom elindul a csoport egy másik összekötő címen. Ez rugalmasságot miért javasoljuk, hogy több összekötő is.

Teljesítményét befolyásoló másik tényező az összekötők, beleértve a közötti hálózati minőségének: 

* **Az online szolgáltatás**: Azure befolyásoló az összekötők teljesítménye lassú vagy nagy késleltetésű kapcsolat az alkalmazásproxynak szolgáltatás. A legjobb teljesítmény érdekében csatlakozzon a szervezet Azure és az Express Route. Ellenkező esetben van a hálózati csoport, győződjön meg arról, hogy az Azure-bA kapcsolatok lehető leghatékonyabb módon kezeli. 
* **A háttérrendszer alkalmazások**: bizonyos esetekben nincsenek további proxyk közötti az összekötő és a háttér-alkalmazások, amelyek lassú vagy megakadályozza a kapcsolódást. Ebben a helyzetben a hibaelhárítás támogatásához, az összekötő kiszolgálója megnyithat egy böngészőt, és próbáljon meg hozzáférni az alkalmazáshoz. Ha az összekötők futtatja az Azure-ban, de az alkalmazások a helyszínen, a felhasználói élmény előfordulhat, a felhasználók várható.
* **A tartományvezérlők**: az összekötők SSO-t a Kerberos által korlátozott delegálás hajthatja végre, ha azok a tartományvezérlőkhöz háttérkiszolgálóra a kérelem elküldése előtt. Az összekötők a Kerberos-jegyek gyorsítótár rendelkeznek, de foglalt környezetben a figyelt tartományvezérlők befolyásolhatja a teljesítményt. A probléma napjainkban egyre általánosabbá összekötők, futtassa az Azure-ban, de olyan tartományvezérlőn, amely a helyszínen kommunikál. 

A hálózati optimalizálás kapcsolatos további információkért lásd: [Azure Active Directory Application Proxy használata esetén a hálózati topológia szempontjai](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Tartománycsatlakoztatás

Olyan számítógépen, amelyen a rendszer nem tartományhoz csatlakozó összekötők is futtathatók. Azonban ha szeretne egyszeri bejelentkezést (SSO) integrált Windows-hitelesítéssel (IWA) használó alkalmazásokhoz, akkor egy tartományhoz gép. Ebben az esetben az összekötő gépek csatlakoznia kell a tartomány által végrehajtható műveleteket [Kerberos](https://web.mit.edu/kerberos) által korlátozott delegálást a felhasználók számára a közzétett alkalmazások nevében.

Összekötők tartományban vagy erdőben, amely részleges bizalmi kapcsolat, illetve írásvédett tartományvezérlők is csatlakoztathatók.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötő központi telepítések a megerősített környezetben

-Összekötő telepítési egyszerű, és semmilyen speciális konfigurációra van szükség. Van azonban néhány egyedi feltételeket, amelyeket érdemes figyelembe venni:

* A szervezeteknek, amelyek korlátozzák a kimenő forgalom kell [nyissa meg a szükséges portok](active-directory-application-proxy-enable.md#open-your-ports).
* A FIPS előírásainak megfelelő gépek engedélyezi az összekötő-folyamatok hozza létre és tárolja a tanúsítványt a konfiguráció módosításához lehet szükség.
* Győződjön meg arról, hogy mindkét összekötő-szolgáltatások engedélyezve vannak-e minden szükséges portokon és IP-cím elérésére kell szervezeteknek, amelyek alapján a folyamatok, amelyek a hálózati kérelmeket bocsásson ki környezetüket zárolását.
* Bizonyos esetekben a kimenő előre proxyk a kétirányú tanúsítványhitelesítés törés és okozhatja a kommunikáció sikertelen lesz.

## <a name="connector-authentication"></a>Összekötő-hitelesítés

Biztonságos kiszolgálása összekötők kell hitelesítenie a szolgáltatás felé, és a szolgáltatás nem tud felé az összekötő hitelesítéséhez. Ez a hitelesítés történik, ügyfél és kiszolgáló-tanúsítványok használatával, ha az összekötők kezdeményezzen kapcsolatot. Ezzel a módszerrel a rendszergazdai felhasználónevet és jelszót nem tárolja a csatlakozó számítógépen.

Az alkalmazásproxy használt tanúsítványok használhatók. A kezdeti regisztráció során létrehozása, és automatikusan megújítani az összekötőket az minden néhány hónappal. 

Ha az összekötő nem csatlakozik-e a szolgáltatás több hónapig, lehet, hogy a tanúsítványok elavult. Ebben az esetben távolítsa el, majd telepítse újra az összekötő eseményindító-regisztrációt. A következő PowerShell-parancsokat is futtathatja:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>A technikai részletek alapján

Összekötők alapulnak Windows Server a webalkalmazás-Proxy, így az azonos felügyeleti eszközeit, beleértve az olyan Windows eseménynaplóiban keresse meg a legtöbb

 ![Az Eseménynapló eseménynaplók kezelése](./media/application-proxy-understand-connectors/event-view-window.png)

és Windows-teljesítményszámlálókat. 

 ![Az összekötő a Teljesítményfigyelő számlálók hozzáadása](./media/application-proxy-understand-connectors/performance-monitor.png)

Az összekötők rendelkezik rendszergazdai és a munkamenet naplókat. A felügyeleti Naplók kulcs kapcsolódó események és a hibák tartalmazzák. A munkamenet-naplók tartalmazzák a tranzakciók és feldolgozási adataikat. 

A naplók megtekintéséhez nyissa meg az eseménynaplót, nyissa meg a **nézet** menüt, és lehetővé teszik **megjelenítése elemzési és hibakeresési naplókat**. Ezt követően engedélyezze megkezdését események gyűjtése. Ezek a naplók nem jelennek meg a webalkalmazás-Proxy a Windows Server 2012 R2, az összekötők alapuló újabb verziója.

A szolgáltatás a szolgáltatások ablakban ellenőrizheti. Az összekötő tartalmazza a két központi Windows-szolgáltatások: a tényleges összekötő, és megtekinti a. Mindkettő folyamatosan kell futtatni.

 ![AzureAD Services Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>További lépések


* [Külön hálózatok és helyek összekötő csoportokat használnak az alkalmazások közzététele](active-directory-application-proxy-connectors-azure-portal.md)
* [A meglévő helyszíni proxykiszolgálókkal működik](application-proxy-working-with-proxy-servers.md)
* [Proxy és összekötő hibák elhárítása](active-directory-application-proxy-troubleshoot.md)
* [Csendes telepítése az Azure AD alkalmazásproxy-összekötő](active-directory-application-proxy-silent-installation.md)

