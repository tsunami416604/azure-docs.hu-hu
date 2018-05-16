---
title: Biztonsági szempontok az Azure AD alkalmazásproxy |} Microsoft Docs
description: Az Azure AD-alkalmazásproxy használatának biztonsági szempontjait ismerteti
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: aaec5febaa8d697ceb9fd32d3bcdd6c37c399e84
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Biztonsági szempontok az alkalmazások távolról a Azure AD alkalmazásproxy elérése

Ez a cikk ismerteti, hogyan működik biztonsága a felhasználók és az alkalmazások Azure Active Directory Alkalmazásproxyjával használatakor összetevőket.

Az alábbi ábrán látható az Azure AD lehetővé teszi, hogy a helyszíni alkalmazások biztonságos távoli hozzáférést.

 ![Azure AD alkalmazásproxy segítségével biztonságos távoli hozzáférés ábrája](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Biztonsági szempontból előnyökkel járhat

Az Azure AD-alkalmazásproxy a következő biztonsági előnyökkel jár:

### <a name="authenticated-access"></a>Hitelesített hozzáférés 

Ha az Azure Active Directoryt az előhitelesítést, majd csak hitelesített kapcsolatokat hozzáférni a hálózathoz.

Az Azure AD-alkalmazásproxy az Azure AD biztonsági jogkivonat-szolgáltatás (STS) összes hitelesítési támaszkodik.  Előhitelesítés használatakor jellegéből adódóan, blokkolja jelentős számú névtelen támadások, mivel csak hitelesített identitást a háttér-alkalmazást érheti el.

Ha csatlakoztatott legyen a előhitelesítési módszer, ez a szolgáltatás nem jelenik meg. 

### <a name="conditional-access"></a>Feltételes hozzáférés

Gazdagabb házirend vezérlőelemek alkalmazni, mielőtt létesít kapcsolatokat a hálózathoz.

A [feltételes hozzáférés](../active-directory-conditional-access-azure-portal-get-started.md), milyen forgalom engedélyezve van a háttér-alkalmazások elérésére vonatkozó korlátozásokat definiálhat. Házirendeket, amelyek korlátozzák a bejelentkezéseket helye, hitelesítési és kockázatú profil alapján is létrehozhat.

Feltételes hozzáférés segítségével is újabb biztonsági réteget ad hozzá a felhasználói hitelesítések multi-factor Authentication hitelesítési házirendek konfigurálása. 

### <a name="traffic-termination"></a>Forgalom megszüntetése

Az összes forgalom megszakad a felhőben.

Mivel az Azure AD-alkalmazásproxy egy fordított proxy, a háttér-alkalmazások összes forgalmat, a szolgáltatás megszakad. A munkamenet csak a háttér-kiszolgálófiók, ami azt jelenti, hogy a háttér-kiszolgálók nem érhetők el a közvetlen HTTP-forgalom a újrakezdte is beolvasása. Ez a konfiguráció azt jelenti, hogy jobban védve célzott támadásoktól.

### <a name="all-access-is-outbound"></a>Minden hozzáférés meg kimenő 

Nem kell megnyitni a bejövő kapcsolatok a vállalati hálózathoz.

Application Proxy összekötők csak az Azure AD alkalmazásproxy szolgáltatásnak, ami azt jelenti, hogy nincs szükség a bejövő kapcsolatok tűzfal portok megnyitására szolgáló kimenő kapcsolatokat használja. Hagyományos proxyk szükséges a szegélyhálózaton (más néven *DMZ*, *demilitarizált zóna*, vagy *végezheti*) és a nem hitelesített kapcsolatokat hozzáférése engedélyezett hálózat peremén. Ebben a forgatókönyvben beruházások értékét, a webes alkalmazás tűzfal termékek forgalom elemzése és a környezet védelméhez szükséges. A Proxy nincs szükség a szegélyhálózaton, mert az összes kapcsolat kimenő, és egy biztonságos csatornán kerül sor.

Összekötők kapcsolatos további információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>A felhőméretű elemzés és a gépi tanulás 

A legmodernebb védelmet beolvasása.

Mivel az Azure Active Directory részét, kihasználhatják a alkalmazásproxy [Azure AD Identity Protection](../active-directory-identityprotection.md), a Microsoft Security Response Center és milyen adataival. Együttesen azt megelőző a sérült biztonságú fiókjait azonosítják, nyújtanak védelmet a magas kockázatú bejelentkezéseket. Azt is figyelembe annak meghatározására, hogy mely bejelentkezési leggyorsabb rendezésére magas kockázatú számos tényező. Ezek a tényezők közé tartozik a megjelölés fertőzött eszközök, névtelenítését hálózatokhoz és a rendellenes vagy valószínű helye.

Ezek a jelentések és események számos már elérhető a biztonsági információk és az esemény (SIEM) rendszerek integrációját egy API-n keresztül.

### <a name="remote-access-as-a-service"></a>A távelérés szolgáltatás

Nincs karbantartásáért, valamint a helyszíni kiszolgálók javítását foglalkoznia.

Továbbra is a nem javított szoftver támadások számos fiókok. Az Azure AD-alkalmazásproxy egy Internet-skálázási szolgáltatás, amely a Microsoft tulajdonában van,, így mindig a legújabb biztonsági javítások és frissítéseket.

Az Azure AD-alkalmazásproxy által közzétett alkalmazás biztonságának javítása érdekében, webes webbejáró robots indexelő és az alkalmazások Archiválás azt letiltása. Minden alkalommal, amikor egy webes webbejáró robot próbálja beolvasni a robot beállításait a közzétett alkalmazások Application Proxy válaszol a robots.txt fájl tartalmazza a `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>DDOS megelőzése

Közzétett proxyn keresztül történő alkalmazás elosztott letiltja a szolgáltatást (DDOS-) támadások elleni védelmét.

Az alkalmazásproxy elérni az alkalmazások és a hálózati forgalom mennyiségét figyeli. Az alkalmazások távoli hozzáférést igénylő eszközök száma napra, ha a Microsoft azelőtt gyorsítja fel a hálózat elérését. 

Microsoft figyeli az egyes alkalmazások és az előfizetés egy teljes forgalmat. Ha egy alkalmazás nagyobbnak, mint a normál kérés érkezik, majd kérelmeket, hogy az alkalmazáshoz való hozzáférés megtagadása rövid időn belül. Ha a teljes előfizetésből nagyobbnak, mint a normál kérés kap, az alkalmazások bármelyikét hozzáférés kérése sem kap. A megelőző intézkedés, hogy a helyi felhasználók is tartsa alkalmazásokhoz fér hozzá a tartja a távelérés kérelem túlterhelését az alkalmazáskiszolgálók. 

## <a name="under-the-hood"></a>A technikai részletek alapján

Az Azure AD-alkalmazásproxy két részből áll:

* A felhő alapú szolgáltatás: A szolgáltatás fut az Azure-ban, és ahol a külső ügyfélfelhasználó kapcsolódnának.
* [A helyszíni összekötő](application-proxy-connectors.md): egy helyszíni összetevő a belső alkalmazásokhoz az Azure AD alkalmazásproxy szolgáltatás és a leírók kapcsolatok érkező kéréseket figyeli az összekötőt. 

A folyamat az összekötő és az alkalmazásproxy-szolgáltatás között létesít során:

* Az összekötő először állítsa be.
* Az összekötő lekéri az alkalmazásproxy-szolgáltatás konfigurációs adatait.
* Egy felhasználó a közzétett alkalmazás fér hozzá.

>[!NOTE]
>Az összes kommunikáció SSL-en keresztül történik, és mindig származnak, az az alkalmazásproxy-összekötő. A szolgáltatás csak akkor beszélünk.

Az összekötő egy ügyfél tanúsítványát használja az alkalmazásproxy szinte minden hívások felé történő hitelesítésre. Az egyetlen kivétel ez alól lépése a kezdeti beállítás, ahol az ügyféltanúsítvány jön létre.

### <a name="installing-the-connector"></a>Az összekötő telepítése

Ha az összekötő először van beállítva, a következő folyamat eseményeinek kerül sor:

1. Az összekötő-regisztrációt, hogy a szolgáltatás az összekötő telepítésének részeként történik. Adja meg az Azure AD rendszergazdai hitelesítő adatokat a rendszer kéri a felhasználóktól. A jogkivonat a alapján a hitelesítés az Azure AD alkalmazásproxy majd áll rendelkezésre.
2. Az alkalmazásproxy kiértékeli a jogkivonatot. Ellenőrzi, hogy a felhasználó egy vállalati rendszergazda a bérlőben. Ha a felhasználó nem rendszergazda, a folyamat megszakadt.
3. Az összekötő egy ügyfél tanúsítványkérelmet állít elő, és továbbítja azokat, együtt a jogkivonatot, az alkalmazásproxy-szolgáltatás. A szolgáltatás pedig ellenőrzi a jogkivonatot, és aláírja az ügyfél tanúsítványkérelmet.
4. Az összekötő használ az ügyféltanúsítvány az alkalmazásproxy jövőbeli kommunikál.
5. Az összekötő hajt végre egy kezdeti lekéréses konfigurációs adatait a szolgáltatásból, az ügyfél-tanúsítványt használ, és azt készen áll a kérelmek igénybe vehet.

### <a name="updating-the-configuration-settings"></a>Konfigurációs beállításainak frissítésekor

Amikor az alkalmazásproxy frissíti a konfigurációs beállításokat, a következő folyamat eseményeinek kerül sor:

1. Az összekötő kapcsolódik az alkalmazásproxy belül a konfigurációs végpont az ügyféltanúsítvány használatával.
2. Az ügyféltanúsítvány érvényesítését követően az alkalmazásproxy-szolgáltatás konfigurációs adatok és az összekötő (például az összekötő csoportot, amely az összekötő részét kell képezniük) adja vissza.
3. Ha az aktuális tanúsítvány 180 napnál régebbi, az összekötő egy új tanúsítványkérelmet, és hatékonyan 180 naponta frissül az ügyfél-tanúsítványt hoz létre.

### <a name="accessing-published-applications"></a>A közzétett alkalmazások eléréséhez

Amikor a felhasználók hozzáférnek a közzétett alkalmazás, a következő események végrehajtani az alkalmazásproxy-szolgáltatás és az alkalmazásproxy-összekötő között:

1. [A szolgáltatás hitelesíti a felhasználót az alkalmazáshoz](#the-service-checks-the-configuration-settings-for-the-app)
2. [A szolgáltatás egy kérelem helyezi az összekötő várólista](#The-service-places-a-request-in-the-connector-queue)
3. [Összekötő feldolgozza a kérést az üzenetsorból](#the-connector-receives-the-request-from-the-queue)
4. [Az összekötő választ vár](#the-connector-waits-for-a-response)
5. [A szolgáltatás az adatfolyamokat, a felhasználói adatok](#the-service-streams-data-to-the-user)

Mi történik, a lépések kapcsolatos további tudnivalókért hagyja az olvasást.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. A szolgáltatás hitelesíti a felhasználót az alkalmazáshoz

Ha konfigurálta az alkalmazásnak, hogy közvetlenül csatlakoztatott használják az előhitelesítési módszer, az ebben a szakaszban kimarad.

Ha konfigurálta az alkalmazásnak, hogy az Azure ad-val preauthenticate, az Azure AD STS hitelesítésére a rendszer átirányítja a felhasználókat, és kerül sor, a következő lépéseket:

1. Alkalmazásproxy ellenőrzi a feltételes hozzáférési házirend követelményeinek, az adott alkalmazáshoz. Ez a lépés biztosítja, hogy a felhasználó kapcsolódik az alkalmazás. Ha szükséges a kétlépéses ellenőrzést, a hitelesítési folyamat bekéri a felhasználótól egy második hitelesítési módszert.

2. Miután az ellenőrzés sikeres rendelkeznek, az Azure AD STS állít ki az alkalmazás aláírt jogkivonat, és visszatér az alkalmazásproxy átirányítja a felhasználót.

3. Alkalmazásproxy ellenőrzi, hogy a jogkivonat az alkalmazás javítása lett-e ki. Azt ellenőrzi más is, például győződjön meg arról, hogy a jogkivonat aláírása Azure ad, és, hogy a rendszer továbbra is a érvényes ablakon belül.

4. Egy titkosított hitelesítő cookie annak jelzésére, hogy az alkalmazás a hitelesítési történt az alkalmazás Proxy beállítása. A cookie-k tartalmaz egy lejárati timestamp, amely az Azure ad- és egyéb adatok, például a felhasználó nevét, amely azon alapul, a hitelesítési alapul. A cookie titkosítása és a titkos kulcs, amit csak az alkalmazásproxy-szolgáltatás.

5. Alkalmazásproxy átirányítja a felhasználót az eredetileg kért URI.

Az előhitelesítést lépéseket bármely részét nem sikerül, ha a felhasználó kérelmet a rendszer megtagadja, és a felhasználó számára megjelenik egy hibaüzenet, amely a probléma forrása.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. A szolgáltatás egy kérelem helyezi az összekötő várólista

Összekötők egy kimenő kapcsolat nyitva hagyja az alkalmazásproxy-szolgáltatás. Amikor egy kérelem érkezik, a szolgáltatás a kérelem a nyitott kapcsolatok egyik átvételéhez összekötő várólisták.

A kérelem tartalmazza az alkalmazás, például az adatok a titkosított cookie-t, a felhasználó a kérelem számára, és a kérelem azonosítója. a kérelem fejlécében szereplő elemeket A titkosított cookie-ból adatokat küldi el a kért, de nem áll a hitelesítési cookie-t saját magát.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Az összekötő feldolgozza a kérést az üzenetsorból. 

A kérés alapján alkalmazásproxy hajtja végre a következő műveletek egyikét:

* Ha a kérés egy egyszerű feladat (például nincs belül a szervezet, mert az egy RESTful adat *beolvasása* kérelem), az összekötő a célerőforrás belső kapcsolatot hoz létre, és választ vár.

* Ha a kérelem törzsében társított adatokat tartalmaz-e (például egy RESTful *POST* művelet), az összekötő egy kimenő kapcsolatot létesít az ügyféltanúsítványt az alkalmazásproxy-példány használatával. Ezt a kapcsolatot a adatokat kér, és kapcsolatot létesíteni a következő belső erőforrást teszi. Azután, hogy megkapja a kérést az összekötőről érkező, az alkalmazásproxy elfogadása a felhasználó tartalmat kezdődik, és továbbítja az adatokat és az összekötő. Az összekötő, továbbítja az adatokat a következő belső erőforrást.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Az összekötő válaszra.

A kérelem és a háttérkiszolgálón minden tartalom továbbításának befejezése után, az összekötő választ vár.

Miután választ kap, az összekötő kapcsolódott egy kimenő az alkalmazásproxy-szolgáltatás, térjen vissza a fejléc részleteit és a visszatérési adatfolyam.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. A szolgáltatás az adatfolyamokat, a felhasználói adatokat. 

Itt az alkalmazás egyes feldolgozási fordulhat elő. Ha az alkalmazás a fejlécek lefordítani alkalmazásproxy vagy URL-címek konfigurálta, a feldolgozása történik, ennél a lépésnél szükség szerint.


## <a name="next-steps"></a>További lépések

[Hálózati topológia szempontjai az Azure AD-alkalmazásproxy használatával](application-proxy-network-topology.md)

[Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-connectors.md)
