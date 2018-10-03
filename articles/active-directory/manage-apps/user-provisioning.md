---
title: Automatikus SaaS alkalmazás felhasználói üzembe helyezését az Azure ad-ben |} A Microsoft Docs
description: Hogyan használhatja az Azure AD automatikus kiépítésére, bemutató megszüntetése, és folyamatosan frissíteni a felhasználói fiókok több külső SaaS-alkalmazás között.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 8a84f2f13318dea5c2b99af0b880f2adb1343c8d
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042785"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Kiépítés és megszüntetés SaaS-alkalmazásokhoz az Azure Active Directory felhasználói automatizálása
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Mi az automatizált felhasználókiépítése SaaS-alkalmazások?
Az Azure Active Directory (Azure AD) segítségével automatizálhatja a létrehozás, a karbantartással és a felhasználói identitások felhőben eltávolítása ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) alkalmazások, például Dropbox, a Salesforce, ServiceNow vagy több.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Az alábbiakban néhány példa a Mi ez a funkció lehetővé teszi, hogy:**

* Automatikus létrehozása új fiókokat a megfelelő rendszerek új felhasználók számára, amikor csatlakoznak a csapat vagy szervezet.
* Automatikusan inaktiválása fiókokat a megfelelő rendszerek amikor személyek hagyja, a csapat vagy szervezet.
* Győződjön meg arról, hogy az alkalmazásai és rendszerei identitásokat mindig naprakészek, mert a könyvtár, vagy az emberi erőforrások rendszer végbement változások alapján.
* Kiépítés nem felhasználói objektumok, például csoportokat, alkalmazásokat, amelyek támogatják ezt.

**Az alábbi funkciókat is automatizált felhasználókiépítése tartalmazza:**

* Forrás- és rendszerek közötti meglévő azonosítók egyeznek lehetővé teszi.
* Testre szabható attribútum-leképezéshez, amelyek meghatározzák, hogy milyen felhasználói adatokat a forrás rendszerből áramlásának a célrendszerbe.
* Nem kötelező e-mailes riasztásokhoz kiépítési hibák
* Jelentéskészítés és a tevékenység naplók monitorozási és hibaelhárítási segítséget.

## <a name="why-use-automated-provisioning"></a>Miért érdemes használni az automatikus üzembe helyezést?
Néhány gyakori motivációit ezzel a funkcióval a következők:

* Kerülje a költségek, a hatékonysági és a manuális üzembe helyezési folyamatok emberi hibákat.
* Üzemeltetési és üzembe helyezési megoldásokat saját fejlesztésű és parancsfájlok társított költségek elkerülése
* A szervezet biztonságának azonnal eltávolítása felhasználók identitását a legfontosabb SaaS-alkalmazásokban, ha elhagyják a szervezet.
* Az egyszerűen importálhatja a felhasználók nagy száma egy adott SaaS-alkalmazáshoz vagy a rendszer.
* Csak egy készletével határozza meg, akik ki van építve, és ki jelentkezhet be az alkalmazás-szabályzatok kellene.

## <a name="how-does-automatic-provisioning-work"></a>Az Automatikus kiépítés működése
    
A **Azure AD-kiépítési szolgáltatás** kiosztja az SaaS-alkalmazások és más rendszerekhez, hogy a felhasználók által az egyes alkalmazás gyártója által biztosított felhasználói felügyeleti API-végpontokhoz való csatlakozáshoz. A felhasználói felügyeleti API-végpontokon lehetővé teszik, hogy programozott módon létrehozása, frissítése és eltávolítása a felhasználók Azure AD. A kiválasztott alkalmazások az eszközkiépítési szolgáltatás is létrehozhat frissítése és eltávolítása a további identitáshoz kapcsolódó objektumok, például csoportokat és szerepköröket. 

![Kiépítés](./media/user-provisioning/provisioning0.PNG)
*1. ábra: az Azure AD létesítési szolgáltatás*

![Kimenő kiépítési](./media/user-provisioning/provisioning1.PNG)
*2. ábra: "Kimenő" felhasználót érintő kiépítési munkafolyamat az Azure ad-ből a népszerű SaaS-alkalmazásokban*

![Inbound Provisioning](./media/user-provisioning/provisioning2.PNG)
*3. ábra: "Inbound" felhasználókiépítés munkafolyamatot a népszerű emberi tőke Management (HCM) alkalmazások Azure Active Directory és a Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Mely alkalmazások és rendszerek is használhatják az Azure AD-felhasználók automatikus átadása?

Az Azure AD-funkciók előre integrált, számos népszerű SaaS-alkalmazásokhoz és a HR-rendszerek támogatása, valamint az SCIM 2.0 szabvány alkotórészével megvalósító alkalmazások általános támogatása.

### <a name="pre-integrated-applications"></a>Előre integrált alkalmazások
Az összes alkalmazás, amelynek az Azure AD egy előre integrált üzembe helyezési összekötőt támogat, lásd: a [felhasználóátadást készíthet elő az alkalmazás oktatóanyagok listáját](../saas-apps/tutorial-list.md).

Mérnöki csapata további alkalmazások üzembe helyezési támogatás kéréséhez forduljon az Azure AD keresztül egy üzenetet elküldenek a [Azure Active Directory-visszajelzési fórumon](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Ahhoz, hogy egy alkalmazás támogatja a felhasználók automatikus átadása, az azt kell adnia a szükséges felhasználói felügyeleti API-k, amelyek lehetővé teszik külső programok számára a létrehozás, a karbantartással és a felhasználók eltávolításának automatizálása. Ezért nem minden SaaS-alkalmazások kompatibilisek ezzel a funkcióval. Felhasználó-felügyeleti API-kat támogató alkalmazások esetében az Azure AD-mérnöki csapat tudnak kiépítési az összekötő ezeket az alkalmazásokat hozhat létre, és ezt a munkát a jelenlegi és a leendő ügyfelek igényeitől előnyt élvez. 

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0 támogató alkalmazásoknak az összekapcsolása
Információk általános az SCIM megvalósító alkalmazások összekapcsolása a 2.0 - alapú felhasználói felügyeleti API-k, lásd: [automatikus kiépítésére a felhasználók és csoportok alkalmazásokhoz az Azure Active Directory használatával SCIM](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan állíthatok be egy alkalmazás automatikus üzembe helyezés?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Az Azure AD létesítési szolgáltatás, a kiválasztott alkalmazás elindítja az konfigurációját a  **[az Azure portal](https://portal.azure.com)**. Az a **Azure Active Directory > Vállalati alkalmazások** szakaszban válassza **Hozzáadás**, majd **minden**, majd adja hozzá a forgatókönyvtől függően a következők egyikét:

* Az összes alkalmazás a **kiemelt alkalmazások** szakaszban támogatási automatikus üzembe helyezés. Tekintse meg a [felhasználóátadást készíthet elő az alkalmazás oktatóanyagok listáját](../saas-apps/tutorial-list.md) újak a.

* Használhatja az "katalógusban nem szereplő alkalmazás" beállítást a saját fejlesztésű SCIM-Integrációk

![Katalógus](./media/user-provisioning/gallery.png)

Az alkalmazás felügyeleti szolgáltatásban képernyőjéről, a kiépítés konfigurálva van a **kiépítési** fülre.

![Beállítások](./media/user-provisioning/provisioning_settings0.PNG)


* **Rendszergazdai hitelesítő adataival** kell adni az Azure ad létesítési szolgáltatás, amely lehetővé teszi, hogy a felhasználókezelési az alkalmazás által biztosított API-hoz csatlakozhat. Ez a szakasz emellett lehetővé teszi e-mail-értesítések engedélyezése, ha sikertelen a hitelesítő adatokat, vagy az üzembe helyezési feladat hiányzóra [karantén](#quarantine).

* **Attribútum-leképezések** konfigurálhatók, amelyek meghatározzák a forrásrendszerben mezők (Példa: Azure ad-ben) lesz azok tartalmát szinkronizálta a mezők a célrendszeren (Példa: ServiceNow). Ha a cél alkalmazás ezt támogatja, ez a szakasz lehetővé teszi, hogy igény szerint állítsa be a felhasználói fiókok mellett csoportok kiépítésének. "Megfelelő tulajdonság" Válassza ki, melyik mezők használt fiókokat a rendszer közötti megfelelő teszi lehetővé. "[Kifejezések](functions-for-customizing-application-data.md)" segítségével módosíthatja, és átalakítása előtt a célrendszer írás a forrásrendszerben lekért értékek. További információkért lásd: [attribútumleképezések testreszabása](customize-application-attributes.md).

![Beállítások](./media/user-provisioning/provisioning_settings1.PNG)

* **Hatókörkezeléshez kapcsolódó szűrők** mondja el a kiépítési szolgáltatás, mely felhasználók és a csoport a forrásrendszerben kell kell kiépített és/vagy a célrendszerbe – figyelmeztetés megszüntetésről. Két szempontot hatókörének beállítása szűrőket, amelyek együtt lett kiértékelve, amelyek meghatározzák, hogy ki a kiépítés hatókörébe van:

    * **Attribútum értékei alapján végezhet szűrést** – meghatározott attribútumértékek szűrését teszi lehetővé az attribútumleképezések "Forrásobjektum hatóköre" menüben. Megadhatja például, hogy csak egy "Eladások", "Részleg" attribútummal rendelkező felhasználók a kiépítés hatókörébe kell lennie. További információkért lásd: [Hatókörszűrő használatával](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Hozzárendelések szűrő** -létesítése a "Hatókör" menü > Beállítások szakaszban a portál lehetővé teszi, hogy adja meg, hogy csak a "kijelölt" felhasználók és csoportok kell lennie a kiépítés hatókörébe, vagy ha az Azure AD-címtár összes felhasználója kell lennie kiépítve. A "hozzárendelése" felhasználók és csoportok információkért lásd: [egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](assign-user-or-group-access-portal.md).
    
* **Beállítások** egy alkalmazáshoz, beleértve azt, hogy éppen fut-e a kiépítési szolgáltatás működését.

* **Auditnaplók** az Azure AD létesítési szolgáltatás által végzett minden művelet rekordjait tartalmazzák. További részletekért tekintse meg a [kiépítési jelentéskészítési útmutató](check-status-user-account-provisioning.md).

![Beállítások](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> A kiszolgáló üzembe helyezése Azure AD-felhasználót is konfigurálhatók és felügyelhetők az a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Mi történik az üzembe helyezés során?

Ha Azure ad-ben a forrásrendszerben, a kiépítési szolgáltatás használja-e a [különbözeti lekérdezés funkció az Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) felhasználók és csoportok figyelése. A kiépítési szolgáltatás fut egy kezdeti szinkronizálást a forrás és cél rendszer, rendszeres növekményes szinkronizálás követ. 

### <a name="initial-sync"></a>Kezdeti szinkronizálás
A kiépítési szolgáltatás elindult, az első szinkronizálás, minden eddiginél végrehajtott fogja végrehajtani:

1. Minden felhasználó és csoport a forrásrendszerből beolvasása a meghatározott attribútumok lekérdezése a [attribútum-leképezések](customize-application-attributes.md).
2. Szűrheti a felhasználókat és csoportokat ad vissza, bármely konfigurált [hozzárendelések](assign-user-or-group-access-portal.md) vagy [Attribútumalapú hatókörének beállítása szűrőket](define-conditional-rules-for-provisioning-user-accounts.md).
3. Amikor egy felhasználó található hozzá kell rendelni vagy a kiépítés hatókörébe, a szolgáltatás lekérdezi a célrendszeren egy egyező felhasználó használja a kijelölt [megfelelő attribútumok](customize-application-attributes.md#understanding-attribute-mapping-properties). Példa: Ha egyező attribútum a forrásrendszerben userPrincipal nevét, és leképezi a felhasználónevet a célrendszeren, majd a kiépítési szolgáltatás lekérdezi a célrendszer a felhasználónevek, amelyek megfelelnek a userPrincipal értékeket a forrásrendszerben.
4. Ha egyező felhasználó nem található a célrendszeren, létrejön a a forrásrendszerben által visszaadott attribútumokat használ. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli, és az új felhasználóval kapcsolatban, hogy a felhasználó az összes jövőbeli műveletek végrehajtásához használt azonosítója a célrendszer gyorsítótárazza.
5. Ha egyező felhasználó található, frissül a forrásrendszerben által biztosított attribútumok használatával. Miután a fiókjuk egyezik, a kiépítési szolgáltatás észleli, és gyorsítótárazza az új felhasználót, hogy a felhasználó az összes jövőbeli műveletek végrehajtásához használja a célrendszer azonosítója.
6. Ha az attribútumleképezések nem tartalmaz "hivatkozás" attribútumok, a szolgáltatás további frissítések a célrendszeren létrehozásához és csatolásához a hivatkozott objektumokat hajtja végre. Például egy felhasználó jogosult a "Manager" attribútum a célrendszeren, amely csatolva van a célrendszeren létrehozott egy másik felhasználó.
7. A kezdeti szinkronizálás, amely a kiindulási pontot biztosít az ezt követő növekményes szinkronizálás végén vízjel maradnak.

Egyes alkalmazások, például nem csak a felhasználók kiépítése, de a csoportok és azok tagjait is kiépítés ServiceNow, a Google Apps és a Box támogatását. Ezekben az esetekben, ha a csoportos kiépítését engedélyezve van a [leképezések](customize-application-attributes.md), a kiépítési szolgáltatás szinkronizálja a felhasználókat és a csoportokat, és ezt követően szinkronizálja a csoporttagságok. 

### <a name="incremental-syncs"></a>Növekményes szinkronizálás
A kezdeti szinkronizálást követően minden ezt követő szinkronizálások lesz:

1. A lekérdezés minden olyan felhasználók és csoportok, a frissített, mert az utolsó vízjel tárolt a forrásrendszerben.
2. Szűrheti a felhasználókat és csoportokat ad vissza, bármely konfigurált [hozzárendelések](assign-user-or-group-access-portal.md) vagy [Attribútumalapú hatókörének beállítása szűrőket](define-conditional-rules-for-provisioning-user-accounts.md).
3. Amikor egy felhasználó található hozzá kell rendelni vagy a kiépítés hatókörébe, a szolgáltatás lekérdezi a célrendszeren egy egyező felhasználó használja a kijelölt [megfelelő attribútumok](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Ha egyező felhasználó nem található a célrendszeren, létrejön a a forrásrendszerben által visszaadott attribútumokat használ. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli, és az új felhasználóval kapcsolatban, hogy a felhasználó az összes jövőbeli műveletek végrehajtásához használt azonosítója a célrendszer gyorsítótárazza.
5. Ha egyező felhasználó található, frissül a forrásrendszerben által biztosított attribútumok használatával. Ha ez egy újonnan hozzárendelt fiókot, amely egyezik, a kiépítési szolgáltatás észleli, és az új felhasználóval kapcsolatban, hogy a felhasználó az összes jövőbeli műveletek végrehajtásához használt azonosítója a célrendszer gyorsítótárazza.
6. Ha az attribútumleképezések nem tartalmaz "hivatkozás" attribútumok, a szolgáltatás további frissítések a célrendszeren létrehozásához és csatolásához a hivatkozott objektumokat hajtja végre. Például egy felhasználó jogosult a "Manager" attribútum a célrendszeren, amely csatolva van a célrendszeren létrehozott egy másik felhasználó.
7. Ha a felhasználó korábban a kiépítés hatókörébe (beleértve az éppen nem hozzárendelt) hatókör eltávolítják, a szolgáltatás letiltása a felhasználó a célrendszeren egy update-en keresztül.
8. Ha a felhasználó korábban a kiépítés hatókörébe le van tiltva, vagy a helyreállíthatóan törölt a forrásrendszerben, a szolgáltatás letiltása a felhasználó a célrendszeren egy update-en keresztül.
9. Ha egy felhasználó a kiépítés hatókörébe korábban törölt a forrásrendszerben, a szolgáltatás törlése a felhasználó a célrendszeren. Az Azure AD-ben, a felhasználók után helyreállíthatóan törölt törölt 30 nap.
10. A növekményes szinkronizálás, amely a kiindulási pontot biztosít az ezt követő növekményes szinkronizálás végén egy új vízjel maradnak.

>[!NOTE]
> Igény szerint letilthatja a létrehozási, frissítési vagy törlési műveletek használatával a **műveletek céljaként** jelölőnégyzetek az [attribútumleképezések](customize-application-attributes.md) szakaszban. Tiltsa le a felhasználó frissítése közben logikát is védi egy attribútum-leképezés egy mezőt, például a "accountEnabled".

A kiépítési szolgáltatás végpontok közötti növekményes szinkronizálás határozatlan ideig történő futtatásához, meghatározott időközönként továbbra is a [minden alkalmazásra jellemző oktatóanyag](../saas-apps/tutorial-list.md), amíg nem történik a következő események egyikét:

* A manuálisan leállították a szolgáltatást az Azure portal használatával, vagy a megfelelő Graph API-parancs segítségével 
* Egy új kezdeti szinkronizálás akkor indul el, használja a **állapot törölje, majd indítsa újra** lehetőséget az Azure Portalon, vagy a megfelelő Graph API-parancs segítségével. Ez töröl minden tárolt vízjel, és újra ki kell értékelni az összes adatforrás-objektumok okoz.
* Egy új kezdeti szinkronizálás akkor aktiválódik, a attribútumleképezések vagy Hatókörszűrő megváltozása miatt. Ez is töröl minden tárolt vízjel, és újra ki kell értékelni az összes adatforrás-objektumok okoz.
* A kiépítési folyamat (lásd alább) magas Hibaarány miatt karanténba kerül, és legfeljebb négy héttel karanténban marad. Ebben az esetben a szolgáltatás automatikusan letiltásra kerül.

### <a name="errors-and-retries"></a>Hibák és az újrapróbálkozások 
Ha egy adott felhasználó nem lehet hozzáadni, frissített vagy törölt a célrendszeren egy hiba miatt a célrendszeren, majd a művelet újra megpróbálja a következő szinkronizálási ciklusban. Ha a felhasználó továbbra is sikertelen, az újrapróbálkozások megkezdődik a kisebb gyakorisággal, fokozatosan méretezést naponta csak egy kísérlet történik. A hiba elhárításához ellenőrizze a rendszergazdák kell a [auditnaplók](check-status-user-account-provisioning.md) a "folyamatletét" meghatározni a legfelső szintű események vezethetnek, és hajtsa végre a megfelelő műveletet. Gyakori hibák a következők lehetnek:

* Töltse be a forrásrendszerben, amelyek szükségesek a célrendszeren attribútum nem rendelkező felhasználók
* A forrásrendszerben, amelynek nincs egyedi korlátozása a célrendszeren egy attribútumérték, és ugyanazzal az értékkel rendelkező felhasználók szerepel egy másik felhasználói rekord

Ezek a hibák megoldható, hogy az érintett felhasználónak attribútumértékei módosítani a forrásrendszerben, vagy az attribútumleképezések nem az ütközéseket okozhat.   

### <a name="quarantine"></a>Karantén
A legtöbb vagy összes a hívások társzolgáltatásokhoz a célrendszer konzisztens módon (például érvénytelen rendszergazdai hitelesítő adatokat is) egy hiba miatt sikertelen, majd a az üzembe helyezési feladat "karanténba helyezése" állapotba kerül. Ez jelzi a a [összesítő jelentés kiépítés](check-status-user-account-provisioning.md), és ha az Azure Portalon konfigurált értesítő e-mailen keresztül. 

Karanténba helyezés, a növekményes szinkronizálás gyakorisága naponta egyszer fokozatosan csökken. 

Az üzembe helyezési feladat eltávolítja a karanténból után az összes folyamatban a hibát okozó hibákat, és a következő szinkronizálási ciklus elindul. Ha az üzembe helyezési feladat legfeljebb négy héttel karanténba helyezés marad, a létesítési feladat le van tiltva.


## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi ideig tart a felhasználók?

Teljesítmény függ attól a létesítési feladat egy kezdeti szinkronizálást és a egy növekményes szinkronizálást végez az előző szakaszban leírtak szerint.

A **a kezdeti szinkronizálás**, feladat idő számos tényezőtől, beleértve a felhasználók és csoportok a kiépítés hatókörébe számát és a felhasználókat és csoportokat a forrásrendszerben teljes száma függ. Kezdeti szinkronizálás teljesítményét befolyásoló tényezőket átfogó listája ebben a szakaszban később foglalja össze.

A **növekményes szinkronizálás**, a feladat ideje függ a szinkronizálási ciklus észlelhető módosítások számát. Ha 5000-nél több felhasználó vagy csoport tagsága módosul, a feladat be tudja belül egy növekményes szinkronizálási ciklust. 

A következő táblázat összefoglalja a szinkronizálás időpontjait általános üzembe helyezési forgatókönyvhöz. Ezekben az esetekben a forrásrendszerben Azure ad-ben pedig a célrendszeren egy SaaS-alkalmazáshoz. A szinkronizálási idő származik, a ServiceNow, a munkahelyen, a Salesforce és a Google Apps SaaS-alkalmazásokhoz tartozó szinkronizálási feladatokat statisztikai elemzése.


| Hatókör-konfiguráció | Felhasználók, csoportok és tagok hatókörében | Kezdeti szinkronizálás ideje | Növekményes szinkronizálás ideje |
| -------- | -------- | -------- | -------- |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |  < 1000 |  < 30 perc | < 30 perc |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |  1000–10 000 | 142 - 708 perc | < 30 perc |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |   10 000 - 100 000 | 1,170 - 2,340 perc | < 30 perc |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  < 1000 | < 30 perc  | < 30 perc |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  1000–10 000 | < 30 – 120 perc | < 30 perc |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  10 000 - 100 000  | 713 - 1,425 perc | < 30 perc |
| Összes felhasználók szinkronizálása az Azure ad-ben|  < 1000  | < 30 perc | < 30 perc |
| Összes felhasználók szinkronizálása az Azure ad-ben | 1000–10 000  | 43 - 86 perc | < 30 perc |


A konfiguráció **szinkronizálási rendelt felhasználók és csoportok csak**, a következő képletek segítségével meghatározhatja a hozzávetőleges minimális és maximális várt **a kezdeti szinkronizálás** időpontok:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Összegzés befejezéséhez szükséges idő befolyásoló tényezők egy **a kezdeti szinkronizálás**:

* Felhasználók és csoportok a kiépítés hatókörébe teljes száma

* A felhasználók, csoportok és a forrás rendszerben (Azure AD) csoport tagjai teljes száma

* E felhasználók kiépítés hatókörébe teljesül, a meglévő felhasználók számára a célalkalmazás vagy első alkalommal kell létrehozni. Szinkronizálási feladat, amelynek minden felhasználó jönnek létre az első alkalommal körülbelül igénybe *kétszer mindaddig* , feladatok, amelynek minden felhasználó egyeztetését a meglévő felhasználók szinkronizálása.

* Hibák száma a [auditnaplók](check-status-user-account-provisioning.md). Teljesítmény lassabb lesz, ha sok hiba, és az eszközkiépítési szolgáltatás karantén állapotba került 

* A sebességkorlátokat és a szabályozás, melyet a célrendszer megvalósított kérelmet. Néhány célrendszereket hajtja végre a kérést a sebességkorlátokat és a szabályozás, amely befolyásolhatja a teljesítményt nagy szinkronizálási műveletek során. Ezen feltételek mellett egy alkalmazást, amely megkapja a túl sok kérelem túl nagy lehet, hogy a válaszadási arányát lassú vagy bontja a kapcsolatot. A teljesítmény javítása érdekében az összekötőjét beállítása nem az alkalmazás kérések küldésével gyorsabb, mint az alkalmazás azokat fel tudná dolgozni. Üzembe helyezési összekötők a Microsoft által készített ellenőrizze ezt a módosítást. 

* A szám és hozzárendelt csoportok méretét. Hozzárendelt csoportok szinkronizálása, mint a felhasználók szinkronizálása hosszabb időt vesz igénybe. A szám és a méretét a hozzárendelt csoportok hatással lehet a teljesítményre. Ha egy alkalmazásnak [engedélyezve csoport objektum szinkronizálása a hozzárendelések](customize-application-attributes.md#editing-group-attribute-mappings), a csoport tulajdonságai, például a csoportneveket és csoporttagságok szinkronizált felhasználók mellett. Ezek további szinkronizálások csak a felhasználói objektumok szinkronizálása hosszabb vesz igénybe.


##<a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Hogyan állapítható meg, ha a felhasználók lettek létrehozva megfelelően?

Az Azure AD létesítési szolgáltatás felhasználó által végrehajtott összes műveletet rögzített naplók. Ez tartalmazza az összes olvasási és írási műveleteken végrehajtott a forrás- és rendszerek, valamint milyen felhasználói adatok volt írható és olvasható az egyes műveletek során.

Hogyan az olvasás a naplók az Azure Portalon a további információkért lásd: a [kiépítési jelentéskészítési útmutató](check-status-user-account-provisioning.md).


##<a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hogyan háríthatom el a problémákat a felhasználók átadásának?

Forgatókönyv-alapú hogyan háríthatók el a felhasználók automatikus átadása, tekintse át [konfigurálásával és a felhasználók alkalmazásban való létrehozásával kapcsolatos problémák](application-provisioning-config-problem.md).


##<a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Mik azok a felhasználók automatikus átadása bevezetéséről ajánlott eljárásai?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Egy példa részletes üzembe helyezés tervezése kimenő felhasználókiépítés egy alkalmazásba, lásd: a [identitás üzembe helyezési útmutató a felhasználók átadásának](https://aka.ms/userprovisioningdeploymentplan).

##<a name="more-frequently-asked-questions"></a>További gyakori kérdések

###<a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Nem a felhasználók automatikus átadása az SaaS-alkalmazásai működését a B2B-felhasználók Azure AD-ben?

Igen, akkor lehet a szolgáltatás kiépítése B2B (vagy vendég) felhasználók kiépítése az Azure AD SaaS-alkalmazásokhoz az Azure AD-felhasználó használata.

Azonban tudni bejelentkezni a SaaS-alkalmazás az Azure AD B2B-felhasználók, a SaaS-alkalmazás kell rendelkeznie a SAML-alapú egyszeri bejelentkezési képesség a meghatározott módon konfigurálva. B2B-felhasználók történő bejelentkezések támogatásához SaaS-alkalmazások konfigurálásáról további információkért lásd: [konfigurálása SaaS-alkalmazások B2B-együttműködés]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

###<a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Nem a felhasználók automatikus átadása SaaS-alkalmazások munkahelyi dinamikus csoportok az Azure AD-ben?

Igen. Ha "szinkronizálás csak a hozzárendelt felhasználók és csoportok" konfigurálva, a kiszolgáló üzembe helyezése az Azure AD felhasználói építhető vagy megszüntetni hozzárendeléseket felhasználók SaaS-alkalmazásban alapul-e a tagjai egy [dinamikus csoport](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule]). Dinamikus csoportok együttműködnek a "minden felhasználó és csoport szinkronizálása" lehetőséget.

Dinamikus csoportok használatáért azonban befolyásolhatja a SaaS-alkalmazásokhoz való üzembe helyezést, az Azure AD-ből teljes körű felhasználói általános teljesítményét. Dinamikus csoportok használata esetén tartsa az alábbi korlátozásokkal és javaslatok szem előtt:

* Milyen gyorsan egy dinamikus csoport felhasználói kiépített vagy SaaS-alkalmazásokban – figyelmeztetés megszüntetésről függ, milyen gyors képes kiértékelni a dinamikus csoport tagsága módosul. A feldolgozás állapotának egy dinamikus csoport információkért lásd: [ellenőrizze a tagsági szabály feldolgozási állapotát](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule#check-processing-status-for-a-membership-rule).

* Dinamikus csoportok használatakor a szabályok alaposan meg kell az üzembe helyezést és megszüntetést szem előtt, felhasználó, a tagsági adatvesztést eredményez a megszüntetési esemény.

###<a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Nem a felhasználók automatikus átadása SaaS-alkalmazások munkahelyi beágyazott csoportok az Azure AD-ben?

Nem. "Szinkronizálás csak a hozzárendelt felhasználók és csoportok" konfigurálva, amikor a kiszolgáló üzembe helyezése az Azure AD felhasználói nem sikerül, olvasni, vagy a beágyazott csoportok lévő felhasználók kiépítése. A rendszer csak olvashatja, és konfigurálta a felhasználókat, amelyek az explicit módon hozzárendelt csoport közvetlen tagjai.

Ez az egyik korlátozása "alkalmazások hozzárendelések. csoport-alapú", amely szintén hatással van az egyszeri bejelentkezés, és leírt [csoport használatával SaaS-alkalmazásokhoz való hozzáférés kezelése](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/groups-saasapps ).

Áthidaló megoldásként, érdemes explicit módon hozzárendelése (vagy más módon [hatókörét a](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) a csoportokat, amelyek a felhasználókkal, akik szükség lesz.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [Attribútum-leképezéshez kifejezések írása](functions-for-customizing-application-data.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
* [Az Azure AD szinkronizálási API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
