---
title: Az Azure AD alkalmazásproxy biztonsági szempontjai | Microsoft dokumentumok
description: Az Azure AD alkalmazásproxy használatával kapcsolatos biztonsági szempontok at ismerteti
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481228"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Biztonsági szempontok az alkalmazások azure AD alkalmazásproxyval történő távoli eléréséhez

Ez a cikk ismerteti azokat az összetevőket, amelyek a felhasználók és az alkalmazások biztonságának megőrzéséhez dolgoznak az Azure Active Directory alkalmazásproxy használatakor.

Az alábbi ábrán bemutatja, hogy az Azure AD hogyan teszi lehetővé a biztonságos távoli hozzáférést a helyszíni alkalmazásokhoz.

 ![Biztonságos távelérés diagramja az Azure AD alkalmazásproxyn keresztül](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Biztonsági előnyök

Az Azure AD alkalmazásproxy a következő biztonsági előnyöket kínálja:

### <a name="authenticated-access"></a>Hitelesített hozzáférés 

Ha úgy dönt, hogy az Azure Active Directory előhitelesítés, majd csak a hitelesített kapcsolatok férhetnek hozzá a hálózathoz.

Az Azure AD alkalmazásproxy az Azure AD biztonsági jogkivonat-szolgáltatásra (STS) támaszkodik az összes hitelesítéshez.  Az előhitelesítés természeténél fogva jelentős számú névtelen támadást blokkol, mert csak hitelesített identitások férhetnek hozzá a háttéralkalmazáshoz.

Ha az Áterzés t választja előhitelesítési módszerként, nem kapja meg ezt az előnyt. 

### <a name="conditional-access"></a>Feltételes hozzáférés

A hálózathoz való csatlakozás létrehozása előtt alkalmazzon gazdagabb házirend-vezérlőket.

A [feltételes hozzáférés](../conditional-access/overview.md)segítségével korlátozásokat határozhat meg arra vonatkozóan, hogy milyen forgalom férhet hozzá a háttéralkalmazásokhoz. Olyan házirendeket hozhat létre, amelyek korlátozzák a bejelentkezéseket a hely, a hitelesítés erőssége és a felhasználói kockázati profil alapján.

A feltételes hozzáférés segítségével többtényezős hitelesítési házirendeket is konfigurálhat, és egy újabb biztonsági réteget adhat a felhasználói hitelesítésekhez. Emellett az alkalmazások is átirányíthatók a Microsoft Cloud App Security-hez az Azure AD feltételes hozzáférésen keresztül, hogy valós idejű figyelést és vezérlőket biztosítsanak [a hozzáférési](https://docs.microsoft.com/cloud-app-security/access-policy-aad) és munkamenet-szabályzatokon keresztül [session](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Forgalom megszűnése

Minden forgalom megszakad a felhőben.

Mivel az Azure AD alkalmazásproxy egy fordított proxy, a háttéralkalmazások ravonatkozó összes forgalmat a szolgáltatás leállítja. A munkamenet csak a háttérkiszolgálóval települhet vissza, ami azt jelenti, hogy a háttérkiszolgálók nincsenek kitéve közvetlen HTTP-forgalomnak. Ez a konfiguráció azt jelenti, hogy jobban védve van a célzott támadásokkal szemben.

### <a name="all-access-is-outbound"></a>Minden hozzáférés kimenő 

Nem kell megnyitnia a bejövő kapcsolatokat a vállalati hálózathoz.

Az alkalmazásproxy-összekötők csak az Azure AD alkalmazásproxy-szolgáltatás kimenő kapcsolatait használják, ami azt jelenti, hogy nincs szükség tűzfalportok megnyitására a bejövő kapcsolatokhoz. A hagyományos proxykhoz peremhálózatra (más néven *DMZ,* *demilitarizált zóna*vagy *árnyékolt alhálózat)* volt szükség, és lehetővé kellett tenni a nem hitelesített kapcsolatokhoz való hozzáférést a hálózat szélén. Ebben a forgatókönyvben szükséges beruházások webalkalmazás tűzfal termékek a forgalom elemzéséhez és a környezet védelméhez. Az alkalmazásproxyval nincs szükség peremhálózatra, mert minden kapcsolat kimenő, és biztonságos csatornán keresztül zajlik.

Az összekötőkről az [Azure AD alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)című témakörben talál további információt.

### <a name="cloud-scale-analytics-and-machine-learning"></a>Felhőszintű elemzés és gépi tanulás 

Élvonalbeli biztonsági védelem.

Mivel az Azure Active Directory része, az application proxy a Microsoft Security Response Center és a Digital Crimes Unit adataival kihasználhatja az [Azure AD Identity Protection](../active-directory-identityprotection.md)szolgáltatást. Együtt proaktív módon azonosítjuk a feltört fiókokat, és védelmet nyújtunk a magas kockázatú bejelentkezésekkel szemben. Számos tényezőt figyelembe veszünk annak meghatározásához, hogy mely bejelentkezési kísérletek magas kockázatúak. Ezek közé a tényezők közé tartozik a fertőzött eszközök megjelölése, a hálózatok anonimizálása, valamint az atipikus vagy valószínűtlen helyek.

Ezek közül a jelentések és események közül sok már elérhető egy API-n keresztül a biztonsági információkkal és eseménykezelési (SIEM) rendszerekkel való integrációhoz.

### <a name="remote-access-as-a-service"></a>Távelérés szolgáltatásként

Nem kell aggódnia a helyszíni kiszolgálók karbantartása és javítása miatt.

A nem javított szoftverek még mindig nagyszámú támadást jelentenek. Az Azure AD alkalmazásproxy egy internetes méretű szolgáltatás, amely a Microsoft tulajdonában van, így mindig megkapja a legújabb biztonsági javításokat és frissítéseket.

Az Azure AD Alkalmazásproxy által közzétett alkalmazások biztonságának növelése érdekében letiltjuk a webbejáró robotokat az alkalmazások indexelésében és archiválásában. Minden alkalommal, amikor egy webbejáró robot megpróbálja beolvasni a robot beállításait egy közzétett alkalmazáshoz, az Alkalmazásproxy egy robots.txt fájllal válaszol, amely tartalmazza `User-agent: * Disallow: /`a .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS védelmi szolgáltatás

Az alkalmazásproxyn keresztül közzétett alkalmazások védettek az elosztott szolgáltatásmegtagadási (DDoS) támadásokkal szemben. **Az Azure DDoS-védelem** az Azure platformmal kínált szolgáltatás, amely megvédi az Azure-erőforrásokat a szolgáltatásmegtagadási támadásoktól. Az **alapszintű** szolgáltatási szint automatikusan engedélyezve van, amely mindig forgalomfigyelést és a gyakori hálózati szintű támadások valós idejű mérséklését biztosítja. Standard **szintű** csomag is elérhető, amely további kockázatcsökkentési képességeket kínál, amelyek kifejezetten az Azure virtuális hálózati erőforrásokra vannak hangolva. További részletek az [Azure DDoS Protection Standard áttekintése](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)című témakörben találhatók.

## <a name="under-the-hood"></a>technikai részletek

Az Azure AD alkalmazásproxy két részből áll:

* A felhőalapú szolgáltatás: Ez a szolgáltatás az Azure-ban fut, és ahol a külső ügyfél-/felhasználói kapcsolatok jönnek létre.
* [A helyszíni összekötő:](application-proxy-connectors.md)Egy helyszíni összetevő, az összekötő figyeli az Azure AD alkalmazásproxy szolgáltatás ból érkező kérelmeket, és kezeli a belső alkalmazásokhoz való kapcsolatokat. 

Az összekötő és az alkalmazásproxy szolgáltatás közötti folyamat akkor jön létre, ha:

* Először az összekötő van beállítva.
* Az összekötő lekéri a konfigurációs adatokat az alkalmazásproxy szolgáltatásból.
* A felhasználó hozzáfér egy közzétett alkalmazáshoz.

>[!NOTE]
>Minden kommunikáció TLS-en keresztül történik, és mindig az alkalmazásproxy-szolgáltatás összekötőjéről származnak. A szolgáltatás csak kimenő.

Az összekötő egy ügyféltanúsítványt használ az alkalmazásproxy-szolgáltatás hitelesítéséhez szinte minden híváshoz. Az egyetlen kivétel ez alól a folyamat alól a kezdeti telepítési lépés, ahol az ügyféltanúsítvány létrejön.

### <a name="installing-the-connector"></a>A csatlakozó telepítése

Az összekötő első beállításakor a következő folyamatesemények történnek:

1. Az összekötő regisztrációja a szolgáltatáshoz az összekötő telepítésének részeként történik. A felhasználók nak meg kell adniuk az Azure AD-rendszergazdai hitelesítő adataik megadását.A hitelesítésből beszerzett jogkivonat ezután megjelenik az Azure AD alkalmazásproxy szolgáltatás.
2. Az alkalmazásproxy szolgáltatás kiértékeli a jogkivonatot. Ellenőrzi, hogy a felhasználó vállalati rendszergazda-e a bérlőben.Ha a felhasználó nem rendszergazda, a folyamat leáll.
3. Az összekötő létrehoz egy ügyféltanúsítvány-kérelmet, és átadja azt a jogkivonattal együtt az alkalmazásproxy-szolgáltatásnak. A szolgáltatás viszont ellenőrzi a jogkivonatot, és aláírja az ügyfél tanúsítványkérelmet.
4. Az összekötő az ügyféltanúsítványt használja az alkalmazásproxy-szolgáltatással való későbbi kommunikációhoz.
5. Az összekötő a rendszerkonfigurációs adatok kezdeti lekérése a szolgáltatásból az ügyféltanúsítvány használatával történik, és most már készen áll a kérelmek teljesítésére.

### <a name="updating-the-configuration-settings"></a>A konfigurációs beállítások frissítése

Amikor az alkalmazásproxy szolgáltatás frissíti a konfigurációs beállításokat, a következő folyamatesemények történnek:

1. Az összekötő az alkalmazásproxy-szolgáltatás konfigurációs végpontjához csatlakozik az ügyféltanúsítvány használatával.
2. Az ügyféltanúsítvány érvényesítése után az alkalmazásproxy szolgáltatás konfigurációs adatokat ad vissza az összekötőnek (például az összekötő, amelynek az összekötőnek részét kell adnia).
3. Ha az aktuális tanúsítvány több mint 180 napos, az összekötő új tanúsítványkérelmet hoz létre, amely 180 naponta hatékonyan frissíti az ügyféltanúsítványt.

### <a name="accessing-published-applications"></a>Közzétett alkalmazások elérése

Amikor a felhasználók hozzáférnek egy közzétett alkalmazáshoz, a következő események történnek az alkalmazásproxy szolgáltatás és az alkalmazásproxy-összekötő között:

1. A szolgáltatás hitelesíti az alkalmazás felhasználóját
2. A szolgáltatás kérelmet helyez el az összekötő várólistájában
3. Egy összekötő feldolgozza a várólistából érkező kérést
4. Az összekötő megvárja a választ
5. A szolgáltatás adatokat továbbít a felhasználónak

Ha többet szeretne megtudni arról, hogy mi történik az egyes lépésekben, olvasson tovább.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. A szolgáltatás hitelesíti a felhasználót az alkalmazás

Ha úgy állította be az alkalmazást, hogy az Áteresztőátviteli módszert használja előhitelesítési módszerként, a program kihagyja az ebben a szakaszban ismertetett lépéseket.

Ha úgy konfigurálta az alkalmazást, hogy az Azure AD-vel előzetesen hitelesítse magát, a felhasználók hitelesítésre az Azure AD STS-re lesznek átirányítva, és a következő lépések lépnek:

1. Az alkalmazásproxy ellenőrzi az adott alkalmazás feltételes hozzáférési házirendkövetelményeinek. Ez a lépés biztosítja, hogy a felhasználó hozzá van rendelve az alkalmazáshoz. Ha kétlépéses ellenőrzésre van szükség, a hitelesítési folyamat egy második hitelesítési módszert kér a felhasználótól.

2. Miután minden ellenőrzés átment, az Azure AD STS kiad egy aláírt jogkivonatot az alkalmazáshoz, és visszairányítja a felhasználót az alkalmazásproxy-szolgáltatásra.

3. Az alkalmazásproxy ellenőrzi, hogy a jogkivonat ot a megfelelő alkalmazásnak adták-e ki. Más ellenőrzéseket is végez, például biztosítja, hogy a jogkivonatot az Azure AD írta alá, és hogy még mindig az érvényes ablakon belül van.

4. Az alkalmazásproxy egy titkosított hitelesítési cookie-t állít be annak jelzésére, hogy az alkalmazás hitelesítése megtörtént. A cookie tartalmaz egy lejárati időbélyeget, amely az Azure AD-ből származó jogkivonaton és más adatokon alapul, például a hitelesítés ahitelesítés ad a felhasználónév alapján. A cookie titkosítva van egy titkos kulccsal, amelyet csak az alkalmazásproxy szolgáltatás ismer.

5. Az alkalmazásproxy visszairányítja a felhasználót az eredetileg kért URL-címre.

Ha az előhitelesítési lépések bármelyikrésze sikertelen, a rendszer elutasítja a felhasználó kérését, és a felhasználó megjelenik egy üzenet, amely jelzi a probléma forrását.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. A szolgáltatás kérelmet helyez el az összekötő várólistájában

Az összekötők nyitva tartják a kimenő kapcsolatot az alkalmazásproxy szolgáltatással. Amikor egy kérelem érkezik, a szolgáltatás várólistára a kérelmet az egyik nyitott kapcsolatok az összekötő felvenni.

A kérelem tartalmazza az alkalmazás ból származó elemeket, például a kérelem fejléceit, a titkosított cookie-ból származó adatokat, a kérést küldő felhasználót és a kérelem azonosítóját. Bár a titkosított cookie-ból származó adatokat a kéréssel együtt küldi el a rendszer, maga a hitelesítési cookie nem.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Az összekötő feldolgozza a várólistából érkező kérést. 

A kérelem alapján az alkalmazásproxy az alábbi műveletek egyikét hajtja végre:

* Ha a kérelem egy egyszerű művelet (például nincs adat a szervezetben, mint a RESTful *GET* kérés), az összekötő kapcsolatot létesít a cél belső erőforrás, és majd megvárja a választ.

* Ha a kérelemhez adatok vannak társítva a törzsben (például egy RESTful *POST* művelet), az összekötő kimenő kapcsolatot hoz létre az ügyféltanúsítvány használatával az alkalmazásproxy-példányhoz. Ezt a kapcsolatot az adatok kéréséhez és a belső erőforráshoz való csatlakozás megnyitásához teszi le. Miután megkapta a kérést az összekötőtől, az alkalmazásproxy szolgáltatás megkezdi a tartalom fogadását a felhasználótól, és továbbítja az adatokat az összekötőnek. Az összekötő viszont továbbítja az adatokat a belső erőforrásnak.

#### <a name="4-the-connector-waits-for-a-response"></a>4. A csatlakozó várja a választ.

Miután a kérelem és az összes tartalom átvitele a háttérrendszer befejeződött, az összekötő várja a választ.

Miután választ kap, az összekötő kimenő kapcsolatot létesít az alkalmazásproxy-szolgáltatással, hogy visszaadja a fejléc részleteit, és megkezdje a visszatérési adatok streamelését.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. A szolgáltatás adatokat továbbít a felhasználónak. 

Az alkalmazás bizonyos feldolgozása itt fordulhat elő. Ha az alkalmazásproxyt úgy konfigurálta, hogy lefordítsa a fejléceket vagy URL-címeket az alkalmazásban, akkor a feldolgozás szükség szerint történik ebben a lépésben.


## <a name="next-steps"></a>További lépések

[Hálózati topológia szempontok az Azure AD alkalmazásproxy használatakor](application-proxy-network-topology.md)

[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
