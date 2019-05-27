---
title: Hozzon létre egy új SaaS-ajánlat a kereskedelmi Marketplace-en
description: Hogyan hozhat létre új szoftverhasználat-szoftverszolgáltatások (SaaS) ajánlatunk listázása vagy az Azure piactéren, az appsource-ban, vagy a Cloud Solution Provider (CSP) program kereskedelmi Marketplace portál használatával a Microsoft Partner Centeren keresztül.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 30e547759cbafabf5f762f671b09b0cbeea3a44c
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851221"
---
# <a name="create-a-new-saas-offer"></a>Hozzon létre egy új SaaS-ajánlat

Szoftver létrehozása, mint a szoftverszolgáltatások (SaaS) kínál első lépésként győződjön meg arról, amikor első [Partnerközpont-fiókkal létrehozása](./create-account.md) , és nyissa meg a [kereskedelmi Marketplace irányítópultjának](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), az a **kínál** lapja van kiválasztva. 

![A Partner Center kereskedelmi Marketplace irányítópultjának](./media/commercial-marketplace-offers.png)

Válassza ki a + **hozzon létre egy új...** gombra, majd válassza a **szolgáltatott szoftver** menüpontot. 

Ha más ajánlat típusok egyikét választja, átirányítjuk a korábbi [Cloud Partner Portalon](https://cloudpartner.azure.com/).  Csak SaaS-ajánlatok érhetők el a kereskedelmi Marketplace portálon, a Partner Center most. 

![A Partner Center ajánlat időszak létrehozása](./media/new-offer.png)


A **új ajánlat** párbeszédpanel jelenik meg. ![Új ajánlat párbeszédpanel](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Ajánlat azonosítója és neve

- **Ajánlat azonosítója**: Hozzon létre egy egyedi azonosítója – az egyes ajánlatok a fiókjában. Ez az azonosító az URL-címét, a Piactéri ajánlat és az Azure Resource Manager-sablonok (ha van) az ügyfelek számára látható lesz. Ajánlat azonosítója kisbetűket, alfanumerikus (beleértve a kötőjeleket és aláhúzásjeleket tartalmazhat, de nincs szóköz) kell lennie. Ez legfeljebb 50 karakter hosszúságú lehet, és nem lehet frissíteni, miután kiválasztotta létrehozása.  
Példa: test-ajánlat – 1.
<br>Az URL-cím eredményez: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat neve**: Az SaaS alkalmazás ajánlatot, konzisztens kiadványok, hirdetések és a webhelyek hivatalos neve.  Ez a név lehet, hogy lehet trademarked.  Ajánlat neve nem tartalmazhat szóközt, hangulatjelek (kivéve, ha azok a védjegy vagy a szerzői jog szimbólum), és legfeljebb 50 karakter lehet.
<br>Példa: 1. teszt ajánlat&#8482;

Kattintson a **Létrehozás** gombra.  Egy **ajánlat áttekintése** lap jön létre ezt az ajánlatot.  

![A Partner Center ajánlat áttekintése](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Az ajánlat áttekintése

A **ajánlat áttekintése** oldal tartalmazza: 

- A **közzétételi állapota** közzétenni ezt az ajánlatot, és mennyi ideig minden egyes lépést tart végrehajtásához szükséges lépéseket vizuális ábrázolását jeleníti meg. Hiányos közzétételi. lépés-ikonokat szürkén jelennek meg. 

- A **ajánlat áttekintése** menü műveleteket hajt végre ezt az ajánlatot a hivatkozások listáját tartalmazza. Ezen a listán szereplő műveletek az ajánlatban beállítástól függően változik.  
    - Ha az ajánlat vázlat – törlés vázlat 
    - Az ajánlat élő – Ha Stop értékesíteni ajánlat 
    - Ha az ajánlat előzetes verzióban érhető el – éles 
    - Ha még nem hajtotta közzétevő jelentkezzen ki – Közzététel megszakítása

## <a name="offer-setup"></a>Az ajánlat beállítása

A **telepítő ajánlat** lapon a következő információkat kéri. Válassza ki **mentése** ezek a mezők befejezése után.

- **Szeretne Microsoft keresztül?** (Igen/nem)
    - **Igen**, szeretné, hogy az ajánlat keretében a Microsoft az Ön nevében; a piactéren tranzakciót üzemeltető Microsoft- vagy 
    - **Nem**, inkább csak az ajánlat keretében a piacterein listázásához függetlenül Microsoft pénzügyi tranzakciók feldolgozását.    

### <a name="sell-through-microsoft"></a>Értékesítsen rajuk keresztül a Microsoft

Keresztül a Microsoft értékesítési biztosít jobb ügyfél-felderítési és -beállítás lehetővé teszi a Microsoft a gazdagép marketplace tranzakciók az Ön nevében, és kihasználja a Microsoft globálisan elérhető kereskedelmi funkcióit.

#### <a name="saas-offer-requirements"></a>SaaS-ajánlat követelmények

Annak érdekében, hogy a szoftver listában, mint a szoftverszolgáltatások (SaaS) kínál a kereskedelmi Piactéri a Partner Center, a következő feltételeknek kell teljesülniük:

- Az ajánlat az Azure-ügyfelek kompatibilisnek kell lennie. (Gyakran SaaS-alkalmazásokat is a legjobb teljesítmény és kompatibilitási üzemeltetett Azure-ban, de ez nem kötelező.) 
- Az ajánlat kell használnia [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) az identitáskezeléshez és hitelesítéshez.
- Az ajánlat kell használnia [SaaS teljesítése API-k](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) integrálása az Azure piactéren.

#### <a name="billing-infrastructure-costs"></a>A számlázás az infrastruktúrára fordítandó költségeket.
SaaS-ajánlatok esetében, a közzétevőként figyelembe kell venni az Azure-infrastruktúra használati díjak és szoftver-licencelési díjak egyetlen költség elemként. A költségek jelenik meg az ügyfél havi fix keretösszegek előtt. Azure-infrastruktúra használati felügyelt, és közvetlenül Önnek, a partner számlázzuk. Az ügyfél nem fogja látni a tényleges infrastruktúra használati díjak. A kiadók általában a szoftver licenc díjszabása Azure-infrastruktúra használati díjak kötegeléséhez tilthatók le. 

Szoftver-licencelési díjat a havi, ismétlődő hely alapú előfizetés átalánydíj jelennek meg, és nem díjköteles, vagy használatalapú.

|**A licenc költsége**|**havonta 100 USD**|
|:---|:---|
|Azure-használat költség (D1/1-mag)|Közvetlenül a közzétevő, az ügyfél nem számítunk fel|
|A Microsoft számlázzuk|100,00 $ / hó (közzétevő az licencdíját felmerült vagy csatlakoztatott infrastruktúra költségeket kell vennie)|

- Ebben a forgatókönyvben a Microsoft keresztül számláz az adott 100,00 dollár a szoftver licencét, és a közzétevőhöz $80,00 ki fizet.
- Partnereinkről, akik rendelkeznek a a **Piactéri szolgáltatási díj csökkentett** május 2019 június 2020-ig kínál a SaaS-csökkentett tranzakciós díj fog megjelenni. Ebben a forgatókönyvben a Microsoft keresztül számláz az adott 100,00 dollár a szoftver licencét, és a közzétevőhöz $90.00 ki fizet.

> [!NOTE]
> **Piactéri szolgáltatási díj csökkentett**: Az egyes SaaS kínál, hogy a kereskedelmi Marketplace-en közzétett, Microsoft csökkenti a Piactéri szolgáltatási díj a 20 %-os (a Microsoft kiadói szerződésében leírt) 10 %-át. Ahhoz, hogy az ajánlat ahhoz az ajánlatok legalább egyikének kell jelölt Microsoft IP közös értékesítési kész vagy a közös értékesítési IP előnyt élvez.  Jogosult-e hónapban a csökkentett Piactéri szolgáltatási díj fogadásához legalább öt (5) üzleti nappal minden naptári hónap vége előtt kell teljesülnie.  A csökkentett Piactéri szolgáltatási díj nem vonatkozik a virtuális gépekhez, felügyelt alkalmazásokban vagy a kereskedelmi piactéren keresztül elérhetővé tett bármilyen más termékekkel.  A csökkentett Piactéri szolgáltatási díj csak akkor érhető el licenc eredő díjakat kiszámlázzuk 2019. május 31-ig és 2020. június 30. között a Microsoft által gyűjtött minősített ajánlatokra.  Ezt követően a Piactéri szolgáltatási díj visszatér a szokásos összeg. 

|**A Microsoft számlák**|**havonta 100 USD**|
|:---|:---|
|A Microsoft fizet 80 %-licenc költségei <br>**Minősített SaaS-alkalmazások Microsoft fizet 90 %-licenc költségei*|80,00 $ / hó <br>*$*/ hónap * 90.00|


#### <a name="csp-program-opt-in"></a>CSP Program vehetnek részt
A [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program lehetővé teszi, hogy a szoftver ajánlatok minimális képzett Microsoft-ügyfelek milliói elérni marketing és értékesítés befektetése jelenti.

- **Csatornák: Az ajánlat elérhetővé a CSP programban** (jelölőnégyzet)

Az ajánlat elérhetővé a CSP programban feladatpéldányokból lehetővé teszi, hogy a Cloud Solution Providers, hogy a termék egy csomagolt megoldás részeként ügyfeleiknek. 

### <a name="list-through-microsoft"></a>Lista – Microsoft

Léptesse elő a Microsoft üzleti hozzon létre egy marketplace piactéren. Kiválasztása az ajánlat csak listázása és a Microsoft nem transact azt jelenti, hogy a Microsoft nem közvetlenül részt software license tranzakciók. Nincs társított tranzakció díja, és a közzétevő tartja bármely szoftver-licencelési díjak az ügyféltől összegyűjtött 100 %-ban. Azonban a kiadó felelős a szoftver licenc tranzakció, de nem kizárólagosan beleértve minden aspektusát támogatása: a megrendelés teljesítése, mérés, a számlázás, Számlázás, fizetési és gyűjtemény. 

- **Hogyan szeretne lehetséges ügyfeleink számára, hogy tőzsdei ajánlatunk?**

##### <a name="get-it-now-free"></a>Letöltés most (ingyenes)
Listázza az ügyfeleknek az ajánlat ingyenes azáltal, hogy egy érvényes URL-CÍMÉT (http vagy https előtaggal kezdődő) ahol elérhetik az alkalmazást.  Például:`https://contoso.com/saas-app`

##### <a name="free-trial"></a>Ingyenes próba
Az ajánlat az ingyenes próbaverziós alapon ügyfelek listázása azáltal, hogy egy érvényes URL-CÍMÉT (http vagy https előtaggal kezdődő) ahol elérhetik az alkalmazást.  Például:`https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Kapcsolatfelvétel
Csatlakozás a felhasználói kapcsolat felügyeleti (CRM) rendszer összegyűjtéséhez megszerzett elérhetőségi adatait. Az ügyfél az információk megosztása engedélyt kell adnia. Ezen ügyfél adatok az ajánlat neve, azonosító és az ajánlatot, találhatók marketplace forrás küld a CRM-rendszerrel, hogy megfelelően konfigurálta. A CRM konfigurálásával kapcsolatos további információkért lásd: [Connect lead felügyelet](#connect-lead-management). 

## <a name="enable-a-test-drive"></a>Egy teszt meghajtó engedélyezése

Próbálja ki az kiválóan alkalmas ad nekik a "Vásárlás előtt kipróbálható" lehetőséget, a megnövelt átalakítás és a magasan képzett Érdeklődők generálása az ajánlat potenciális vevők az bemutatására. [További információ a test-meghajtókat.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Próbálja ki az engedélyezni** (jelölőnégyzetet) 

Tesztverzió engedélyezésével kell adnia egy bemutató környezetben, próbálkozzon az ajánlatot egy meghatározott időtartamra vonatkozóan, hogy az ügyfelek konfigurálása. 

### <a name="type-of-test-drive"></a>Tesztverzió típusa

- **[Az Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: A központi telepítési sablont, amely tartalmazza az összes Azure-erőforrást a megoldást alkotó. Ebben a forgatókönyvben igazodó termékek csak Azure-erőforrások használatához.
- **[Dynamics 365 for Business központi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: A Microsoft üzemelteti, és a meghajtó Tesztszolgáltatás (beleértve a kiépítési és telepítési) fenntart egy központi üzleti vállalatierőforrás-Tervező a rendszer a (pénzügyi, a művelet, ellátási lánc, CRM, stb.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: A Microsoft üzemelteti, és fenntartja a meghajtó Tesztszolgáltatás (beleértve a kiépítési és telepítési) Customer Engagement rendszerhez (sales, service, projekt szolgáltatás, helyszíni szolgáltatás, stb.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: A Microsoft üzemelteti, és fenntartja a meghajtó Tesztszolgáltatás (beleértve a kiépítési és telepítési) a Finance and Operations vállalatierőforrás-tervezési (pénzügyi, műveletek, gyártási, ellátási lánc, stb.), a rendszer a. 
- **[Logikai alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: A központi telepítési sablont, amely magában foglalja az összes összetett megoldások létrehozására szolgáló architektúrák. Egyéni termékek Test Drive az ilyen típusú kell használnia.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: A személyre szabott irányítópult beágyazott hivatkozást. Egy interaktív Power BI vizuális kell használnia a Test Drive az ilyen típusú bemutatásához kívánt termékeket. Fel kell töltenie az itt csak a Power BI embedded URL-CÍMÉT.

#### <a name="additional-test-drive-resources"></a>További vizsgálat meghajtó-erőforrások
- [Test Drive műszaki ajánlott eljárások](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Próbálja ki a Marketing-ajánlott eljárások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztelje a meghajtó áttekintése egy Stránkování](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Csatlakozás a lead felügyelet

Csatlakozás az ügyfelek közvetlenül az ajánlati az ajánlatot a piacterein, és összekapcsolja a felhasználói kapcsolat felügyeleti (CRM) rendszert, úgy, hogy egy ügyfél érdeklődés viszonyra, illetve telepíti után közvetlenül megszerzett elérhetőségi adatait is megkapja a termék.

- **Válasszon egy érdeklődő célhelyet** (legördülő menü): Adja meg a CRM-rendszerrel, hova szeretné elküldeni a ügyfélérdeklődések kapcsolódási adatait. 

Partnerközpont lead felügyelet a következő CRM rendszereket támogatja. Válassza ki a hivatkozást a telepítési utasításokat.

- Az Azure Blob – adja meg kapcsolattartási e-mail címe, a tároló neve és a tárfiók kapcsolati sztringje. 
- [Az Azure Table](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – adja meg a kapcsolattartási e-mail és a tárfiók kapcsolati sztringje. 
- [A Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – adja meg a kapcsolattartási e-mail címe, URL-címet és hitelesítési mód (Office 365 vagy Azure Active Directory).
- [HTTPS-végpont](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – adja meg a kapcsolattartási e-mail- és HTTPS-végpont URL-címe. 
- [A Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – adja meg a kapcsolattartási e-mail címe, űrlap-azonosító, Munchkin Fiókazonosító és azonosítóját.
- [A Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) – adja meg a kapcsolattartási e-mail és a szervezet azonosítója. 

#### <a name="additional-lead-management-resources"></a>További érdeklődő management-erőforrásokkal
- [Érdeklődő felügyeleti – gyakori kérdések](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Gyakori érdeklődő konfigurációs hibák](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Áttekintés egy Stránkování felügyeleti vezethet](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ne felejtse el **mentése** mielőtt a következő szakaszban!

## <a name="properties"></a>Tulajdonságok
A **tulajdonságok** lapon kéri, hogy meghatározza a kategóriák és csoportosíthatók az ajánlat a piactér, a jogi szerződések, az ajánlat és az alkalmazás verziójának támogatása az iparágban. 

Válassza ki **mentése** ezek a mezők befejezése után. 

### <a name="category"></a>Category
Válasszon ki legalább az egyhez (1) és legfeljebb három (3) a megfelelő marketplace keresési területre az ajánlat csoportosításához használt kategóriák. Kérjük, hívja meg az ajánlat hogyan ezekben a kategóriákban támogatja az ajánlat leírása. 

### <a name="industry"></a>Iparág
Válassza ki a megfelelő marketplace keresési területre az ajánlat csoportosításához használt legfeljebb két (2) iparágak. Ha az ajánlat nem adott, az iparágban, ne válassza az egyik. Kérjük, hívja meg, hogyan az ajánlat az ajánlat leírása a kiválasztott iparágak támogatja. 

### <a name="app-version"></a>Az alkalmazás verziója
Ez az az AppSource-piactéren a verziószám az előfizetési csomag azonosításához használt választható mező. 

### <a name="standard-contract"></a>Standard szerződés
- **Általános szerződési használni?** 

Ügyfelei a Beszerzéselemzési megkönnyítik és szoftvergyártók jogi csökkenthető, a Microsoft kínál a szabványos szerződés sablon annak érdekében, hogy egy tranzakciónak a Marketplace-en megkönnyítése érdekében. 

Ahelyett, hogy elvégezte az egyéni használati feltételeket, Azure Marketplace-en a kiadók választhat a standard szintű szerződést, amelyet csak a vet, és fogadja el a egyszer kell ügyfelek alatt Szoftverüket. 

A standard szintű szerződés itt található: https://go.microsoft.com/fwlink/?linkid=2041178.

##### <a name="terms-of-use"></a>Használati feltételek
Ha a licencfeltételek különbözik a standard szintű szerződést, választhatja a saját jogi feltételeit itt adja meg. Ez a mező is megadhat a szöveg legfeljebb 10 000 karakternél. Ha a használati feltételek hosszabb leírást, adjon meg egy egyetlen URL-Címhivatkozás ebbe a mezőbe, ahol a további licencfeltételek található. Az ügyfelek számára egy aktív hivatkozásként jelenik meg.

Fogadja el ezeket a feltételeket, mielőtt az alkalmazás próbálkozhatnak ügyfelek szükségesek. 

Ne felejtse el **mentése** mielőtt a következő szakaszban!

## <a name="offer-listing"></a>Az ajánlat listázása
Az ajánlati információs lap megjeleníti a nyelv (és piacok) hol érhető el az ajánlatot, angol (Egyesült Államok) jelenleg az egyetlen rendelkezésre álló hely. Ezen a lapon emellett a nyelvspecifikus listázása és a dátum/idő adták állapotát jeleníti meg. Szüksége lesz a piactér-beli részleteit (ajánlat nevét, leírását, keresési kifejezéseket, stb.) meghatározására az egyes nyelvekhez / a piacra jutási időt.

### <a name="offer-listings"></a>Ajánlati információk
Adja meg a Marketplace-en, beleértve az ajánlat leírásának és marketing-eszközök megjeleníteni kívánt adatokat.

- **Név** (kötelező): Az itt megadott név a kiválasztott marketplace(s) az ajánlati címeként jelenik meg. A név előre feltöltve az előző alapján **új ajánlat** bejegyzés.  Ez előfordulhat, hogy trademarked.  Ez nem tartalmazhat szóközt, hangulatjelek (kivéve, ha azok a védjegy és szerzői jogi szimbólumok), és legfeljebb 50 karakter lehet.
- **Összefoglalás** (kötelező): Adja meg az Ön ajánlatát a Marketplace-en listaelem találatok használandó rövid leírását. Legfeljebb 100 karakter hosszú lehet a szöveg lehet megadni ebben a mezőben.
- **Leírás** (kötelező): Adja meg a Marketplace-en listaelem áttekintése megjeleníteni az előfizetési csomag leírását. Fontolja meg, beleértve egy értékajánlat, legfontosabb előnyökkel, kategória vagy iparág társításait, alkalmazáson belüli vásárlás lehetőségek, szükséges közzétételeket és a további hivatkozást.
Legfeljebb 3000 karakterek a szöveg lehet megadni ebben a mezőben. További tippek: a cikk [egy kiváló alkalmazást leírást](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Keresés kulcsszavak**: Adja meg legfeljebb három keresési kulcsszavakat, amellyel az ügyfelek az ajánlatot a marketplace(s) megkereséséhez.
- **Első lépések útmutató** (kötelező): Azt ismertetik, hogyan konfigurálja, és a lehetséges ügyfelek számára az alkalmazás használatának megkezdéséhez.  Ez a rövid útmutató részletes online dokumentációban mutató hivatkozásokat is tartalmazhat. Legfeljebb 3000 karakterek a szöveg lehet megadni ebben a mezőben. 

#### <a name="links"></a>Hivatkozások

- **Adatvédelmi szabályzat** (kötelező): A szervezet adatvédelmi házirendjének mutató hivatkozás. Felelőssége arról gondoskodni, hogy az alkalmazás megfelel-e az adatvédelmi törvény és előírás vonatkozik, és a egy érvényes adatvédelmi szabályzatot, amelyek biztosítják az
- **CSP Program olyan marketinganyagokat** (nem kötelező): Ha úgy dönt, hogy az ajánlat az kiterjesztése termékfényképek marketingcélú mutató hivatkozást kell adnia a [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) program. CSP az ajánlat az ügyfeleknek minősített szélesebb palettájához kiterjeszti a csomagot, piaci, és már viszonteladóként is értékesítheti az ajánlatot a CSP-partnerek engedélyezésével. Ezek a viszonteladók kell anyagok a marketing-ajánlat a hozzáférést. További információkért lásd: [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Hasznos hivatkozások** (nem kötelező): Az alkalmazás vagy biztosításával felsorolt kapcsolódó szolgáltatások választható kiegészítő online dokumentumok egy **cím** és **URL-cím**. Adjon hozzá további hasznos hivatkozásokat kattintva **+ URL-cím hozzáadása**.

#### <a name="contact-information"></a>Kapcsolattartási adatok

- **Névjegyek**: Minden egyes ügyfél-elérhetőségi, adjon meg egy alkalmazott **neve** , **telefonszám**, és **E-mail** címet.  (Ezek *nem lesz* nyilvánosan jelenik meg). A **Adresa URL** megadása is szükséges a **támogatási kapcsolattartó** csoport.  (Ez az információ *fog* nyilvánosan jelenik meg).

**Támogatási kapcsolat** (kötelező): Az általános támogatási kérdésekre.

**Kapcsolattartó mérnöki** (kötelező): Technikai kérdései vannak.

**Csatorna Manager forduljon** (kötelező): A CSP program kapcsolatos viszonteladói kérdéseket.

#### <a name="files-and-images"></a>Fájlok és képek

- **Dokumentumok** (kötelező): Adja hozzá az ajánlathoz kapcsolódó marketing dokumentumok legalább az egyhez (1), és legfeljebb három (3) az ajánlat dokumentumok, PDF formátumban.
- **Képek** (nem kötelező): Van több helyen, ahol az ajánlat embléma lemezképek is megjelenhet a marketplace(s), az alábbi méretek – kis igénylő: 48 x 48 képpont _(kötelező),_ Közepes: 90 x 90 pixeles, nagy: 216 x 216 képpont _(kötelező),_ széles: 255 x 115 képpont, és a Hero: 815 x 290 képpont. Az összes rendszerkép kell lennie. PNG formátumú.
- **Képernyőképek** (kötelező): Adja hozzá a képernyőképek bemutatásához az ajánlatot. Legfeljebb öt (5) képernyőképek vehetők fel, és kell méretezni, 1280 x 720 képpont. Az összes rendszerkép kell lennie. PNG formátumú.
- **Videók** (nem kötelező): Hivatkozások hozzáadása videókhoz bemutatásához az ajánlatot. YouTube-on és/vagy Vimeo videók, az ügyfelek számára láthatók és az ajánlat mutató hivatkozásokat is használhatja. Adja meg az asztalnak a miniatűrjére, a videó is kell méretezni PNG formátumú 1280 x 720 képpont. Legfeljebb négy videót ajánlat / jeleníthet meg.

Ne felejtse el **mentése** mielőtt a következő szakaszban!

#### <a name="additional-marketplace-listing-resources"></a>További marketplace listázott erőforrások

- [Ajánlott eljárások a Piactéri ajánlat listaelemek](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)



## <a name="preview"></a>Előnézet

A **előzetes** lap lehetővé teszi egy korlátozott definiálása **előzetes célközönség** kiadása az ajánlatot a piactéren szélesebb közönség számára az élő ajánlat közzététele előtt.

> [!IMPORTANT]
> Ki kell választania **élesben** előtt az ajánlat lesz közzétéve élő célközönség piactér nyilvános előzetes verzióban érhető el az ajánlatot ellenőrzése után.

- **Adja meg egy előzetes közönség számára: Egyetlen AAD/MSA fiók e-mailt hozzá minden sorában, és opcionális leírását.**

Manuálisan adja hozzá az e-mail címeket legfeljebb tíz (10), vagy élő erdőtopológia (20.), ha egy CSV-fájl feltöltése a meglévő Microsoft fiók (MSA) vagy az ajánlat közzététele előtt ellenőrzése az Azure Active Directory (AAD) fiókokba. Ezek a fiókok hozzáadásával definiálása egy adott célközönségnek engedélyezett előzetes hozzáférést az ajánlat a marketplace(s) való közzététel előtt. Ha már élő ajánlatát, továbbra is adhat meg egy előzetes célközönség tesztelési módosításaiért vagy frissítéseiért az ajánlatra.

> [!NOTE]
> Az előzetes verzió célközönség eltér egy privát közönség számára. Egy előzetes célközönség hozzáférhessen-e az ajánlat _előzetes_ élő közzé van téve a a piacterein való. Dönthet úgy is, hozzon létre egy csomagot, és csak egy privát közönség számára elérhető legyen. Az a **listaelem megtervezése** lapon megadhat egy privát célközönségét a a **Ez az egy privát terv** jelölőnégyzetet. Megadhatja, hogy egy privát célközönség használó Azure Bérlőazonosítók legfeljebb 20 000 ügyfelei.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **műszaki konfigurációs** lapon határozza meg a technikai részleteket (URL-cím, webhookot, bérlő azonosítója és alkalmazás azonosítója) az ajánlat csatlakozhat. Ez a kapcsolat lehetővé teszi számunkra megszerzésére, ha az ajánlat létrehozásához az ügyfél az Azure-előfizetés erőforrásként.

- **Kezdőlap URL-címe** (kötelező): Adja meg a webhely URL-címet, az ügyfelek felé irányuló az ajánlatot a piactérről beszerzése után megjelenni. Az URL-cím is a végpontot, amely fog kapni a kapcsolat API-k a Microsoft kereskedelmi megkönnyítése érdekében.

- **Kapcsolat webhook** (kötelező): Az összes aszinkron esemény, amelyet a Microsoft küld Önnek a vásárló nevében (például: Azure-előfizetés felment érvénytelen), kérjük, adjon meg egy kapcsolati webhook. Ha még nem rendelkezik egy webhook rendszer helyben, a legegyszerűbb konfiguráció kell egy HTTP végpont logikai alkalmazást, amely minden, az csökkentheti a közzétett események figyelésére és majd kezelni őket megfelelően-e (pl. https://prod-1westus.logic.azure.com:443/work). További információkért lásd: [hívása, eseményindító, vagy HTTP-végpontokat, a logic apps-munkafolyamatok beágyazása](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure AD-bérlő azonosítója** (kötelező): Az Azure-portálon belül kérjük, [hozzon létre egy Azure Active Directory (AD) alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) egy hitelesített kommunikáció mögött van, hogy a Microsoft ellenőrizheti a kapcsolatot a két szolgáltatás között. Található a [bérlőazonosító](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), nyissa meg az Azure Active Directoryban, és válassza **tulajdonságok**, majd keresse meg a **címtár-azonosító** száma szerepel (például) 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-alkalmazás azonosítója** (kötelező): Emellett a [Alkalmazásazonosító](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) és egy hitelesítési kulcsra. Az értékek beszerzéséhez nyissa meg az Azure Active Directoryban, és válassza **alkalmazásregisztrációk**, majd keresse meg a **Alkalmazásazonosító** szám (pl. 50c464d3-4930-494c-963c-1e951d15360e) szerepel. A hitelesítési kulcs találja, **beállítások** válassza **kulcsok**. Adjon meg egy leírást és időtartama és lesz majd meg kell adni egy számértéket kell.

 Vegye figyelembe, hogy az Azure application ID a Gyártóazonosítóval társítva, ezért ügyeljen arra, hogy ugyanazon Alkalmazásazonosítóval minden ajánlat szerepel-e.

## <a name="plan-overview"></a>Csomag – áttekintés

A **csomag áttekintése** lap lehetővé teszi számos belül az azonos ajánlat csomag lehetőséget biztosít. Az alábbi díjcsomagok (más néven SKU-k) sikerült eltérő verzió, a felhasználómegtartás és a szolgáltatási szint tekintetében. Annak érdekében, hogy az ajánlatot a piactéren értékesítheti legalább egy csomagot kell állítania.

Létrehozása után láthatja a csomag nevével, azonosítók, díjszabási modellekhez, rendelkezésre állás (nyilvános vagy privát) aktuális állapotát, és minden rendelkezésre álló műveletek közzététele.

-   **Műveletek** érhető el a **csomag áttekintése** a csomag aktuális állapotától függően eltérőek lehetnek, és előfordulhat, hogy tartalmazzák:
  - Ha a csomag állapota **Draft** – tervezet törlése
  - Ha a csomag állapota **élő** – Stop értékesítése a szolgáltatáscsomag vagy a szinkronizálási privát célközönség

**Hozzon létre új csomagot** (minimális azok számára, akik válassza ki a Microsofttal, hogy egy csomag)

- **Csomagazonosító:** Hozzon létre egy egyedi terv azonosítója, az egyes csomagokhoz tartozó ezt az ajánlatot. Ezt az Azonosítót (ha alkalmazható) termék URL-cím és az Azure Resource Manager-sablonok ügyfelei számára látható lesz. Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásjel használja. Legfeljebb 50 karakter használata engedélyezett a csomag azonosítóját. Vegye figyelembe, hogy az azonosító létrehozása kiválasztása után nem módosítható.
- **Csomag neve:** Ügyfelek Ez a név jelenik meg, amikor eldönti, amely tervezi, hogy az ajánlat belül válassza ki. Hozzon létre egy egyedi ajánlat nevét az egyes csomagokhoz tartozó ezt az ajánlatot. A csomag nevét használja megkülönböztetni, hogy az azonos ajánlat (pl. egy részét képezheti szoftvercsomagok – alap Ajánlat neve: A Windows Server; csomagok: A Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Listaelem megtervezése

A **listaelem megtervezése** lap megjeleníti a nyelv (és piacok) hol érhető el a csomagot, az egyetlen rendelkezésre álló hely jelenleg az angol (Egyesült Államok). Ezen a lapon emellett a nyelvspecifikus listázása és a dátum/idő adták állapotát jeleníti meg. Szüksége lesz a piactér-beli részleteit (ajánlat nevét, leírását, keresési kifejezéseket, stb.) meghatározására az egyes nyelvekhez / a piacra jutási időt.

#### <a name="plan-listing-details"></a>Csomag lista részletei

Egy csomag nyelvén jelennek a **listaelem megtervezése** információkat, beleértve a **neve** és **leírása.**

- **Név**: Előre fel van töltve az előzetes verzió alapján **új terv** bejegyzés és az ajánlat "szoftvercsomag" jelenik meg a Marketplace-en címeként jelenik meg.
- **Leírás:** A leírás megadása nem annak magyarázata, hogy miből ehhez a szoftvercsomaghoz egyedi lehetőséget, és az egyéb szoftverfrissítési csomagok belül az ajánlat a különbségeket. Legfeljebb 500 karaktereket tartalmazhat.

Válassza ki **mentése** ezek a mezők befejezése után.

#### <a name="plan-pricing-and-availability"></a>Díjszabás és a rendelkezésre állás tervezése

A **díjszabás és a rendelkezésre állási** lap lehetővé teszi a piacon, amely a csomag lesz elérhető, konfigurálja a kívánt bevételszerzési modell, ár és számlázási időszak. Emellett jelezheti, hogy a terv láthatóvá tétele mindenki számára, vagy csak egy adott ügyfélnek (egy privát közönség számára).

#### <a name="markets"></a>Piacok

- **Szerkesztés piacok** (nem kötelező)

Minden csomag legalább egy piacon elérhetőnek kell lennie. Jelölje be a bármely piaci helyre, ahol szeretné elérhetővé tenni ezt a tervet. A keresőmezőbe, és a "Adó elengedik" országban, amelyben a Microsoft remits forgalmi és használati adó az Ön nevében kiválasztására szolgáló gomb is segítik. 


Ha korábban beállított árak a terv az Amerikai dollár (USD), és adja hozzá a piaci egy másik helyre, az új piaci díjszabása szerint az aktuális átváltási árfolyamokat számolja ki. Mindig tekintse át az ár, az egyes piacokon közzététel előtt. Díjszabás szerint "Exportálás díjszabás (xlsx)" hivatkozást használva a módosítások mentése után tekinthető.

#### <a name="pricing"></a>Díjszabás

- **Díjszabási modell**: Alapján, átalánydíjjal történik, vagy felhasználószám alapján

**Alapján, átalánydíjjal történik:** Engedélyezze a hozzáférést az ajánlatra, és az egyetlen havi vagy éves ár alapján, átalánydíjjal árat. Ez a más néven helyalapú díjszabása.

**A munkaállomás-alapú:** Az ajánlat elérésének lehetővé tétele a felhasználók fér hozzá az ajánlat vagy a kötnek az ár a *munkaállomások*. Ez a munkaállomás-alapú modell lehetővé teszi, hogy állítsa be a minimális, és az ár alapján engedélyezett munkaállomások maximális számát. Ezzel a módszerrel eltérő árat pontok konfigurálhatók több tervek konfigurálásával a felhasználók száma alapján.  Ezek a mezők kitöltése nem kötelező. Ha üresen hagyja a mezőt, a munkaállomások számát (1 perc), és tetszőleges számú, a rendszer is támogatja a maximális korlát nem rendelkező fogja értelmezni. Ezek a mezők előfordulhat, hogy szerkeszthető egy frissítést a csomag részeként.

Közzététele után a számlázási díjszabási modell választás nem módosítható. Emellett az azonos ajánlat minden csomag kell osztani ugyanazt a díjszabási modellt.

- **Számlázási időszak**: A havi vagy éves

Válassza ki, hogy az ügyfeleknek kell kell fizetniük a felsorolt díjak a gyakoriság. Legalább egy havi vagy éves ár meg kell adni, vagy mindkét beállítást is elérhetővé az ügyfelek számára.

- **Ár**: USD havonta vagy évente USD

Set árak a helyi pénznemben számított (USD = USA dollár) a helyi pénznemben számított használatával az aktuális átváltási árfolyamok érhető el a telepítés során az összes kijelölt piacok át. Ellenőrizze a közzététel előtt a díjak a díjszabási táblázat és a díj az adott piacon megtekintésével exportálásával. Ha szeretne egyéni beállítása egy egyéni piaci szereplő díjak módosítsa, és importálja a díjszabási táblázatot. Felelőssége ellenőrzése a díjszabás, és saját ezeket a beállításokat.
**Először mentenie kell a díjszabási módosításokat az árképzési adatok exportálásának engedélyezése.*

Az árak a közzététel előtt gondosan tekintse meg, a mi egy csomag közzététele után módosíthatja bizonyos korlátozások vonatkoznak:

- Egy csomag közzététele után a díjszabási modell nem módosítható.
- Egy csomag közzététele egy számlázási időszak után azt nem lehet eltávolítani később.
- Miután közzétette a piacra a terv árat, azt később már nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Lehetősége van konfigurálása minden előfizetéssel mindenki számára elérhető, illetve megszakíthatja csak egy adott célközönséget számára láthatóvá tenni. Tagság a korlátozott célközönség Azure AD-bérlő azonosítók használatával rendelhet hozzá.

#### <a name="privacy"></a>Adatvédelem

- **Ez a privát csomag** (nem kötelező jelölőnégyzet)

Ezt a jelölőnégyzetet, hogy a csomag magánjellegű, és csak Ön a korlátozott közönség számára látható. Miután közzétett egy privát csomag, a közönség frissítése, vagy válassza ki a tervet mindenki számára elérhetővé. Mindenki számára, egy csomag közzététele után mindenki számára láthatóvá kell maradnia. (A csomag nem konfigurálható egy privát terv újra).

- **Korlátozott közönség (Bérlőazonosítók)**

Rendelje hozzá a célközönség, mely fogja tudni érni a privát csomag számára. Hozzáférés hozzá van rendelve a bérlőazonosítók használata a beállítás minden hozzárendelt Bérlőazonosító leírását tartalmazza. Legfeljebb 10 Bérlőazonosítók adhatók hozzá, vagy 20 000 ügyfél bérlőazonosítók Ha számolótábla .csv-fájlba importálni.

A bérlő az egy szervezet GUID (globálisan egyedi azonosító, erőforrások azonosítására használt 128 bites egész szám) jelölt azonosítójú reprezentációját. Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. A bérlő ellenőrzéséhez jelentkezzen be az alkalmazás kezeléséhez használni kívánt fiókkal az Azure Portalon. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik. Emellett megtekintheti a szervezet bérlői azonosító egy tartomány neve URL-címen: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

SaaS-ajánlatok bérlőazonosítók egy privát célközönség meghatározására használja, míg más típusú felhasználhatja az Azure előfizetés azonosítókat (amely szintén jelentésekként jelennek meg GUID).

> [!NOTE]
> A privát célközönség (vagy korlátozott közönség) eltér a egy előzetes közönség számára. Az a **[előzetes](#preview)** lapon megadhat egy előzetes közönség számára. Egy előzetes célközönség hozzáférhessen-e az ajánlat *előzetes* élő közzétételét a piactéren az ajánlatra. A privát célközönség megjelölés csak a megadott terven vonatkozik, miközben az előzetes verzió célközönség tekintheti meg minden csomag (privát vagy nem), de csak a korlátozott előzetes verzió időszakában, amíg a terv tesztelve és érvényesítve.


## <a name="test-drive"></a>Próbaüzem

A **kipróbálása** lap lehetővé teszi egy bemutatót (vagy "próbálja ki a") beállítása, amely lehetővé teszi az ügyfeleknek az ajánlat végrehajtása előtt meg kell vásárolnia, próbálkozzon. További információ: a cikk [Mi a Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Ha már nem szeretne biztosítani egy tesztverziós az ajánlatban, térjen vissza a **[kínálnak a telepítő](#offer-setup)** lapon, és törölje a jelet **engedélyezése tesztverziós**.

### <a name="technical-configuration"></a>Technikai konfiguráció
A következő típusú tesztverziók érhetők el, minden egyes technikai konfigurációs követelményeknek.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi-ban](#technical-configuration-not-required-for-power-bi-test-drives) (műszaki konfigurálása nem kötelező)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technikai konfiguráció az Azure Resource Manager kipróbálása

A központi telepítési sablont, amely tartalmazza az összes Azure-erőforrást a megoldást alkotó. Ebben a forgatókönyvben igazodó termékek csak Azure-erőforrások használatához. További tudnivalók a beállítása egy [Azure Resource Manager tesztverziós](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régiók** (kötelező): Jelenleg nincsenek 26 Azure által támogatott régiók, a test drive lesz elérhető. Általában érdemes a test drive elérhetővé tenni a régiókban, ahol a legnagyobb számot, akik várhatóan úgy, hogy és kiválaszthatja a legközelebbi régió, a legjobb teljesítmény érdekében. Győződjön meg arról, hogy engedélyezett-e az előfizetés összes kiválasztja a régiók minden szükséges erőforrások üzembe helyezése kell.

- **Példányok**: Adja meg (meleg vagy hideg) és szám a rendelkezésre álló példányok, hol érhető el az ajánlatot régiók számának meg kell szorozni.

**Gyakori elérésű**: Ez a példány típus egy kiválasztott régióban üzembe helyezett és várakozás hozzáférést. Ügyfelek azonnal hozzáférhetnek *interaktív* egy tesztverziós ahelyett, hogy várnia a központi telepítési példánya. A hosszabb, hogy ezek a példányok mindig futtat az Azure-előfizetést, akkor számítunk fel a költségek nagyobb rendelkezésre állását. Erősen javasoljuk, hogy legalább egy *interaktív* példányt, mivel a legtöbb ügyfél nem szeretne várni a teljes üzembe helyezési egy Gyűjtőtár az ügyfelek általi használatot eredményez, ha nincs *interaktív* példány érhető el.

**Ritkán használt**: Az ilyen típusú példányt jelöli, valószínűleg régiónként telepíthető példányok teljes száma. Ritkán használt példány szükséges a teljes teszt meghajtó Resource Manager-sablon üzembe helyezéséhez, amikor az ügyfél kéri a test drive, így *hideg* példányai sokkal lassabb, mint betöltése *interaktív* példányok. A kompromisszummal jár, hogy csak akkor kell fizetnie, a test drive időtartama, hogy *nem* mindig fut az Azure-előfizetésbe, egy *interaktív* példány.

- **Test drive Azure Resource Manager-sablon**: Töltse fel a .zip, amely tartalmazza az Azure Resource Manager-sablon.  További információk a rövid útmutató a cikkben az Azure Resource Manager-sablon létrehozásáról [létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Tesztverzió időtartama** (kötelező): Adja meg az, hogy mennyi ideig marad aktív, az órák száma, a Test Drive. A Test Drive Ez az időtartam lejárta után automatikusan leáll. Ennek az időtartamnak előfordulhat, hogy csak bízza beállítása egy egész órák száma alapján (például) "2" óra "1.5" nem érvényes).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció, a Dynamics 365 kipróbálása

A Microsoft eltávolíthatja a üzemeltetése és karbantartása, a szolgáltatás üzembe helyezését és a test drive az ilyen üzembe helyezési egy test drive beállítása bonyolultságát. Az ilyen típusú üzemeltetett tesztverziós konfigurálása számára ugyanaz, függetlenül attól, hogy a test drive van-e állítva egy üzleti központi, a Customer Engagement vagy a műveletek közönség számára.

- **Max. egyidejű tesztverziók** (kötelező): Ügyfeleink számára, akik egy időben használhatja a test drive maximális számának megadása. Minden egyes. párhuzamos felhasználó fog felhasználni a Dynamics 365-licenc, amíg a test drive aktív, ezért gondoskodjon arról, hogy elegendő licenccel érhető el a beállított maximális korlátot támogatására kell. Ajánlott érték 3 – 5.

- **Tesztverzió időtartama** (kötelező): Adja meg az, hogy mennyi ideig órák száma definiálásával a Test Drive aktív marad. Ennyi óra után a munkamenet befejezése, és már nem igényelnek a licencei egyikét. Azt javasoljuk, hogy az ajánlat összetettségétől függően 2 – 24 óra értéket. Ennek az időtartamnak előfordulhat, hogy csak bízza beállítása egy egész órák száma alapján (például) "2" óra "1.5" nem érvényes).  A felhasználó kérheti egy új munkamenetet, ha azok ideje elfogyott és a test drive újból elérhető.

- **Példány URL-cím** (kötelező): Az URL-címe, ahol az ügyfél megkezdődik a tesztverziós. Általában a példány URL-címét a Dynamics 365 mintaadatok telepítve van az alkalmazás futtatásához (pl. https://testdrive.crm.dynamics.com).

- **Webes API URL-Címének példány** (kötelező): A Microsoft 365-fiókjába, és ellenőrizheti, hogy a webes API URL-címe, a Dynamics 365-példány beolvasása **beállítások** \&gt; **Testreszabási** \&gt; **Fejlesztői erőforrások** \&gt; **Példány webes API-t (szolgáltatási gyökerének URL)**, másolja az itt található URL-CÍMÉT (pl. https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Szerepkör neve** (kötelező): Adja meg a Dynamics 365 egyéni Tesztverzió meghatározott biztonsági szerepkör nevét. Ez rendeli hozzá a felhasználót a tesztverziós (pl. test-drive-szerepkör) alatt.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Logikai alkalmazás tesztverziós műszaki konfigurációja

Egyéni termékek test drive központi telepítési sablont, amely magában foglalja a különböző összetett megoldások létrehozására szolgáló architektúrák az ilyen típusú kell használnia. Logikai alkalmazás beállításával kapcsolatos további információk a kipróbálási, keresse fel [műveletek](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a Githubon.

- **Régió** (szükséges, egyetlen kijelölést legördülő listában): Jelenleg nincsenek 26 Azure által támogatott régiók, a test drive lesz elérhető. A kiválasztott terület lesz telepítve, a logikai alkalmazás az erőforrásokat. Ha a logikai alkalmazás minden olyan egyéni erőforrás egy adott régióban tárolja, győződjön meg arról, adott régióban itt ki van jelölve. A legjobb módja, hogy teljesen helyileg, az Azure-előfizetést a portálon, a logikai alkalmazás üzembe helyezése, és győződjön meg arról, hogy megfelelően működik-e ez a beállítás elvégzése előtt.

- **Max. egyidejű tesztverziók** (kötelező): Ügyfeleink számára, akik egy időben használhatja a test drive maximális számának megadása. Ezen teszt meghajtók már telepítve lettek, így az ügyfelek azonnal Várakozás a központi telepítés nélkül eléréséhez.

- **Tesztverzió időtartama** (kötelező): Adja meg az, hogy mennyi ideig marad aktív, az órák száma, a Test Drive. A test drive Ez az időtartam lejárta után automatikusan leáll.

- **Azure erőforráscsoport-név** (kötelező): Adja meg a [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevet a logikai alkalmazás tesztverziós mentési helye.

- **Az Azure logic app name** (kötelező): Adja meg a logikai alkalmazás, amely a test drive rendel a felhasználó nevét. Ez a logikai alkalmazás a fenti Azure-erőforrások csoportban kell menteni.

- **Megszüntetési logikai alkalmazás nevét** (kötelező): Adja meg a logikai alkalmazás, amely a test drive deprovisions, Miután befejeződött az ügyfél nevét. Ez a logikai alkalmazás a fenti Azure-erőforrások csoportban kell menteni.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>A Power BI tesztverziók nem szükséges technikai konfiguráció

Szeretné bemutatni egy interaktív Power BI vizuális egy beágyazott hivatkozás használatával személyre szabott irányítópult megosztása a tesztverziós, további technikai konfigurálást igényel,-termékek. További tudnivalók a beállítása[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) sablon alkalmazásokat.

### <a name="deployment-subscription-details"></a>Üzembe helyezési előfizetés részletei

Az Ön nevében a Test Drive üzembe helyezéséhez, hozza létre, majd adjon meg egy különálló, egyedi Azure-előfizetést. (Nem a Power BI tesztverziók szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és a Logic apps esetén szükséges): Adja meg az előfizetés-azonosító, az erőforrás-használat, jelentéskészítési és számlázás az Azure-fiók szolgáltatásokhoz való hozzáférés megadását. Azt javasoljuk, hogy érdemes [egy külön Azure-előfizetés létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription) tesztverziók használandó, ha még nincs ilyen. Annak az Azure-előfizetés azonosítója jelentkezik be a [az Azure portal](https://portal.azure.com/) ellenőrizheti, hogy a **előfizetések** lapján a bal oldali menüben. Válassza a lap jelenik meg az előfizetés-azonosító (pl. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-bérlő azonosítója** (kötelező): Adja meg az Azure Active Directory (AD) [bérlőazonosító](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Ez Azonosítójának megkereséséhez jelentkezzen be a [az Azure portal](https://portal.azure.com/), az Active Directory lapon válassza a bal oldali menüben válassza ki **tulajdonságai** , majd keresse meg a **címtár-azonosító** száma szerepel (például) 50c464d3-4930-494c-963c-1e951d15360e). A tartomány URL-CÍMÉT, használja a szervezet Bérlőazonosító is kereshet: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Az Azure AD-bérlő neve** (szükséges Dynamic 365): Adja meg az Azure Active Directory (AD) nevét. Ez a név megkereséséhez jelentkezzen be a [az Azure portal](https://portal.azure.com/), a jobb felső sarokban a bérlő neve fog szerepelni a fiók neve alatt.

- **Azure AD-alkalmazás azonosítója** (kötelező): Adja meg az Azure Active Directory (AD) [Alkalmazásazonosító](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Ez Azonosítójának megkereséséhez jelentkezzen be a [az Azure portal](https://portal.azure.com/), az Active Directory lapon válassza a bal oldali menüben válassza ki **alkalmazásregisztrációk**, majd keresse meg a **Alkalmazásazonosító** száma a felsorolt (pl. 50c464d3-4930-494c-963c-1e951d15360e).

- **Az Azure AD-Alkalmazáskulcs** (kötelező): Adja meg az Azure Active Directory (AD) [alkalmazáskulcsot](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Ez Azonosítójának megkereséséhez jelentkezzen be a [az Azure portal](https://portal.azure.com/), az Active Directory lapon válassza a bal oldali menüben válassza ki **alkalmazásregisztrációk** , majd **beállítások**  >  **Kulcsok**.

Ne felejtse el **mentése** mielőtt a következő szakaszban!

### <a name="test-drive-listings-optional"></a>Test drive listaelemek (nem kötelező)

A **Test Drive listaelemek** lehetőség alatt található a **kipróbálása** lap jeleníti meg, hol érhető el a test drive, nyelvek (és piacok) az egyetlen rendelkezésre álló hely jelenleg az angol (Egyesült Államok) . Ezen a lapon emellett a nyelvspecifikus listázása és a dátum/idő adták állapotát jeleníti meg. Szüksége lesz a teszt meghajtó részletei (leírását, használati, videók stb.) az egyes nyelvi piacokon.

- **Leírás** (kötelező): A test drive, mit fog mutatni, ismertetik célkitűzései kísérletezhet a felhasználói funkciók megismeréséhez és információkat határozza meg, hogy az ajánlat beszerzéséhez elősegítése érdekében. Legfeljebb 3000 karakterek a szöveg lehet megadni ebben a mezőben. 

- **Adatok eléréséhez** (az Azure Resource Manager és a logikai meghajtó teszteléséhez szükséges): Elmagyarázza, mit ügyfél tudnia kell, annak érdekében, hogy eléri és használja a test drive. Végig egy olyan helyzetben az ajánlatot, és pontosan mit az ügyfélnek tudnia kell, a test drive egész funkcióhoz férhet hozzá. Legfeljebb 10 000 karakter hosszú lehet a szöveg lehet megadni ebben a mezőben.

- **Felhasználó manuális** (kötelező): A test drive felhasználói élmény részletes leírását. A felhasználó manuális le kell fednie a kívánt elemet az ügyfél előnyökre tehet szert a test drive tapasztal, és bármilyen kérdése van, lehet, hogy referenciaként szolgálnak. A fájl PDF formátumban kell megadni, és feltöltése után neve (255 karakter max) lehet.

- **Videók: Videók hozzáadása** (nem kötelező): Videók feltöltött YouTube vagy Vimeo, és itt hivatkozást és a miniatűr kép (533 x 324 képpont) hivatkozott, hogy az ügyfelek tekintheti meg egy útmutató információkat, amelyekkel jobban megismerheti a test drive, beleértve az a funkciók használatához a kínálnak, így megismerheti a forgatókönyvek, amelyek kiemelnek előnyeiket.
  - **Név** (kötelező)
  - **URL-címe (YouTube vagy Vimeo csak)** (kötelező)
  - **Miniatűr (533 x 324px)**: Képfájl PNG formátumúnak kell lennie.

Válassza ki **mentése** ezek a mezők befejezése után.

## <a name="publish"></a>Közzététel

#### <a name="submit-offer-to-preview"></a>Küldje el az előzetes verzióra ajánlat

Miután végzett a szükséges szakaszok az előfizetési csomag, válassza ki a **közzététele** a portál jobb felső sarkában. Lesz újból irányított, hogy a **tekintse át, és tegye közzé** lap. 

Ha most először tesz közzé ezt az ajánlatot, Ön a következőket teheti:

- Tekintse meg a befejezettség állapotát az egyes szakaszok az ajánlat.
    - *Nem indult el* – azt jelenti, hogy az a szakasz nem csinált semmit, és kell elvégezni.
    - *Hiányos* – azt jelenti, hogy a szakasz a hibákat, ki kell javítani kell, vagy további információ szükséges. Lépjen vissza a szakasz, és frissítse azt.
    - *Teljes* – azt jelenti, hogy a szakasz befejeződött, az összes szükséges adatok lettek megadva, és nem tartalmaz hibát. Az ajánlat minden szakasza egy kész állapotban kell lennie, az ajánlat elküldése előtt.
- Győződjön meg arról, hogy az alkalmazás megfelelően lett tesztelve mellett kiegészítő megjegyzéseket segít annak megértésében, az alkalmazás a tanúsítási csapatának tesztelési utasításokat biztosítanak.
- Küldje el az ajánlat közzétételi kiválasztásával **küldés**. Küldünk a érhető el egy e-mail értesíti, amikor az ajánlat előzetes verzióját, hogy tekintse át és hagyja jóvá. Térjen vissza a Partnerközpont kell és válassza ki **éles** az ajánlat az ajánlat közzététele a nyilvános (vagy ha egy privát ajánlat, a titkos közönség számára).

## <a name="next-steps"></a>További lépések

- [A kereskedelmi piactéren egy létező ajánlat frissítése](./update-existing-offer.md)
