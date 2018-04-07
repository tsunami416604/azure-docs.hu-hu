---
title: Az Azure-ban irányítás |} Microsoft Docs
description: További információk a felhőalapú számítási szolgáltatás, amely tartalmazza a számítási példányokért széles kijelölt & szolgáltatások, amely is fel-le automatikusan az alkalmazás vagy a vállalat igényeinek.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 04d9e6152c87a49a5f1b1b1a29c16d80de00f4e9
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="governance-in-azure"></a>Irányítás az Azure-ban

Tudjuk, hogy biztonsági-e a feladat a felhőben, és hogy mennyire fontos, hogy található-e az Azure biztonsági pontos és időben információt. A legjobb okai az alkalmazások és szolgáltatások Azure használandó, a széles köréről a biztonsági eszközök és képességek előnyeit. Ezekkel az eszközökkel és képességek segítségével történő biztonságos megoldásokat keres a biztonságos Azure platformon.

Jobb megértése érdekében a tömb, az irányítás segítséget az ügyfél és a Microsoft operations perspektívából, ez a cikk "Irányítás az Azure" Microsoft Azure-ban végrehajtott vezérlők adjon meg egy átfogó nézze meg a Cégirányítási írni a Microsoft Azure használatával elérhető szolgáltatások.

## <a name="azure-platform"></a>Azure-platform

Azure egy nyilvános felhőszolgáltatási platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles körű kijelölés. Linux-tárolók futtatható kikötõmunkások integrálása; alkalmazások, JavaScript, Python, .NET, PHP, Java és Node.js; build vissza-végpontok az iOS, Android és Windows eszközökhöz. Azure nyilvános felhőjében services támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Létrehozása vagy áttelepítése informatikai eszközök, egy nyilvános felhőszolgáltatóhoz meg az adott szervezet képességek az alkalmazások és a szolgáltatások és a vezérlők adatok védelme érdekében a megbízható függő biztosítanak a biztonság, a felhő alapú eszközök kezeléséhez.

Azure infrastruktúrája alkalmazások felhasználók millióit egyidejűleg üzemeltetéséhez a létesítmény a készült, és biztosít egy megbízható foundation, amelyre vállalatok megfelel a biztonsági követelményeknek. Emellett Azure biztosít sok biztonsági beállítások és képes azokat, hogy a szervezet központi telepítések egyedi követelményeinek biztonsági testre.

Ez a dokumentum segítenek megérteni, hogyan Azure irányítás képességek segítségével ezek a követelmények teljesítéséhez.

## <a name="abstract"></a>Absztrakt

Microsoft Azure felhőbe irányítás biztosít egy integrált naplózási és áttekintését, és hogy tanácsadást a szervezet az Azure platformon a felhasználási tanácsadási megközelítést. A Microsoft Azure felhőbe irányítás hivatkozik a döntéshozó folyamatainak, a feltételek és a házirendeket a tervezés, architektúra, beszerzési, telepítési, műveletet és egy felhőalapú felügyeleti részt számítástechnikai.

Megtervezheti a Microsoft Azure felhőbe irányításhoz, meg kell elfogadnia egy részletes nézze meg a személyeket, folyamatok és technológiák jelenleg helyen, majd kialakítható keretrendszerek, amelyek megkönnyítik az informatikai részleg következetesen támogatja az üzleti igények ugyanakkor biztosítható a rugalmasság a végfelhasználók számára a hatékony Microsoft Azure szolgáltatásainak használatára.

A dokumentum azt ismerteti, hogyan érhet el egy emelt szintű szintet, az irányítás az informatikai erőforrások a Microsoft Azure-ban. A dokumentum azt segítenek megérteni a építve a Microsoft Azure biztonsági és irányítási funkciókat.

A cégirányítási kérdéseket tárgyalja a dokumentum fő a következők:

- Házirendek, folyamatok és eljárások szerint szervezeti célok végrehajtására.

- Biztonság és a szervezet szabványoknak való megfelelés folyamatos

- Riasztások és figyelése

## <a name="implementation-of-policies-processes-and-procedures"></a>Házirendek, folyamatok és eljárások végrehajtása 

Felügyeleti létesített szerepkörök és felelősségek meghatározása az információk biztonsági házirendet, és a folyamatos működésének végrehajtásának felügyeletére Azure között. A Microsoft Azure felügyeleti biztonsági és a megfelelő csapat (többek között a következőket harmadik felek), és elősegítsék való megfelelés biztonsági házirendek, folyamatok és szabványok belül folytonossági eljárások felügyeletéért felelős.

Az alábbiakban a továbbfejlődtek tényezőket:

- Fiók kiépítése

- Előfizetés vezérlők

- Szerepkör alapján hozzáférés-vezérlést

- Erőforrás-kezelés

- Erőforrás-követés

- Kritikus fontosságú erőforrás-vezérlő

- API-hozzáférés az számlázási adatokat

- Hálózati vezérlő

## <a name="account-provisioning"></a>Fiók kiépítése

Fiók hierarchia meghatározása az Azure struktúra, és egy fő lépés szolgáltatások a vállalaton belül, és irányítási core szerkezete. A nagyvállalati szerződéssel rendelkező ügyfelek esetén az ügyfelek további tovább lehet részlegek, fiókok, és végül előfizetések a környezetben.

![Fiók kiépítése](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Ha nem rendelkezik nagyvállalati szerződéssel, érdemes lehet [Azure címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) hierarchia meghatározásához az előfizetés szintjén. Az Azure-előfizetésre a alapvető egysége, ahol minden erőforrás található. Egyúttal meghatározza, milyen több határokon belül Azure, például a magok, erőforrások stb. Előfizetések tartalmazhat [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), amely erőforrásai szerepelhetnek. [Az RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) alapelvek a ezen három szinten.

Minden vállalati más, és az Azure-címkék használata esetén nem vállalati ügyfelek a hierarchia lehetővé teszi, hogy hogyan vannak rendszerezve Azure a vállalaton belül jelentős rugalmasságot. A Microsoft Azure erőforrásait telepítés megkezdése előtt hierarchia modell és megismerheti a hatását a számlázás, erőforrás-hozzáférés és összetettségét.

## <a name="subscription-controls"></a>Előfizetés vezérlők

Előfizetési erőforrások használatának jelentett és számlázható hogyan szabályozza. Előfizetések a külön számlázási és a fizetési állíthatnak be. Említett korábbi egy Azure-fiókot, hogy több előfizetéssel is rendelkezhet. Előfizetések segítségével határozza meg a vállalat több részlegek az Azure erőforrás-használatát.

Például, ha a vállalat informatikai, HR és Marketing szervezeti és ezen osztályok futtató különböző projekt van. Például a virtuális gépek Azure-erőforrások használatának alapján minden részleg, ezek számlázható ennek megfelelően. Ez minden részleg a pénzügyi szabályozhatja azt.

Azure-előfizetések létrehozása három paramétert:

- egy egyedi előfizető azonosítója

- a számlázási helye

- Rendelkezésre álló erőforrások csoportja

Az egy adott, amely tartalmazhat egy Microsoft-fiókot azonosító, a hitelkártya száma és az Azure-szolgáltatások – teljes készlete bár Microsoft érvényesíti a felhasználás-korlátok, attól függően, hogy az előfizetés-típus.

Az Azure regisztrációs hierarchiák határozza meg, hogyan szolgáltatások felépítése nagyvállalati szerződéssel belül. A vállalati portál lehetővé teszi az ügyfelek felosztása nagyvállalati szerződéssel rugalmas hierarchiák egy szervezet egyedi igényeinek megfelelően testre szabható alapján társított Azure-erőforrások eléréséhez. A hierarchia mintát meg kell felelnie a szervezetek felügyeleti és földrajzi struktúra, hogy a társított számlázási és erőforrás-hozzáférési pontosan számítani lehet.

A három magas szintű minták: működési, üzleti egység és földrajzi, a részlegek, egy felügyeleti szerkezet fiók csoporthoz. Minden részleg fiókokhoz rendelhetők hozzá előfizetések, amelyek a számlázási és számos kulcsfontosságú korlátok silók létrehozása az Azure (például virtuális gépek számát storage-fiókok stb.).

![Előfizetés vezérlők](./media/governance-in-azure/security-governance-in-azure-fig2.png)


A nagyvállalati szerződéssel rendelkező szervezetek számára Azure-előfizetések hierarchiája egy negyedik szintű:

- Vállalati alkalmazásbeléptetési rendszergazda

- szervezeti egység rendszergazdája

- fiók tulajdonosának

- Szolgáltatásadminisztrátor

Ezt a hierarchiát szabályozza a következő:

- Számlázási kapcsolatban

- Felügyeleti fiók

- Szerepköralapú hozzáférés vezérlés (RBAC) összetevőihez

- Határok/korlátok

- Határok

  - Használati és elszámolási (díjszabási kártyát ajánlat számok alapján)

  - Korlátok

  - Virtual Network

- 1 AAD csatlakoztatott (1 AAD társítható sok előfizetések)

- Vállalati beléptetési fiók társított

## <a name="role-based-access-controls"></a>Szerepköralapú hozzáférés-vezérlést

Ha Azure jelent, hozzáférés-vezérlést a előfizetés voltak: rendszergazdának vagy társadminisztrátornak. A portál az erőforrások eléréséhez a Klasszikus modell hallgatólagos előfizetésre való hozzáférést. Ez részletesebb vezérlést nyújtanak a megfelelő hozzáférés-vezérlés az Azure-tagság a kereslet az olyan előfizetések elterjedése hiánya.

![Szerepköralapú hozzáférés-vezérlést](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Ez a előfizetések elterjedése már nem szükséges. Szerepköralapú hozzáférés-vezérlést felhasználókat rendelhet szabványos szerepkörök (például a szerepkörök közös "olvasó" és "író" típusok). Egyéni szerepkörök is meghatározhat.

[Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) lehetővé teszi a részletes hozzáféréskezelést az Azure-bA. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges. A vállalat biztonsági célú van szükségük a pontos engedélyeket ad az alkalmazottak kell összpontosítania. Túl sok engedélyek támadók fiókot fedheti fel. Túl kevés engedélyek jelenti azt, hogy az alkalmazottak nem munkavégzéséhez hatékony. Azure szerepköralapú hozzáférés-vezérlés (RBAC) segít a részletes hozzáféréskezelést az Azure felajánlásával oldja meg a problémát. Segít az RBAC elkülönítse a csapaton belüli feladatokat, és csak olyan mértékű hozzáférést biztosítania a felhasználók számára, hogy be kell elvégezni a munkájukat. Jogosultságot ad a Mindenki helyett korlátozás engedélyek az Ön Azure-előfizetés vagy erőforrásokhoz, engedélyezheti a csak bizonyos műveleteket.

Például, hogy egy előfizetésben található virtuális gépek kezeléséhez, miközben egy másik kezelheti az SQL-adatbázisok egyazon előfizetésen belül egy alkalmazott RBAC használatát.

Az Azure RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök:

- **Tulajdonos** minden erőforrást, beleértve a jogot, hogy mások számára delegálása teljes hozzáféréssel rendelkezik.

- **A közreműködői** is létrehozása és kezelése az Azure-erőforrások minden típusú, de nem tud hozzáférést biztosítani, mások számára.

- **Olvasó** megtekintheti a meglévő Azure-erőforrások.

A többi Azure RBAC szerepkört az adott Azure-erőforrások felügyelet lehetővé tételéhez. Például a virtuális gép közreműködői szerepkör lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozására és kezelésére. Azt nem betekintést biztosít a virtuális hálózat vagy az alhálózatot, amely a virtuális géphez csatlakozik.

[Beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) felsorolja azokat a szerepköröket, az Azure-ban érhető el. Azt adja meg az operatív és hatókörhöz, amely minden beépített szerepkörök a felhasználók számára.

Adjon hozzáférést a megfelelő RBAC szerepkört rendel a felhasználók, csoportok és alkalmazások egy adott hatókörben. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást. A szülő hatókörben szerepkörrel is benne a gyermekek számára engedélyezi a hozzáférést.

Például egy felhasználó, aki hozzáfér az erőforráscsoporthoz az összes erőforrást tartalmaz, például webhelyekhez, virtuális gépek és alhálózatok kezelheti.

Az Azure RBAC az Azure-erőforrások felügyeleti műveletek csak támogatja az Azure-portál és az Azure Resource Manager API-k. Nem lehet engedélyezni, az összes adat szintű műveletet Azure-erőforrások. Például engedélyezheti valaki Storage-fiókok kezeléséhez, de a BLOB vagy egy Tárfiókon belül táblák nem. Hasonlóképpen SQL-adatbázis kezelése, de nem szereplő táblák.

Ha további részleteket szeretne arról, hogy hogyan segít az RBAC a hozzáférések kezelésében, tekintse meg [a szerepköralapú hozzáférés-vezérlést](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) ismertető szakaszt.

Emellett [hozzon létre egy egyéni biztonsági szerepkört](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) a átruházásához hozzáférés-vezérlés (RBAC) Ha a beépített szerepkörök egyike sem felel meg a kívánt hozzáférés szükséges. Egyéni szerepkörök segítségével hozhatók létre [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [Azure parancssori felület (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli), és a [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Hasonlóan a beépített szerepkörök felhasználók, csoportok és alkalmazások előfizetés, az erőforráscsoport és az erőforrás hatókörök egyéni szerepkörök rendelhetők.

Előfizetésenként 2000 szerepkör-hozzárendelés osztható ki.

## <a name="resource-management"></a>Erőforrás-kezelés

Azure eredetileg csak a klasszikus üzembe helyezési modell tartalmaz. Ebben a modellben minden erőforrás már létezett a egymástól függetlenül; nem lehetett kapcsolódó erőforrások csoportosítása. Ehelyett kellett manuálisan nyomon követése a megoldás vagy az alkalmazáshoz készült erőforrásokat, és ne felejtse el összehangolt kezelheti őket.

A megoldás telepítéséhez kellett létrehozni az egyes erőforrások külön-külön az Azure portálon keresztül, vagy hozzon létre olyan parancsfájlt, amely a megfelelő sorrendben az erőforrások telepítése. Megoldás törlése, az egyes erőforrások törlése külön-külön kellett. Akkor lehetett könnyen nem vonatkoznak, és kapcsolódó erőforrások hozzáférés-vezérlési házirendek frissítése. Végül, akkor nem alkalmazható a következő címkék a erőforrásokhoz, hogy lássa el a feltételeket, amelyek segítenek az erőforrások figyelése és kezelése.

A 2014-re az Azure erőforrás-kezelő, amely hozzá, az erőforráscsoport fogalmát vezette be. Erőforráscsoport egy olyan tároló, amelyek egy közös életciklussal erőforrások. A Resource Manager üzembe helyezési modellel számos előnyt kínál:

- Ezt központi telepítése, kezelése, és figyelheti a megoldás a szolgáltatások csoportosan helyett külön-külön szolgáltatások ezek kezelése.

- Ismételten a teljes életciklus megoldás üzembe helyezése, és lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Az erőforráscsoportban összes erőforrásokhoz való hozzáférés-vezérlés is alkalmazhat, és ezek a házirendek automatikusan alkalmazza, amikor új erőforrásokat ad az erőforráscsoportot.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- JavaScript Object Notation (JSON) segítségével határozza meg az infrastruktúra megoldást. A JSON-fájlt egy Resource Manager-sablon néven ismert.

- Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

![Erőforrás-kezelés](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Erőforrás-kezelő lehetővé teszi, amelyre az erőforrások kezelése, számlázási vagy természetes affinitás jelentéssel bíró csoportokba. Amint azt korábban említettük, az Azure két üzembe helyezési modellel rendelkezik. A korábbi [Klasszikus modell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), a felügyeleti alapvető egysége lett az előfizetés. Előfizetés, amely nagy mennyiségű előfizetések létrehozásához vezetett erőforrások lebontva bonyolult volt. A Resource Manager modellt látott erőforráscsoportok bevezetése.

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. [Az erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szerepelhet a megoldás összes erőforrást, és csak azokat az erőforrásokat, amelyet szeretne kezelése csoportként. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

A sablonokra vonatkozó javaslatokat talál a [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai) című cikkben.

Az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. Ha egy erőforrás egy másik erőforráshoz tartozó értéket használ fel (például egy virtuális gép, amely egy tárfiókot igényel a lemezekhez), akkor beállíthat egy függőséget.

>[!Note]
>További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

A sablonokat az infrastruktúra frissítésére is használhatja. Hozzáadhat például egy erőforrást a megoldáshoz, valamint hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon egy erőforrás létrehozását határozza meg, de az adott erőforrás már létezik, az Azure Resource Manager az új eszköz létrehozása helyett frissítést végez. Az Azure Resource Manager frissíti a meglévő eszközt, hogy az állapota olyan legyen, mintha új lenne.

Erőforrás-kezelő bővítményeket biztosít olyan forgatókönyvek amikor további műveletek, például a Szoftvertelepítés nem szerepel a telepítő van szüksége.

## <a name="resource-tracking"></a>Erőforrás-követés

Mivel a szervezet felhasználói erőforrások hozzáadása az előfizetéshez, lesz egyre fontosabb rendelni az erőforrások a megfelelő részleg, az ügyfél és a környezet. Metaadatok csatolhat címkék erőforrásoknak. Használhat [címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) az erőforrás vagy a tulajdonos kapcsolatos adatok megadása. Címkék lehetővé teszik a nem csak összesítése és többféle módon erőforrások csoport, de az adatokat a jóváírási alkalmazásában.

Használjon címkéket, ha összetett erőforráscsoport- és erőforrás-gyűjteménnyel rendelkezik, és egyedi módon szeretné vizuálisan megjeleníteni az eszközöket. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak.

Címkék nélkül a szervezet felhasználói létrehozhatnak több olyan erőforrást is, amelyeket később nehéz lehet azonosítani és felügyelni. Például előfordulhat, hogy törölni kívánt projekt összes erőforrást. Ha ezeket az erőforrásokat a projekt nem címkével rendelkeznek, manuálisan találnia őket. A címkézés hasznos módja a felesleges költségek csökkentéséhez az előfizetésében.

Az erőforrásoknak nem kell megegyező erőforráscsoportban lenniük, hogy azonos címkével lássa el őket. Létrehozhat saját címkerendszert, hogy a szervezetben lévő összes felhasználó ugyanolyan címkéket használjon, hogy a felhasználók ne használjanak véletlenül némileg eltérő címkéket (például „részleg” helyett „részl”).

Erőforrás-házirendek lehetővé teszik a szervezet általános szabályok létrehozásához. Létrehozhat házirendeket, amelyek biztosítják az erőforrások vannak látva a megfelelő értékeket.

> [!Note]
> További információkért lásd: [számlázási címkéket házirend kezdeményezésére](../azure-policy/scripts/billing-tags-policy-init.md).

A címkézett erőforrásokat az Azure Portalon is megtekintheti.

Az előfizetéshez tartozó [használati jelentés](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) tartalmazza a címkék nevét és értékeit, így a címkék alapján részletezheti a költségeket.

> [!Note]
> Címkékkel kapcsolatos további információkért lásd: [számlázási címkéket házirend kezdeményezésére](../azure-policy/scripts/billing-tags-policy-init.md).

Az alábbi korlátozások érvényesek a címkékre:

- Minden erőforrás vagy erőforráscsoport legfeljebb 15 címke kulcs/érték párok rendelkezhet. Ez a korlátozás csak érvényes címkék közvetlenül alkalmazható legyen az erőforráscsoportot, vagy az erőforrás. Erőforráscsoport 15 címke kulcs/érték párok tartalmazó sok erőforrást tartalmazhat.

- A címke neve legfeljebb 512 karakter hosszúságú lehet.

- A címke értéke legfeljebb 256 karakter hosszúságú lehet.

- Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-karakterláncot a címke értékéhez. A JSON-karakterláncban tartalmazhat egyetlen címke kulcs alkalmazott sok értéket.

### <a name="tags-and-billing"></a>Címkék és a számlázás

Címkék lehetővé teszik az elszámolási adatok. Például, ha a másik szervezet számára több virtuális gép fut, a címkék használata csoport használatra költségközpont által. Használhatja a címkék a költségek kategorizálására által futtatókörnyezetben; például a számlázási használata termelési környezetben futó virtuális gépeket.

Információ a címkék keresztül lekérhesse a [Azure erőforrás-használat és RateCard API-k](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) vagy a használati vesszővel tagolt (CSV) fájl. Használat a fájl letöltése a [fiókok az Azure portál](https://account.windowsazure.com/) vagy [EA portal](https://ea.azure.com/).

>[!Note]
> További információ a számlázási adatokat programozott hozzáférést: [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). A REST API-műveleteket, lásd: [Azure számlázási REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Ha tölthetik le a fürt megosztott kötetei szolgáltatás használatát támogató szolgáltatások számlázási, a címkéket a címkék jelennek meg a címkék oszlop.

## <a name="critical-resource-controls"></a>Kritikus fontosságú erőforrás-vezérlők

A szervezet alapvető szolgáltatások ad hozzá az előfizetés, mert lesz egyre fontosabb annak érdekében, hogy ezek a szolgáltatások elérhetők-e üzleti megszűnésének megelőzése érdekében. [Erőforrás zárolások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) engedélyezi, hogy hol módosítsa vagy törölje azokat az alkalmazások és a felhőalapú infrastruktúra jelentős hatással van a jó minőségű erőforrásokon végrehajtott műveletek korlátozása. Zárolások alkalmazhat egy előfizetés, az erőforráscsoportot, vagy az erőforrás. Általában zárolások alkalmazása eligazodást erőforrások, például a virtuális hálózatok, az átjárók és a storage-fiókok.

Erőforrás zárolások jelenleg két értéket támogatja: CanNotDelete és csak olvasható. CanNotDelete azt jelenti, hogy a felhasználói (a megfelelő jogosultságokkal) is még olvasni vagy módosítani az erőforrást, de nem lehet törölni. Csak olvasható azt jelenti, hogy a jogosult felhasználók nem lehet törölni vagy módosítani az erőforrást.

Csak a fordul elő a felügyeleti vezérlősík, amely küldött műveletek műveletek érvényes erőforrás-kezelő zárolások <https://management.azure.com>. A zárolásokat korlátozza, hogy milyen erőforrások feladatokat a saját. Erőforrás módosítások korlátozott, de erőforrás műveletek nem korlátozottak. Például egy SQL-adatbázis csak olvasható zárolást megakadályozza az törölhessék vagy módosíthassák az adatbázist, de nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázisban.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel bizonyos műveleteket, amelyek az adatok, például olvasási műveletek további műveleteket igényelnek. Például, ha kialakít egy **ReadOnly** zárolását egy tárfiókot minden felhasználót megakadályoz a kulcsainak listázása. A listában kulcsok művelet segítségével egy POST kérést kezel, mert a visszaadott kulcsok érhetők el az írási műveletek.

![Kritikus fontosságú erőforrás-vezérlők](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Például egy App Service-erőforrást egy csak olvasható zárolási helyezi megakadályozza, hogy a Visual Studio Server Explorer megjelenítése az erőforrás fájlok, mert adott interakció írási hozzáféréssel kell rendelkeznie.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolás korlátozás alkalmazandó összes felhasználók és szerepkörök használhat. A felhasználók és szerepkörök engedélyeinek beállításával kapcsolatos további tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

A szülő hatókörben zárolást alkalmazásakor hatókörön belüli összes erőforrás öröklik az azonos zárolása. A későbbiekben még akkor is, erőforrások örökölt a zárolást. Az öröklés a legszigorúbb zár lép érvénybe.

Hozzon létre, vagy törölje a felügyeleti zárolás, Microsoft.Authorization/ hozzáféréssel kell rendelkeznie _vagy Microsoft.Authorization/locks/_ műveletek. A beépített szerepkörök, csak **tulajdonos** és **felhasználói hozzáférés adminisztrátora** kapnak a csatolási műveleteket.

## <a name="api-access-to-billing-information"></a>API-hozzáférés az számlázási adatokat

Azure számlázás API-k segítségével lekéréses használati és erőforrás adatokat be a kívánt adatok elemzésére szolgáló eszközöket. Az Azure erőforrás-használat és RateCard API-k segítségével pontosan előre jelezni, és a költségek kezelésére. Az API-kat használják, mint a egy erőforrás-szolgáltató és az Azure Resource Manager által közzétett API-kban család része.

### <a name="azure-resource-usage-api-preview"></a>Az Azure erőforrás-használat API (előzetes verzió)

Használja az Azure [erőforrás-használati API](https://msdn.microsoft.com/library/azure/mt219003) a becsült Azure felhasználási adatok eléréséhez. Az API-t tartalmazza:

- **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com/) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) adhatja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.

- **Óránkénti vagy a napi aggregáció** - hívóknak adhat meg, hogy azok szeretné-e az Azure használati adatokat óránkénti időszakok vagy napi időszakok. Az alapértelmezett érték a napi.

- **(Erőforrás-címkét tartalmaz) példány metaadatok** – például a teljesen minősített erőforrás uri példányszintű részletek beszerzése (/subscriptions/ {előfizetés-azonosító} /..), a erőforrásadatok csoport és az erőforráscímkék. A metaadatok segítségével deterministically és szoftveresen használati lefoglalni a címkék alapján a használati esetek, például határokon díjszabási.

- **Erőforrás-metaadatok** -erőforrás részletek, például a mérési nevét, a mérési kategória, a mérő az alkategória, az egység és a régió biztosítják a hívó szeretné jobban megismerni mi felhasznált. Erőforrás-metaadatok terminológia igazítása az Azure-portálon keresztül is dolgozunk Azure használati CSV, számlázási CSV, és más nyilvánosan elérhető lép adatok összefüggéseket elemek között, így EA.

- **Minden használati kínálnak típusok** – használati adatok érhető el az összes kínálnak, például a használatalapú fizetés, MSDN, pénzügyi kötelezettségvállalást, pénzügyi kreditet és EA.

**Azure-erőforrás RateCard API (előzetes verzió)**

Az Azure erőforrás RateCard API segítségével listájának elérhető Azure-erőforrások és az egyes becsült díjszabási információkat. Az API-t tartalmazza:

- **Szerepköralapú hozzáférés-vezérlés az Azure** – a hozzáférési házirendek konfigurálása az Azure portálon vagy Azure PowerShell-parancsmagok segítségével adja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az RateCard adatokat. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása az olvasó, a tulajdonos vagy a közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.

- **Használatalapú fizetés, az MSDN, a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet biztosít a (EA nem támogatott)** -Ez az API felület Azure ajánlat szintű arány információkat nyújt. Ez az API felület védőfalkezelőbe meg kell felelnie az ajánlat információkat az erőforrás részletek és sebességét. A rendszer jelenleg nem tudja EA díjszabás adja meg, mert EA ajánlatok testreszabott beléptetési mértékek. Az alábbiakban néhány lehetséges a használati és a RateCard API-k végrehajtott forgatókönyv:

- **A hónap során töltött Azure** - a használati kombinációját használja, és a hónap során töltött RateCard API-k, a felhő jobb betekintést eléréséhez. A használati és kell fizetni becslések óránkénti és napi gyűjtők elemezheti.

- **Riasztások beállítása** – a használati és a RateCard API-k segítségével becsült felhő használat és díjak és erőforrás-alapú vagy pénzügyi-alapú értesítések beállítása.

- **Számlázási előrejelzése** – Get a becsült felhasználás és a felhő kiadásokat, és gépi tanulási algoritmusok előre jelezni, mi a számlázási lenne az elszámolási időszak végén alkalmazni.

- **Elemzés előtti fogyasztás** – a RateCard API használatával megjósolható, hogy mekkora a számlázási okozna a várható használati a munkaterhelések az Azure-bA helyezi át. Ha meglévő alkalmazások más felhők vagy magánfelhőkben, az az Azure-ral használatának is leképezheti Azure jobb becslése beolvasandó díjszabás televíziózással töltenek. Ez a becslés lehetővé teszi a forgáspont kínálnak, és hasonlítsa össze a különböző típusú túl használatalapú fizetés, például a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet között. Az API-t is lehetővé teszi az költség különbségek régiónként lehetővé teszi, és lehetővé teszi egy lehetőségelemzések költség elemzés annak érdekében, hogy a telepítési döntések meghozatalában.

- **Elemzési** -akkor is határozzák meg, hogy egy másik régióban, vagy egy másik Azure-erőforrás-konfigurációban alkalmazásokat és szolgáltatásokat futtathatnak költséghatékonyabb. Azure-erőforrás költségek a használata az Azure-régió változhatnak.

- Azt is meghatározhatja, hogy ha egy másik Azure-ajánlat típusa a nagyobb mértékben nyújt egy Azure-erőforrás.

## <a name="networking-controls"></a>Hálózati vezérlő

Erőforrásokhoz való hozzáférést lehet (a vállalati hálózatból) belső vagy külső (internetes) keresztül. Is egyszerűen a szervezet felhasználói erőforrások véletlenül be a megfelelő hellyel, és potenciálisan nyissa meg ezeket a rosszindulatú hozzáférésektől. Például a helyszíni eszközök, a vállalatok hozzá kell adnia a megfelelő ellenőrzéssel győződjön meg arról, hogy Azure-felhasználók a megfelelő döntések /.

![Hálózati vezérlő](./media/governance-in-azure/security-governance-in-azure-fig6.png)

A előfizetés irányítás azt határozza meg, amely biztosítja a alapvető hozzáférés-vezérlést alapvető erőforrásai. Az alapvető erőforrások foglalják magukban:

### <a name="network-connectivity"></a>Hálózati kapcsolat

[Virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) alhálózatok tároló objektumok. Bár nem feltétlenül szükséges, gyakran használják, ha van alkalmazások belső vállalati erőforrásokhoz való csatlakozás. Az Azure Virtual Network szolgáltatás lehetővé teszi, hogy biztonságosan kapcsolódjanak a Azure-erőforrások egymástól a virtuális hálózatokon (Vnetek).

A virtuális hálózat a felhőben saját hálózati ábrázolása. Egy Vnetet az Azure-előfizetéshez dedikált felhő logikai elkülönítése. A helyszíni hálózat csatlakozhat Vnetek is.

Azure virtuális hálózatok képességei a következők:

- **Elkülönítési**: Vnetek el különítve egymástól. Létrehozhat külön Vnetek fejlesztési, tesztelési és éles, amelyek ugyanazon a CIDR címblokkokat használják. Ezzel szemben, amelyek különböző CIDR címblokkokat és összekapcsolhatja az hálózatok több Vnetek is létrehozhat. Egy VNet érdekében több alhálózatra is szegmentálhatja. Azure belső névfeloldást biztosít a virtuális gépek és Felhőszolgáltatások szerepkörpéldányokat egy Vnetet csatlakozik. Konfigurálhatja egy VNet és a saját DNS-kiszolgálók használata az Azure belső névfeloldást használata helyett.

- **Internetkapcsolat**: csatlakozik egy VNet minden Azure virtuális gépek (VM) és a felhőalapú szolgáltatások szerepkörpéldányokat alapértelmezés szerint rendelkezik Internet,-hozzáférés. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is.

- **Az Azure erőforrás-kapcsolat**: Azure-erőforrások, például a Felhőszolgáltatások és virtuális gépek ugyanazt a virtuális hálózatot lehet csatlakoztatni. Az erőforrások egymást magánhálózati IP-címek is csatlakozhat, ha külön alhálózatokon vannak. Azure biztosít alapértelmezett között alhálózatok, a Vnetek és a helyszíni hálózatokhoz, így nem kell konfigurálnia és felügyelnie az útvonalakat.

- **VNet-kapcsolatot**: Vnetek csatlakozhatnak egymáshoz, csatlakoznak bármely virtuális hálózat bármely más virtuális hálózaton erőforrás kommunikálni erőforrások engedélyezése.

- **A helyi kapcsolat**: Vnetek lehet csatlakoztatni a helyszíni hálózatokban magánhálózati kapcsolatok a hálózati és az Azure között, vagy egy pont-pont VPN-kapcsolaton keresztül az interneten keresztül.

- **Forgalomszűrést végez**: virtuális gép és a felhőalapú szolgáltatások szerepkör példányok hálózati forgalom bejövő és kimenő alapján szűrhetők forrás IP-cím és port, cél IP-cím és port és protokoll.

- **Útválasztás**: opcionálisan felülbírálhatja Azure alapértelmezett útválasztási saját útvonalak konfigurálása, vagy használja a BGP-útvonalakat hálózati átjárón keresztül.

## <a name="network-access-controls"></a>Hálózati hozzáférés-vezérlést

[Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) például egy tűzfal, és adjon meg szabályokat a hogyan erőforrás "működik" a hálózaton keresztül. Hogyan részletes szabályozását biztosítják / Ha egy alhálózat (vagy a virtuális gép) csatlakozni tud az internethez vagy más alhálózatok ugyanabban a virtuális hálózatban.

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC).

Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. A forgalom tovább korlátozható egy NGS-t virtuális géphez vagy hálózati adapterhez társításával.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Biztonság és a vállalati szabványoknak való megfelelés folyamatos

Minden üzleti különböző igények rendelkezik, és minden üzleti élvezzék rendszer által nyújtott megoldások különböző előnyeit. Továbbra is ügyfeleknek mindenfajta az ugyanazon alapvető kétségei vannak a felhőre történő. Szeretnék megőrizni az adatok feletti ellenőrzési, és szeretnék, hogy az adatok minden megőrzésével átláthatóság és megfelelőségi biztonságos és titkos, tárolni.

Mi az ügyfelek szeretné, hogy a szolgáltatók van:

- **Az adatok biztonságos** közben az adatok biztonsági és felügyeleti szabályozást igazolása, hogy a felhő képes biztosítani növekedett, informatikai vezetők továbbra is van szó, hogy a felhőben történő hagyja őket ki lehetnek téve a támadók a jelenlegi belső fejlesztésű megoldások.

- **Az adatok megőrzése** titkos felhőszolgáltatások előléptetése egyedi adatvédelmi kapcsolatban felmerülő kihívások vállalatok számára. Mivel a vállalatok keresse meg a felhőbe, az infrastruktúra használati díjait és javíthatják a nagyon hatékony eszközök, azokat is aggódni vezérelhető, hogy az adatok tárolására, ki fér hozzá, és felhasználásáról lekérdezi.

- **Vezérlő biztosítják** még akkor is, mivel azok a felhő telepítése további innovatív megoldások előnyeit, vállalatok nagyon fontos az adatok feletti ellenőrzési elveszíti tartoznak. A legutóbbi közzétételeket, állami intézményekhez el az ügyféladatokat, mind a jogi, mind a extra jogi azt jelenti, ellenőrizze a néhány igazgatók óvatos az adatok tárolása a felhőben.

- **Átláthatóságának elősegítése** biztonsági, adatvédelmi és vezérlő számára fontos üzleti döntéshozók, amíg azok is szeretnénk, függetlenül a módját a adatot tárol, és a biztonságos hozzáférés ellenőrzése.

- **Megfelelőség fenntartásában** vállalatok bontsa ki a felhőalapú technológiái által nyújtott használatát, az összetettségét és szabványok és köre tovább fejleszteni. A vállalatok kell arról, hogy teljesülnek a megfelelőségi követelményeket, és a megfelelőségi szabályzat módosítása adott idő alatt, változik.

## <a name="security-configuration-monitoring-and-alerting"></a>Biztonsági beállítások, figyelés és riasztás

Az Azure-előfizetők több eszközről kezelhetik felhőkörnyezeteiket, például felügyeleti munkaállomásokról, fejlesztői PC-kről, és olyan jogosult végfelhasználói eszközökről is, amelyek feladatspecifikus engedélyekkel rendelkeznek. Bizonyos esetekben a felügyeleti funkciók webalapú konzolok, például az Azure-portálon keresztül történik. Más esetekben az Azure-hoz való közvetlen kapcsolat létesíthető virtuális magánhálózatokon (VPN), terminálszolgáltatásokon, ügyfél-alkalmazásprotokollokon, vagy (szoftveresen) az Azure Service Management API-n (SMAPI) keresztül. Továbbá az ügyfél-végpontok lehetnek vagy tartományhoz csatlakoztatottak, vagy pedig elkülönítettek és felügyelet nélküliek, mint például a táblagépek vagy az okostelefonok.

A sokféle hozzáférési és kezelési képesség a lehetőségek széles tárházát biztosítja, ugyanakkor nagymértékű kockázatot is jelent a felhőkörnyezetek esetén. A felügyeleti műveletek kezelése, nyomon követése és naplózása nehézségekbe ütközhet. Ez a sokrétűség biztonsági fenyegetésekkel is járhat, mivel nem szabályozott hozzáférést tesz lehetővé a felhőszolgáltatások kezelésére használt ügyfélvégpontokhoz. Az általános vagy személyes munkaállomások fejlesztésre és infrastruktúra-kezelésre való használata olyan kiszámíthatatlan fenyegetési vektoroknak enged utat, mint például a webböngészés (pl. alapesetben megbízható weboldalak megfertőződése, ún. watering hole attack) vagy az e-mail (pl. pszichológiai manipuláció és adathalászat).

A megfigyelés és a naplózás biztosítják a felügyeleti tevékenység követésének és értelmezésének alapjait, de a keletkező adatmennyiség miatt nem mindig valósítható meg az összes művelet teljes részletességű naplózása. Ajánlott eljárás viszont a felügyeleti házirendek hatékonyságának naplózása.

Az Azure biztonsági irányítás az AD DS csoportházirend-objektumokban, szabályozhatja a rendszergazdák Windows felületeihez, mint például a fájlmegosztást. Terjessze ki a naplózási és megfigyelési folyamatokat a felügyeleti munkaállomásokra. Kövessen nyomon minden rendszergazdai és fejlesztői hozzáférést és tevékenységet.

### <a name="azure-security-center"></a>Azure Security Center

A [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) központi az előfizetések erőforrások biztonsági állapotát jeleníti meg, és ajánlásokat, amelyekkel megakadályozható a fertőzött erőforrásokat. Ez részletesebb házirendek (például fog alkalmazni házirendeket, amelyek lehetővé teszik a vállalat személyessé tétele érdekében azok állapotát a kockázat, azok címzési adott erőforráscsoportban) engedélyezéséhez.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

A Security Center itt integrált biztonsági monitorozást és kezelése az Azure-előfizetések között, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik. Miután engedélyezte a [biztonsági házirendek](https://docs.microsoft.com/azure/security-center/security-center-policies) az előfizetéshez tartozó erőforrásokra, a Security Center a potenciális biztonsági réseket azonosításához az erőforrások biztonsági elemzi. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők.

Az Azure Security Center ajánlott eljárás elemzési és biztonsági házirendek kezelése Azure-előfizetés belül az összes erőforrás kombinációját jelenti. A hatékony és könnyen használható eszköz lehetővé teszi, hogy biztonsági csoportok és kockázatkezelési tisztviselő megakadályozása, észlelését és válaszadás a biztonsági fenyegetések automatikusan gyűjti és elemzi az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó programok és tűzfalak biztonsági adatait.

Ezenkívül az Azure Security Center bővített analitikát alkalmaz, beleértve a gépi tanulási és viselkedési elemzési során, ami a Microsoft-termékek és a szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft biztonsági válasz Center (MSRC), a külső hírcsatornák globális fenyegetésfelderítési adataival. [Biztonsági irányítás](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) körben alkalmazza az előfizetés szintjén vagy maradt meg egyedi erőforrás-házirenddefiníción keresztül alkalmazott meghatározott, a részletes követelményeinek.

Végül az Azure Security Center erőforrás biztonsági állapota ezek a házirendek alapján elemzi, és használja ahhoz, hogy adja meg a osztályon irányítópultok és a riasztás esemény, például a kártevő-észlelési vagy kártékony IP-kapcsolatot kísérletek.

>[!Note]
> A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

A Security Center adatokat gyűjti össze a virtuális gépek felmérheti a biztonsági állapotát, adja meg a biztonsági javaslatok és riasztásokat fenyegetéseket. A Security Center első megnyitásakor adatgyűjtés engedélyezett az előfizetésében szereplő összes virtuális gépet. Adatgyűjtés ajánlott azonban akkor is lemondáshoz által [adatok gyűjtésének letiltása](https://docs.microsoft.com/azure/security-center/security-center-faq) a Security Center-házirendben.

Végül az Azure Security Center, amely lehetővé teszi a Microsoft-partnerek és a független szoftvergyártók létrehozása az Azure Security Center javítása érdekében a képességei keresztül csatlakozó szoftver nyílt platformon.

Az Azure Security Center figyeli a következő Azure-erőforrások:

- Virtuális gépek (VM) (beleértve a Cloud Services)

- Azure virtuális hálózatok

- Azure SQL service

- Webalkalmazási tűzfal virtuális gépeken és a például az Azure-előfizetésében integrált partnermegoldások [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="log-analytics"></a>Log Analytics

A Naplóelemzési szoftverek fejlesztési és szolgáltatás csapat információ-biztonság és [cégirányítási program](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) támogatja az üzleti követelmények és részben ismertetett módon törvény és szabályozás megfelelő [Microsoft Azure megbízhatóság Center](https://azure.microsoft.com/support/trust-center/) és [Microsoft adatvédelmi központ megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hogyan Naplóelemzési biztonsági követelmények meghatározásához, biztonsági vezérlők azonosítja, kezeli, és figyeli a kockázatok is létezik ismerteti. Évente, azt felülvizsgálati házirendeket, szabványokat, eljárásokra és útmutatást.

Minden Naplóelemzési fejlesztési csoport egy tagja megkapja a formális alkalmazás biztonsági képzés. A verziókezelő rendszer belsőleg, szoftverek fejlesztésére használjuk. Minden szoftver projekt védik a rendszerhez.

A Microsoft hogyan felügyeli, és értékeli az összes szolgáltatás a Microsoft biztonsági és megfelelőségi csoport rendelkezik. Biztonsági informatikusok jött létre a team, és nincsenek Naplóelemzési fejlesztése mérnöki osztályai társítva. A biztonsági tisztviselő saját felügyeleti lánc rendelkezik, és végezze el a termékek és szolgáltatások biztosításához, biztonsági és megfelelőségi független értékeléseket.

Azure szolgáltatások elindítása a felhőben lévő tervezett gyűjteményét tartalmazza. Ahelyett, hogy telepíti, és a helyi erőforrások kezelése, ezek az összetevők teljes mértékben az Azure-ban üzemeltetett. Minimális konfigurációt igényelnek, és akár percek alatt használatba vehetők.

![Az Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Csak, mert a felhőben futtatható szolgáltatások Naplóelemzési nem jelenti azt, hogy azokat ténylegesen nem kezelheti a helyszíni környezetben.

Az ügynök helyezhető el a Windows vagy az adatközpont, és a Linux-számítógép adatokat küld Naplóelemzési, ahol elemzése a felhőből vagy a helyi szolgáltatások gyűjtött összes egyéb adatokkal együtt. Azure biztonsági mentési és az Azure Site Recovery segítségével kihasználhatja a biztonsági mentési és magas rendelkezésre álláshoz tartozó a helyi erőforrásokkal a felhőben.

A felhőben futó runbookok általában nem férnek hozzá a helyszíni erőforrásokhoz, ha azonban egy vagy több gépre ügynököt telepít, az ügynökök futtathatják a runbookokat az adatközpontban. Amikor elindít egy runbookot, csak meg kell adnia, hogy a felhőben vagy a helyi feldolgozón szeretné futtatni azt.

Naplóelemzési legfontosabb funkcióit biztosítja az Azure-ban futó szolgáltatások készlete. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

![Az Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure-művelet manager bővíti a Funkciók, adja meg a felügyeleti megoldásokra. [Megoldások](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) logika előre csomagolt csoportja egy egy vagy több felügyeleti szolgáltatást, ami felügyeleti forgatókönyv megvalósításához.

![Azure-művelet kezelése](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Más megoldások érhetők el, a Microsoft és a partnerek, hogy könnyen adhat az Azure-előfizetéshez Naplóelemzési befektetéseit értékének növelése érdekében.

Partnerként a saját megoldások támogatják az alkalmazások és szolgáltatások és azok a felhasználók számára az Azure piactér vagy a gyors üzembe helyezési sablonokat is létrehozhat.

## <a name="performance-alerting-and-monitoring"></a>Teljesítményriasztások és figyelése

### <a name="alerting"></a>Riasztások kezelése

A riasztások jelezhetik az Azure-erőforrás metrikáit, eseményeket és naplók figyelésére szolgáló módszer, és értesítést kapjanak, ha a megadott feltétel teljesül.

**Riasztások a különböző Azure-szolgáltatások**

Különböző szolgáltatások, beleértve a különböző riasztások érhetők el:

- Az Application Insights: Lehetővé teszi a webalkalmazás-teszt és metrika riasztásokat.

>[!Note]
> Lásd: [riasztások beállítása a Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) és [figyelése rendelkezésre állásának és reakcióidőt, a webhely](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Naplóelemzési: Lehetővé teszi, hogy a tevékenység és a diagnosztikai naplók szolgáltatáshoz útválasztását, és lehetővé teszi, hogy a metrika, naplózási és más riasztástípusok ettől.

>[!Note]
> További információkért lásd: a riasztások [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Az Azure figyelő: Lehetővé teszi, hogy a riasztások metrika értékek és a tevékenység alkalmazásnapló-események alapján. Használhatja a [Azure figyelő REST API](https://msdn.microsoft.com/library/dn931943.aspx) riasztások kezelése.

>[!Note]
> További információkért lásd: [riasztások létrehozása az Azure-portálon, a PowerShell vagy a parancssori felület használatával](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Figyelés

A cloud app teljesítményével kapcsolatos problémákat hatással lehet az üzleti. A több összekapcsolt összetevőkkel és gyakori kiadásokban degradations bármikor fordulhat elő. És ha az alkalmazást, a felhasználók általában felderítése a tesztjei során nem talált problémákat. Meg kell ezekről a problémákról értesülnek azonnal, eszközöket, és diagnosztizálására és a problémák elhárításához. A Microsoft Azure tartománnyal rendelkező eszközök a problémák azonosításához.

**Hogyan figyelhetek az Azure felhőalapú alkalmazásokat?**

Nincs számos eszközt az Azure-alkalmazások és szolgáltatások figyelését. A funkciók némelyike átfedésben vannak. Ez egy korábbi okokból részben és részben miatt a fellazítja a fejlesztési és a művelet az alkalmazások között.

Az alábbiakban a fő eszközöket:

- **Az Azure figyelő** alapvető eszköz Azure-on futó szolgáltatások figyelése. Biztosít egy szolgáltatás és a környezet átviteli infrastruktúra szintű adatait. Kezeli az alkalmazások az összes Azure-ban, ha eldöntötte, hogy a méretezési felfelé vagy lefelé erőforrásokat, majd Azure a figyelő lehetővé teszi az szeretne használni.

- **Az Application Insights** fejlesztési és egy üzemi felügyeleti megoldás is használható. Működését tekintve a csomag telepítése az alkalmazásba, és így lehetővé teszi több belső nézetét jelenít meg. Az adatok közé tartoznak a függőségi, kivétel nyomkövetési adatok, pillanatképeket, végrehajtási profilok hibakeresés válaszidejét. Intelligens hatékony eszközöket biztosít minden a telemetriai adat segítséget az alkalmazás hibakeresését és segít megérteni a felhasználók tevékenységeit vele elemzése. Beállíthatja, hogy egy csúcs az igények válaszidők van valami miatt egy alkalmazást, illetve az egyes külső resourcing probléma. Ha használja a Visual Studio és az alkalmazás hibás, akkor használható-e megfelelő a probléma sor(ok) kód, hogy kijavíthassuk.

- **Naplófájl Analytics** rendelkező kell állítani a teljesítményt és a karbantartási terv éles környezetben futó alkalmazások van. Az Azure-ban alapul. Gyűjti, és számos más forrásból, bár a 10-15 percig várnia összesíti. Körű informatikai felügyeleti megoldást nyújt az Azure-ba, a helyszíni és a külső felhőalapú infrastruktúra (például az Amazon Web Services). Adatok elemzése több forrás gazdagabb eszközöket lehetővé teszi összetett lekérdezések összes naplófájlt, és a megadott feltételek proaktív riasztást küldhet biztosít. Egyéni adatok azokat a központi tárházához lekérdezhetik és megjelenítheti azt, még akkor is gyűjtheti.

- **A System Center Operations Manager (SCOM)** van, felügyelheti és figyelheti a nagy felhőalapú telepítések. Valószínűleg már tisztában van, a felügyeleti eszköz a helyi Windows Server és Hyper-V alapú-felhők, de is integrálása és az Azure-alkalmazások kezelése. Többek között az Application Insights telepíthet a meglévő élő alkalmazásokra. Ha egy alkalmazás leáll, láthatja, másodpercben.


## <a name="next-steps"></a>További lépések

- [Ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Azure-előfizetés irányítás implementációi](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples).

- [A Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).
