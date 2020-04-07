---
title: Tanácsadási szolgáltatási ajánlat létrehozása a Partnerközpontban – Azure Piactér
description: Megtudhatja, hogyan tehet közzé tanácsadási szolgáltatási ajánlatot az Azure Marketplace-en vagy az AppSource-ban a PartnerCenter használatával.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ce8df36d3417417a5f70a5385aa94d9c8c7ff0cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674279"
---
# <a name="consulting-service-creation-overview"></a>Tanácsadási szolgáltatás létrehozása – áttekintés

> [!IMPORTANT]
> Tanácsadói szolgáltatási ajánlatainak kezelését áthelyezzük a Cloud Partner Portal-ról a Partner Centerre. Az ajánlatok áttelepítéséig kövesse az Azure és a [Dynamics 365 tanácsadási szolgáltatásajánlatának](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) utasításait a Cloud Partner Portal számára az ajánlatok kezeléséhez.

Ez a cikk azt ismerteti, hogy miként tehet közzé tanácsadási szolgáltatási ajánlatot az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) vagy az [AppSource-on.](https://appsource.microsoft.com/) A Microsoft [Dynamics 365](https://dynamics.microsoft.com/) és az AppSource Power Platform szolgáltatáson alapuló tanácsadási szolgáltatások listázása. A Microsoft Azure-on alapuló tanácsadási szolgáltatási ajánlatok at sorolja fel az Azure Marketplace-en.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piacon való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft márkanév használatával.
- Potenciálisan több mint 100 millió Office 365- és Dynamics 365-felhasználót érhet el az AppSource-on, és több mint 200 000 szervezetet az Azure Marketplace-en keresztül.
- Szerezzen kiváló minőségű érdeklődőket ezekről a piacterekről.
- A szolgáltatások népszerűsítése a Microsoft helyszíni és távértékesítési csapatai által

## <a name="requirements"></a>Követelmények

### <a name="business-requirements"></a>Üzleti követelmények

Azoknál az ajánlatoknál, ahol az Azure van kiválasztva elsődleges termékként, az ajánlatnak legalább az alábbi, teljes körűen szerzett kompetenciák egyikét fel kell sorolnia:

- Alkalmazásfejlesztés
- Alkalmazásintegráció
- Alkalmazáséletciklus-kezelés
- Felhőplatform
- Adatelemzés
- Adatközpont
- Adatplatform
- DevOps

Az elsődleges termékként kiválasztott alábbi lehetőségek valamelyikével rendelkező ajánlatok esetében meg kell felelnie a felsorolt jogosultsági követelményeknek, vagy együtt kell értékesítenie az elsődleges termékre vonatkozó ajánlatot, amelyhez a szolgáltatásajánlat kapcsolódik.

**Ügyfél-aktivitási alkalmazások**

- **A következőre vonatkozik:** Dynamics 365 Sales, Dynamics 365 Marketing, Dynamics 365 Customer Service, Dynamics 365 Field Service, Dynamics 365 Emberi Erőforrások

- **Feltételek**: Gold vagy Silver tanúsítvánnyal kell rendelkeznie a [Cloud Business Applications kompetenciában](https://partner.microsoft.com/membership/cloud-business-applications-competency) a Customer Engagement beállításhoz.

**Pénzügyi és üzemeltetési alkalmazások**

- **A következőkre vonatkozik:** Dynamics 365 Finance, Dynamics 365 Operations, Dynamics 365 Commerce, Dynamics 365 Emberi Erőforrások, Dynamics 365 Project Service Automation

- **Feltételek**: Az Egyesített műveletek hez a [Cloud Business Applications kompetencia](https://partner.microsoft.com/membership/cloud-business-applications-competency) minősítése arany vagy ezüst minősítéssel kell rendelkeznie.

**Dynamics 365 Customer Insights**

- **Feltételek**: A [Dynamics 365 Customer Insights](https://dynamics.microsoft.com/ai/customer-insights/) legalább egy sikeres, termelésen belül imitálta, legalább öt mértékkel és öt szegmenssel.

**Dynamics 365 Business Central**

- **Feltételek**: Gold vagy Silver tanúsítvánnyal kell rendelkeznie a [Vállalati erőforrás-tervezés kompetenciában,](https://partner.microsoft.com/membership/enterprise-resource-planning-competency) és legalább három ügyfelet kell kiszolgálnia, vagy közzé kell tennie egy Business Central alkalmazást a Microsoft AppSource alkalmazásban.

**Power BI**

- **Feltételek**: Szerepelnie kell a [Power BI-partnerkirakatban.](https://powerbi.microsoft.com/partner-showcase/)

**Power Apps**

- **Feltételek**: A [Power Apps partnerségi](https://aka.ms/PowerAppsPartner) programban jogosultnak kell lennie a speciális előnyökre.

Az előfeltételek teljesítéséről a [Consulting szolgáltatás előfeltételei](consulting-service-prerequisites.md)című témakörben talál részleteket.

### <a name="logistical-requirements"></a>Logisztikai követelmények

Ha tanácsadási szolgáltatási ajánlatot szeretne létrehozni az Azure Marketplace-en vagy az AppSource-tanácsadási szolgáltatásokban, először rendelkeznie kell [egy közzétevőfiókkal a Partnerközpontban,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)és fiókját be kell vonni a kereskedelmi piactér programba.

## <a name="create-a-new-consulting-service-offer-in-partner-center"></a>Új tanácsadási szolgáltatási ajánlat létrehozása a Partnerközpontban

A fent leírt követelmények teljesítése után kövesse az alábbi lépéseket egy tanácsadási szolgáltatási ajánlat létrehozásához.

1. Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com), majd válassza a felső menü **Irányítópult** parancsát.
2. A bal oldali navigációs sávon válassza a **Kereskedelmi piactér**lehetőséget, majd az **Áttekintés**lehetőséget.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="A kereskedelmi piactér menüjének szemléltetése":::

3. Válassza a **+ Új ajánlat**lehetőséget, majd a Consulting service **lehetőséget.**

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Új ajánlat létrehozásához a gombot mutatja be.":::

4. Adja meg **az ajánlatazonosítót.** Ez a fiókban lévő minden egyes ajánlat egyedi azonosítója.

    - Ez az azonosító látható az ügyfelek számára a piactéri ajánlat webcímében.
    - Csak kisbetűket, számokat, kötőjeleket és aláhúzásjeleket használjon, szóközöket azonban nem. A hossz legfeljebb 50 karakter ből állhat. Ha például beírja az **1-es tesztajánlatot,** az ajánlat URL-címe a . `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`
    - Az ajánlatazonosító&#39;nem módosítható, miután kiválasztotta a **Létrehozás lehetőséget.**

5. Adja meg **az ajánlat aliasát**. Ez a név a Partnerközpontban található ajánlatra vonatkozik.

    - Ez a név nem&#39;a piactéren. Ez&#39;eltér az ajánlat nevét és egyéb értékeket, amelyek megjelennek az ügyfelek számára. Ebben a mezőben olyan nevet rendelhet az ajánlathoz, amely hasznosabb az ajánlat belső azonosításához; nem jelenik meg az ügyfelek számára.
    - Az ajánlatalias&#39;nem módosítható a **Létrehozás (Létrehozás) választógombot.**

Miután megadta ezt a két értéket, válassza a **Létrehozás** gombot az **Ajánlat beállítási** lapján való folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

Miután megadta az ajánlatazonosítót és az ajánlataliast, a PartnerKözpont létrehoz egy ajánlattervezetet, és megjeleníti az **Ajánlat beállítási** oldalát. Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

Amikor az ajánlatot a Partnerközponttal teszi közzé a piactéren, csatlakoztatnia _kell_ azt egy ügyfélkapcsolat-kezelőhöz (CRM) vagy marketingautomatizálási rendszerhez. Ez lehetővé teszi, hogy megkapja az ügyfél elérhetőségi adatait, amint valaki érdeklődést mutat vagy használja a terméket.

1. Válassza a **Csatlakozás** lehetőséget, ha meg szeretné adni, hogy hová küldjük az ügyfélérdeklődőket. A Partnerközpont a következő rendszereket támogatja:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) az ügyfelek elköteleződése érdekében
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM-rendszer&#39;a fenti felsorolásban nem szerepel, az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy a [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) segítségével tárolja az ügyfél érdeklődői adatait, majd exportálja az adatokat a CRM-rendszerbe.

2. A Partnerközpontban történő közzétételkor csatlakoztassa az ajánlatot az érdeklődő célhelyéhez.
3. Ellenőrizze, hogy az érdeklődő célállomásához való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpontban,&#39;érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amíg megtekinti az ajánlat előnézeti verzióját, tesztelheti az érdeklődőkapcsolatot is, ha megpróbálja megvásárolni az ajánlatot az előzetes verzióban.
4. Győződjön meg arról, hogy az érdeklődő célállomásához való kapcsolat naprakész marad, hogy ne&#39;veszítse el az érdeklődőket.

Íme néhány további érdeklődőkezelési erőforrás:

- [Érdeklődőkezelés – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Érdeklődőkezelés – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Győződjön meg arról, hogy az előugró ablakok blokkolása ki van kapcsolva)

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszhoz, tulajdonságokhoz lépne.

### <a name="properties"></a>Tulajdonságok

Ezen az oldalon beállíthatja azt az elsődleges terméket, amelyet a tanácsadó szolgáltatás a legjobban kínál, beállíthatja a tanácsadási szolgáltatás típusát, és kiválaszthatja a megfelelő termékeket.

1. Válasszon egy **elsődleges terméket** a legördülő listából.
2. Válasszon **egy tanácsadó szolgáltatástípust** a legördülő listából:

    - **Értékelés** : Az ügyfél&#39;környezetének értékelése a megoldás alkalmazhatóságának meghatározásához, valamint a költségek és az időzítés becsléséhez.
    - **Eligazítás** : Bevezetés egy megoldásba vagy egy tanácsadási szolgáltatásba, amely keretrendszerek, demók és ügyfélpéldák használatával felkelti az ügyfelek érdeklődését.
    - **Megvalósítás** : Teljes körű telepítés, amely egy teljesen működő megoldást eredményez. Legfeljebb két hét alatt valósítható meg megoldások.
    - **A koncepció igazolása** : Korlátozott hatókörű megvalósítás annak megállapítására, hogy egy megoldás megfelel-e az ügyféligényeinek.
    - **Workshop** : Interaktív elkötelezettség, amelyet az ügyfél&#39;telephelyén. Ez magában foglalhatja a képzés, tájékoztatók, értékelések, vagy demók épül az ügyfél&#39;s adatok vagy környezet.

1. Ha az **Azure**elsődleges termékét választotta, legfeljebb három **megoldási területet**választhat ki. Ezek megkönnyítik az Azure Marketplace-en lévő ügyfelek számára az ajánlat megkeresését. Ha nem&#39;az Azure-t választotta, hagyja ki ezt a lépést.
2. Ha az Azure-tól _eltérő_ elsődleges terméket választott, legfeljebb három **alkalmazható terméket**választhat ki. Ezek megkönnyítik az AppSource ügyfelei számára az ajánlat megtalálását. További információt a Microsoft AppSource Consulting Service Listing Guidelines (PDF) [című témakörben talál.](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)
3. Legfeljebb hat iparágat választhat **ki,** amelyekre az ajánlatvonatkozik. Ez megkönnyíti az ügyfelek számára, hogy megtalálják az ajánlatot.
4. Legfeljebb három kompetenciát adhat hozzá, **amelyeket** a vállalata szerzett, hogy megjelenjen a tanácsadási szolgáltatási ajánlathirdetésén. Legalább egy kompetencia szükséges, kivéve az Azure Expert MSP&#39;s és az Azure Networking MSP&#39;s.

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszra lépne, az Ajánlat listaelem.

## <a name="offer-listing"></a>Ajánlat lista

Itt&#39;határozza meg a piactéren megjelenő ajánlat részleteit. Ez magában foglalja az ajánlat nevét, leírását, képeit és így tovább. Ügyeljen arra, hogy kövesse a [Microsoft&#39;szabályzati lapján](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) részletezett irányelveket az ajánlat konfigurálása kor.

> [!NOTE]
> Ajánlat részletei nem&#39;, hogy az angol, ha az &quot;ajánlat leírása kezdődik a mondat, Ez az alkalmazás csak akkor érhető el [nem angol nyelven]. &quot; Az is&#39;, hogy hasznos linket ad meg, hogy olyan nyelven kínáljon tartalmat, amely&#39;eltér az Ajánlat lista részleteiben használttól.

### <a name="name"></a>Név

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az **Ajánlat alias** mezőjében az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjeggyel védhető (és védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat).
- Nem lehet&#39;50 karakternél hosszúabb.
- Nem&#39;tartalmazhat emojikat.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez legfeljebb 100 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról, akár 3000 karakterig. Ez jelenik meg az ügyfelek számára a piactéri lista áttekintése.

A leírásban szerepeljen az alábbiak közül egy vagy több:

- Az ajánlat értékét és legfontosabb előnyeit
- Kategória vagy iparági szövetségek, vagy mindkettő
- Alkalmazáson belüli vásárlási lehetőségek
- Minden szükséges közzététel

Íme néhány tipp az írás a leírás:

- Egyértelműen írja le az ajánlat értékét a leírás első néhány mondatában. Adja meg a következő elemeket:
  - Az ajánlat leírása.
  - Az ajánlat előnyeit élvező felhasználó típusa.
  - Az ügyfél igényeinek megfelelően vagy az ajánlat címeinek kérdésén.
- Ne feledje, hogy az első néhány mondat megjelenhet a keresési eredmények között.
- Ne&#39;a termék értékesítéséhez szükséges funkciókra és funkciókra támaszkodjon. Ehelyett összpontosítson az ajánlat által nyújtott értékre.
- Próbálja meg használni az iparág-specifikus szókincs vagy előny-alapú megfogalmazás.

Ahhoz, hogy a leírás vonzóbbá, használja a Rich Text szerkesztő formázni a leírást. A Rich Text szerkesztő lehetővé teszi számok, felsorolásjelek, félkövér, dőlt betűk és behúzások hozzáadását, hogy a leírás olvashatóbb legyen.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="A Rich Text szerkesztőt mutatja be az ajánlat leírásának megírásához." border="false":::

### <a name="keywords"></a>Kulcsszavak

Legfeljebb három olyan keresési kulcsszót adjon meg, amelyek relevánsak az elsődleges termék- és tanácsadási szolgáltatás szempontjából. Ezek megkönnyítik az ajánlat megtalálását.

### <a name="duration"></a>Időtartam

Állítsa be az ügyféllel való elköteleződés várható időtartamát.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia az **elsődleges** és másodlagos kapcsolattartó nevét, e-mail címét és **telefonszámát.** Ez az információ nem&#39;jelenik meg az ügyfeleknek. A Microsoft rendelkezésére áll, és a Felhőszolgáltató (CSP) partnerei számára is elérhető.

### <a name="supporting-documents"></a>Igazoló dokumentumok

Legfeljebb három (de legalább egy) támogató PDF-dokumentum hozzáadása az ajánlathoz.

### <a name="marketplace-images"></a>Marketplace-képek

Adjon meg emblémákat és képeket az ajánlatához. Minden képnek .png formátumúnak kell lennie. Az elmosódott képek elutasításra kerülnek.

#### <a name="store-logos"></a>Áruházi emblémák

Adjon meg .png fájlokat az ajánlathoz&#39;s embléma az alábbi képpontméretekben:

- **Kicsi (48 x 48)**
- **Nagy (216 x 216)**

Minden embléma kötelező, és használják a különböző helyeken a piactér i.

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Adjon hozzá legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és .png formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár négy videót is hozzáadhatsz, amelyek bemutatják az ajánlatodat. Adja meg a videó&#39;nevét, annak webcímét (URL-címét) és a videó miniatűr .png képét 1280 x 720 képpont méretűen.

Válassza **a Vázlat mentése lehetőséget,** mielőtt folytatná a következő szakaszt, az Árak és az elérhetőség szakaszt.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Itt olyan elemeket határozhat meg, mint az árképzés, a piac és a személyes kulcs.

1. **Piac:** Állítsa be a piacon az ajánlat lesz elérhető. Ajánlatonként csak egy piacot választhat ki.
    1. Az Egyesült Államok vagy Kanada piacain válassza **az Államok szerkesztése** (vagy **tartományok)** lehetőséget, hogy megadja, hol lesz elérhető az ajánlat.
2. **Célközönség előnézete**: Konfigurálja az **elrejtési kulcsot,** amely az ajánlat privát közönségének beállításához használatos.
3. **Árképzés:** Adja meg, hogy az ajánlat **ingyenes** vagy **fizetős** ajánlat-e.

    > [!NOTE]
    > A tanácsadási szolgáltatás ajánlatai csak a hirdetésre szolgálnak. Minden tranzakció közvetlenül, a kereskedelmi piacon kívül történik.

4. Fizetős ajánlat esetén adja meg az Árat és a **pénznemet,** valamint azt, hogy az ár **Rögzített** vagy **Becsült.** Ha a Becsült érték, a leírásban meg kell adnia, hogy milyen tényezők befolyásolják az árat.
5. Válassza **a Vázlat mentése**lehetőséget.

## <a name="review-and-publish"></a>Véleményezés és közzététel

Miután elvégezte az ajánlat összes szükséges szakaszát, elküldheti az ajánlatot az ellenőrzésre és a közzétételre.

1. Ha készen áll a tanácsadási szolgáltatásajánlat közzétételére, kattintson az **Áttekintés gombra, és tegye közzé.**
2. Tekintse át a részleteket a végső benyújtási oldalon.
3. Ha szükséges, írjon egy megjegyzést a minősítő csapatnak, ha úgy gondolja, hogy az ajánlat bármely részletét magyarázatot igényel.
4. Ha készen áll, válassza a **Küldés lehetőséget.**
5. Az **Ajánlat áttekintése** lap bemutatja, hogy milyen közzétételi szakaszban van az ajánlat.

Ha többet szeretne tudni arról, hogy mennyi ideig várható az ajánlat az egyes közzétételi fázisokban, [olvassa el A Kereskedelmi piactér ajánlatközzétételi állapotának ellenőrzése című témakört.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)

## <a name="update-your-existing-consulting-service-offers"></a>Meglévő tanácsadási szolgáltatási ajánlatok frissítése

- [Meglévő ajánlat frissítése a kereskedelmi piacon](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
