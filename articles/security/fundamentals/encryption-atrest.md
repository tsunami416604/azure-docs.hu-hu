---
title: Adattitkosítás Microsoft Azure-at-Rest | Microsoft Docs
description: Ez a cikk áttekintést nyújt a REST-alapú adattitkosításról, a teljes képességekről és az általános szempontokról Microsoft Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2019
ms.author: barclayn
ms.openlocfilehash: 0fddabc7973fbea147de9f3b6e6da5556407030c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498456"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-beli adatok titkosítása – Rest

Microsoft Azure olyan eszközöket tartalmaz, amelyek a vállalat biztonsági és megfelelőségi igényeinek megfelelően védik az adatvédelmet. A tanulmány a következőkre összpontosít:

- Hogyan védi az adatok az egész Microsoft Azure
- Az adatvédelmi implementációban részt vevő különböző összetevőkről tárgyal
- A különböző kulcskezelő védelmi megközelítések előnyeit és hátrányait tekinti át.

A inaktív adatok titkosítása gyakori biztonsági követelmény. Az Azure-ban a szervezetek egy egyéni kulcskezelő megoldás kockázata vagy díja nélkül titkosíthatók az adatok. A szervezetek lehetővé teszik, hogy az Azure teljes mértékben kezelhesse a titkosítást a nyugalmi állapotban. Emellett a szervezetek különböző lehetőségekkel rendelkeznek a titkosítási vagy titkosítási kulcsok szoros kezeléséhez.

## <a name="what-is-encryption-at-rest"></a>Mi a titkosítás a REST-ben?

A nyugalmi állapotban lévő titkosítás az adatok kódolása (titkosítása), amikor megmarad. Az Azure REST-alapú titkosítása szimmetrikus titkosítást használ a nagy adatmennyiségek gyors titkosításához és visszafejtéséhez egy egyszerű elméleti modell szerint:

- A szimmetrikus titkosítási kulcs használatával titkosíthatja az adattárakat a tárolóba való írás során.
- Ugyanazt a titkosítási kulcsot használja a rendszer a memóriában való használathoz readied.
- Előfordulhat, hogy az adatpartíciók particionálva vannak, és az egyes partíciók esetében különböző kulcsok is használhatók.
- A kulcsokat biztonságos helyen kell tárolni, az identitás-alapú hozzáférés-vezérléssel és a naplózási házirendekkel. Az adattitkosítási kulcsokat gyakran titkosítja Azure Key Vault a hozzáférés további korlátozása érdekében a kulcs titkosítási kulcsával.

A gyakorlatban a kulcsfontosságú felügyeleti és ellenőrzési forgatókönyvek, valamint a méretezési és rendelkezésre állási garanciák további szerkezeteket igényelnek. Az alábbiakban részletesen ismertetjük Microsoft Azure titkosítást a REST-fogalmakon és-összetevőkön.

## <a name="the-purpose-of-encryption-at-rest"></a>A titkosítás célja a nyugalmi állapotban

A inaktív adatok titkosítása adatvédelmet biztosít a tárolt adatok számára (nyugalmi állapotban). A REST-adatok elleni támadások közé tartozik az adatok tárolására szolgáló hardver fizikai hozzáférésének beszerzésére irányuló kísérlet, majd a benne foglalt adatok biztonsága. Ilyen támadás esetén előfordulhat, hogy a kiszolgáló merevlemeze helytelenül lett kezelve a karbantartás során, ami lehetővé teszi a támadó számára a merevlemez eltávolítását. Később a támadó a vezérlőbe helyezi a merevlemezt, hogy megpróbáljon hozzáférni az adathoz.

A inaktív adatok titkosítása úgy lett kialakítva, hogy a támadók ne férhessenek hozzá a titkosítatlan adatokhoz, mivel a lemezeken titkosítva vannak az adatok. Ha egy támadó titkosított adattal rendelkező merevlemezt szerez be, de nem a titkosítási kulcsokat, a támadónak le kell győznie a titkosítást az információk olvasásához. Ez a támadás sokkal összetettebb és erőforrás-felhasználást igényel, mint a titkosítatlan adatok merevlemezen való elérése. Emiatt a inaktív adatok titkosítása erősen ajánlott, és nagy prioritású követelmény számos szervezet számára.

A munkahelyen történő titkosításhoz a szervezetnek adatirányítási és megfelelőségi erőfeszítésekre is szüksége lehet. Az iparági és kormányzati rendeletek, például a HIPAA, a PCI és a FedRAMP, az adatvédelem és a titkosítási követelményekkel kapcsolatos konkrét óvintézkedéseket határoznak meg. A inaktív adatok titkosítása egy kötelező mérték, amely megfelel ezeknek a szabályozásoknak.

A megfelelőségi és szabályozási követelmények teljesítése mellett a inaktív titkosítás a védelem részletes védelmét is biztosítja. A Microsoft Azure megfelelő platformot biztosít a szolgáltatások, az alkalmazások és az adatkezeléshez. Emellett átfogó lehetőséget és fizikai biztonságot, adathozzáférés-vezérlést és auditálást is biztosít. Fontos azonban a további "átfedés" biztonsági intézkedések biztosítása abban az esetben, ha az egyik másik biztonsági intézkedés meghiúsul, és a titkosítás inaktív állapotban van.

A Microsoft elkötelezte magát a különböző Cloud Services-beli Rest-lehetőségek titkosítására, és lehetővé teszi az ügyfeleknek a titkosítási kulcsok és a kulcsfontosságú használati naplók felügyeletét. Emellett a Microsoft az összes vásárlói adatok alapértelmezés szerint történő titkosítására is törekszik.

## <a name="azure-encryption-at-rest-components"></a>Azure encryption a REST-összetevőknél

A korábban leírtaknak megfelelően a titkosítás célja, hogy a lemezen tárolt adatok titkos titkosítási kulccsal legyenek titkosítva. Ennek a célnak a biztosításához meg kell adni a titkosítási kulcsok létrehozását, tárolását, hozzáférés-vezérlését és felügyeletét. Bár a részletek eltérőek lehetnek, az Azure-szolgáltatások titkosítása Rest-implementációkban az alábbi ábrán látható feltételek szerint olvasható.

![Összetevők](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A titkosítási kulcsok és az azokhoz való hozzáférés-vezérlés tárolási helye a REST-modellben lévő titkosítási kulcs központi része. A kulcsoknak nagyon biztonságosnek kell lenniük, de a megadott felhasználók kezelhetik, és bizonyos szolgáltatások számára elérhetők. Az Azure-szolgáltatások esetében Azure Key Vault az ajánlott kulcstároló-megoldás, amely általános felügyeleti élményt nyújt a szolgáltatások között. A kulcsok tárolása és kezelése a kulcstartókban történik, és a Key vaulthoz való hozzáférés a felhasználók vagy szolgáltatások számára adható meg. Azure Key Vault támogatja a kulcsok ügyfelek általi létrehozását, illetve az ügyfelek által felügyelt titkosítási kulcsokban való használatra való adatimportálást.

### <a name="azure-active-directory"></a>Azure Active Directory

A Azure Key Vaultban tárolt kulcsok használatára vonatkozó engedélyek, amelyek a titkosításhoz vagy a titkosításhoz való hozzáféréshez szükségesek a REST-titkosításhoz és a visszafejtéshez Azure Active Directory fiókok számára is.

### <a name="key-hierarchy"></a>Kulcs hierarchiája

A REST-implementációban egynél több titkosítási kulcs van használatban. A titkosítási kulcs tárolása Azure Key Vault biztosítja a biztonságos kulcs-hozzáférést és a kulcsok központi felügyeletét. Azonban a szolgáltatás helyi hozzáférése a titkosítási kulcsokhoz hatékonyabb a tömeges titkosításhoz és a visszafejtéshez, mint a Key Vault összes adatművelettel való interakciója, ami erősebb titkosítást és jobb teljesítményt tesz lehetővé. Egyetlen titkosítási kulcs használatának korlátozása csökkenti annak a kockázatát, hogy a kulcs biztonsága sérül, valamint a kulcs cseréjének újratitkosításának költségeit. Az Azure-beli titkosítások a REST-modellekben a következő típusú kulcsokból állnak, hogy az összes ilyen szükségletet kezelni lehessen:

- **Adattitkosítási kulcs (adattitkosítási kulcsot)** – egy partíció vagy adatblokk titkosításához használt szimmetrikus AES256-kulcs.  Egyetlen erőforrás több partícióval és számos adattitkosítási kulccsal is rendelkezhet. Az egyes adatblokkok egy másik kulccsal való titkosítása nehezebbé teszi a titkosítási elemzési támadásokat. A DEKs való hozzáférésre az erőforrás-szolgáltató vagy az alkalmazás-példány szükséges, amely egy adott blokk titkosítását és visszafejtését végzi. Ha egy ADATTITKOSÍTÁSI kulcsot egy új kulccsal van lecserélve, csak a hozzá tartozó blokkban lévő adatmennyiséget kell újra titkosítani az új kulccsal.
- **Kulcs titkosítási kulcsa (KEK)** – az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. Olyan kulcs-titkosítási kulcs használata, amely soha nem hagy Key Vault lehetővé teszi, hogy az adattitkosítási kulcsok titkosítva és szabályozva legyenek. Előfordulhat, hogy a KEK-hez hozzáférő entitás nem azonos a ADATTITKOSÍTÁSI kulcsot igénylő entitással. Az entitások a ADATTITKOSÍTÁSI kulcsot való hozzáférést az egyes ADATTITKOSÍTÁSI kulcsot adott partícióhoz való hozzáférésének korlátozására használhatják. Mivel a KEK a DEKs visszafejtéséhez szükséges, a KEK gyakorlatilag egyetlen pont, amellyel a DEKs hatékonyan törölhető a KEK törlésével.

A titkosítási kulcsokkal titkosított adattitkosítási kulcsokat a rendszer külön tárolja, és csak a kulcs titkosítási kulcshoz hozzáféréssel rendelkező entitások tudják visszafejteni ezeket az adattitkosítási kulcsokat. A Key Storage különböző modelljei támogatottak. Az egyes modelleket a következő szakasz későbbi részében részletesebben ismertetjük.

## <a name="data-encryption-models"></a>Adattitkosítási modellek

A különböző titkosítási modellek és azok előnyeinek és hátrányainak megértése elengedhetetlen ahhoz, hogy a különböző Azure-beli erőforrás-szolgáltatók hogyan implementálják a titkosítást a nyugalmi állapotban. Ezek a definíciók az Azure összes erőforrás-szolgáltatóján megoszthatók a közös nyelv és taxonómia biztosítása érdekében.

A kiszolgálóoldali titkosításnak három forgatókönyve van:

- Kiszolgálóoldali titkosítás a szolgáltatás által felügyelt kulcsokkal
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - A Microsoft kezeli a kulcsokat
  - Teljes körű Felhőbeli funkciók

- Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsok használatával Azure Key Vault
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - Az ügyfelek Azure Key Vaulton keresztül vezérlik a kulcsokat
  - Teljes körű Felhőbeli funkciók

- Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsokkal az ügyfél által vezérelt hardveren
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - Az ügyfél által vezérelt hardveren az ügyfelek vezérlik a kulcsokat
  - Teljes körű Felhőbeli funkciók

Ügyféloldali titkosítás esetén vegye figyelembe a következőket:

- Az Azure-szolgáltatások nem látják a visszafejtett információt
- Az ügyfelek a helyszínen (vagy más biztonságos áruházakban) kezelhetik és tárolják a kulcsokat. A kulcsok nem érhetők el az Azure-szolgáltatások számára
- Csökkentett Felhőbeli funkciók

Az Azure-ban támogatott titkosítási modellek két fő csoportra oszlanak: az "ügyfél-titkosítás" és a "kiszolgálóoldali titkosítás", ahogy korábban említettük. A REST-modellben használt titkosítástól függetlenül az Azure-szolgáltatások mindig a biztonságos átvitel, például a TLS vagy a HTTPS használatát ajánlják. Ezért a átvitel titkosítását az átviteli protokollnak kell megcéloznia, és nem lehet jelentős tényező annak meghatározásában, hogy melyik titkosítást használja a REST-modell.

### <a name="client-encryption-model"></a>Ügyfél-titkosítási modell

Az ügyfél-titkosítási modell az erőforrás-szolgáltatón vagy az Azure-on kívül a szolgáltatás vagy a hívó alkalmazás által végrehajtott titkosításra utal. A titkosítás az Azure-beli szolgáltatásalkalmazás vagy az ügyfél adatközpontjában futó alkalmazások által végezhető el. Mindkét esetben a titkosítási modell kihasználása esetén az Azure erőforrás-szolgáltató titkosított blobot kap az adattitkosítás bármilyen módon történő visszafejtése és a titkosítási kulcsok elérésének lehetősége nélkül. Ebben a modellben a kulcskezelő szolgáltatást/alkalmazást a Calling Service/Application végzi el, és az nem átlátható az Azure szolgáltatásban.

![Ügyfél](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Kiszolgálóoldali titkosítási modell

A kiszolgálóoldali titkosítási modellek az Azure-szolgáltatás által végrehajtott titkosításra vonatkoznak. Ebben a modellben az erőforrás-szolgáltató végrehajtja a titkosítási és a visszafejtési műveletet. Előfordulhat például, hogy az Azure Storage egyszerű szöveges műveletekben fogadja az adatgyűjtést, és belsőleg fogja végrehajtani a titkosítást és a visszafejtést. Előfordulhat, hogy az erőforrás-szolgáltató a Microsoft vagy az ügyfél által kezelt titkosítási kulcsokat használja a megadott konfigurációtól függően.

![Kiszolgáló](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Kiszolgálóoldali titkosítási kulcsok kezelési modelljei

A kiszolgálóoldali titkosítás a REST-modellekben a kulcskezelő megkülönböztető jellemzőit foglalja magában. Ide tartozik a titkosítási kulcsok létrehozásának helye és módja, valamint a hozzáférési modellek és a kulcsfontosságú rotációs eljárások.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás a szolgáltatás által felügyelt kulcsokkal

Sok ügyfél esetében az alapvető követelmény az, hogy az adatok titkosítva legyenek, amikor nyugalmi állapotban van. A szolgáltatás által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás lehetővé teszi ezt a modellt, mivel lehetővé teszi az ügyfeleknek az adott erőforrás (Storage-fiók, SQL-adatbázis stb.) megjelölését a titkosításhoz, és az összes kulcsfontosságú felügyeleti szempont, például a kulcs kiadása, elforgatása és biztonsági mentése a Microsoftnak . A REST titkosítást támogató legtöbb Azure-szolgáltatás általában támogatja ezt a modellt, amellyel kiszervezheti a titkosítási kulcsok kezelését az Azure-ba. Az Azure erőforrás-szolgáltató létrehozza a kulcsokat, elhelyezi őket a biztonságos tárolóban, és szükség esetén lekéri őket. Ez azt jelenti, hogy a szolgáltatásnak teljes hozzáférése van a kulcsokhoz, és a szolgáltatás teljes körű vezérléssel rendelkezik a hitelesítő adatok életciklusának kezelése során.

![felügyelt](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

A szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítás ezért gyors megoldást jelent a titkosítás nyugalmi állapotba való bevezetésének szükségessége az ügyfél számára alacsony terheléssel. Amikor egy ügyfél elérhetővé válik, általában megnyitja a cél-előfizetés és az erőforrás-szolgáltató Azure Portalét, és bejelöl egy mezőt, amely azt jelzi, hogy az adattitkosítást szeretné. Egyes erőforrás-kezelők kiszolgálóoldali titkosítása szolgáltatás által felügyelt kulcsokkal beállítás alapértelmezés szerint be van kapcsolva.

A Microsoft által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsok tárolásához és kezeléséhez. Előfordulhat, hogy egyes ügyfelek kezelhetik a kulcsokat, mivel úgy érzik, hogy nagyobb biztonságot nyújtanak, a modell kiértékelése során figyelembe kell venni az egyéni kulcsú tárolási megoldáshoz kapcsolódó költségeket és kockázatokat. Sok esetben a szervezet megállapíthatja, hogy az erőforrás-megszorítások vagy egy helyszíni megoldás kockázata nagyobb lehet, mint a REST-kulcsok titkosításának Felhőbeli felügyeletének kockázata.  Előfordulhat azonban, hogy ez a modell nem elegendő olyan szervezeteknek, amelyek a titkosítási kulcsok létrehozását vagy életciklusát szabályozzák, vagy ha a különböző személyzet kezeli a szolgáltatás titkosítási kulcsait, mint a szolgáltatás kezelése (azaz elkülönítés a szolgáltatás általános felügyeleti modelljéből származó kulcskezelő szolgáltatások esetében.

##### <a name="key-access"></a>Kulcs elérése

A szolgáltatás által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás használata esetén a kulcs létrehozása, tárolása és a szolgáltatáshoz való hozzáférés mind a szolgáltatás által felügyelt. Az alapszintű Azure-erőforrás-szolgáltatók jellemzően az adattitkosítási kulcsokat tárolják egy olyan tárolóban, amely az adatforráshoz közeledik, és gyorsan elérhető és hozzáférhető, miközben a kulcs titkosítási kulcsait egy biztonságos belső tároló tárolja.

**Előnyei**

- Egyszerű beállítás
- A Microsoft a legfontosabb rotációt, biztonsági mentést és redundanciát kezeli
- Az ügyfél nem rendelkezik a megvalósítással vagy egy egyéni kulcskezelő séma kockázatával.

**Hátrányai**

- A titkosítási kulcsok (a kulcs specifikációja, az életciklus, a visszavonás stb.) nem adhatók meg az ügyfelek felett.
- Nincs lehetőség a kulcsfontosságú felügyelet elkülönítésére a szolgáltatás általános felügyeleti modelljéből

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsok használatával Azure Key Vault

Olyan esetekben, amikor a követelmény az inaktív adatok titkosítása és a titkosítási kulcsok használata, a felhasználók a Key Vault felügyelt kulcsaival használhatják a kiszolgálóoldali titkosítást. Egyes szolgáltatások csak a legfelső szintű kulcs titkosítási kulcsát tárolhatják Azure Key Vaultban, és a titkosított adattitkosítási kulcsot egy belső helyen tárolják, amely közelebb áll az adatforgalomhoz. Ebben az esetben az ügyfelek saját kulcsaikat hozhatnak Key Vault (BYOK – Bring Your Own Key), vagy újakat generálnak, és a kívánt erőforrások titkosítására használhatják őket. Amíg az erőforrás-szolgáltató végrehajtja a titkosítási és a visszafejtési műveleteket, a konfigurált kulcs titkosítási kulcsát használja az összes titkosítási művelet legfelső szintű kulcsaként.

A kulcs titkosítási kulcsainak elvesztése adatvesztést jelent. Emiatt a kulcsokat nem szabad törölni. A kulcsok létrehozásakor vagy elforgatásakor biztonsági mentést kell készíteni. A helyreállítható [törlést](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) engedélyezni kell minden olyan tárolón, amely a kulcs titkosítási kulcsait tárolja. A kulcs törlése helyett állítsa a beállítást hamis értékre, vagy állítsa be a lejárati dátumot.

##### <a name="key-access"></a>Kulcs elérése

A Azure Key Vault ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítási modell magában foglalja a kulcsoknak a titkosításhoz és a visszafejtéshez szükséges hozzáférését a szolgáltatáshoz. A REST kulcsok titkosítása hozzáférés-vezérlési házirend segítségével érhető el a szolgáltatás számára. Ez a szabályzat biztosítja, hogy a szolgáltatás identitása hozzáférjen a kulcs fogadásához. Egy társított előfizetés nevében futó Azure-szolgáltatás konfigurálható identitással az előfizetésben. A szolgáltatás elvégezheti Azure Active Directory hitelesítését, és olyan hitelesítési tokent kap, amely magát az előfizetés nevében működő szolgáltatásként azonosítja. Ezt a tokent ezután megtekintheti Key Vault egy olyan kulcs beszerzéséhez, amelyhez hozzáférést kapott.

A titkosítási kulcsokat használó műveletek esetében a szolgáltatás identitása a következő műveletek bármelyikéhez biztosítható: visszafejtés, titkosítás, unwrapKey, wrapKey, ellenőrzés, aláírás, beolvasás, Listázás, frissítés, létrehozás, importálás, törlés, biztonsági mentés és visszaállítás.

Az adatok titkosítása vagy visszafejtése során használt kulcs beszerzéséhez a Resource Manager-szolgáltatás példányának futtatásához szükséges UnwrapKey (a visszafejtési kulcs lekérése) és a WrapKey (kulcs behelyezése a Key vaultba új kulcs létrehozásakor).

>[!NOTE]
>Az Key Vault engedélyezésével kapcsolatos további információkért tekintse meg a [Azure Key Vault dokumentációjában](../../key-vault/key-vault-secure-your-key-vault.md)a Key Vault biztonságossá tétele című oldalt.

**Előnyei**

- A használt kulcsok teljes körű vezérlése – a titkosítási kulcsok kezelése az ügyfél Key Vault az ügyfél vezérlése alatt történik.
- Több szolgáltatás titkosításának lehetősége egyetlen főkiszolgálóra
- A szolgáltatás általános felügyeleti modelljéből elkülönítheti a kulcskezelő szolgáltatást
- Meghatározhatja a szolgáltatás és a kulcs helyét a régiók között

**Hátrányai**

- Az ügyfél teljes felelősséggel rendelkezik a kulcs-hozzáférés kezeléséhez
- Az ügyfél teljes felelősséggel rendelkezik a fő életciklus-felügyelethez
- További beállítás & konfiguráció terhelése

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsokkal az ügyfél által vezérelt hardverben

Egyes Azure-szolgáltatások lehetővé teszik a saját kulcsú (HYOK) kulcskezelő modell üzemeltetését. Ez a felügyeleti mód olyan forgatókönyvekben hasznos, ahol szükség van az inaktív adatok titkosítására és a Microsoft vezérlőn kívüli védett adattárban található kulcsok kezelésére. Ebben a modellben a szolgáltatásnak egy külső helyről kell lekérnie a kulcsot. A teljesítményre és a rendelkezésre állásra vonatkozó garanciák hatással vannak, és a konfiguráció összetettebb. Továbbá, mivel a szolgáltatás a titkosítási és a visszafejtési műveletek során is hozzáfér a ADATTITKOSÍTÁSI kulcsot, a modell általános biztonsági garanciái hasonlóak ahhoz, amikor a kulcsokat ügyfél által felügyelik Azure Key Vaultban.  Ennek eredményeképpen ez a modell nem felel meg a legtöbb szervezet számára, kivéve, ha meghatározott kulcskezelő követelményekkel rendelkeznek. A korlátozások miatt a legtöbb Azure-szolgáltatás nem támogatja a kiszolgálóoldali titkosítást a kiszolgáló által felügyelt kulcsok használatával az ügyfél által vezérelt hardverben.

##### <a name="key-access"></a>Kulcs elérése

Ha az ügyfél által vezérelt hardverben a szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítást használja, a rendszer a kulcsokat az ügyfél által konfigurált rendszeren tartja karban. A modellt támogató Azure-szolgáltatások biztosítják a biztonságos kapcsolódást az ügyfél által megadott kulcstárolóhoz.

**Előnyei**

- Teljes hozzáférés a használt legfelső szintű kulcshoz – a titkosítási kulcsokat egy ügyfél által biztosított tároló kezeli
- Több szolgáltatás titkosításának lehetősége egyetlen főkiszolgálóra
- A szolgáltatás általános felügyeleti modelljéből elkülönítheti a kulcskezelő szolgáltatást
- Meghatározhatja a szolgáltatás és a kulcs helyét a régiók között

**Hátrányai**

- A kulcsok tárolásának, biztonságának, teljesítményének és rendelkezésre állásának teljes felelőssége
- A kulcsok hozzáférés-kezelésének teljes felelőssége
- A fő életciklus-felügyelet teljes felelőssége
- Jelentős telepítési, konfigurálási és folyamatos karbantartási költségek
- A hálózat rendelkezésre állásának növelése az ügyfél-adatközpont és az Azure-adatközpontok között.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Inaktív adatok titkosítása a Microsoft Cloud Servicesben

Microsoft Cloud szolgáltatásokat mindhárom Felhőbeli modellben használják: IaaS, Pásti, SaaS. Az alábbiakban néhány példát láthat arra, hogyan illeszkednek az egyes modellekhez:

- A szoftver, amelyet kiszolgálóként vagy SaaS-ként, a felhő által biztosított alkalmazással, például az Office 365-vel biztosítanak.
- Azok a platform-szolgáltatások, amelyekkel az ügyfelek a felhőt használják alkalmazásaikban, például a Storage, az Analytics és a Service Bus funkcióinak használatával.
- Infrastruktúra-szolgáltatások vagy infrastruktúra-szolgáltatás (IaaS), amelyben az ügyfél üzembe helyezi a felhőben üzemeltetett operációs rendszereket és alkalmazásokat, és esetleg más felhőalapú szolgáltatásokat hasznosít.

### <a name="encryption-at-rest-for-saas-customers"></a>Inaktív adatok titkosítása SaaS-ügyfelek számára

A szolgáltatott szoftverként (SaaS) használó ügyfelek jellemzően inaktív titkosítással rendelkeznek, vagy az egyes szolgáltatásokban elérhetők. Az Office 365 számos lehetőséget kínál az ügyfelek számára, hogy ellenőrizze vagy engedélyezze a titkosítást a nyugalmi állapotban. Az Office 365-szolgáltatásokkal kapcsolatos információkért lásd: [titkosítás az office 365-ben](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Inaktív titkosítás a Pásti-ügyfelek számára

A szolgáltatásként szolgáló platform (a Pásti) ügyfeleinek általában egy tárolási szolgáltatásban (például Blob Storage) vannak tárolva, de az alkalmazás-végrehajtási környezetben (például egy virtuális gépen) is gyorsítótárazva lehet vagy tárolhatók. Ha szeretné megtekinteni a titkosítást a rendelkezésre álló Rest-beállítások között, vizsgálja meg az alábbi táblázatot a használt tároló-és alkalmazás-platformok esetében.

### <a name="encryption-at-rest-for-iaas-customers"></a>Inaktív adatok titkosítása IaaS-ügyfelek számára

Az infrastruktúra-szolgáltatás (IaaS) ügyfelei számos szolgáltatást és alkalmazást használhatnak. A IaaS Services lehetővé teszi, hogy az Azure-ban üzemeltetett virtuális gépeken és a virtuális merevlemezeken is engedélyezze a titkosítást Azure Disk Encryption használatával.

#### <a name="encrypted-storage"></a>Titkosított tároló

A Pástihoz hasonlóan a IaaS-megoldások más Azure-szolgáltatásokat is használhatnak, amelyek a nyugalmi állapotban titkosított adatok tárolására használhatók. Ezekben az esetekben engedélyezheti a titkosítást az összes felhasznált Azure-szolgáltatás által biztosított Rest-támogatásban. Az alábbi táblázat felsorolja a főbb tárolási, szolgáltatás-és alkalmazás-platformokat, valamint a REST titkosítású modellt. 

#### <a name="encrypted-compute"></a>Titkosított számítás

A Managed Disks, a pillanatképek és a képek a szolgáltatás által felügyelt kulccsal vannak titkosítva Storage Service Encryption használatával. A REST-alapú teljes titkosítási megoldás biztosítja, hogy az adatok soha ne legyenek megmaradva titkosítatlan formában. A virtuális gépek adatainak feldolgozásakor a Windows-lapozófájl vagy a Linux-lapozófájl, egy összeomlási memóriakép vagy egy alkalmazásnapló is megmarad. Annak biztosítása érdekében, hogy az adatok titkosítva legyenek, a IaaS-alkalmazások az Azure IaaS virtuális gépen (Windows vagy Linux) és virtuális lemezen is használhatják a Azure Disk Encryption.

#### <a name="custom-encryption-at-rest"></a>Egyéni titkosítás nyugalmi állapotban

Azt javasoljuk, hogy ha lehetséges, a IaaS alkalmazások Azure Disk Encryption és titkosítást használjanak a felhasznált Azure-szolgáltatások által biztosított Rest-beállításokban. Bizonyos esetekben – például a szabálytalan titkosítási követelmények vagy a nem Azure-alapú tárolás – esetében előfordulhat, hogy egy IaaS-alkalmazás fejlesztője maga is implementálja a titkosítást. A IaaS-megoldások fejlesztői a bizonyos Azure-összetevők kihasználása révén jobban integrálhatók az Azure felügyeleti és vevői elvárásaival. Konkrétan a fejlesztőknek az Azure Key Vault szolgáltatást kell használniuk a biztonságos kulcstároló biztosításához, valamint az ügyfeleket a legtöbb Azure platform-szolgáltatáshoz tartozó konzisztens felügyeleti lehetőségekkel. Emellett az egyéni megoldásoknak az Azure által felügyelt szolgáltatásbeli identitásokkal kell rendelkezniük, hogy a szolgáltatásfiókok hozzáférjenek a titkosítási kulcsokhoz. A Azure Key Vault-és felügyelt szolgáltatásbeli identitásokkal kapcsolatos fejlesztői információkért tekintse meg a megfelelő SDK-kat.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure Resource Providers titkosítási modell támogatása

Microsoft Azure szolgáltatások a REST-modellekben egy vagy több titkosítást támogatnak. Egyes szolgáltatások esetében azonban előfordulhat, hogy egy vagy több titkosítási modell nem alkalmazható. Az ügyfél által felügyelt kulcsfontosságú forgatókönyveket támogató szolgáltatások esetében csak a kulcs titkosítási kulcsainak Azure Key Vault által támogatott kulcstárolók egy részhalmazát támogatják. Emellett a szolgáltatások a különböző időpontokban is támogathatják az ilyen forgatókönyvek és a kulcsfontosságú típusok támogatását. Ez a szakasz a nagy Azure-beli adattárolási szolgáltatásokra vonatkozó írás időpontjában a REST-támogatás titkosítását ismerteti.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Az Azure-alapú infrastruktúra-szolgáltatás (IaaS) funkcióit használó bármely ügyfél a IaaS-alapú virtuális gépek és Azure Disk Encryption lemezek számára is lehetővé teheti a titkosítást. Az Azure Disk Encryption szolgáltatással kapcsolatos további információkért tekintse meg a [Azure Disk Encryption dokumentációját](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Azure Storage-tárterület

Az összes Azure Storage-szolgáltatás (blob Storage, üzenetsor-tárolás, Table Storage és Azure Files) támogatja a kiszolgálóoldali titkosítást a nyugalmi állapotban; néhány szolgáltatás emellett az ügyfél által felügyelt kulcsokat és az ügyféloldali titkosítást is támogatja. 

- Kiszolgálóoldali: minden Azure Storage-szolgáltatás alapértelmezés szerint a szolgáltatás által felügyelt kulcsok használatával engedélyezi a kiszolgálóoldali titkosítást, amely transzparens az alkalmazás számára. További információ: [Azure Storage Service encryption for](../../storage/common/storage-service-encryption.md)inaktív adatok. Az Azure Blob Storage és a Azure Files támogatja az RSA 2048 bites ügyfél által felügyelt kulcsokat Azure Key Vault. További információ: [Storage Service encryption ügyfél által felügyelt kulcsok használata Azure Key Vaultban](../../storage/common/storage-encryption-keys-portal.md).
- Ügyféloldali: az Azure-Blobok,-táblák és-várólisták támogatják az ügyféloldali titkosítást. Az ügyféloldali titkosítás használatakor az ügyfelek titkosítják az adataikat, és feltöltik az adataikat titkosított blobként. A kulcskezelőt az ügyfél végzi. További információ: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

A Azure SQL Database jelenleg a Microsoft által felügyelt szolgáltatás-és ügyféloldali titkosítási helyzetekben támogatja a REST titkosítást.

A kiszolgálói titkosítás támogatását jelenleg a transzparens adattitkosítás nevű SQL-szolgáltatás nyújtja. Ha egy Azure SQL Database ügyfél lehetővé teszi, hogy a rendszer automatikusan létrehozza és felügyeli a TDE kulcsot. A inaktív adatok titkosítása az adatbázis és a kiszolgáló szintjén is engedélyezhető. A 2017. júniusi [transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) alapértelmezés szerint engedélyezve van az újonnan létrehozott adatbázisokon. Azure SQL Database támogatja az RSA 2048-bites ügyfél által felügyelt kulcsokat Azure Key Vault. További információ: [transzparens adattitkosítás bring your own Key támogatással a Azure SQL Database és az Adattárházhoz](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

A Azure SQL Database-adat ügyféloldali titkosítása támogatott a [Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funkción keresztül. A Always Encrypted az ügyfél által létrehozott és tárolt kulcsot használja. Az ügyfelek a főkulcsot egy Windows tanúsítványtárolóban, Azure Key Vaultban vagy egy helyi hardveres biztonsági modulban tárolhatják. A SQL Server Management Studio használatával az SQL-felhasználók kiválaszthatják, hogy milyen kulcsot szeretnének használni az oszlop titkosításához.

#### <a name="encryption-model-and-key-management-table"></a>Titkosítási modell és kulcskezelő tábla

|                                  |                    | **Titkosítási modell és kulcskezelő** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Kiszolgálóoldali szolgáltatás által felügyelt kulcs használata**     | **Ügyfél által felügyelt kulcs kiszolgálóoldali használata**             | **Ügyfél által felügyelt kulcs ügyféloldali használata**      |
| **AI és Machine Learning**      |                    |                    |                    |
| Azure Search                     | Igen                | Előzetes verzió            | -                  |
| Azure Machine Learning   | Igen                | -                  | -                  |
| Azure Machine Learning Studio    | Igen                | Előzetes verzió, RSA 2048 bites | -               |
| Power BI                         | Igen                | Előzetes verzió, RSA 2048 bites | -                  |
| **Elemzés**                    |                    |                    |                    |
| Azure Stream Analytics           | Igen                | -                  | -                  |
| Azure Event Hubs-eseményközpontok                       | Igen                | Előzetes verzió, az összes RSA-hossz. | -                  |
| Azure Analysis Services          | Igen                | -                  | -                  |
| Azure Data Catalog               | Igen                | -                  | -                  |
| Apache Kafka az Azure HDInsight  | Igen                | Az összes RSA-hossz.   | -                  |
| Azure Data Explorer              | Igen                | -                  | -                  |
| Azure Data Factory               | Igen                | Igen                | -                  |
| Azure Data Lake Store            | Igen                | Igen, RSA 2048-bit  | -                  |
| **Tárolók**                   |                    |                    |                    |
| Azure Kubernetes Service         | Igen                | -                  | -                  |
| Container Registry               | Igen                | -                  | -                  |
| **Számítás**                      |                    |                    |                    |
| Virtuális gépek                 | Igen                | Igen, RSA 2048-bit  | -                  |
| Virtuálisgép-méretezési csoport        | Igen                | Igen, RSA 2048-bit  | -                  |
| SAP HANA                         | Igen                | Igen, RSA 2048-bit  | -                  |
| **Adatbázisok**                    |                    |                    |                    |
| SQL Server on Virtual Machines   | Igen                | Igen, RSA 2048-bit  | Igen                |
| Azure SQL Database               | Igen                | Igen, RSA 2048-bit  | Igen                |
| MariaDB Azure SQL Database   | Igen                | -                  | -                  |
| MySQL-Azure SQL Database     | Igen                | -                  | -                  |
| Azure SQL Database PostgreSQL-hez | Igen                | -                  | -                  |
| Azure SQL Data Warehouse         | Igen                | Igen, RSA 2048-bit  | Igen                |
| SQL Server Stretch Database      | Igen                | Igen, RSA 2048-bit  | Igen                |
| Table Storage                    | Igen                | -                  | Igen                |
| Azure Cosmos DB                  | Igen                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Igen                | -                  | Igen                |
| Azure Repos                      | Igen                | -                  | Igen                |
| **Identitáskezelés**                     |                    |                    |                    |
| Azure Active Directory           | Igen                | -                  | -                  |
| Azure Active Directory tartományi szolgáltatások | Igen          | Igen, RSA 2048-bit  | -                  |
| **Integrációs**                  |                    |                    |                    |
| Service Bus                      | Igen                | -                  | Igen                |
| Event Grid                       | Igen                | -                  | -                  |
| API Management                   | Igen                | -                  | -                  |
| **IoT szolgáltatások**                 |                    |                    |                    |
| IoT Hub                          | Igen                | -                  | Igen                |
| **Felügyelet és irányítás**    |                    |                    |                    |
| Azure Site Recovery              | Igen                | Igen, RSA 2048-bit  | Igen                |
| **Media**                        |                    |                    |                    |
| Media Services                   | Igen                | -                  | Igen                |
| **Tárolás**                      |                    |                    |                    |
| Blob Storage                     | Igen                | Igen, RSA 2048-bit  | Igen                |
| Lemezes tárolás                     | Igen                | -                  | -                  |
| Managed Disk Storage             | Igen                | -                  | -                  |
| File Storage                     | Igen                | Igen, RSA 2048-bit  | -                  |
| Queue Storage                    | Igen                | -                  | Igen                |
| Avere vFXT                       | Igen                | -                  | -                  |
| Azure NetApp Files               | Igen                | -                  | -                  |
| Archive Storage                  | Igen                | Igen, RSA 2048-bit  | -                  |
| StorSimple                       | Igen                | Igen, RSA 2048-bit  | Igen                |
| Azure Backup                     | Igen                | -                  | Igen                |
| Data Box                         | Igen                | -                  | Igen                |

## <a name="conclusion"></a>Összegzés

Az Azure-szolgáltatásokon belül tárolt ügyféladatok védelme kiemelkedő fontosságú a Microsoft számára. Az összes Azure-beli üzemeltetett szolgáltatás elkötelezett amellett, hogy titkosítást biztosítson a REST-beállításokban. Az Azure Storage, a Azure SQL Database és a Key Analytics és a hírszerző szolgáltatások olyan alapvető szolgáltatások, amelyek már biztosítják a titkosítást a REST-beállításokban. Ezen szolgáltatások némelyike az ügyfél által vezérelt kulcsokat és az ügyféloldali titkosítást, valamint a szolgáltatás által felügyelt kulcsokat és titkosítást is támogatja. A Microsoft Azure szolgáltatások széles körben javítják a titkosítást a REST rendelkezésre állása során, és az új beállítások az előzetes verzióra és az általános elérhetőségre vannak tervezve a következő hónapokban.
