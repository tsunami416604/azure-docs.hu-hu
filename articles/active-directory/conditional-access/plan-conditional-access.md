---
title: Feltételes hozzáférési szabályzatokat az Azure Active Directory megtervezése |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja az Azure Active Directory feltételes hozzáférési házirendek tervezéséről.
services: active-directory
author: MicrosoftGuyJFlo
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4832055bffa72732991cb766ce88cc48c07530e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518711"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>kézikönyv: Az Azure Active Directoryban a feltételes hozzáférés üzembe helyezésének megtervezése

A feltételes hozzáférés üzembe helyezésének megtervezése fontos, hogy a hozzáférési stratégia, alkalmazások és erőforrások elérése érdekében a szervezetben. Legjobban a központi telepítés megtervezéséhez szükséges engedélyezéséhez, vagy blokkolhatja a körülmények úgy dönt, a felhasználók különböző házirendek tervezési fázisában fel kell költségek. Ez a dokumentum ismerteti a lépéseket kell tennie, biztonságos és hatékony feltételes hozzáférési házirendek megvalósítása. Mielőtt elkezdené, győződjön meg arról, hogy hogyan [feltételes hozzáférési](overview.md) működik, és mikor érdemes használni.


## <a name="what-you-should-know"></a>Alapismeretek

Úgy feltételes hozzáférést, mint egy keretrendszer, amely lehetővé teszi, hogy ki férhet hozzá a munkahelyi alkalmazásokhoz és erőforrásokhoz, egy különálló szolgáltatás helyett. Ennek következtében néhány feltételes hozzáférési beállításai megkövetelik a további funkciók konfigurálhatók. Például konfigurálhatja egy szabályzatot, amely egy adott válaszol [bejelentkezési kockázati szint](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). Azonban a szabályzatot bejelentkezési kockázati szint alapján igényel [Azure Active Directory identity protection](../identity-protection/overview.md) engedélyezni kell.

További funkciók szükségesek, ha is szüksége lehet beolvasni a kapcsolódó licenceket. Például Azure AD Premium P1 szolgáltatás pedig a feltételes hozzáférés identity protection egy Azure AD Premium P2-licenc szükséges.

Feltételes hozzáférési házirendek két típusa van: baseline és standard. A [alapvető házirendet](baseline-protection.md) egy előre meghatározott feltételes hozzáférési szabályzat. Ezek a házirendek az a célja, hogy győződjön meg arról, hogy rendelkezik-e legalább engedélyezve van a biztonsági alapkonfiguráció szintjét. Alapkonfiguráció házirendek. Alapkonfiguráció házirendek az Azure AD összes kiadásokban érhető el, és csak korlátozott testreszabási lehetőségeket biztosítanak. Ha ez a forgatókönyv igényel nagyobb rugalmasságot, tiltsa le az alapvető házirendet, és a követelmények megvalósítása az egyéni standard házirendet.

A standard szintű feltételes hozzáférési szabályzatot testre szabható összes beállítást módosíthatja a szabályzatot, az üzleti igényeinek megfelelően. Standard szintű szabályzatok egy Azure AD Premium P1-licenc szükséges.




## <a name="draft-policies"></a>Vázlat házirendek

Az Azure Active Directory feltételes hozzáférés lehetővé teszi elérhetővé tehet a védelmet a felhőalapú alkalmazások egy új szintre. Az új szinten hogyan férhet hozzá a cloud app alapul egy dinamikus szabályzat-kiértékelés helyett egy statikus konfiguráció. Egy feltételes hozzáférési szabályzattal együtt egy válasz meghatározása (**ehhez**) egy hozzáférési feltételnek (**ebben az esetben**).

![OK és válasz](./media/plan-conditional-access/10.png)

Adja meg minden feltételes hozzáférési szabályzat megvalósítása a tervezési modell használatával szeretne. A tervezési. gyakorlat:

- Megismerheti, hogyan válaszokat és minden szabályzat feltételeit.
- Egy jól dokumentált feltételes hozzáférési szabályzat katalógust a szervezet eredményez. 

A katalógus használhatja annak ellenőrzéséhez, hogy a házirend megvalósítása tükrözi a szervezet üzleti követelményeinek. 

A következő példa sablon használatával a szervezet számára feltételes hozzáférési szabályzatokat hozhat létre:

|Amikor *ez* történik:|Tegye *ez*:|
|-|-|
|Hozzáférési kísérlet történik:<br>– Ha egy felhőalkalmazás*<br>– felhasználók és csoportok szerint*<br>Használatával:<br>-Feltétel 1 (például kívül vállalati hálózatával)<br>-Feltétel 2 (például eszközplatformok)|Az alkalmazás elérésének letiltása|
|Hozzáférési kísérlet történik:<br>– Ha egy felhőalkalmazás*<br>– felhasználók és csoportok szerint*<br>Használatával:<br>-Feltétel 1 (például kívül vállalati hálózatával)<br>-Feltétel 2 (például eszközplatformok)|Hozzáférést biztosít a (és):<br>-1. követelmény (például a többtényezős hitelesítés)<br>– 2. követelmény (például az eszközök megfelelőségének)|
|Hozzáférési kísérlet történik:<br>– Ha egy felhőalkalmazás*<br>– felhasználók és csoportok szerint*<br>Használatával:<br>-Feltétel 1 (például kívül vállalati hálózatával)<br>-Feltétel 2 (például eszközplatformok)|Hozzáférést biztosít a (vagy):<br>-1. követelmény (például a többtényezős hitelesítés)<br>– 2. követelmény (például az eszközök megfelelőségének)|

Minimális **ebben az esetben** határozza meg a rendszerbiztonsági tag (**akik**), amely megpróbál hozzáférni egy felhőalkalmazás (**mi**). Ha szükséges, is belefoglalhatja **hogyan** hozzáférési kísérlet történik. A feltételes hozzáférés, az elemek, amelyek meg ki, mi történik, és hogyan feltételek nevezzük. További információkért lásd: [feltételek Mik az Azure Active Directory feltételes hozzáférés?](conditions.md) 

A **majd ehhez**, meghatározhatja a választ a szabályzat egy hozzáférési feltételnek. A válaszban letiltása, vagy adjon hozzáférést a további követelményeket, például a többtényezős hitelesítés (MFA). Egy teljes körű áttekintése: [azt szabályozza, Mik azok a hozzáférés az Azure Active Directory feltételes hozzáférés?](controls.md)  
 

A hozzáférés-vezérlés feltételekhez való kötésével hozhat létre feltételes hozzáférési szabályzatot.

![OK és válasz](./media/plan-conditional-access/51.png)


További információkért lásd: [mi szükséges munka házirendet egy](best-practices.md#whats-required-to-make-a-policy-work).

Ezen a ponton, egy jó ideje vonatkozó olyan elnevezési konvenciót-szabályzatokat. Az elnevezési szabványnak segítségével keresse meg a szabályzatokat, és céljuk megismerheti azokat az Azure felügyeleti portál megnyitása nélkül. A szabályzat megjeleníthető kell neve:

- Sorozatszám
- A cloud app vonatkozik
- A válasz
- Akik vonatkozik
- Ha vonatkozik (ha van)
 
![Standard elnevezése](./media/plan-conditional-access/11.png)

Egy leíró nevet segít a feltételes hozzáférés megvalósításának áttekintést tartani, amíg a sorszám akkor hasznos, ha egy házirendet a beszélgetés hivatkoznia kell. Például ha megosztana rendszergazda beszélgetés a telefonon, megkérheti számára, hogy nyissa meg a probléma megoldásához EM063 szabályzatot.



Például a következő név meggyőződik, hogy a házirend többtényezős Hitelesítést követel meg a marketing-felhasználók külső hálózatokon, a Dynamics CRP alkalmazással:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`


Az aktív szabályzat mellett célszerű is működjön, mint a másodlagos le van tiltva megvalósítása házirendek [rugalmas hozzáférés-vezérlők kimaradás vagy vészhelyzet forgatókönyvekben](../authentication/concept-resilient-controls.md). A feltételes szabályzatokat az elnevezési szabványnak néhány további elemet kell tartalmaznia: 

- `ENABLE IN EMERGENCY` a többi olyan szabályzat kiemelkedhetnek versenytársaik nevet elején.

- Megszakítás alkalmaznia kell a neve.

- Egy rendelési sorszáma tudni, hogy milyen sorrendben házirendek engedélyezni kell a rendszergazda segítségével. 


Például a következő név azt jelzi, hogy ez a szabályzat az első házirend kívül négy, engedélyeznie kell a többtényezős hitelesítés bekövetkező szolgáltatáskimaradás esetén:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`







## <a name="plan-policies"></a>Házirendek megtervezése

A feltételes hozzáférési szabályzat megoldás megtervezésekor ellenőrzéséhez, hogy az alábbi eredmények elérése érdekében a szabályzatok létrehozásához szükséges. 


### <a name="block-access"></a>Hozzáférés letiltása

Hatékony, a lehetőség letiltja a hozzáférést mert azt:

- Egy felhasználó összes többi hozzárendelés trumps

- A letiltása a bejelentkezést a bérlő vállalata rendelkezik
 
Ha azt szeretné, az összes felhasználó hozzáférésének letiltására, kell legalább egy felhasználó kizárása (általában a vészelérési fiókok esetében) a szabályzat alól. További információkért lásd: [válassza ki a felhasználók és csoportok](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>MFA megkövetelése

A felhasználók bejelentkezési élmény egyszerűsítése, érdemes, hogy jelentkezzen be a felhőalapú alkalmazások, a felhasználónév és jelszó használatával. Azonban általában legalább néhány forgatókönyvet, amelyekre tanácsos egy erősebb formája fiókhitelesítés szükséges. Egy feltételes hozzáférési szabályzattal együtt korlátozhatja a követelmény a multi-factor Authentication – bizonyos forgatókönyvek esetén. 

MFA igénylése gyakori alkalmazási helyzetek hozzáférés:

- [Rendszergazdák](baseline-protection.md#require-mfa-for-admins)
- [Adott alkalmazásokra](app-based-mfa.md) 
- [A hálózati helyről, nem megbízható](untrusted-networks.md).


### <a name="respond-to-potentially-compromised-accounts"></a>Vélhetően feltört fiókoktól válaszolni

Feltételes hozzáférési szabályzatok a vélhetően feltört identitásokat bejelentkezések automatikus válaszokat valósíthat meg. Annak a valószínűsége, hogy a fiók biztonsága sérült kockázati szintek formájában van kifejezve. Identity protection által számított két kockázati szint: bejelentkezési kockázat és felhasználói kockázat. A válasz a bejelentkezési kockázat megvalósításához, két lehetősége van:

- [A bejelentkezési kockázati feltétellel](conditions.md#sign-in-risk) a feltételes hozzáférési szabályzat
- [A bejelentkezési kockázati házirend](../identity-protection/howto-sign-in-risk-policy.md) az identity protection 

Az előnyben részesített módszer a bejelentkezési kockázat feltételként címzés azért, mert azt további testreszabási lehetőségeket kínál.

A felhasználói kockázat szintje csak érhető el [felhasználói kockázati házirend](../identity-protection/howto-user-risk-policy.md) az identity protectionben. 

További információkért lásd: [Mi az Azure Active Directory Identity Protection?](../identity-protection/overview.md) 


### <a name="require-managed-devices"></a>Felügyelt eszközök megkövetelése

Támogatott eszközök hozzáférését a felhőbeli erőforrások elterjedése segítségével a felhasználók a hatékonyság növelése érdekében. A tükrözés oldalon valószínűleg nem lesz szüksége bizonyos erőforrások érnek el egy ismeretlen védelmi szintet az eszközök a környezetben. Érintett erőforrások érdemes beállítani, hogy felhasználók csak érhetik el azokat a felügyelt eszközök használata. További információkért lásd: [a feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés igénylése a felügyelt eszközök](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Jóváhagyott ügyfélalkalmazások megkövetelése

Az első döntéseit, végre kell hajtania egyik a saját eszközök használata (BYOD) forgatókönyvei, hogy szüksége van a teljes eszköz vagy csak a rajta lévő adatok kezelésére. Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Gondoskodik róla, hogy az alkalmazottak számára hatékony munkavégzést, miközben is szeretné megakadályozni az adatvesztést. Az Azure Active Directory (Azure AD) feltételes hozzáférés korlátozhatja a felhőalapú alkalmazások a jóváhagyott ügyfélalkalmazások, amelyek a vállalati adatait védheti a hozzáférést. További információkért lásd: [jóváhagyott ügyfélalkalmazások szükségesek a cloud app hozzáféréshez a feltételes hozzáférés hogyan](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

Az Azure AD számos, beleértve az örökölt hitelesítési leggyakrabban használt hitelesítési és engedélyezési protokollokat támogatja. Hogyan megakadályozhatja az elérését a bérlői erőforrásokhoz örökölt hitelesítést használó alkalmazások? A javaslat, hogy csak letiltja a feltételes hozzáférési szabályzattal együtt. Szükség esetén engedélyezi a csak bizonyos felhasználók és a megfelelő hálózati helyeket az örökölt hitelesítési alapuló alkalmazások használatához. További információkért lásd: [blokkolása az örökölt hitelesítés az Azure AD feltételes hozzáférés](block-legacy-authentication.md).


## <a name="test-your-policy"></a>A házirend tesztelése

Mielőtt éles környezetben megvalósítaná a szabályzat, tesztelje, hogy ellenőrizheti, hogy a várt módon.

1. Tesztfelhasználók létrehozása

2. A tesztelési terv létrehozása

3. A szabályzat konfigurálása

4. Egy szimulált bejelentkezési kiértékelése

5. A házirend tesztelése

6. Felesleges tartalmak törlése



### <a name="create-test-users"></a>Tesztfelhasználók létrehozása

A szabályzat teszteléséhez hozzon létre a felhasználók egy csoportja, amely hasonlít a felhasználók a környezetben. Tesztfelhasználók létrehozása lehetővé teszi, hogy ellenőrizze, hogy a szabályzatok hatással vannak a tényleges felhasználókra és potenciálisan akadályozza az alkalmazásokhoz és erőforrásokhoz való hozzáférésüket előtt elvárt módon működnek-e. 


Egyes szervezetek teszt bérlővel rendelkezik erre a célra. Azonban nehézkes lehet újra létrehozni a feltételeket és a egy házirend eredményének teszteléséhez tesztelési célú bérlői alkalmazások. 

### <a name="create-a-test-plan"></a>A tesztelési terv létrehozása

A tesztelési terv esetében lényeges, hogy a várt eredményt és a tényleges eredmények összehasonlítását. Mindig rendelkeznie kell egy valami teszteléshez formálisság. Az alábbi táblázat ismerteti a tesztelési példák. Módosítsa a forgatókönyveket és a várt eredményt miként vannak konfigurálva a feltételes hozzáférési szabályzatok alapján.

|Szabályzat |Forgatókönyv |Várt eredmény | Eredmény |
|---|---|---|---|
|[Többtényezős hitelesítés munkahelyen kívül](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Engedélyezett a felhasználók bejelentkeznek *alkalmazás* során megbízható helyen lévő / működik|A többtényezős hitelesítés nem kéri a felhasználót| |
|[Többtényezős hitelesítés munkahelyen kívül](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Engedélyezett a felhasználók bejelentkeznek *alkalmazás* során nem megbízható helyen lévő / működik|Felhasználói MFA a rendszer kéri, és sikeresen bejelentkezhet| |
|[Többtényezős hitelesítés (a rendszergazda)](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|Globális rendszergazdaként jelentkezik be *alkalmazás*|Rendszergazdai kéri, hogy a többtényezős hitelesítés| |
|[Kockázatos bejelentkezések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Felhasználó jelentkezik be *alkalmazás* használatával egy [Tor-böngésző](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Rendszergazdai kéri, hogy a többtényezős hitelesítés| |
|[Eszközkezelés](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Jogosulatlan felhasználó megpróbál bejelentkezni egy hitelesített eszközről|Megadja a hozzáférést| |
|[Eszközkezelés](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Jogosult felhasználói kísérlet, illetéktelen eszközre való bejelentkezéshez|Hozzáférés letiltása| |
|[Kockázatos felhasználók számára jelszó módosítása](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Jogosult felhasználó próbál jelentkezzen be a hitelesítő adatokkal való visszaélés (magas kockázatú bejelentkezés)|Jelszó módosítása a felhasználótól, vagy a hozzáférés le lesz tiltva, a házirend alapján| |


### <a name="configure-the-policy"></a>A szabályzat konfigurálása

Feltételes hozzáférési szabályzatok kezelése a manuális feladat. Az Azure Portalon a feltételes hozzáférési szabályzatokat egyetlen központi helyen – a feltételes hozzáférési lapon kezelheti. Egy belépési pont a feltételes hozzáférési oldalára a **biztonsági** című rész a **Active Directory** navigációs ablaktáblán. 

![Feltételes hozzáférés](media/plan-conditional-access/03.png)


Ha azt szeretné, további információ a feltételes hozzáférési szabályzatokat hozhat létre, olvassa el [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md). Ez a rövid útmutató segítségével:

- Megismerkedhet a felhasználói felületen.
- A feltételes hozzáférés működését első benyomást beolvasása. 


### <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési szabályzatot, érdemes ellenőriznie, hogy a várt módon működik-e. Első lépésként, a feltételes hozzáférés használata a [mi történik, ha házirend eszközzel](what-if-tool.md) egy jelentkezzen be a tesztfelhasználó szimulálásához. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.

>[!NOTE]
> Bár egy szimulált Futtatás biztosít a hatás benyomást feltételes hozzáférési szabályzattal rendelkezik, nem cserélje le tényleges tesztfuttatás.


### <a name="test-your-policy"></a>A házirend tesztelése

A tesztelési terv alapján vizsgálati eset futtatása. Ebben a lépésben futtatja az egyes szabályzatok a vizsgált felhasználók győződjön meg arról, hogy az egyes házirendek megfelelően viselkedjen, egy teljes körű tesztelés keresztül. Használja a fent létrehozott minden teszt végrehajtásához forgatókönyveket.

Fontos, hogy a házirend a kizárási feltétel tesztelése. Például előfordulhat, hogy kizárni egy felhasználót vagy csoportot egy szabályzatot, amely többtényezős Hitelesítést követel meg. Ezért tesztelje, ha a kizárt a rendszer kéri a többtényezős hitelesítés, mert egyéb házirendek kombinációja lehet, hogy többtényezős hitelesítés azoknak a felhasználóknak.


### <a name="cleanup"></a>Felesleges tartalmak törlése

A takarítási eljárás a következő lépésekből áll:

1. Tiltsa le a szabályzatot.

2. Távolítsa el a hozzárendelt felhasználók és csoportok.

3. A teszt felhasználók törlése.  




## <a name="move-to-production"></a>Helyezze át az éles környezetbe

Amikor új házirendeket a környezet készen áll, telepíteni kell őket fázisok::

- Belső módosítás kommunikációt tesznek a végfelhasználók számára.

- Indítsa el a felhasználók egy kis készletét, és győződjön meg arról, hogy a szabályzat viselkedik-e a várt módon.

- Amikor kibővít egy szabályzatot, amely további felhasználókat tartalmazzák, továbbra is minden rendszergazda kizárása. A rendszergazdák kivételével biztosítja, hogy valaki továbbra is hozzáfér egy házirend Ha szükség egy az megváltoztatására.

- A szabályzat minden felhasználóra vonatkozni csak akkor, ha ez szükséges.

Ajánlott eljárásként hozzon létre legalább egy felhasználói fiókkal, amely:

- Dedikált házirend felügyelet

- Minden szabályzat zárva

 



## <a name="rollback-steps"></a>Visszaállítási lépések

Abban az esetben állítsa vissza az újonnan megvalósított szabályzatok van szüksége, használja a következő beállítások közül legalább egyet visszaállítása:

1. **Tiltsa le a szabályzatot** -letiltása a házirend biztosítja, hogy ez nem vonatkozik, ha a felhasználó megpróbál bejelentkezni. Mindig visszatérhet, és engedélyezze a házirendet, ha használni szeretné.

    ![Szabályzat letiltása](media/plan-conditional-access/07.png)

2. **Zárja ki a felhasználó / csoport házirendből** – Ha egy felhasználó nem fér hozzá az alkalmazást, ha szeretné, a felhasználó zárni a szabályzatból

    ![Exluce felhasználók](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Ez a beállítás csak az olyan helyzetekben, ahol a felhasználó megbízható takarékosan, használandó. A felhasználó minél hamarabb be a házirend vagy újra kell felvenni.

3. **A szabályzat törlésének** – Ha a házirend már nem szükséges, törölje azt.

## <a name="next-steps"></a>További lépések

Tekintse meg [Azure AD feltételes hozzáférés dokumentációjának](index.yml) a rendelkezésre álló információk áttekintése.
