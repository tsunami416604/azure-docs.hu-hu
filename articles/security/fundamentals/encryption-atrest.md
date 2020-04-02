---
title: Microsoft Azure data encryption-at-in-in | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Microsoft Azure inaktív adattitkosításáról, az általános képességekről és az általános szempontokról.
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 42b83963dc4996a7347d57be712451086fa79b26
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548630"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-adatok titkosítása in-rest

A Microsoft Azure olyan eszközöket is tartalmaz, amelyekkel a vállalat biztonsági és megfelelőségi igényeinek megfelelően védheti az adatokat. Ez a dokumentum a következőkre összpontosít:

- Az adatok védelme a Microsoft Azure-ban
- Ismerteti az adatvédelem végrehajtásában részt vevő különböző elemeket,
- Áttekinti a különböző kulcsfontosságú irányítási védelmi megközelítések előnyeit és hátrányait.

Az inaktív titkosítás gyakori biztonsági követelmény. Az Azure-ban a szervezetek titkosíthatja az inaktív adatokat az egyéni kulcskezelési megoldás kockázata vagy költsége nélkül. A szervezetek nek lehetősége van arra, hogy az Azure teljes mértékben kezelje a titkosítást inaktív szinten. Emellett a szervezetek különböző lehetőségekkel rendelkeznek a titkosítási vagy titkosítási kulcsok szoros kezelésére.

## <a name="what-is-encryption-at-rest"></a>Mi a titkosítás inaktív?

Az inaktív titkosítás az adatok kódolása (titkosítása) az adatok megőrzése esetén. Az Azure-ban a titkosítás imázsában lévő tervek szimmetrikus titkosítást használnak nagy mennyiségű adat gyors titkosításához és visszafejtéséhez egy egyszerű fogalmi modellnek megfelelően:

- A szimmetrikus titkosítási kulcs az adatok titkosítására szolgál, atároláshoz írt adatokkal.
- Ugyanazt a titkosítási kulcsot használják az adatok visszafejtésére, mivel azok a memóriában való használatra vannak újrahasználva.
- Az adatok particionálhatók, és minden partícióhoz különböző kulcsok használhatók.
- A kulcsokat biztonságos helyen kell tárolni identitásalapú hozzáférés-vezérlési és naplózási házirendekkel. Az adattitkosítási kulcsok gyakran titkosítva vannak egy kulcstitkosítási kulccsal az Azure Key Vaultban a hozzáférés további korlátozása érdekében.

A gyakorlatban a kulcskezelési és vezérlési forgatókönyvek, valamint a méretezési és rendelkezésre állási biztosítékok további konstrukciókat igényelnek. A Microsoft Azure inaktív titkosítási fogalmait és összetevőit az alábbiakban ismertetjük.

## <a name="the-purpose-of-encryption-at-rest"></a>A titkosítás célja inaktív

Az inaktív titkosítás biztosítja a tárolt adatok (nyugalmi) védelmét. Az inkontott adatok elleni támadások közé tartoznak az adatokat tároló hardverhez való fizikai hozzáférés, majd a tárolt adatok veszélyeztetése. Egy ilyen támadás során előfordulhat, hogy a kiszolgáló merevlemezét rosszul kezelték a karbantartás során, lehetővé téve a támadó számára a merevlemez eltávolítását. Később a támadó az irányításuk alatt álló számítógépbe helyezte a merevlemezt, hogy megpróbáljon hozzáférni az adatokhoz.

Az inaktív titkosítás célja, hogy megakadályozza, hogy a támadó hozzáférjen a titkosítatlan adatokhoz, biztosítva, hogy az adatok titkosítva legyenek a lemezen. Ha a támadó titkosított adatokat, de titkosítási kulcsokat nem kap merevlemezt, a támadónak le kell győznie a titkosítást az adatok olvasásához. Ez a támadás sokkal összetettebb és erőforrás-igényesebb, mint a titkosítatlan adatok elérése a merevlemezen. Emiatt az inaktív titkosítás erősen ajánlott, és számos szervezet számára kiemelt fontosságú követelmény.

Az inaktív titkosítást a szervezet adatirányítási és megfelelőségi erőfeszítéseinek szükségessége is megkövetelheti. Az olyan iparági és kormányzati szabályozások, mint a HIPAA, a PCI és a FedRAMP, konkrét biztosítékokat állapítanak meg az adatvédelmi és titkosítási követelményektekintetében. Az inaktív titkosítás kötelező intézkedés, amely e rendeletek némelyikének való megfeleléshez szükséges. A Microsoft FIPS 140-2-ellenőrzéssel kapcsolatos megközelítésével kapcsolatos további információkért lásd a [Federal Information Processing Standard (FIPS) 140-2 című kiadványát.](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2) 

A megfelelőségi és szabályozási követelmények nek való megfelelés mellett az inaktív titkosítás mélyreható védelmet nyújt. A Microsoft Azure megfelelő platformot biztosít a szolgáltatásokhoz, alkalmazásokhoz és adatokhoz. Átfogó eszköz- és fizikai biztonságot, adathozzáférés-vezérlést és naplózást is biztosít. Fontos azonban további "egymást átfedő" biztonsági intézkedéseket biztosítani abban az esetben, ha az egyéb biztonsági intézkedések valamelyike meghibásodik, és az inaktív titkosítás ilyen biztonsági intézkedést biztosít.

A Microsoft elkötelezett a felhőszolgáltatások inaktív titkosítási lehetőségei mellett, és lejáratja az ügyfeleknek a titkosítási kulcsok és a kulcshasználat naplóinak vezérlését. Emellett a Microsoft alapértelmezés szerint az összes inaktív ügyféladat titkosításán dolgozik.

## <a name="azure-encryption-at-rest-components"></a>Azure titkosítás a többi összetevőben

Ahogy azt korábban leírtuk, az inaktív titkosítás célja az, hogy a lemezen megőrzött adatok titkos titkosítással vannak titkosítva. Ennek a célnak a elérése érdekében biztonságos kulcs létrehozását, tárolását, hozzáférés-vezérlését és a titkosítási kulcsok kezelését kell biztosítani. Bár a részletek eltérőek lehetnek, az Azure-szolgáltatások titkosítása inaktív megvalósítások az alábbi ábrán bemutatott módon ismertetése.

![Összetevők](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A titkosítási kulcsok tárolási helye és a kulcsok hozzáférés-vezérlése központi szerepet játszik az inaktív modellben. A kulcsokat nagy biztonságban kell biztosítani, de a megadott felhasználók számára kezelhetőnek kell lenniük, és bizonyos szolgáltatások számára elérhetőnek kell lenniük. Az Azure-szolgáltatások esetében az Azure Key Vault az ajánlott kulcstárolási megoldás, és közös felügyeleti élményt nyújt a szolgáltatások között. A kulcsok tárolása és kezelése a kulcstartókban van tárolva, és a felhasználók vagy a szolgáltatások számára hozzáférést biztosíthat a kulcstartóhoz. Az Azure Key Vault támogatja a kulcsok ügyféláltali létrehozását vagy az ügyfélkulcsok importálását az ügyfél által felügyelt titkosítási kulcsforgatókönyvekben való használatra.

### <a name="azure-active-directory"></a>Azure Active Directory

Az Azure Active Directory-fiókok az Azure Active Directory-fiókok nak az Azure Active Directory-fiókok számára adhatók az Azure Key Vaultban tárolt kulcsok használatára, akár az inaktív titkosítás titkosítása és visszafejtése érdekében.

### <a name="key-hierarchy"></a>Kulcshierarchia

Egynél több titkosítási kulcsot használnak egy titkosítási inaktív megvalósításban. Titkosítási kulcs tárolása az Azure Key Vault biztonságos kulcshozzáférést és a kulcsok központi kezelését biztosítja. A titkosítási kulcsokhoz való helyi hozzáférés azonban hatékonyabb a tömeges titkosítás és visszafejtés során, mint a Key Vault-tal való interakció minden adatművelethez, ami erősebb titkosítást és jobb teljesítményt tesz lehetővé. Az egyetlen titkosítási kulcs használatának korlátozása csökkenti annak kockázatát, hogy a kulcs veszélybe kerül, és az újratitkosítás költsége, amikor egy kulcsot ki kell cserélni. Az Azure-titkosítások inaktív modellek a következő típusú kulcsokból álló kulcshierarchiát használják az alábbi igények kielégítésére:

- **Adattitkosítási kulcs (DEK)** – egy szimmetrikus AES256 kulcs, amely egy partíció vagy adatblokk titkosítására szolgál.  Előfordulhat, hogy egyetlen erőforrás sok partícióval és sok adattitkosítási kulcssal rendelkezik. Az egyes adattömbök más kulccsal történő titkosítása megnehezíti a titkosítási elemzési támadásokat. A DEK-khez való hozzáférésre az erőforrás-szolgáltatónak vagy az alkalmazáspéldánynak szüksége van egy adott blokk titkosítására és visszafejtésére. Ha egy DEK-t új kulccsal cserélnek le, csak a társított blokkban lévő adatokat kell újratitkosítani az új kulccsal.
- **Kulcstitkosítási kulcs (KEK)** – Az adattitkosítási kulcsok titkosításához használt titkosítási kulcs. A key encryption kulcs használata, amely soha nem hagyja key vault lehetővé teszi az adattitkosítási kulcsok magukat titkosítani és vezérelhető. A KEK-hez hozzáféréssel rendelkező entitás eltérhet a DEK-t igénylő entitástól. Az entitás közvetítői hozzáférést biztosíthat a DEK-hez, hogy korlátozza az egyes DEK-k hozzáférését egy adott partícióra. Mivel a KEK-nek vissza kell fejtenie a DEK-ket, a KEK gyakorlatilag egyetlen pont, amellyel a DEK-k hatékonyan törölhetők a KEK törlésével.

A kulcstitkosítási kulcsokkal titkosított adattitkosítási kulcsok at külön tárolja a rendszer, és csak a kulcstitkosítási kulcshoz hozzáféréssel rendelkező entitás tudja visszafejteni ezeket az adattitkosítási kulcsokat. A kulcstárolás különböző modelljei támogatottak. Az egyes modelleket a következő szakasz későbbi részében részletesebben tárgyaljuk.

## <a name="data-encryption-models"></a>Adattitkosítási modellek

A különböző titkosítási modellek, valamint azok előnyeinek és hátrányainak megértése elengedhetetlen annak megértéséhez, hogy az Azure-ban a különböző erőforrás-szolgáltatók hogyan valósítják meg a titkosítást az inaktív állapotban. Ezek a definíciók az Azure-ban lévő összes erőforrás-szolgáltató között vannak megosztva a közös nyelv és besorolás biztosítása érdekében.

A kiszolgálóoldali titkosításnak három forgatókönyve van:

- Kiszolgálóoldali titkosítás a Felügyelt szolgáltatás kulcsaival
  - Az Azure resource providers végrehajtják a titkosítási és visszafejtési műveleteket
  - A Kulcsokat a Microsoft kezeli
  - Teljes felhőfunkció

- Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsokkal az Azure Key Vaultban
  - Az Azure resource providers végrehajtják a titkosítási és visszafejtési műveleteket
  - Az ügyfél az Azure Key Vaulton keresztül vezérli a kulcsokat
  - Teljes felhőfunkció

- Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsokkal az ügyfél által vezérelt hardveren
  - Az Azure resource providers végrehajtják a titkosítási és visszafejtési műveleteket
  - Az ügyfél szabályozza a kulcsokat az ügyfél által vezérelt hardveren
  - Teljes felhőfunkció

Az ügyféloldali titkosításhoz vegye figyelembe az alábbiakat:

- Az Azure-szolgáltatások nem látják a visszafejtett adatokat
- Az ügyfelek a helyszíni (vagy más biztonságos üzletekben) kezelik és tárolják a kulcsokat. A kulcsok nem érhetők el az Azure-szolgáltatások számára
- Csökkentett felhőalapú funkciók

Az Azure-ban a támogatott titkosítási modellek két fő csoportra oszthatók: "Ügyféltitkosítás" és "Kiszolgálóoldali titkosítás", ahogy azt korábban említettük. Függetlenül a használt inaktív titkosítási modelltől, az Azure-szolgáltatások mindig biztonságos átvitel, például TLS vagy HTTPS használatát javasolják. Ezért az átvitelben a titkosítást az átviteli protokollnak kell kezelnie, és nem lehet fontos tényező annak meghatározásában, hogy melyik titkosítást használja az inaktív modell.

### <a name="client-encryption-model"></a>Ügyféltitkosítási modell

Az ügyféltitkosítási modell olyan titkosítást használ, amelyet a szolgáltatás vagy a hívó alkalmazás az erőforrás-szolgáltatón vagy az Azure-on kívül hajt végre. A titkosítást elvégezheti a szolgáltatásalkalmazás az Azure-ban, vagy egy alkalmazás fut az ügyfél adatközpontban. Mindkét esetben, ha ezt a titkosítási modellt kihasználva az Azure Resource Provider kap egy titkosított adatblob ot anélkül, hogy bármilyen módon visszafejtheti az adatokat, vagy hozzáférhet a titkosítási kulcsokhoz. Ebben a modellben a kulcskezelést a hívó szolgáltatás/alkalmazás végzi, és az Azure-szolgáltatás számára átlátszatlan.

![Ügyfél](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Kiszolgálóoldali titkosítási modell

A kiszolgálóoldali titkosítási modellek az Azure-szolgáltatás által végrehajtott titkosításra hivatkoznak. Ebben a modellben az erőforrás-szolgáltató végrehajtja a titkosítási és visszafejtési műveleteket. Például az Azure Storage adatokat fogadhat egyszerű szöveges műveletekben, és belsőleg hajtja végre a titkosítást és a visszafejtést. Az erőforrás-szolgáltató a megadott konfigurációtól függően használhat titkosítási kulcsokat, amelyeket a Microsoft vagy az ügyfél kezel.

![Kiszolgáló](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Kiszolgálóoldali titkosítási kulcskezelési modellek

A kiszolgálóoldali titkosítás inaktív modellek mindegyike a kulcskezelés megkülönböztető jellemzőit jelenti. Ez magában foglalja a titkosítási kulcsok létrehozásának és tárolási helyét és módját, valamint a hozzáférési modelleket és a kulcsrotációs eljárásokat.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsok használatával

Sok ügyfél számára az alapvető követelmény annak biztosítása, hogy az adatok titkosítva legyenek, amikor az inkban van. A szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítás lehetővé teszi ezt a modellt, mivel lehetővé teszi az ügyfelek számára, hogy megjelölje az adott erőforrást (tárfiók, SQL DB stb.) titkosításra, és az összes kulcskezelési szempontot, például a kulcskiállítást, az elforgatást és a biztonsági mentést a Microsoftnak hagyja. A legtöbb inaktív titkosítást támogató Azure-szolgáltatások általában támogatja ezt a modellt a titkosítási kulcsok kezelésének azure-ba való kiszervezése. Az Azure-erőforrás-szolgáltató létrehozza a kulcsokat, biztonságos tárolóba helyezi őket, és szükség esetén lekéri azokat. Ez azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsokhoz, és a szolgáltatás teljes hozzáféréssel rendelkezik a hitelesítő adatok életciklusának kezelése felett.

![Kezelt](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

A szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítás ezért gyorsan lecímezi annak szükségességét, hogy a titkosítás inaktív állapotban legyen, és az ügyfél számára alacsony többletterhelést jelent. Ha egy ügyfél általában megnyitja az Azure-portált a cél-előfizetés és erőforrás-szolgáltató számára, és bejelöl egy négyzetet, amely jelzi, hogy az adatoktitkosítást szeretnék. Egyes Erőforrás-kezelők kiszolgálóoldali titkosítása szolgáltatás által felügyelt kulcsokkal alapértelmezés szerint be van kapcsolva.

A Microsoft által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsok tárolásához és kezeléséhez. Míg egyes ügyfelek érdemes kezelni a kulcsokat, mert úgy érzik, hogy nagyobb biztonságot, a költségek és a kockázat egy egyéni kulcs tárolási megoldás figyelembe kell venni a modell kiértékelésekor. Sok esetben a szervezet megállapíthatja, hogy az erőforrás-korlátozások vagy a helyszíni megoldások kockázatai nagyobbak lehetnek, mint a titkosítás identatin belüli titkosítási felhőbeli felügyeletének kockázata.  Ez a modell azonban nem feltétlenül elegendő azon szervezetek számára, amelyek nek vannak követelményei a titkosítási kulcsok létrehozásának vagy életciklusának szabályozásához, vagy hogy a szolgáltatás titkosítási kulcsait eltérő személyzet kezelje, mint a szolgáltatást kezelők (azaz a kulcskezelés elkülönítése a szolgáltatás általános felügyeleti modelljétől).

##### <a name="key-access"></a>Kulcshozzáférés

Ha a kiszolgálóoldali titkosítást szolgáltatás által felügyelt kulcsokkal használja, a kulcs létrehozását, tárolását és szolgáltatáshoz való hozzáférést a szolgáltatás kezeli. Az alapvető Azure-erőforrás-szolgáltatók általában az adatok titkosítási kulcsait egy olyan tárolóban tárolják, amely közel van az adatokhoz, és gyorsan elérhető és elérhető, miközben a kulcstitkosítási kulcsok egy biztonságos belső tárolóban tárolódnak.

**Előnyök**

- Egyszerű beállítás
- A Microsoft kezeli a kulcsrotációt, a biztonsági mentést és a redundanciát
- Az ügyfél nem rendelkezik a végrehajtással kapcsolatos költségekkel vagy az egyéni kulcskezelési séma kockázatával.

**Hátrányok**

- Nincs ügyfél-ellenőrzés a titkosítási kulcsok (kulcs specifikáció, életciklus, visszavonás, stb)
- Nem lehet elkülöníteni a kulcskezelést a szolgáltatás általános felügyeleti modelljétől

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsokkal az Azure Key Vaultban

Olyan esetekben, amikor a követelmény az inaktív adatok titkosítása és a titkosítási kulcsok szabályozása az ügyfelek használhatják a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsok a Key Vaultban. Egyes szolgáltatások csak a gyökérkulcs titkosítási kulcs az Azure Key Vault és tárolja a titkosított adattitkosítási kulcs egy belső helyen közelebb az adatokhoz. Ebben az esetben az ügyfelek saját kulcsokat hozhatnak a Key Vaultba (BYOK – Saját kulcs létrehozása), vagy újakat hozhatnak létre, és a kívánt erőforrások titkosítására használhatják őket. Míg az erőforrás-szolgáltató végrehajtja a titkosítási és visszafejtési műveleteket, a konfigurált kulcstitkosítási kulcsot használja gyökérkulcsként az összes titkosítási művelethez.

A kulcstitkosítási kulcsok elvesztése adatvesztést jelent. Ezért a kulcsokat nem szabad törölni. A kulcsokról minden létrehozáskor vagy elforgatáskor biztonsági másolatot kell kapni. [A soft-delete minden](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) olyan tárolón engedélyezni kell a kulcstitkosítási kulcsokat tároló tároló tároló tárolókon. Kulcs törlése helyett állítsa be, hogy hamis legyen, vagy állítsa be a lejárati dátumot.

##### <a name="key-access"></a>Kulcs-hozzáférés

A kiszolgálóoldali titkosítási modell az ügyfél által felügyelt kulcsok az Azure Key Vault magában foglalja a szolgáltatás a kulcsok titkosításához és visszafejtéséhez szükség szerint. Az inaktív kulcsok hozzáférés-vezérlési házirenden keresztül érhetők el a szolgáltatás számára. Ez a házirend hozzáférést biztosít a szolgáltatás identitásához a kulcs fogadásához. Egy társított előfizetés nevében futó Azure-szolgáltatás konfigurálható az adott előfizetésidentitással. A szolgáltatás azure Active Directory-hitelesítést végezhet, és kap egy hitelesítési jogkivonatot, amely azonosítja magát, mint az előfizetés nevében eljáró szolgáltatás. Ezt a jogkivonatot ezután a Key Vault ban is be lehet mutatni egy kulcs beszerzéséhez, amelyhez hozzáférést kapott.

A titkosítási kulcsokat használó műveletek esetén a szolgáltatásidentitás a következő műveletek bármelyikéhez biztosítható: visszafejtés, titkosítás, kicsomagolásKulcs, wrapKey, ellenőrzés, aláírás, beolvasás, lista, frissítés, létrehozás, importálás, törlés, biztonsági mentés és visszaállítás.

A kulcs beszerzése az adatok titkosításához vagy visszafejtéséhez nyugalmi a szolgáltatás identitását, amely az Erőforrás-kezelő szolgáltatáspéldány fog futni, mint kell UnwrapKey (a kulcs beolvasása a visszafejtés) és WrapKey (beszúrni egy kulcsot a kulcstartóba, amikor új kulcsot hoz létre).

>[!NOTE]
>A Key Vault-engedélyezésről további részleteket az [Azure Key Vault dokumentációjában](../../key-vault/key-vault-secure-your-key-vault.md)talál a kulcstartó biztonságos lapján.

**Előnyök**

- A használt kulcsok teljes körű vezérlése – a titkosítási kulcsok kezelése az ügyfél key vaultjában történik az ügyfél irányítása alatt.
- Több szolgáltatás titkosításának lehetősége egy főkiszolgálóra
- Elkülönítheti a kulcskezelést a szolgáltatás általános felügyeleti modelljétől
- Meghatározhatja a szolgáltatást és a kulcs helyét a régiók között

**Hátrányok**

- Az ügyfél teljes felelősséggel tartozik a kulcshozzáférés-kezelésért
- Az ügyfél teljes felelősséggel tartozik a kulcsfontosságú életciklus-kezelésért
- További telepítési & konfigurációs többletterhelés

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsokkal ügyfél által vezérelt hardverben

Egyes Azure-szolgáltatások engedélyezik a saját kulcs (HYOK) kulcskezelési modelljét. Ez a felügyeleti mód olyan esetekben hasznos, amikor az inaktív adatok titkosítására és a kulcsok kezelésére a Microsoft hatáskörén kívül álló saját tárházban van szükség. Ebben a modellben a szolgáltatásnak le kell kérnie a kulcsot egy külső helyről. A teljesítmény- és rendelkezésre állási garanciákat befolyásolja, és a konfiguráció összetettebb. Emellett, mivel a szolgáltatás a titkosítási és visszafejtési műveletek során rendelkezik hozzáféréssel a DEK-hez, a modell általános biztonsági garanciái hasonlóak ahhoz, amikor a kulcsokat az Azure Key Vaultban kezelik az ügyfél által.  Ennek eredményeképpen ez a modell nem megfelelő a legtöbb szervezet számára, kivéve, ha speciális kulcskezelési követelményekkel rendelkeznek. E korlátozások miatt a legtöbb Azure-szolgáltatások nem támogatják a kiszolgálóoldali titkosítást kiszolgáló által felügyelt kulcsok használatával az ügyfél által vezérelt hardveren.

##### <a name="key-access"></a>Kulcs-hozzáférés

Ha a szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítást az ügyfél által vezérelt hardverben használják, a kulcsok az ügyfél által konfigurált rendszeren maradnak fenn. A modellt támogató Azure-szolgáltatások biztonságos kapcsolatot biztosítanak az ügyfél által biztosított kulcstárolóval.

**Előnyök**

- A használt gyökérkulcs teljes körű vezérlése – a titkosítási kulcsokat az ügyfél által biztosított áruház kezeli
- Több szolgáltatás titkosításának lehetősége egy főkiszolgálóra
- Elkülönítheti a kulcskezelést a szolgáltatás általános felügyeleti modelljétől
- Meghatározhatja a szolgáltatást és a kulcs helyét a régiók között

**Hátrányok**

- Teljes felelősség a kulcsfontosságú tárolásért, biztonságért, teljesítményért és rendelkezésre állásért
- Teljes felelősség a kulcshozzáférés-kezelésért
- Teljes felelősség a kulcsfontosságú életciklus-menedzsmentért
- Jelentős beállítási, konfigurációs és folyamatos karbantartási költségek
- Az ügyféladatközpont és az Azure-adatközpontok közötti hálózati rendelkezésre állástól való fokozott függőség.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Titkosítás inaktív módon a Microsoft felhőszolgáltatásaiban

A Microsoft Cloud-szolgáltatások mindhárom felhőmodellben használatosak: IaaS, PaaS, SaaS. Az alábbiakban példákat, hogyan illeszkednek az egyes modellek:

- Szoftverszolgáltatások, más néven Szoftver kiszolgálóként vagy SaaS-ként, amelyek a felhő által biztosított alkalmazással rendelkeznek, például az Office 365.Software, a software as a Server or SaaS, which have application provided by the cloud such as Office 365.
- Platformszolgáltatások, amelyeket az ügyfelek az alkalmazásaikfelhőben használnak, és a felhőt olyan célokra használják, mint a tárolás, az elemzés és a szolgáltatásbusz-funkciók.
- Infrastruktúra-szolgáltatások, vagy infrastruktúra szolgáltatásként (IaaS), amelyben az ügyfél telepíti a felhőben üzemeltetett operációs rendszereket és alkalmazásokat, és esetleg más felhőszolgáltatásokat is igénybe vesz.

### <a name="encryption-at-rest-for-saas-customers"></a>Titkosítás inaktív ként a SaaS-ügyfelek számára

A szoftver szolgáltatásként (SaaS) rendelkező ügyfelek általában minden szolgáltatásban engedélyezve vannak vagy elérhetők az inaktív titkosítással. Az Office 365 számos lehetőséget kínál az ügyfelek számára a titkosítás inaktív ellenőrzésére vagy engedélyezésére. Az Office 365 szolgáltatásairól a [Titkosítás az Office 365-ben](https://docs.microsoft.com/office365/securitycompliance/encryption)című témakörben talál további információt.

### <a name="encryption-at-rest-for-paas-customers"></a>Titkosítás inaktív helyen a PaaS-ügyfelek számára

Platform szolgáltatásként (PaaS) az ügyfél adatai általában egy tárolási szolgáltatásban, például a Blob Storage-ban találhatók, de az alkalmazás végrehajtási környezetében, például egy virtuális gépben is tárolhatók. Az alábbi táblázatban megtekintheti a titkosítási beállításokat, hogy milyen tároló- és alkalmazásplatformokat használ.

### <a name="encryption-at-rest-for-iaas-customers"></a>Titkosítás inaktív ként az IaaS-ügyfelek számára

Infrastruktúra szolgáltatásként (IaaS) ügyfelek számos szolgáltatást és alkalmazást használhatnak. Az IaaS-szolgáltatások engedélyezhetik az Azure által üzemeltetett virtuális gépek és virtuális merevlemezek inaktív titkosítását az Azure Disk Encryption használatával.

#### <a name="encrypted-storage"></a>Titkosított tárolás

A PaaS-hoz hasonlóan az IaaS-megoldások is kihasználhatják azokat az Azure-szolgáltatásokat, amelyek az inkett titkosítással titkosított adatokat tárolják. Ezekben az esetekben engedélyezheti az inaktív titkosítás támogatását az egyes felhasznált Azure-szolgáltatások által biztosított módon. Az alábbi táblázat a főbb tárolási, szolgáltatási és alkalmazásplatformokat, valamint az inaktív titkosítás imázsának modelljét sorolja fel. 

#### <a name="encrypted-compute"></a>Titkosított számítás

A felügyelt lemezek, pillanatképek és képek titkosítása a Storage Service Encryption szolgáltatás által felügyelt kulcs használatával történik. Egy teljesebb titkosítás i vevesi megoldás biztosítja, hogy az adatok soha nem marad meg titkosítatlan formában. A virtuális gépen lévő adatok feldolgozása közben az adatok megőrizhetők a Windows lapfájlban vagy a Linux-cserefájlban, az összeomlási memóriaképben vagy egy alkalmazásnaplóban. Annak érdekében, hogy ezek az adatok inaktív titkosítása, IaaS-alkalmazások azure lemeztitkosítás azure IaaS virtuális gépen (Windows vagy Linux) és a virtuális lemez.

#### <a name="custom-encryption-at-rest"></a>Egyéni titkosítás inaktív

Javasoljuk, hogy amikor csak lehetséges, az IaaS-alkalmazások az Azure Disk Encryption and Encryption inin titkosítási beállításokat használják ki, amelyeket bármely felhasznált Azure-szolgáltatás biztosít. Bizonyos esetekben, például a szabálytalan titkosítási követelmények vagy a nem Azure-alapú tárolás, egy IaaS-alkalmazás fejlesztője előfordulhat, hogy végre titkosítást inaktív módon. Az IaaS-megoldások fejlesztői bizonyos Azure-összetevők kihasználásával jobban integrálhatók az Azure-felügyelettel és az ügyfelek elvárásaival. Pontosabban a fejlesztőknek az Azure Key Vault szolgáltatást kell használniuk a biztonságos kulcstárolás biztosításához, valamint ügyfeleiknek a legtöbb Azure platformszolgáltatáshoz tartozó konzisztens kulcskezelési lehetőségek biztosításához. Emellett az egyéni megoldások nak az Azure által felügyelt szolgáltatásidentitások használatával kell engedélyezniük a szolgáltatásfiókok számára a titkosítási kulcsok elérését. Az Azure Key Vault és a felügyelt szolgáltatás identitások fejlesztői információkért tekintse meg a megfelelő SDK-k.

## <a name="azure-resource-providers-encryption-model-support"></a>Az Azure-erőforrás-szolgáltatók titkosítási modellének támogatása

A Microsoft Azure-szolgáltatások mindegyike támogatja a titkosítást inaktív modellek. Egyes szolgáltatások esetében azonban előfordulhat, hogy egy vagy több titkosítási modell nem alkalmazható. Az ügyfelek által felügyelt kulcsforgatókönyveket támogató szolgáltatások esetében előfordulhat, hogy az Azure Key Vault által a kulcstitkosítási kulcsokhoz támogatott kulcstípusoknak csak egy részét támogatja. Emellett a szolgáltatások különböző ütemezések esetén is adhatnak ki támogatást ezekhez a forgatókönyvekhez és kulcstípusokhoz. Ez a szakasz ismerteti az inaktív titkosítástámogatása az írás időpontjában az egyes főbb Azure-adattárolási szolgáltatások.

### <a name="azure-disk-encryption"></a>Azure lemeztitkosítás

Az Azure Infrastructure szolgáltatásként (IaaS) szolgáltatást használó ügyfelek az Azure Disk Encryption szolgáltatáson keresztül inaktív titkosítást érhetnek el az IaaS virtuális gépei és lemezei számára. Az Azure Disk titkosításáról az [Azure Disk Encryption dokumentációjában](../azure-security-disk-encryption-overview.md)olvashat bővebben.

#### <a name="azure-storage"></a>Azure Storage-tárterület

Az összes Azure Storage-szolgáltatás (Blob storage, Queue storage, Table storage és Azure Files) támogatja a kiszolgálóoldali titkosítást; egyes szolgáltatások emellett támogatják az ügyféláltal kezelt kulcsokat és az ügyféloldali titkosítást. 

- Kiszolgálóoldali: Az összes Azure Storage-szolgáltatás alapértelmezés szerint engedélyezi a kiszolgálóoldali titkosítást a szolgáltatás által felügyelt kulcsok használatával, amely az alkalmazás számára transzparens. További információ: [Azure Storage Service Encryption for Data at In.](../../storage/common/storage-service-encryption.md) Az Azure Blob storage és az Azure Files is támogatja az RSA 2048 bites, ügyfél által felügyelt kulcsokat az Azure Key Vaultban. További információ: [Storage Service Encryption using customer-managed keys in Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Ügyféloldali: Az Azure Blobs, a Táblák és a várólisták támogatják az ügyféloldali titkosítást. Ügyféloldali titkosítás használata esetén az ügyfelek titkosítják az adatokat, és titkosított blobként töltik fel az adatokat. A kulcskezelést az ügyfél végzi. További információ: [Ügyféloldali titkosítás és Az Azure Key Vault for Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Az Azure SQL Database jelenleg támogatja a titkosítást inaktív microsoft által felügyelt szolgáltatási és ügyféloldali titkosítási forgatókönyvek.

A kiszolgálótitkosítás támogatása jelenleg az Átlátszó adattitkosítás nevű SQL-szolgáltatáson keresztül érhető el. Miután egy Azure SQL Database-ügyfél engedélyezi a TDE-kulcsot, a rendszer automatikusan létrehozza és kezeli őket. Az inaktív titkosítás engedélyezhető az adatbázis és a kiszolgáló szintjén. 2017 júniusátantól az [átlátszó adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) alapértelmezés szerint engedélyezve van az újonnan létrehozott adatbázisokban. Az Azure SQL Database támogatja az RSA 2048 bites, ügyfél által felügyelt kulcsokat az Azure Key Vaultban. További információ: [Transparent Data Encryption with Bring Your Own Key support for Azure SQL Database and Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Az Azure SQL Database-adatok ügyféloldali titkosítása a [Mindig titkosított](https://msdn.microsoft.com/library/mt163865.aspx) szolgáltatás on keresztül támogatott. Mindig titkosított használ egy kulcsot, amely létrehozta és tárolja az ügyfél. Az ügyfelek tárolhatják a főkulcsot egy Windows tanúsítványtárolóban, az Azure Key Vaultban vagy egy helyi hardverbiztonsági modulban. Az SQL Server Management Studio használatával az SQL-felhasználók kiválasztják, hogy milyen kulccsal szeretnének titkosításra használni.

#### <a name="encryption-model-and-key-management-table"></a>Titkosítási modell és kulcskezelési tábla

|                                  |                    | **Titkosítási modell és kulcskezelés** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Kiszolgálóoldali szolgáltatás által felügyelt kulccsal**     | **Kiszolgálóoldali ügyfél által kezelt kulcs használatával**             | **Ügyféloldal ügyféláltal kezelt kulccsal**      |
| **AI és a machine learning**      |                    |                    |                    |
| Azure Cognitive Search           | Igen                | Igen                | -                  |
| Azure Machine Learning           | Igen                | Igen                | -                  |
| Azure Machine Learning Studio    | Igen                | Előnézet, RSA 2048 bites | -               |
| Power BI                         | Igen                | Előnézet, RSA 2048 bites | -                  |
| **Elemzés**                    |                    |                    |                    |
| Azure Stream Analytics           | Igen                | -                  | -                  |
| Event Hubs                       | Igen                | Igen, minden RSA hossz. | -                  |
| Functions                        | Igen                | Igen, minden RSA hossz. | -                  |
| Azure Analysis Services          | Igen                | -                  | -                  |
| Azure Data Catalog               | Igen                | -                  | -                  |
| Apache Kafka az Azure HDInsighton  | Igen                | Minden RSA hossz.   | -                  |
| Azure Monitor alkalmazáselemzési adatok | Igen                | Igen                | -                  |
| Azure Monitor-naplóelemzés | Igen                | Igen                | -                  |
| Azure Data Explorer              | Igen                | Igen                | -                  |
| Azure Data Factory               | Igen                | Igen                | -                  |
| Azure Data Lake Store            | Igen                | Igen, RSA 2048 bites  | -                  |
| **Tárolók**                   |                    |                    |                    |
| Azure Kubernetes Service         | Igen                | Igen                | -                  |
| Tárolópéldányok              | Igen                | Igen                | -                  |
| Container Registry               | Igen                | Igen                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtuális gépek                 | Igen                | Igen, RSA 2048 bites  | -                  |
| Virtuális gép méretezési készlete        | Igen                | Igen, RSA 2048 bites  | -                  |
| SAP HANA                         | Igen                | Igen, RSA 2048 bites  | -                  |
| App Service                      | Igen                | Igen                | -                  |
| Automation                       | Igen                | Igen                | -                  |
| Azure Portal                     | Igen                | Igen                | -                  |
| Logic Apps                       | Igen                | Igen                | -                  |
| Azure Managed Applications       | Igen                | Igen                | -                  |
| Service Bus                      | Igen                | Igen                | -                  |
| Site Recovery                    | Igen                | Igen                | -                  |
| **Adatbázisok**                    |                    |                    |                    |
| SQL Server on Virtual Machines   | Igen                | Igen, RSA 2048 bites  | Igen                |
| Azure SQL Database               | Igen                | Igen, RSA 2048 bites  | Igen                |
| Azure MariaDB SQL-adatbázis   | Igen                | -                  | -                  |
| Azure SQL-adatbázis a MySQL-hez     | Igen                | Igen                | -                  |
| Azure PostgreSQL-adatbázis | Igen               | Igen                | -                  |
| Azure Synapse Analytics          | Igen                | Igen, RSA 2048 bites  | -                  |
| SQL Server Stretch Database      | Igen                | Igen, RSA 2048 bites  | Igen                |
| Table Storage                    | Igen                | Igen                | Igen                |
| Azure Cosmos DB                  | Igen                | Igen                | -                  |
| Azure Databricks                 | Igen                | Igen                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Igen                | -                  | Igen                |
| Azure Repos                      | Igen                | -                  | Igen                |
| **Identitás**                     |                    |                    |                    |
| Azure Active Directory           | Igen                | -                  | -                  |
| Azure Active Directory tartományi szolgáltatások | Igen          | Igen, RSA 2048 bites  | -                  |
| **Integráció**                  |                    |                    |                    |
| Service Bus                      | Igen                | Igen                | Igen                |
| Event Grid                       | Igen                | -                  | -                  |
| API Management                   | Igen                | -                  | -                  |
| **IoT-szolgáltatások**                 |                    |                    |                    |
| IoT Hub                          | Igen                | Igen                | Igen                |
| **Felügyelet és irányítás**    |                    |                    |                    |
| Azure Site Recovery              | Igen                | -                  | -                  |
| **Média**                        |                    |                    |                    |
| Media Services                   | Igen                | -                  | Igen                |
| **Tárterület**                      |                    |                    |                    |
| Blob Storage                     | Igen                | Igen, RSA 2048 bites  | Igen                |
| Disk Storage                     | Igen                | Igen                | -                  |
| Felügyelt lemeztároló             | Igen                | Igen                | -                  |
| File Storage                     | Igen                | Igen, RSA 2048 bites  | -                  |
| Queue Storage                    | Igen                | Igen                | Igen                |
| Avere vFXT                       | Igen                | -                  | -                  |
| Azure NetApp Files               | Igen                | -                  | -                  |
| Archive Storage                  | Igen                | Igen, RSA 2048 bites  | -                  |
| StorSimple                       | Igen                | Igen, RSA 2048 bites  | Igen                |
| Azure Backup                     | Igen                | Igen                | Igen                |
| Data Box                         | Igen                | -                  | Igen                |
| Data Box Edge                    | Igen                | Igen                | -                  |

## <a name="conclusion"></a>Összegzés

Az Azure Servicesben tárolt ügyféladatok védelme rendkívül fontos a Microsoft számára. Az Azure által üzemeltetett összes szolgáltatás elkötelezett a titkosítási intvetvei beállítások biztosítása mellett. Az olyan alapvető szolgáltatások, mint az Azure Storage, az Azure SQL Database, valamint a kulcselemzési és intelligenciaszolgáltatások már biztosítják a titkosítási intvetusi beállításokat. A szolgáltatások némelyike támogatja az ügyfélvezérelt kulcsokat és az ügyféloldali titkosítást, valamint a szolgáltatás által felügyelt kulcsokat és titkosítást. A Microsoft Azure-szolgáltatások széles körben bővítik az inaktív titkosítás elérhetőségét, és új lehetőségeket terveznek az előzetes verzióés az általános elérhetőség érdekében a következő hónapokban.
