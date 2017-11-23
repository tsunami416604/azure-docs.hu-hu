---
title: A Microsoft Azure Data Encryption nyugalmi |} Microsoft Docs
description: "Ez a cikk áttekintése a Microsoft Azure data encryption nyugalmi általános képességek, és az általános szempontokat tartalmazza."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: b02afa77ce99f576fed76b398642ba3f3ce2ba98
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-encryption-at-rest"></a>Az Azure Data Encryption nyugalmi
Nincsenek több eszközt a vállalati biztonsági és megfelelőségi igényeknek megfelelő adatok védelme a Microsoft Azure-ban. A dokumentum koncentrál:
- Hogyan védett adatok inaktív között a Microsoft Azure
- Ismerteti, amelyek a data protection végrehajtása részt vevő összetevők
- Ellenőrzi, hogy a különböző kulcskezelés védelmi módszerek és. 

Aktívan nem adattitkosítás egy közös biztonsági követelménye. A Microsoft Azure előnye, hogy a szervezetek érhető el titkosítását, anélkül, hogy a megvalósítása és kezelése és egy egyéni fő megoldás kockázatát. A szervezetek lehetővé teszik a teljesen kezelheti a aktívan Azure van. Emellett a szervezetek többféleképpen is szorosan a titkosítás vagy a titkosítási kulcsok kezeléséhez.

## <a name="what-is-encryption-at-rest"></a>Mi az a titkosítását?
Titkosítását a kriptográfiai kódolási () az adatok titkosítása hivatkozik, amikor azt. Rest-tervek az Azure-ban, a titkosítás szimmetrikus titkosítási titkosításához és visszafejtéséhez nagy adatmennyiségek gyors egyszerű fogalmi modell szerint használja:

- Egy szimmetrikus titkosítási kulcs használatával titkosítja az adatokat, akkor is megőrződjenek 
- Ugyanazt a titkosítási kulcs használatával fejti vissza az adatokat, akkor a rendszer a memóriában readied
- Adatok particionálható, és különböző kulcsok használhatók minden partíció esetében
- A hozzáférés-vezérlési házirendeket korlátozza a hozzáférést bizonyos identitások és kulcshasználat-naplózás kulcsokat tárolja biztonságos helyen. Az adattitkosítási kulcsokat gyakran aszimmetrikus titkosítási mód tovább korlátozhatja a hozzáférést a titkosított (ismertet a *kulcs hierarchia*, ez a cikk későbbi)

A fenti ismerteti az általános magas szintű elemeinek titkosítását. A gyakorlatban a fő felügyeleti és ellenőrzési forgatókönyvek, valamint a méretezés és a rendelkezésre állási biztosítékok, további szerkezetek igényelnek. A Microsoft Azure titkosítás Rest fogalmakat és az összetevők az alábbiakban található.

## <a name="the-purpose-of-encryption-at-rest"></a>Az a rendeltetése, aktívan titkosítása
Titkosítását az célja, hogy az adatok védelméhez az adatokat nyugalmi (a fentiekben ismertetett.) Adatokat nyugalmi elleni támadások közé tartozik a próbálja megszerezni a fizikai érje el a hardvert, amelyen az adatok tárolását, és majd veszélyeztetheti a benne lévő adatokat. Az ilyen támadások egy kiszolgáló merevlemezén előfordulhat, hogy rendelkezik lett kezelésük a merevlemez-meghajtó eltávolítása a támadó karbantartás során. Később a támadó a merevlemez-meghajtóról kellene üzembe egy számítógépen hozzáférésének szabályozására, hogy az adatok elérésére tett kísérlet alatt. 

Titkosítását az célja, hogy megakadályozható, hogy a támadó a titkosítatlan hozzáférjenek az adatok biztosításával adattitkosítás a lemezen. Ha egy támadó beszerzése egy merevlemezen, az ilyen titkosított adatok, és nem lehet hozzáférni a titkosítási kulcsokat, a támadó nem veszélyezteti az adatok nélkül nehéz. Ilyen esetben a támadónak támadások elleni titkosított adatok, amelyek jóval összetettebb kísérletet, és erőforrás fel mint elérése nem titkosított adatokat a merevlemezen. Emiatt aktívan titkosítása erősen ajánlott, és magas prioritású követelmény a legtöbb szervezet számára. 

Bizonyos esetekben titkosítását is szüksége van egy szervezet szükséges adatok irányítási és a megfelelőségi erőfeszítéseket. Például a HIPAA, PCI és FedRAMP, iparági és kormányzati szabályok adott óvintézkedéseket vonatkozó adatok védelme és titkosítási követelmények elrendezését. Ezek a szabályozások számos titkosítását mérőszáma kötelező a megfelelő adatok kezelése és védelme szükséges. 

Megfelelőségi és üzletszabályzati követelményeken kívül titkosítását kell tekinteni, mint egy védelmi jellegű platform képességei. Amíg biztosít a Microsoft a szolgáltatások, alkalmazások, adatok, majd átfogó létesítményt a személyes és a fizikai biztonsági adatok a megfelelő platform hozzáférés-vezérlés, és naplózás esetén fontos, hogy adja meg a további "átfedő" biztonsági intézkedéseket, abban az esetben, ha a további biztonsági intézkedéseket egyike meghibásodik. Titkosítását ilyen egy további védelmi mechanizmust biztosít.

A Microsoft elkötelezett a rest-beállítások a titkosítási szolgáltató felhőszolgáltatás, és a felhasználók megfelelő kezelhetőségi titkosítási kulcsok és a titkosítási kulcsok használatakor megjelenítő naplók elérését. Emellett a Microsoft dolgozik, amellyel az összes felhasználói adat titkosítása alapértelmezés szerint a cél felé.

## <a name="azure-encryption-at-rest-components"></a>Az Azure titkosítását Rest-összetevők

Az előzőleg leírtak titkosítását célja, hogy a lemezen fennállásának adattitkosítás titkos kulccsal. A cél biztonságos kulcs létrehozása, tárolási, eléréséhez meg kell adni hozzáférés-vezérlés és a titkosítási kulcsok kezelése. Részletek változhat, bár az Azure szolgáltatások titkosítási Rest-megvalósítások jelentheti a az alábbi fogalmak, amelyek az alábbi ábra mutatja majd.

![Összetevők](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A titkosítási kulcsok és a hozzáférés-vezérlést az ezeknek a kulcsoknak tárolási helye a következő központi helyet foglalnak el egy rest-modell titkosítását. A kulcsok kell magas védett, de a megadott felhasználók által kezelhető és adott szolgáltatások elérhető. Az Azure szolgáltatások esetében az Azure Key Vault ajánlott kulcstároló megoldás, és egy közös kezelést biztosít szolgáltatásban. Kulcsok tárolása és kezelése a kulcstárolót, és a kulcstároló elérésére adható meg felhasználót vagy szolgáltatást. Az Azure Key Vault ügyfél által felügyelt titkosítási kulcs olyan esetekben támogatja a kulcsok létrehozását ügyfél vagy ügyfél kulcsok importálása.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory-fiókokat adhatók engedélyek kezeléséhez vagy azok eléréséhez a titkosításhoz inaktív adatok titkosítása és visszafejtése, az Azure Key Vault tárolt kulcsok használatával. 

### <a name="key-hierarchy"></a>Kulcs hierarchia

Egynél több titkosítási kulcs használatban van egy rest-megvalósítási titkosítását. A megbízhatóság és a hozzáférés a kulcshoz és felügyeleti szükséges hitelesítési létrehozó aszimmetrikus titkosítási. Szimmetrikus titkosítást hatékonyabb tömeges titkosítás és visszafejtés, lehetővé teszi a erősebb titkosítást és jobb teljesítményt. Emellett használatának korlátozását a egyetlen titkosítási kulcs csökken a kockázata, hogy a kulcs biztonsága sérült a fog és újbóli titkosítása költségét Ha egy kulcsot le kell cserélni. Aszimmetrikus és szimmetrikus titkosítási előnyeinek kihasználásához, valamint használatát és adott kulcs veszélyeztetettségének korlátozása, a többi modellek Azure titkosítások használatára áll a következő típusú kulcsok kulcs hierarchia használatának:

- **Adatok titkosítási kulcs-(adattitkosítási kulcs)** – a partíció vagy az adatblokk titkosításához használt szimmetrikus kulcs AES256.  Előfordulhat, hogy egyetlen sok partíciót és sok az adattitkosítási kulcsokat. Minden adatblokk különböző kulccsal titkosított nehezebbé titkosítási elemzés támadásokat. Az erőforrás-szolgáltató vagy az alkalmazás példányhoz, titkosítása és egy adott adatblokk visszafejtése DEKs elérésére van szükség. Ha a adattitkosítási kulcs helyére egy új kulcsot csak azokat az adatokat a társított blokkban újra titkosítani az új kulccsal kell lennie.
- **Kulcs titkosítási kulcscserekulcs (KEK)** – az adatok titkosítási kulcsok titkosítására használt aszimmetrikus titkosítási kulcsot. A fő titkosítási kulcs használata lehetővé teszi a titkosítási kulcsokat magukat, hogy a titkosított és ellenőrzött. Lehet, hogy az entitás, amely hozzáfér a KEK eltér az entitás, amely az adattitkosítási kulcsot igényel. Ez lehetővé teszi, hogy egy entitás replikaszervező az adattitkosítási kulcs az egyes adattitkosítási kulcsot adott partícióra korlátozott hozzáférés biztosítása érdekében a hozzáférést. A KEK a DEKs visszafejtéséhez szükséges, mivel a KEK hatékonyan egy olyan hibaérzékeny pontot, amellyel DEKs hatékonyan törölheti az a KEK törlését.

Az adattitkosítási kulcsokat, a kulcs titkosítási kulcsok titkosítva tárolódnak, és csak a kulcs titkosítási kulccsal rendelkező entitás bármely az adattitkosítási kulcsokat, ezzel a kulccsal titkosított kérheti le. A kulcs tárolása különböző modellek támogatottak. Mindegyik modellt a következő szakasz későbbi részében részletesebben ismertetik.

## <a name="data-encryption-models"></a>Adatok titkosítása modellek

A különböző titkosítási modellek és azok előnyei és hátrányai elengedhetetlen megérteni a megértése, hogyan valósítja meg az Azure-ban a különböző erőforrás-szolgáltató az titkosítását. Ezek a definíciók összes erőforrás-szolgáltató között megosztott közös nyelvi és besorolás biztosításához az Azure-ban. 

Kiszolgálóoldali titkosítás három forgatókönyv van:

- Kiszolgálóoldali titkosítás szolgáltatás felügyelt kulcsok használata
    - Az Azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása
    - A Microsoft felügyeli a kulcsok
    - Teljes felhő funkció

- Ügyfél által felügyelt kulcsok használata az Azure Key Vault kiszolgálóoldali titkosítás
    - Az Azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása
    - Felhasználói vezérlők kulcsok Azure Key Vault keresztül
    - Teljes felhő funkció

- Kiszolgálóoldali titkosítás kulcsokkal ügyfél által felügyelt ügyfél által felügyelt hardveren.
    - Az Azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása
    - Felhasználói vezérlők ügyfél által felügyelt hardver kulcsok
    - Teljes felhő funkció

Ügyféloldali titkosítás vegye figyelembe a következőket:

- Azure-szolgáltatások visszafejtett adatait nem látja.
- Ügyfelek kezelése és a helyszíni kulcsok tárolására (vagy más biztonságos tárolók). Kulcsok nem érhetők el az Azure-szolgáltatások
- Csökkentett felhő funkció

A támogatott titkosítási modellek felosztása két fő csoportok Azure-ban: "Titkosítási ügyfél" és "kiszolgálóoldali titkosítás" mint azt korábban említettük. Figyelje meg, függetlenül a többi modell titkosítását az használt, az Azure szolgáltatások mindig ajánlott egy biztonságos átviteli, például a TLS vagy HTTPS használatát. Ezért titkosításra átvitelt kell figyelembe venni az átviteli protokoll, és nem lehet meghatározni, hogy melyik titkosítási rest modellt használja, a jelentős tényező.

### <a name="client-encryption-model"></a>Ügyfél titkosítási modell

Ügyfél titkosítási modell titkosítás a szolgáltatás vagy a hívó alkalmazás erőforrás-szolgáltató vagy Azure kívül végrehajtott műveletek hivatkozik. A titkosítás a szolgáltatásalkalmazás Azure-ban, vagy az egyes alkalmazások vevői adatközpontban fut hajtható végre. Mindkét esetben, ha a titkosítás modell kihasználva az Azure erőforrás-szolgáltató kap egy titkosított blob adatok nélkül képes visszafejteni az adatokat bármely olyan módon, vagy férhet hozzá a titkosítási kulcsokat. Ebben a modellben kulcskezelést végezhető el a hívó szolgáltatás vagy alkalmazás és az Azure szolgáltatásban nem átlátszó.

![Ügyfél](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Kiszolgálóoldali titkosítás modell

Tekintse meg a kiszolgálóoldali titkosítás modellek titkosítás, az Azure-szolgáltatás által végrehajtott műveletek. Minta az erőforrás-szolgáltató titkosítása és visszafejtése műveleteket hajtja végre. Azure Storage például kapnak adatokat egyszerű szöveges műveletek, és végrehajtják a titkosítási és visszafejtési belső. Az erőforrás-szolgáltató használhatja a Microsoft vagy a megadott konfigurációjától függően az ügyfél által felügyelt titkosítási kulcsokat. 

![Kiszolgáló](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Kiszolgálóoldali titkosítás kulcskezelés modellek

A kiszolgálóoldali titkosítás a többi modellek mindegyikének azt jelenti, hogy kulcskezelés megkülönböztető jellemzői. Ez magában foglalja hol és hogyan titkosítási kulcsok létrehozása, és és a hozzáférési modellek és a kulcs Elforgatás eljárások tárolja. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsok használata

Sok ügyfél az alapvető követelmény, hogy, hogy az adatok titkosítása, amikor az aktívan nem biztosítására. Kiszolgálóoldali titkosítás segítségével felügyelt kulcsai lehetővé teszi, hogy ez a modell lehetővé teszi az ügyfelek számára az adott erőforrás (Tárfiók, SQL DB stb.) jelölje a titkosítási és kulcsok kiadásához, elforgatás és biztonsági mentés minden kulcskezelés szempontjai Kilépés a Microsoft által. A legtöbb aktívan titkosítása általában támogató Azure-szolgáltatások márkája kiszervezésével a titkosítási kulcsokat az Azure kezelését támogatja. Az Azure erőforrás-szolgáltató a kulcsokat hoz létre, ki vannak téve a biztonságos és olvassa be ezeket, ha szükséges. Ez azt jelenti, hogy a szolgáltatás a kulcsok teljes körű hozzáféréssel rendelkezik, és a szolgáltatás a hitelesítő adatok életciklus-felügyeletének teljes körű vezérléssel rendelkezik.

![Felügyelt](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Kiszolgálóoldali titkosítás segítségével felügyelt kulcsai ezért gyorsan kell adatokat titkosítás, az ügyfélnek az alacsony többletterhelést aktívan kezeli. Ha elérhető az ügyfél általában nyitja meg a célként megadott előfizetés-és erőforrás-szolgáltató az Azure portálon, és ellenőrzi, hogy a mezőben, amely jelzi az adatok titkosításához szeretnének. Az egyes erőforrás-kezelő szolgáltatás által kezelt kulccsal rendelkező kiszolgálóoldali titkosítás alapértelmezés szerint be van. 

A Microsoft által felügyelt kulccsal rendelkező kiszolgálóoldali titkosítás nem jelenti azt, a szolgáltatás tárolására teljes hozzáféréssel rendelkezik, és kezeli a kulcsokat. Amíg egyes ügyfelek előfordulhat, hogy szeretné, hogy a kulcsok kezeléséhez, mert azok érzi, hogy azt is biztosítják a fokozott biztonság, a költség, valamint olyan egyéni kulcs tárolási megoldást kockázatának figyelembe kell venni a modell kiértékelése során. Sok esetben egy szervezet dönthet, hogy az erőforrás-korlátozások vagy egy helyszíni megoldás kockázatok nagyobb, mint a többi kulcsokat, a titkosítás a felhőfelügyelet kockázatát is.  Ez a modell azonban nem feltétlenül elégségesek vezérlésére létrehozását vagy a titkosítási kulcsok életciklusát, vagy másik csoporthoz, mint a szolgáltatás (azaz a szolgáltatás a teljes felügyeleti modellből kulcskezelés elkülönítése) kezelése a szolgáltatás titkosítási kulcsok kezeléséhez rendelkeznie követelményekkel rendelkező szervezetek számára.

##### <a name="key-access"></a>Hozzáférés a kulcshoz

Kiszolgálóoldali titkosítás szolgáltatás felügyelt kulccsal rendelkező használatakor, a kulcs létrehozása, tárolási és a szolgáltatás eléréséhez a szolgáltatás által kezelt összes vannak. Általában a legalapvetőbb az Azure erőforrás-szolgáltatók tárolóban, az adatok közel fogja tárolni a titkosítási kulcsokat, és gyorsan elérhető-közben a kulcs titkosítási kulcsok egy biztonságos belső tárolóban vannak tárolva.

**Előnyei**

- Egyszerű telepítés
- A Microsoft kezeli a kulcs elforgatás, a biztonsági mentés és a redundancia
- Ügyfél nem rendelkezik végrehajtási vagy egy egyéni kulcskezelés séma kockázatát költsége.

**Hátrányok**

- Nem ügyfél szabályozza, a titkosítási kulcsok (kulcs jellemzője, életciklusát, visszavonás, stb.)
- A szolgáltatás általános felügyeleti modellből kulcskezelés elkülönítse nem képes

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Felügyelt felhasználói kulcsok használata az Azure Key Vault kiszolgálóoldali titkosítás 

A titkosítási kulcsok ügyfelek forgatókönyvekben, ahol a követelmény titkosítani az adatokat a többi és vezérlés használhat ügyfél által felügyelt kulcsok használata a Key Vault kiszolgálóoldali titkosítás. Egyes szolgáltatások előfordulhat, hogy csak a legfelső szintű kulcs titkosítási kulcs tárolása az Azure Key Vault, és tárolja a titkosított titkosítási kulcs egy belső helyen közelebb az adatokhoz. Amely a forgatókönyv ügyfelek is a saját kulcsaikat kerüljön a Key Vault (BYOK – Bring Your Own Key), vagy újakat létrehozni, és használhatják azokat a kívánt erőforrások titkosításához. Az erőforrás-szolgáltató titkosítási és visszafejtési műveleteket hajtja végre, amíg minden titkosítási műveleteket a legfelső szintű kulcsot, használja a konfigurált kulcs. 

##### <a name="key-access"></a>Hozzáférés a kulcshoz

A kiszolgálóoldali titkosítás modellt az Azure Key Vault felügyelt ügyfél kulcsokkal rendelkező magában foglalja a szolgáltatás használata a kulcs titkosításához és visszafejtéséhez szükség szerint. Rest-kulcsok titkosítását elérhető szolgáltatás egy hozzáférés-vezérlési házirend keresztül történik. Ezzel a házirend-hozzáférési jogosultságot ad a szolgáltatás identitásának megkapják a kulcsot. Az Azure-szolgáltatások egy társított előfizetés nevében fut az adott előfizetés identitással konfigurálható. A szolgáltatás Azure Active Directory-hitelesítést végezni, és egy hitelesítési jogkivonatot azonosítja magát, hogy az előfizetés nevében szolgáltatást kap. A token majd megjeleníthetők a Key Vault kapott hozzáférést egy kulcs beszerzése.

A titkosítási kulcsok használatával műveleteket, szolgáltatásidentitás kaphatnak hozzáférést a következő műveletek egyikének sem: visszafejtés, a titkosítása, unwrapKey, wrapKey, győződjön meg arról, jelentkezzen, beolvasása, listában, frissítése, létrehozása, importálása, törlése, biztonsági mentése és visszaállítása.

Kulcs titkosítása vagy visszafejtése inaktív adatok beszerzése a szolgáltatás identitás, amely az erőforrás-kezelő szolgáltatás példány fog futni, és rendelkeznie kell (az beszerzése a visszafejtési kulcs) UnwrapKey és WrapKey (lehet beszúrni az kulcstároló egy kulcsot, ha új kulcs létrehozása).


>[!NOTE] 
>További részletek a Key Vault engedélyezési oldal jelenik meg, a biztonságos a kulcstartót a a [dokumentáció az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Előnyei**

- A kulcsok teljes ellenőrzést használt – titkosítási kulcsok kezelése az az ügyfél Key Vault a felhasználói ellenőrzés alatt.
- Több szolgáltatás egy fő titkosíthatók.
- A szolgáltatás általános felügyeleti modellből kulcskezelést is elkülönítse
- Különböző régiókban a szolgáltatás és a kulcs helyének megadása is

**Hátrányok**

- Ügyfél van a kulcs kezelési vonatkozó teljes felelősség
- Ügyfél van a kulcs életciklusához kapcsolódó felügyeleti vonatkozó teljes felelősség
- A telepítő a & konfigurációs többletterhelést

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás szolgáltatással kezelt szabályozott ügyfélhardvereken kulcsok

Egyes Azure-szolgáltatások forgatókönyvekben, ahol a követelmény az inaktív adatok titkosítása és egy saját fejlesztésű tárházban kívül a Microsoft a kulcsok kezelése, engedélyezze a gazdagép a saját Key (HYOK) kulcskezelés modell. Ebben a modellben a szolgáltatás egy külső helyről kell lekérnie a kulcsot, és ezért a teljesítmény és rendelkezésre állás garanciák érintettek, és konfigurációs összetettebb. Továbbá mivel a szolgáltatás hozzáférése az adattitkosítási kulcsot a titkosítási és visszafejtési műveletek során ez a modell a teljes biztonsági garanciákat hasonlóak a Ha a kulcs-e az ügyfelek felügyelete az Azure Key Vault.  Ennek eredményeképpen a modell nincs a legtöbb szervezet számára megfelelő hacsak nem rendelkeznek, így szükségessé teszi az adott kulcskezelés követelmények. Ezek a korlátozások miatt legtöbb Azure-szolgáltatások nem támogatják a kiszolgáló által kezelt kulcsok használata a szabályozott ügyfélhardvereken kiszolgálóoldali titkosítás.

##### <a name="key-access"></a>Hozzáférés a kulcshoz

Felügyelt kulcsai használatával szabályozott ügyfélhardvereken kiszolgálóoldali titkosítás használata esetén a kulcsok az ügyfél által konfigurált rendszerek karbantartása. Azure-szolgáltatásokat, amelyek támogatják ezt a modellt adja meg az ügyfél való biztonságos kapcsolódás módszert megadott kulcstároló.

**Előnyei**

- A legfelső szintű kulcsot a faxrendszergazdáknak teljes hozzáférésük használt – titkosítási kulcsok egy megadott ügyfél-tároló által felügyelt
- Több szolgáltatás egy fő titkosíthatók.
- A szolgáltatás általános felügyeleti modellből kulcskezelést is elkülönítse
- Különböző régiókban a szolgáltatás és a kulcs helyének megadása is

**Hátrányok**

- Kulcstároló, biztonság, teljesítmény és rendelkezésre állás vonatkozó teljes felelősség
- Hozzáférés a kulcshoz felügyeleti vonatkozó teljes felelősség
- Kulcs életciklus-felügyeletének vonatkozó teljes felelősség
- Jelentős telepítése, a konfiguráció és a folyamatos karbantartási költségek
- A hálózat rendelkezésre állásának az ügyfél datacenter és az Azure adatközpontjaiban közötti függőség nagyobb.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>A Microsoft más felhőszolgáltatásaival aktívan titkosítása

A Microsoft Cloud services csomag használt összes modellt a felhőinfrastruktúra:, IaaS és PaaS, SaaS. Az alábbi példák ezek hogyan illeszkednek minden modellel rendelkezik:

- Egy kiszolgáló vagy SaaS, a felhő, például az Office 365 által biztosított alkalmazás rendelkező olykor szoftverszolgáltatások.
- Mely ügyfelek kihasználja a felhő az alkalmazásokban, többek között a tárolási, elemzés és szolgáltatási busz funkciót használja a felhő platform szolgáltatásaiból.
- Infrastruktúra-szolgáltatásokat, vagy infrastruktúrák (IaaS) mely ügyfelek operációs rendszerek központi telepítéséhez és alkalmazásokhoz, amelyek a felhőben, és valószínűleg más felhőalapú szolgáltatások kihasználása révén.

### <a name="encryption-at-rest-for-saas-customers"></a>A Szolgáltatottszoftver-ügyfelek aktívan titkosítása

A szolgáltatott szoftverként (SaaS) ügyfél szoftver általában rendelkeznek titkosítás engedélyezve vagy nem érhető el minden egyes szolgáltatás inaktív. Az Office 365 az ügyfelek számára titkosítását, illetve ellenőrizze több lehetőséggel rendelkezik. Office 365-szolgáltatásokhoz kapcsolatos információkat lásd: adatok titkosítási technológiák az Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>A PaaS ügyfelek aktívan titkosítása

Platform (PaaS) szolgáltatás ügyfél adatként általában egy alkalmazás végrehajtási környezetben található, és bármely Azure erőforrás-szolgáltató felhasználói adatok tárolására használt. Lásd: a titkosítás aktívan lehetőségeket az alábbi táblázatban a tárolás és alkalmazást platformokat, amelyekkel vizsgálja meg. Támogatott, ha mindegyik erőforrás-szolgáltató célokat szolgálnak titkosítását engedélyezésével kapcsolatos mutató hivatkozásokat tartalmaz. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Az infrastruktúra-szolgáltatási ügyfelek aktívan titkosítása

Szolgáltatott infrastruktúra (IaaS) szolgáltatás-ügyfelek különböző szolgáltatásokat és alkalmazásokat lehet használatban. IaaS-szolgáltatások engedélyezheti titkosítását az Azure-ban futó virtuális gépek és VHD-k használata az Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Titkosított tároló

A PaaS, például IaaS-megoldásokat más Azure-szolgáltatásokkal, inaktív adatok tárolására szolgáló használhatják fel. Ezekben az esetekben engedélyezheti a titkosítás Rest terméktámogatási minden felhasznált Azure szolgáltatás által biztosított. Az alábbi táblázat felsorolja a fő tárolási, a szolgáltatások és az alkalmazás platformok és a támogatott titkosítását modelljét. Támogatott, ha hivatkozásokkal titkosítását engedélyezésével kapcsolatban. 

#### <a name="encrypted-compute"></a>Titkosított számítási

A teljes titkosítás Rest megoldást igényel, hogy az adatok soha nem megőrződjenek titkosítatlan formában. Használja a kiszolgálón, a memória, az adatok betöltése az adatok is állandóként helyileg különböző módokon, például a Windows lapozófájlja, összeomlási memóriaképet és az alkalmazás hajthat végre naplózásának. Győződjön meg arról, ezek az adatok titkosítása IaaS-alkalmazások segítségével Azure Disk Encryption egy Azure IaaS virtuális gép (Windows vagy Linux) és a virtuális lemez. 

#### <a name="custom-encryption-at-rest"></a>Egyéni titkosítását

Javasoljuk, hogy amikor csak lehetséges, IaaS alkalmazások tudják hasznosítani Azure Disk Encryption és a titkosítás, felhasznált Azure-szolgáltatások által biztosított Rest-beállítások. Egyes esetekben például a szabálytalan titkosítási követelményeket vagy az Azure-alapú tárolási, az infrastruktúra-szolgáltatási alkalmazás fejlesztők kell előfordulhat, hogy a titkosítás megvalósításáról való rest-magukat. IaaS-megoldások jobban fejlesztői integrálása az Azure felügyeleti és az ügyfél verziójával kapcsolatos elvárások, ami bizonyos Azure összetevők. Azt jelzi, pontosabban a fejlesztők használjon-e a biztonságos kulcs tárolási megoldás biztosítása, valamint hogy következetes kulcskezelési beállításokat az Azure platform szolgáltatásaiból vele az Azure Key Vault szolgáltatás. Emellett egyéni megoldások által használandó Azure-szolgáltatás identitások felügyelt szolgáltatásfiókok titkosítási kulcsok eléréséhez engedélyezése. Az Azure Key Vault és a szolgáltatás-identitások felügyelt fejlesztői információkhoz lásd: a megfelelő SDK-k.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure-erőforrás szolgáltatók titkosítás modell támogatása

Microsoft Azure-szolgáltatások minden támogatásához legalább egy, a többi modellek titkosítását. Bizonyos szolgáltatások esetén azonban egy vagy több, a titkosítási modellek nem vonatkoznak. Emellett a szolgáltatások előfordulhat, hogy kibocsátási különböző ütemezéseket, ezek a forgatókönyvek támogatása. Ez a szakasz ismerteti az egyes a fő az Azure data tárolószolgáltatások írásának időpontjában rest terméktámogatási a titkosítás.

### <a name="azure-disk-encryption"></a>Az Azure lemeztitkosítás

Minden ügyfél Azure infrastruktúra (IaaS) szolgáltatás használatával szolgáltatások érhető el az infrastruktúra-szolgáltatási virtuális gépeket és a lemezek keresztül Azure Disk Encryption titkosítását. További információ az Azure lemezen titkosítási: a [Azure Disk Encryption dokumentáció](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure Storage

Az Azure Blob, és a fájl titkosítását támogatja a kiszolgálóoldali titkosított forgatókönyvek, valamint a titkosított adatok (ügyféloldali titkosítás).

- Kiszolgálóoldali: az Azure blob storage használatával az ügyfelek titkosítását az Azure storage erőforrás számlára engedélyezheti. Az alkalmazás egyszer engedélyezett kiszolgálóoldali titkosítás átlátható módon történik. Lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](https://docs.microsoft.com/azure/storage/storage-service-encryption) további információt.
- Ügyféloldali: az Azure BLOB ügyféloldali titkosítás használata támogatott. Ha ügyféloldali titkosítás ügyfelek használatával titkosítja az adatokat és az adatokat egy titkosított blob feltöltése. Az ügyfél kulcskezelés végezhető el. Lásd: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) további információt.


#### <a name="sql-azure"></a>SQL Azure

Az SQL Azure jelenleg titkosítását a Microsoft által felügyelt szolgáltatás és az ügyféloldali titkosítás forgatókönyvek.

Támogatja a kiszolgáló titkosítási keresztül az átlátható adattitkosítási nevű SQL-funkció jelenleg elérhető. Ha egy SQL Azure-ügyfelünkkel lehetővé teszi, hogy TDE kulcs automatikusan létrehozása és kezelése a számukra. Titkosítását az adatbázis és a kiszolgáló szintjén engedélyezhető. Től június 2017 [átlátszó Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) alapértelmezés szerint az újonnan létrehozott adatbázisok engedélyezve lesz.

Ügyféloldali titkosítás az SQL Azure adatok támogatott keresztül a [mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx) szolgáltatás. Mindig titkosított kulcsot használ, amely a létrehozott és tárolt az ügyfél által. Az ügyfelek a fő oszlopkulcs tárolhat Windows tanúsítványtárolót, az Azure Key Vault vagy helyi hardveres biztonsági modult. SQL Server Management Studio használatával, SQL-felhasználók eldönthetik, milyen kulcs titkosítására melyik oszlop szeretnének.

|                                  |                |                     | **Titkosítási modell**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Ügyfél** |
|                                  | **Kulcskezelés** | **Szolgáltatás felügyelt kulcs** | **A Key vaultban felügyelt ügyfél** | **Helyszíni felügyelt ügyfél** |        |
| **Tárolás és adatbázisok**            |                |                     |                              |                              |        |
| Lemez (IaaS)                      |                | -                   | Igen                          | Igen*                         | -      |
| SQL Server (IaaS)                |                | Igen                 | Igen                          | Igen                          | Igen    |
| Az SQL Azure (PaaS)                 |                | Igen                 | Előzetes verzió                      | -                            | Igen    |
| Az Azure Storage (blokk/Lapblobokat) |                | Igen                 | Előzetes verzió                      | -                            | Igen    |
| Az Azure Storage (fájlok)            |                | Igen                 | -                            | -                            | -      |
| Az Azure Storage (táblák, üzenetsorok)   |                | -                   | -                            | -                            | Igen    |
| A cosmos DB (dokumentum DB)          |                | Igen                 | -                            | -                            | -      |
| StorSimple                       |                | Igen                 | -                            | -                            | Igen    |
| Biztonsági mentés                           |                | -                   | -                            | -                            | Igen    |
| **Az eszközintelligencia és az elemzések**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Igen                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Előzetes verzió                      | -                            | -      |
| Azure Stream Analytics           |                | Igen                 | -                            | -                            | -      |
| HDInsights (az Azure Blob-tároló)  |                | Igen                 | -                            | -                            | -      |
| HDInsights (Data Lake-tároló)   |                | Igen                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Igen                 | Igen                          | -                            | -      |
| Azure Data Catalog               |                | Igen                 | -                            | -                            | -      |
| Power BI                         |                | Igen                 | -                            | -                            | -      |
| **IoT-szolgáltatásaival**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Igen    |
| Service Bus                      |                | Igen (prémium csomagban)              | -                            | -                            | Igen    |
| Event Hubs                       |                | Igen             | -                            | -                            | -      |


## <a name="conclusion"></a>Összegzés

Azure Services belül tárolt ügyféladatok védelméről van kiemelkedő fontosságú a Microsoftnak. Minden Azure üzemeltetett szolgáltatások elkötelezettek vagyunk a titkosítás, Rest-beállítások. Például az Azure tárolás, az SQL Azure és a kulcs analytics eligazodást szolgáltatásokat és az eszközintelligencia már titkosítást biztosíthat, Rest-beállítások. Ezen szolgáltatások ellenőrzött felhasználói kulcsok és a ügyféloldali titkosítás támogatása, valamint a felügyelt kulcsok és a titkosítási szolgáltatás. Microsoft Azure-szolgáltatások rendszer körben javítja a titkosítási többi rendelkezésre álló, illetve új beállítások előzetes és általánosan rendelkezésre álló tervezett jövőbeli hónapban.

