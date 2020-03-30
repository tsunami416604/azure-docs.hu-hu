---
title: Helyfeltétel az Azure Active Directory feltételes hozzáférésében
description: Ismerje meg, hogyan használhatja a helyfeltételt a felhőalapú alkalmazásokhoz való hozzáférés szabályozására a felhasználó hálózati helye alapján.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263230"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében? 

Az [Azure Active Directory (Azure AD) feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)szabályozhatja, hogy a jogosult felhasználók hogyan férhetnek hozzá a felhőalapú alkalmazásokhoz. A feltételes hozzáférési házirend helyfeltétele lehetővé teszi, hogy a hozzáférés-vezérlési beállításokat a felhasználók hálózati helyéhez kösse.

Ez a cikk a helyfeltétel konfigurálásához szükséges információkat tartalmazza.

## <a name="locations"></a>Helyek

Az Azure AD lehetővé teszi az eszközökre, alkalmazásokra és szolgáltatásokra való egyszeri bejelentkezést a nyilvános interneten bárhol. A helyfeltételsel szabályozhatja a felhőalapú alkalmazásokhoz való hozzáférést a felhasználó hálózati helye alapján. A helyfeltétel gyakori használati esetei a következők:

- Többtényezős hitelesítés megkövetelése a szolgáltatáshoz a vállalati hálózaton kívül hozzáférő felhasználók számára.
- A szolgáltatáshoz bizonyos országokból vagy régiókból származó felhasználók hozzáférése letiltása.

A hely egy hálózati hely címkéje, amely egy elnevezett helyet vagy többtényezős hitelesítést jelöl megbízható IP-k.

## <a name="named-locations"></a>Nevesített helyek

Az elnevezett helyek segítségével logikai csoportosításokat hozhat létre AZ IP-címtartományokból vagy országokból és régiókból.

A megnevezett helyeket a Feltételes hozzáférés lap **Kezelés** szakaszában érheti el.

![Elnevezett helyek a feltételes hozzáférésben](./media/location-condition/02.png)

Egy elnevezett hely a következő összetevőkkel rendelkezik:

![Új elnevezett hely létrehozása](./media/location-condition/42.png)

- **Név** - Egy elnevezett hely megjelenítendő neve.
- **IP-tartományok** – Egy vagy több IPv4-címtartomány CIDR formátumban. Az IPv6-címtartomány megadása nem támogatott.

   > [!NOTE]
   > Az IPv6-címtartományok jelenleg nem szerepelhetnek elnevezett helyen. Ez azt jelenti, hogy az IPv6-tartományok nem zárhatók ki a feltételes hozzáférési házirendből.

- **Megjelölés megbízható helyként** – Egy elnevezett helyhez beállítható jelző, amely megbízható helyet jelöl. A megbízható helyek általában olyan hálózati területek, amelyeket az informatikai részleg vezérel. A feltételes hozzáférés mellett az Azure Identity Protection és az Azure AD biztonsági jelentések is használják a megbízható névvel ellátott helyeket a hamis pozitív értékek csökkentése [érdekében.](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)
- **Országok/régiók** – Ez a beállítás lehetővé teszi egy vagy több ország vagy régió kiválasztását egy elnevezett hely meghatározásához.
- **Ismeretlen területek belefoglalása** – Egyes IP-címek nincsenek leképezve egy adott országra vagy régióra. Ezzel a beállítással megadhatja, hogy ezek az IP-címek szerepeljenek-e a megnevezett helyen. Akkor használja ezt a beállítást, ha a megnevezett helyet használó házirend ismeretlen helyekre vonatkozik.

A konfigurálható elnevezett helyek számát korlátozza a kapcsolódó objektum mérete az Azure AD-ben. A helyeket a következő korlátozások alapján konfigurálhatja:

- Egy elnevezett hely, akár 1200 IP-tartománysal.
- Legfeljebb 90 elnevezett hely, mindegyikhez egy IP-tartomány van rendelve.

A feltételes hozzáférés házirendje az IPv4- és IPv6-forgalomra vonatkozik. A jelenleg elnevezett helyek nem teszik lehetővé az IPv6-tartományok konfigurálását. Ez a korlátozás a következő helyzeteket eredményezi:

- A feltételes hozzáférési házirend nem célozható meg meghatározott IPv6-tartományokra
- A feltételes hozzáférési házirend nem zárhat ki bizonyos IPV6-tartományokat

Ha egy házirend úgy van beállítva, hogy a "Bármely helyre" vonatkozzon, akkor az IPv4- és IPv6-forgalomra vonatkozik. A megadott országokhoz és régiókhoz konfigurált elnevezett helyek csak az IPv4-címeket támogatják. Az IPv6-forgalom csak akkor jelenik meg, ha az "ismeretlen területek felvétele" lehetőség van kiválasztva.

## <a name="trusted-ips"></a>Megbízható IP-címek

A többtényezős hitelesítési szolgáltatás beállításaiban a szervezet helyi intranetjét képviselő [IP-címtartományokat is beállíthatja.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Ez a szolgáltatás lehetővé teszi akár 50 IP-címtartomány konfigurálását. Az IP-címtartományok CIDR formátumúak. További információ: [Trusted IP.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Ha konfigurált megbízható IP-címekkel rendelkezik, azok **MFA-megbízható IPS-ként** jelennek meg a helyfeltétel helyeinek listájában.

### <a name="skipping-multi-factor-authentication"></a>Többtényezős hitelesítés kihagyása

A többtényezős hitelesítési szolgáltatás beállítások lapján azonosíthatja a vállalati intranetes felhasználókat, ha az **intraneten lévő összevont felhasználók tól érkező kérelmekhez a Többtényezős hitelesítés kihagyása lehetőséget választja.** Ez a beállítás azt jelzi, hogy az AD FS által kiadott belső vállalati hálózati jogcímnek megbízhatónak kell lennie, és a felhasználó vállalati hálózaton lévőként való azonosítására kell használni. További információt [a Megbízható IP-szolgáltatások engedélyezése feltételes hozzáféréssel című témakörben talál.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

Miután bejelölte ezt a beállítást, beleértve a megnevezett helyet, az **MFA megbízható IPS** minden olyan házirendre vonatkozik, amelynek be van jelölve ez a beállítás.

A hosszú élettartamú mobil- és asztali alkalmazások esetében a feltételes hozzáférést rendszeresen újraértékeli a rendszer. Az alapértelmezett érték óránként egyszer. Ha a belső vállalati hálózati jogcím csak a kezdeti hitelesítés időpontjában jelenik meg, előfordulhat, hogy az Azure AD nem rendelkezik megbízható IP-tartományok listájával. Ebben az esetben nehezebb megállapítani, hogy a felhasználó még mindig a vállalati hálózaton van-e:

1. Ellenőrizze, hogy a felhasználó IP-címe a megbízható IP-tartományok egyikében van-e.
2. Ellenőrizze, hogy a felhasználó IP-címének első három oktettje megegyezik-e a kezdeti hitelesítés IP-címének első három oktettével. Az IP-címet a rendszer összehasonlítja a kezdeti hitelesítéssel, amikor a belső vállalati hálózati jogcím eredetileg megjelent, és a felhasználó helyét ellenőrizték.

Ha mindkét lépés sikertelen, a felhasználó már nem megbízható IP-cím.

## <a name="location-condition-configuration"></a>Helyfeltétel-konfiguráció

A helyfeltétel konfigurálásakor a következők között lehet különbséget tenni:

- Bármely hely
- Minden megbízható hely
- Kijelölt helyek

![Helyfeltétel-konfiguráció](./media/location-condition/01.png)

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint **a Bármely hely** beállítás beállításával a rendszer házirendet alkalmaz az összes IP-címre, ami az interneten található bármely címet jelenti. Ez a beállítás nem korlátozódik az elnevezett helyként konfigurált IP-címekre. Ha **a Bármely helyet választja,** továbbra is kizárhat bizonyos helyeket a házirendből. Alkalmazhat például egy házirendet az összes helyre, kivéve a megbízható helyeket, hogy a hatókört a vállalati hálózat kivételével az összes helyre állítsa be.

### <a name="all-trusted-locations"></a>Minden megbízható hely

Ez a beállítás a következőkre vonatkozik:

- Minden megbízható helyként megjelölt hely
- **MFA megbízható IPS** (ha konfigurálva van)

### <a name="selected-locations"></a>Kijelölt helyek

Ezzel a beállítással kijelölhet egy vagy több elnevezett helyet. Ahhoz, hogy egy ilyen beállítással rendelkező házirend érvényes edzhessen, a felhasználónak a kiválasztott helyek bármelyikéről kell csatlakoznia. Ha a **Megnevezett** hálózatkijelölési vezérlő kiválasztása elemre kattint, amely az elnevezett hálózatok listáját jeleníti meg. A lista azt is megjeleníti, hogy a hálózati hely megbízhatóként van-e megjelölve. Az **MFA megbízható IP-k** nevű elnevezett hely tartalmazza a többtényezős hitelesítési szolgáltatás beállítási lapján konfigurálható IP-beállításokat.

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="when-is-a-location-evaluated"></a>Mikor értékelik ki a helyszínt?

A feltételes hozzáférési házirendek kiértékelése a következő korban történik:

- A felhasználó először bejelentkezik egy webalkalmazásba, mobil- vagy asztali alkalmazásba.
- Egy modern hitelesítést használó mobil- vagy asztali alkalmazás frissítési jogkivonatot használ egy új hozzáférési jogkivonat beszerzéséhez. Alapértelmezés szerint ez az ellenőrzés óránként egyszer történik.

Ez az ellenőrzés azt jelenti, hogy a mobil és asztali alkalmazások modern hitelesítést használó, a változás helyét lenne észlelni egy órán belül a változó a hálózati helyét. A mobil- és asztali alkalmazások, amelyek nem használnak modern hitelesítést, a szabályzat minden jogkivonat-kérelem vonatkozik. A kérelem gyakorisága az alkalmazástól függően változhat. Hasonlóképpen a webes alkalmazások esetében a szabályzat a kezdeti bejelentkezéskor kerül alkalmazásra, és jó a webalkalmazás munkamenetének élettartama. Az alkalmazások munkamenet-élettartamának különbségei miatt a házirendek kiértékelése közötti idő is változik. Minden alkalommal, amikor az alkalmazás új bejelentkezési jogkivonatot kér, a házirend alkalmazásra kerül.

Alapértelmezés szerint az Azure AD óránként kiad egy jogkivonatot. A vállalati hálózatról való leköltözés után egy órán belül a házirend a modern hitelesítést használó alkalmazásokra van kényszerítve.

### <a name="user-ip-address"></a>Felhasználói IP-cím

A házirend-kiértékelésben használt IP-cím a felhasználó nyilvános IP-címe. A magánhálózaton lévő eszközök esetében ez az IP-cím nem a felhasználó intraneten lévő eszközének ügyfél-IP-címe, hanem a hálózat által a nyilvános internethez való csatlakozáshoz használt cím.

> [!WARNING]
> Ha az eszköz csak IPv6-címmel rendelkezik, a helyfeltétel konfigurálása nem támogatott.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Elnevezett helyek tömeges feltöltése és letöltése

Ha elnevezett helyeket hoz létre vagy frissít tömeges frissítésekhez, feltölthet vagy letölthet egy CSV-fájlt az IP-tartományokkal. A feltöltés a listában szereplő IP-tartományokat a fájlból származóra cseréli. A fájl minden sora egy IP-címtartományt tartalmaz CIDR formátumban.

### <a name="cloud-proxies-and-vpns"></a>Felhőalapú proxyk és VPN-ek

Ha egy felhőalapú proxy vagy VPN-megoldás használata, az Ip-cím Az Azure AD használja a szabályzat kiértékelésekor a proxy IP-címét. A felhasználó nyilvános IP-címét tartalmazó X-Forwarded-For (XFF) fejléc et nem használja a rendszer, mert nincs érvényesítés, hogy megbízható forrásból származik, így az IP-cím megjátszásának módját is bemutatja.

Ha egy felhőalapú proxy van érvényben, a tartományhoz csatlakozott eszköz megköveteléséhez használt szabályzat használható, vagy az AD FS belső corpnet-jogcím.

### <a name="api-support-and-powershell"></a>API-támogatás és PowerShell

Az API és a PowerShell még nem támogatott elnevezett helyek, illetve feltételes hozzáférési szabályzatok.

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan konfigurálhat feltételes hozzáférési szabályzatot, olvassa [el az MFA megkövetelése bizonyos alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel című témakört.](app-based-mfa.md)
- Ha készen áll a feltételes hozzáférési szabályzatok konfigurálására a környezetéhez, olvassa el az Azure Active Directory feltételes hozzáféréssel kapcsolatos [gyakorlati tanácsait.](best-practices.md)
