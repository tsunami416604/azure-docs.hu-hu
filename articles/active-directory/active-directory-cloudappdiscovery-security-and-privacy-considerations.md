---
title: "A cloud App Discovery biztonsági és adatvédelmi megfontolások |} Microsoft Docs"
description: "Ez a témakör ismerteti a biztonsági és adatvédelmi szempontjairól a Cloud App Discovery modulhoz."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3ebcb5b3b4a84f7a5c25caa3f6b245f97bc8049f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Cloud App Discovery biztonsági és adatvédelmi megfontolások
Ez a témakör azt ismerteti, hogyan adatok gyűjtése, feldolgozása, és Azure Active Directory a Cloud App Discovery belül védett. A Microsoft elkötelezett az Ön adatainak védelmében, és az adatok védelme. A Microsoft biztonságos szoftver fejlesztési életciklus eljárásokat a szolgáltatás működtetéséhez csatlakozik. Biztonságossá tétele és az adatok védelmének a Microsoft a legnagyobb prioritással.

> [!TIP] 
> Tekintse meg az új ügynök nélkül a Cloud App Discovery az Azure Active Directoryban (Azure AD), amely szerint a továbbfejlesztett [integráció a Microsoft Cloud App Security](https://portal.cloudappsecurity.com). 

## <a name="overview"></a>Áttekintés
A cloud App Discovery szolgáltatás az Azure AD és az Microsoft Azure-ban.  
A Cloud App Discovery endpoint agent informatikai kezelt készülékekről alkalmazás felderítési adatok összegyűjtésére szolgál. Az összegyűjtött adatokat küld biztonságosan egy titkosított csatornán keresztül az Azure AD Cloud App Discovery szolgáltatásba. A Cloud App Discovery szervezet adatai majd láthatóvá válnak az Azure portálon. 

![A Cloud App Discovery működése](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

Az alábbi szakaszok a biztonságos információáramlás kövesse a szervezet a Cloud App Discovery szolgáltatásra, és végső soron a Cloud App Discovery portálra.

## <a name="collecting-data-from-your-organization"></a>A szervezet adatainak begyűjtése
Ahhoz, hogy az Azure Active Directory Cloud App discovery szolgáltatás használatával nyerhet a szervezet alkalmazottai által használt alkalmazásokba, kell először gépekre való telepítéséhez a Azure AD Cloud App Discovery endpoint agent a szervezetében.

Az Azure Active Directory-bérlő (és a delegált) a rendszergazdák az ügynök telepítési csomag letölthető az Azure-portálon. Az ügynök vagy manuálisan telepíthető vagy SCCM vagy csoportházirend segítségével a szervezet több számítógépen telepítve.

A központi telepítési beállítások további utasításokért lásd: [Cloud App Discovery csoport házirend telepítési útmutató](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### <a name="data-collected-by-the-agent"></a>Az ügynök által gyűjtött adatok
Az alábbiakban ismertetett információk az ügynök által gyűjtött egy kapcsolat egy webalkalmazás létrehozásakor. Az adatgyűjtés csak a rendszergazda úgy konfigurálta, a felderítéshez azon alkalmazások esetében. Szerkesztheti a felhőalkalmazások, amelyeknek az ügynök figyeli a Cloud App Discovery révén a Microsoft Azure AD-ben listája [Azure-portálon](https://portal.azure.com/)a **beállítások**->**adatok gyűjtése**  -> **Alkalmazáskatalógus lista**. 

**Információk kategória**: felhasználói adatok  
**Leírás**: a folyamat a kérést leadó a cél-webalkalmazást (például TARTOMÁNY\felhasználónév) valamint a Windows biztonsági azonosítók (SID) a felhasználó a Windows felhasználói nevét.

**Információk kategória**: feldolgozni adatait  
**Leírás**: a folyamat a kérést a cél (például iexplore.exe) webalkalmazás neve

**Információk kategória**: számítógép-információk  
**Leírás**: A számítógép NetBIOS-név az ügynök telepítve van.

**Információk kategória**: alkalmazás forgalmi információk  
**Leírás**: A következő kapcsolati adatokat:

* A forrás (helyi számítógép) és a cél IP-címek és a portszámok
* A nyilvános IP-cím a szervezet, amelyen keresztül a kérelem kerül ki.
* A kérelem ideje
* Küldött és fogadott forgalom mennyiségét
* Az IP-verziót (4 vagy 6)
* A TLS-kapcsolatok csak: A cél gazdagép neve a kiszolgálónév jelzése kiterjesztés vagy a kiszolgáló tanúsítványát.

A következő HTTP-információk:

* Metódus (GET, POST, stb.)
* Protokoll (HTTP/1.1, stb.)
* Felhasználói ügynök karakterlánca
* Gazdagépnév
* Cél URI (kivéve a lekérdezési karakterlánc)
* Tartalom típussal kapcsolatos információk
* Hivatkozó URL-cím adatait (kivéve a lekérdezési karakterlánc)

> [!NOTE]
> A fenti HTTP-információk gyűjtése az összes nem titkosított kapcsolat.
> A TLS-kapcsolatok esetén ezt az információt csak rögzített Ha a "Mélyreható vizsgálat" beállítás be van kapcsolva a portálon. Az érték "ON" alapértelmezés szerint.
> További részletekért lásd az alábbi, és [első lépések a Cloud App Discovery szolgáltatásra](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 

Az ügynök által gyűjtött, a hálózati tevékenységre vonatkozó adatokat, akkor is névtelen adatokat gyűjt kapcsolatos
* Hardver- és konfigurációs
* Hibajelentések
* Az adatok az ügynök használatáról.


### <a name="how-the-agent-works"></a>Az ügynök működése
Az ügynök telepítése a két összetevőből áll:

* A felhasználói módú összetevő
* Kernelmódú illesztőprogram-összetevő (a Windows szűrőplatform-illesztőprogramok)

Az ügynök első telepítésekor a számítógépen, majd használja a Cloud App Discovery szolgáltatás egy biztonságos kapcsolatot létrehozni tárol egy számítógép-specifikus megbízható tanúsítvány. Az ügynök rendszeres időközönként lekérdezi házirend-konfigurációt a Cloud App Discovery szolgáltatásba a biztonságos kapcsolaton keresztül. A házirend mely felhőalapú alkalmazások figyelésére, és hogy az automatikus frissítési engedélyezni kell, többek között információkat tartalmaz.

Webes forgalom küldése és fogadása a számítógépen az Internet Explorer és a Chrome, mivel a Cloud App Discovery-ügynök elemzi a forgalmat, és kinyeri a megfelelő metaadatok (lásd a **az ügynök által gyűjtött adatok** fenti szakaszban).  
Percenként, az ügynök feltölti az összegyűjtött metaadatokból a Cloud App Discovery szolgáltatás egy titkosított csatornán keresztül.

Az illesztőprogram-összetevő elfogja a titkosított forgalmat, és maga beilleszti a titkosított adatfolyam. További részletek a **elfogja a titkosított kapcsolatokat (mélyreható vizsgálat) adatait** az alábbi szakasz.

### <a name="respecting-user-privacy"></a>Felhasználói adatok tiszteletben
Célunk, így a rendszergazdák az eszközöket, amelyekkel az alkalmazás használatát és a felhasználói adatvédelem a szervezet megfelelő részletes optika közötti egyensúly beállítása. Ebből a célból nyújtunk a következő forgatógombját a portál beállítások lapján:

* **Adatgyűjtés**: rendszergazdák kiválaszthatják, mely alkalmazások vagy azok szeretné, hogy a felderítési adatokat Alkalmazáskategóriák megadásához.
* **Mélyreható vizsgálat**: rendszergazdák úgy is dönt, hogy adja meg, ha az ügynök gyűjti az SSL/TLS kapcsolatok HTTP-forgalom (más néven **"Mélyreható vizsgálat"**). Bővebben a következő szakaszban.
* **Hozzájárulási beállítások**: rendszergazdák használhatják a Cloud App Discovery portálon, hogy az adatgyűjtés az ügynök által a felhasználók értesítése e, és hogy a felhasználó-e a felhasználói adatok gyűjtése ügynök kezdete előtt hozzájárulás.

A Cloud App Discovery endpoint agent csak leírt adatokat gyűjt a **az ügynök által gyűjtött adatok** fenti szakaszban.

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Adatok elfogja a titkosított kapcsolatokat (mélyreható vizsgálat)
A korábban említett azt, a rendszergazdák konfigurálhatják a titkosított kapcsolatokat (a "mélyreható vizsgálat") adatok figyeléséről az ügynököt. A TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) szerepel a leggyakoribb protokollok az interneten használt ma. Kommunikáció a TLS titkosításával ügyfél létesíthet-e egy biztonságos és személyes kommunikációs csatorna webkiszolgáló; TLS az hitelesítő adatok továbbításához alapvető védelmet biztosít, és a bizalmas információk felfedésének megakadályozására.

A végpontok közötti biztonságos titkosított csatornán TLS által biztosított fontos biztonsági és adatvédelmi védelem lehetővé teszi, akkor a protokoll gyakran visszaélt, rosszindulatú vagy nefarious célokra. Adjon annyi így valójában, hogy a TLS gyakran nevezik az "univerzális tűzfal-megkerülési protokoll". A probléma gyökere, hogy a legtöbb tűzfalon sem TLS kommunikációs vizsgálata, mert az alkalmazásszintű adatok titkosítása SSL használatával. Ennek tudatában, a támadók gyakran használja ki a TLS rosszindulatú hasznos adat található benne, hogy még a legtöbb intelligens alkalmazásréteg-tűzfalak teljesen vak, a TLS és egyszerűen kell továbbítani a TLS kommunikációs állomások között a felhasználó telepíthet. A végfelhasználók gyakran kihasználja a TLS kényszeríti ki a vállalati tűzfalak és proxykiszolgálók, hozzáférés-vezérlést elkerülésére kapcsolódjanak nyilvános proxyk és a tűzfalon, ellenkező esetben előfordulhat, hogy a házirend által blokkolt a TLS protokollok bújtatásra használja azt.

Mélyreható vizsgálat lehetővé teszi, hogy a Cloud App Discovery-ügynök egy megbízható man-az-átjárójának nevében járhasson el. Kérelemkor egy ügyfél egy védett HTTPS erőforrás elérésére, az Endpoint-ügynök illesztőprogram elfogja a kapcsolatot, és megállapítja, hogy a célkiszolgáló új kapcsolatot lekéri az SSL-tanúsítványt az ügyfél nevében. Az ügynök majd ellenőrzi, hogy tud-e a tanúsítvány megbízható (igény szerinti ellenőrzése, hogy azt nem visszavonva, és más tanúsítvány ellenőrzések végrehajtása), és ha a fázis, majd a Endpoint-ügynök átmásolja az adatokat a kiszolgálói tanúsítvány, és létrehozza a saját kiszolgáló tanúsítványt--néven egy hozzáférés-tanúsítvány – ezt az információt. A hozzáférés tanúsítványa nem aláírt az azonnali főtanúsítvánnyal, amelyen telepítve van a Windows megbízható tanúsítványok tárába endpoint-ügynök. A önaláírt legfelső szintű tanúsítvány nem exportálható van megjelölve, és hozzáférés-vezérlési lista rendszergazdáknak kellett. Célja, hogy sosem hagyják el a gép, amelyen létrehozták. Ha a végfelhasználó ügyfélalkalmazás fogadja a hozzáférés-tanúsítványt, a azt bízó sikeresen tudja érvényesíteni a tanúsítványlánc egészen a legfelső szintű tanúsítvány is. Ez a folyamat alapvetően a végfelhasználó szempontjából az alább ismertetett néhány kikötésekkel átlátszó.

Mélyreható vizsgálat engedélyezése esetén a Cloud App Discovery Endpoint Agent visszafejthetik és vizsgálhatja meg TLS titkosított kommunikáció, így a szolgáltatás zaj csökkentésére, és a titkosított felhőalapú alkalmazások használatával kapcsolatos információkat adnak.

#### <a name="a-word-of-caution"></a>A figyelmeztetés szót
Mélyreható vizsgálat bekapcsolása, előtt erősen ajánlott a jogi és HR-részleg szándékai kommunikálnak, és szerezze be a hozzájárulásukat. Tanulmányozza a végfelhasználó személyes titkosított kommunikációt nyilvánvaló oka egy bizalmas tulajdonos lehet. Egy éles kiépítése mélyreható vizsgálat, előtt gondoskodjon arról, hogy a vállalati biztonsági, és használati házirendek frissültek annak jelzésére, hogy titkosított kommunikáció ellenőrizni kell. Felhasználói értesítés és a kivételt a helyek tekinteni bizalmas (pl. banki és orvosi helyek) is lehet szükség, ha konfigurál, a Cloud App Discovery figyelheti azokat. Fent említett, akkor a rendszergazdák a a Cloud App Discovery portál segítségével válassza ki, hogy értesítse a felhasználókat az adatgyűjtés az ügynök által-e, valamint hogy az ügynök a felhasználói adatgyűjtés megkezdése előtt a felhasználói hozzájárulás-e.

### <a name="known-issues-and-drawbacks"></a>Ismert problémák és a hátrányai
Van néhány olyan esetekben, ahol TLS hozzáférés is hatással lehet a végfelhasználó számára:

* Bővített érvényesítési (Bővített) tanúsítványok segítségével egy visual clue, hogy egy megbízható webhelyen megjelenő zöld képezhető le a webböngésző címsorába. TLS-ellenőrzési EV nem lehet ugyanaz a tanúsítvány kapcsolatos az ügyfélnek, így Bővített tanúsítványok használó webhelyek megszokott módon fognak működni, de a címsor nem jelenik meg a zöld.  
* Nyilvános kulcs rögzítési (más néven tanúsítvány rögzítését) védelmének elősegítésére felhasználók-átjárójának a támadó tanúsítványszolgáltatók készültek. Ha egy rögzített vonatkozóan a legfelső szintű tanúsítvány nem felel meg az ismert helyes hitelesítésszolgáltató egyik, a böngésző elutasítja hiba történt a kapcsolat. Mivel a TLS-hozzáférés, valójában egy man-az-átjárójának, ezek a kapcsolatok sikertelen lesz.
* Ha a felhasználók kattintanak, a böngésző cím sáv böngésző vizsgálhatja meg a helyadatok lakat ikonra, nem fogják látni a hitelesítésszolgáltatótól, a webhely tanúsítványát aláírásához használt végződése lánc, de ehelyett egy a Windows végződő megbízható tanúsítványtárolójába.

Ezek a problémák előfordulásának csökkentése érdekében nyomon követjük, felhőszolgáltatások és ügyfélalkalmazások bővített, az érvényesítés vagy a nyilvános kulcs rögzítését, és kérje meg a végpont ügynök ismert érintett kapcsolatok elfogja elkerülése érdekében. Ezekben az esetekben, még akkor is, azonban továbbra is kap jelentések ezekbe a felhőalkalmazásokba használatát és az átvitt adatok mennyiségét, de mivel azok nincsenek mély megvizsgálni, hogyan az alkalmazások használt adatait nem lesz elérhető.

## <a name="sending-data-to-cloud-app-discovery"></a>Adatok küldése a Cloud App Discovery szolgáltatásra
Miután a metaadatok az ügynök által gyűjtött, gyorsítótárazza a gépen legfeljebb egy percig, vagy amíg a gyorsítótárazott adatokat 5MB mérete eléri a. Majd tömörített, és a Cloud App Discovery szolgáltatás egy biztonságos kapcsolaton keresztül.

Ha az ügynök nem képes kommunikálni a bármilyen okból a Cloud App Discovery szolgáltatásba, az összegyűjtött metaadatokból tárolódik a helyi gyorsítótárban csak megfelelő jogosultsággal rendelkező felhasználók (például a Rendszergazdák csoport) a számítógép hozzáfér.  
Az ügynök automatikusan megpróbálja újraküldeni a gyorsítótárban levő metaadatok, amíg sikeresen érkezett a Cloud App Discovery szolgáltatás által.

## <a name="receiving-the-data-at-the-service-end"></a>A szolgáltatás végén az adatok fogadása
A Cloud App Discovery szolgáltatás a számítógép egyedi ügyfél-hitelesítési tanúsítványt használ a fentiekben említett, és egy titkosított csatornán keresztül továbbítja az adatokat az ügynökök hitelesíteni.  
A Cloud App Discovery szolgáltatásba elemzési folyamatok kiépítéséhez dolgozza fel metaadatok minden ügyfél esetében külön-külön logikailag particionálás az analytics-feldolgozási folyamat minden szakaszában.
Az elemzett metaadatok meghajtók a különböző jelentéseket a portálon.

A feldolgozatlan metaadatok és az elemzett metaadatok 180 napig tárolja. Ezenkívül az ügyfelek választhat egy Azure blob storage-fiók igénye az elemzett metaadatok rögzítése.
Ez akkor hasznos, kapcsolat nélküli elemzéshez metaadatok, valamint az adatok hosszabb megőrzési.

## <a name="accessing-the-data-using-the-azure-portal"></a>Fér hozzá az adatokhoz, az Azure portál használatával
Annak érdekében, hogy a metaadatok gyűjtött biztonsága alapértelmezés szerint csak a bérlő globális rendszergazdák hozzáférhetnek a Cloud App Discovery szolgáltatásra az Azure portálon.  
Azonban a rendszergazdák kiválaszthatják a hozzáférést biztosíthat más felhasználóknak vagy csoportoknak.

> [!NOTE]
> További részletekért lásd: [első lépések a Cloud App Discovery szolgáltatásra](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 


Minden olyan felhasználó, a portál, az adatok elérése az Azure AD Premium licenccel rendelkező licenccel kell rendelkezniük.

## <a name="additional-resources"></a>További erőforrások
* [Hogyan lehet használt, jóvá nem hagyott felhőalkalmazások felderítése a szervezeten belül](active-directory-cloudappdiscovery-whatis.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

