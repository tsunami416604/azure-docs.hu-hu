---
title: "Azure Active Directory feltételes hozzáférés |} Microsoft Docs"
description: "Az Azure Active Directoryban feltételes hozzáférés-vezérlés segítségével meghatározott feltételek keresése, amikor az alkalmazás-hozzáférés hitelesítéséhez."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 40849e7f0c8a76bdd8a0e03d0780534569d9874a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="conditional-access-in-azure-active-directory"></a>Feltételes hozzáférés az Azure Active Directoryban

Mobileszköz-first, a felhő-első világában Azure Active Directory lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. (Beleértve a BYOD) eszközök elterjedésével használhatók ki a vállalati hálózathoz, és a 3. fél Szolgáltatottszoftver-alkalmazásoknál, informatikai szakemberek számára két másik célt szemben:

- A végfelhasználók számára, hogy termelékenyebben legyenek a tetszőleges helyről és időben ellenőrizniük
- A vállalati eszközök védelme bármikor

A termelékenység növelése, Azure Active Directory nyújt a vállalati eszközök eléréséhez beállítások széles köre a felhasználókat. Alkalmazáshozzáférés-kezeléshez, az Azure Active Directory lehetővé teszi annak érdekében, hogy csak *a megfelelő személyeknek* férhet hozzá az alkalmazásokhoz. Mi történik, ha szeretné jobban vezérelheti, hogy a megfelelő személyeknek érnek el bizonyos körülmények között az erőforrások? Mi történik, ha lehetősége van arra is használt kívánt még a bizonyos alkalmazásokhoz való hozzáférés letiltása a *jobb gombbal a személyek*? Például előfordulhat OK gombra, ha a megfelelő személyeknek bizonyos alkalmazásokhoz fér hozzá a megbízható hálózatból; azonban nem biztos hogy ezek az alkalmazások hozzáférjenek a nem megbízható hálózaton keresztül. Meg lehet oldani ezeket a kérdéseket a feltételes hozzáférés használatának.

Feltételes hozzáférés egy olyan képességet, az Azure Active Directory, amely lehetővé teszi a környezetében a meghatározott feltételek alapján alkalmazásokhoz való hozzáférés vezérlőit kényszerítéséhez. Szabályozza akkor vagy nagy terhelést jelent a eléréséhez további követelmények vagy letilthatja azt. Feltételes hozzáférés végrehajtásának házirendek alapul. Egy csoportházirend-alapú módszer egyszerűbbé teszi a konfigurációs felhasználói élmény, mert ez azt jelenti, hogy a hozzáférési követelmények a véleménye módja.  

Általában adja meg a hozzáférési követelmények, a következő mintát alapuló utasítások segítségével:

![vezérlő](./media/active-directory-conditional-access-azure-portal/10.png)

Ha két előfordulását lecseréli a "*ez*" valós adatokkal, például egy házirend-utasítás, amely valószínűleg ismerős lehet rendelkezik:

*Ha a alvállalkozói megpróbált hozzáférni a felhőalapú alkalmazások, amelyek nem megbízható hálózatokon, majd blokkolja a hozzáférést.*

A fenti házirend-utasítás emel ki, a teljesítmény, a feltételes hozzáférés. Amíg engedélyezheti alvállalkozói, alapvetően a felhőalkalmazások eléréséhez (**ki**), a feltételes hozzáférés is adja meg a feltételeket, amelyek alapján a hozzáférés lehetséges (**hogyan**).

Azure Active Directory feltételes hozzáférés, kontextusában

- "**Ha ez történik**" nevezik **utasítás feltétel**
- "**Majd ehhez**" nevezik **vezérlők**

![vezérlő](./media/active-directory-conditional-access-azure-portal/11.png)

Egy feltétel utasítást a vezérlőkkel kombinációja a feltételes hozzáférési házirend jelöli.

![vezérlő](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Vezérlők

A feltételes hozzáférési házirendek vezérlők meghatározása. azt, amely kell fordulhat elő, ha egy feltétel utasítás teljesítését.  
A vezérlők letiltja a hozzáférést, vagy a további követelményeknek megfelelő hozzáférést.
Amikor konfigurál egy házirendet, amely lehetővé teszi a hozzáférést, kell kiválasztania legalább egy követelmény.  

A vezérlők két típusa van: 

- **Támogatás szabályozza** -támogatás vezérlőelemek szabályozására, függetlenül attól, a felhasználó befejezheti a hitelesítést és eléri az erőforrást, amely akkor jelentkezhet be a végrehajtani kívánt. Ha több vezérlő kijelölt, beállítható, hogy az összes lesz szükség, ha a házirend feldolgozása.
Az Azure Active Directory a jelenlegi megvalósításától a következő grant-ellenőrzésre vonatkozó követelmények konfigurálását teszi lehetővé:

    ![vezérlő](./media/active-directory-conditional-access-azure-portal/73.png)

- **Munkamenet-vezérlők** -munkamenet engedélyezése a felhőalapú alkalmazások élmény korlátozása szabályozza. A munkamenet vezérlők felhőalkalmazások érvényesíti, és további Azure AD-be a munkamenetre vonatkozó az alkalmazás által biztosított információk alapján.

    ![vezérlő](./media/active-directory-conditional-access-azure-portal/31.png)


További információkért lásd: [az Azure Active Directory feltételes hozzáférést vezérlő](active-directory-conditional-access-controls.md).


## <a name="condition-statement"></a>Feltételutasításhoz

Az előző szakaszban vezetett támogatott beállítások letiltása, vagy az erőforrások a vezérlők űrlapon való hozzáférés korlátozása. A feltételes hozzáférési szabályzatot a feltételek, amelyeknek teljesülniük kell a vezérlők feltétel utasítás formában alkalmazandó határozhatók meg.  

A következő hozzárendelések a feltételutasításhoz az alábbiakból állhat:

![vezérlő](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>Ki?

A feltételes hozzáférési házirendjének konfigurálásakor kell kiválasztania a felhasználóknak vagy csoportoknak a házirend vonatkozik. Sok esetben azt szeretné, a vezérlők, a felhasználók adott csoportja alkalmazandó. A feltétel utasításban definiálhat a kiválasztásával, a szükséges felhasználók és csoportok a házirend vonatkozik. Ha szükséges, explicit módon is kizárhat a felhasználók egy csoportja a házirend adásával.  

![vezérlő](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>Mi?

A feltételes hozzáférési házirendjének konfigurálásakor kell kiválasztania a felhőalapú alkalmazások, a házirend vonatkozik.
Általában nincsenek bizonyos alkalmazások, a védelem szempontjából, mint a többire további figyelmet igénylő környezetében. Ez a beállítás befolyásolja, például alkalmazások, amelyekre a bizalmas adatokhoz való hozzáférést.
Ha felhőalapú alkalmazások, a házirend vonatkozik felhőalkalmazások hatókörének meghatározása. Ha szükséges, akkor közvetlenül is kizárhatja. utóbbi esetben az alkalmazások a házirend.

![vezérlő](./media/active-directory-conditional-access-azure-portal/09.png)

A felhőalapú alkalmazásokat is használhatja a feltételes hozzáférési házirend a teljes listáját lásd: a [Azure Active Directory feltételes hozzáférési technikai útmutató](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>Hogyan?

Mindaddig, amíg a alkalmazásokhoz való hozzáférés szabályozhatja körülmények történik, előfordulhat, hogy nincs szükség további vezérlők hogyan érhetők el a felhőalapú alkalmazásokat a felhasználók által a előíró lehet. Azonban dolgot eltérő, ha a felhőalapú alkalmazásokhoz való hozzáférés hajtja végre, például a nem megbízható hálózatokhoz, vagy nem megfelelő eszközök lehet. A feltétel utasításban bizonyos hozzáférési feltételek, amelyek további követelményei: hogyan történik a alkalmazásokhoz való hozzáférés adhat meg.

![Feltételek](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Feltételek

Az Azure Active Directory, a jelenlegi megvalósításától meghatározhatja feltételeinek a következő területeken:

- Bejelentkezési kockázata
- Eszközök
- Helyek
- Ügyfél-alkalmazások


![Feltételek](./media/active-directory-conditional-access-azure-portal/01.png)

### <a name="sign-in-risk"></a>Bejelentkezési kockázata

A bejelentkezési kockázata olyan objektum, amely az Azure Active Directory nyomon követésére szolgál annak valószínűsége, hogy a bejelentkezés kísérlet történt egy felhasználói fiókot jogos tulajdonosa nem lett végrehajtva. Ez az objektum (magas, közepes vagy alacsony) annak a valószínűségét nevű attribútum formában tárolja [bejelentkezési kockázati szint](active-directory-reporting-risk-events.md#risk-level). Ez az objektum bejelentkezéskor a felhasználó generál, ha az Azure Active Directory bejelentkezési kockázatok észlelt. További információkért lásd a [kockázatos bejelentkezésekkel](active-directory-identityprotection.md#risky-sign-ins) foglalkozó részt.  
A számított bejelentkezési kockázati szint egy feltételes hozzáférési házirendben feltételként használható. 

![Feltételek](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Eszközök

Az eszköz platformjától jellemzőek, az eszközön futó operációs rendszer: meghatározhatja azokat az eszközplatformokat, amelyeknek tartalmazza, valamint a eszközplatformokat, amelyeken a házirend alól.  
A házirend eszközplatformok használatához előbb módosítsa a konfigurálás váltógombok **Igen**, majd válassza ki az összes vagy az egyes eszközplatformok a házirend vonatkozik. Ha az egyes eszközplatformok választja, a házirend ezekről a platformokról csak hatással van. Ebben az esetben, más támogatott platformra bejelentkezések nem érinti a házirendet.


![Feltételek](./media/active-directory-conditional-access-azure-portal/02.png)

A támogatott eszközplatformok teljes listáját lásd: [eszköz platform feltétel](active-directory-conditional-access-technical-reference.md#device-platform-condition).


### <a name="locations"></a>Helyek

A helyek lehetősége van a ahol kapcsolódási kísérlet kezdeményezték alapuló feltételeinek megadása. A helyek listáját bejegyzései vagy **helyek nevű** vagy **MFA megbízható IP-címek**.  

**Helyek nevű** egyik szolgáltatása, a címkék adhatók meg, a történt, hogy a helyek kapcsolódási kísérlet az Azure Active Directoryban. Adja meg egy helyet, vagy konfigurálhat olyan IP-címet címtartományt, vagy válasszon egy országot / régiót.  

![Feltételek](./media/active-directory-conditional-access-azure-portal/42.png)

Emellett jelölheti meg egy nevesített helyet megbízható helyként. Egy feltételes hozzáférési házirend a megbízható helyen, amely lehetővé teszi egy másik szűrje *a megbízható helyek* a helyek állapotban.
Elnevezett helyek is fontosak kimutatása környezetében [kockázati események](active-directory-reporting-risk-events.md) hamis-figyelmeztetéséket bejelentkezés szokatlan helyekről kockázat eseményre lehetetlen utazás számának csökkentése érdekében. 

Elnevezett helyek konfigurálhatja az Azure ad-ben a kapcsolódó objektum mérete korlátozza. Konfigurálhatja:
 
 - Egy olyan hellyel, amely legfeljebb 500 IP-címtartományok nevű
 - Legfeljebb egy IP-címtartomány hozzájuk rendelt 60 elnevezett helyekre (előzetes verzió) 

További információkért lásd: [az Azure Active Directory helyek nevű](active-directory-named-locations.md).


**Többtényezős hitelesítés a megbízható IP-címek** egyik szolgáltatása, amely lehetővé teszi a megbízható IP-címtartományok a szervezet helyi intranet képviselő adható meg a multi-factor authentication. Konfigurál egy helyen feltételt, megbízható IP-címek lehetővé teszi a vállalati hálózathoz, és az egyéb helyekre kapcsolatok megkülönböztetésére. További információkért lásd: [megbízható IP-címek](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

A feltételes hozzáférési házirendben a következőket teheti:

- Tartalmazza
    - Bármely helyen
    - A megbízható helyek
    - A kijelölt helyek
- Kizárása
    - A megbízható helyek
    - A kijelölt helyek
     
![Feltételek](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>Ügyfél-alkalmazások

Az ügyfélalkalmazás lehet az alkalmazás (webböngésző, mobilalkalmazás, asztali ügyfél), és az Azure Active Directory segítségével általános szinten, vagy kifejezetten választhatja ki az Exchange Active Sync.  
Alapszintű hitelesítés, mint például a régebbi Office-ügyfelek, amelyek nem használják a modern hitelesítést használó ügyfelek régebbi hitelesítési hivatkozik. Feltételes hozzáférés jelenleg nem támogatott a hagyományos hitelesítéssel.

![Feltételek](./media/active-directory-conditional-access-azure-portal/04.png)


A feltételes hozzáférési házirend használható ügyfél-alkalmazások teljes listáját lásd: a [Azure Active Directory feltételes hozzáférési technikai útmutató](active-directory-conditional-access-technical-reference.md#client-apps-condition).




## <a name="common-scenarios"></a>Gyakori forgatókönyvek

### <a name="requiring-multi-factor-authentication-for-apps"></a>Többtényezős hitelesítés megkövetelése az alkalmazások

Sok környezetben a többinél magasabb szintű védelmet igénylő alkalmazások rendelkeznek.
Ez helyzet, például az alkalmazások, amelyekre a bizalmas adatokhoz való hozzáférést.
Ha a kívánt védelmi réteget hozzá ezekhez az alkalmazásokhoz, konfigurálhatja egy feltételes hozzáférési szabályzatot, amely többtényezős hitelesítést igényel, amikor a felhasználók elérik-e ezeket az alkalmazásokat.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Többtényezős hitelesítés megkövetelése a nem megbízható hálózatokhoz való hozzáférést

Ebben a forgatókönyvben nem hasonló az előző helyzethez, mert a multi-factor authentication követelmény hozzáadása.
A fő különbség azonban ez a követelmény feltételét.  
Míg az előző példában a fókuszában sensitve adatokhoz való hozzáférés alkalmazások volt, az ebben a forgatókönyvben elsősorban megbízható helyeken.  
Ez azt jelenti lehetséges, hogy a multi-factor authentication követelmény az alkalmazások a felhasználó nem megbízható hálózaton keresztül illetéktelen.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Csak a megbízható eszközök férhetnek hozzá az Office 365-szolgáltatásokhoz

Intune használ a környezetben, ha azonnal megkezdheti a feltételes hozzáférési házirendek felülete a Azure-konzolon.

Számos Intune-ügyfél számára a feltételes hozzáférés segítségével győződjön meg arról, hogy csak a megbízható eszközök férhetnek hozzá az Office 365-szolgáltatásokhoz. Ez azt jelenti, hogy a mobileszközök az Intune-nal beléptetett és megfelelőségi házirend követelményeknek, és, hogy a Windows rendszerű számítógépek csatlakozik egy helyszíni tartományban. A kulcs fokozása, nem kell ugyanabban a házirendben beállítása az egyes az Office 365-szolgáltatásokhoz.  Ha egy új házirendet hoz létre, konfigurálhatja az egyes az Office 365-alkalmazásokat, amelyek feltételes hozzáférés védeni kíván felvenni a felhőalapú alkalmazásokat.

## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
