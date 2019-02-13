---
title: A Microsoft Azure Data Encryption inaktív |} A Microsoft Docs
description: A cikk a Microsoft Azure titkosítás inaktív adatok áttekintését, általános képességek és általános szempontokat ismerteti.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: c9e3a98b1073a2b198a341f31a320bba670b0596
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117901"
---
# <a name="azure-data-encryption-at-rest"></a>Az Azure Data Encryption inaktív

A Microsoft Azure révén a cég biztonsági és megfelelőségi igényeinek megfelelően adatok eszközt tartalmaz. Ez a tanulmány összpontosít:

- Az adatok védelme inaktív állapotban a Microsoft Azure-ban
- A cikk ismerteti a különböző összetevők vesz részt a data protection végrehajtása
- Ellenőrzi, és a különböző kulcskezelés védelmi megközelítés hátrányai. 

Titkosítás inaktív állapotban egy közös biztonsági követelmény. Az Azure-ban a szervezetek a kockázati vagy egy egyéni kulcskezelési megoldást költsége nélkül az inaktív adatok használatával titkosítsa. Szervezetek számára, hogy a teljesen kezelése az inaktív adatok titkosítását az Azure lehetősége van. Ezenkívül szervezetek többféleképpen is felügyelni az titkosítás vagy a titkosítási kulcsokat.

## <a name="what-is-encryption-at-rest"></a>Mi az inaktív adatok titkosítását?

Titkosítás inaktív állapotban az adatok kódolási (titkosítás) esetén, a rendszer megőrzi. A titkosítás a Rest-minták az Azure-ban szimmetrikus titkosítási titkosításához és visszafejtéséhez nagy adatmennyiségek gyors szerint egy egyszerű fogalmi modellt használja:

- A szimmetrikus titkosítási kulcsot tárolási írás adatok titkosítására szolgál. 
- Ugyanazt a titkosítási kulcsot, hogy-adatainak visszafejtése, ahogy azt a memóriában van readied szolgál.
- Adatok particionálható, és más kulcsok minden partíció esetében használható.
- Kulcsok identitásalapú hozzáférés-vezérléssel biztonságos helyen kell tárolni, és naplózási házirendek. Az adattitkosítási kulcsokat gyakran van titkosítva, az aszimmetrikus titkosítási korlátozhatja a hozzáférést.

A gyakorlatban kezelésére és felügyeletére alkalmazási helyzeteket, valamint a méretezés és rendelkezésre állás biztosítékok szükséges további szerkezeteket. A Microsoft Azure titkosítása Rest kapcsolatos fogalmakról és összetevőkről az alábbiakban tekintheti át.

## <a name="the-purpose-of-encryption-at-rest"></a>Titkosítás inaktív állapotban célját

Titkosítás inaktív állapotban (inaktív) tárolt adatok védelmet biztosít. Inaktív adatok elleni támadások közé tartozik a hardver, amelyen az adatok tárolásának történő fizikai hozzáférés érdekében tett kísérletek, és majd veszélyezteti az abban található adatokat. Az ilyen támadások a kiszolgáló merevlemez előfordulhat, hogy rendelkezik lett kezelésük a merevlemez-meghajtó eltávolítása a támadó karbantartás során. Később a támadó a merevlemez lenne helyezzen egy számítógép alatt a vezérlő megpróbálja elérni az adatokat. 

Inaktív adatok titkosítását az célja, hogy megakadályozza, hogy a támadó elérése a titkosítatlan adatok úgy, hogy az adatok titkosítása a lemezen. Ha egy támadó megszerzi a merevlemez-meghajtó titkosított adatokat, de nem a titkosítási kulcsokat, a támadó a titkosítási beolvasni az adatokat kell kiiktatására. A támadás, sokkal több bonyolult és erőforrás használ, mint a nem titkosított adatokat a merevlemezen. Ebből kifolyólag a titkosítás inaktív állapotban erősen ajánlott, és a egy magas prioritású követelmény számos szervezet. 

Titkosítás inaktív állapotban is szükség lehet a szervezet adatokat cégirányítás és megfelelőség erőfeszítések szükség szerint. Iparági és kormányzati előírások, például a HIPAA, a PCI és a FedRAMP, meghatározott védelmi mechanizmusokra vonatkozó adatok védelmét és titkosítási követelmények elrendezését. Titkosítás inaktív állapotban mérőszáma kötelező egyes ezen szabályzat a megfelelőséghez szükséges.

Titkosítás inaktív állapotban a mellett felel meg a megfelelőségi és szabályozási követelmények, a defense jellegű védelmet is biztosít. A Microsoft Azure szolgáltatások, alkalmazások és adatok megfelelő platformot biztosít. Átfogó létesítmény és a fizikai biztonságot, a hozzáférés-vezérlés és a naplózás is tartalmazza. Fontos azonban, adja meg a további "egymást átfedő" biztonsági intézkedéseket, abban az esetben egy másik biztonsági intézkedések meghibásodik, és titkosítás inaktív állapotban biztosít az ilyen biztonsági intézkedés

A Microsoft elkötelezett a titkosítás a rest-beállításokat a cloud services és a titkosítási kulcsok ellenőrzésére és a fő használati naplóit, így a vevők. Ezenkívül a Microsoft felé titkosítása inaktív tárolt összes ügyféladathoz alapértelmezés szerint működik.

## <a name="azure-encryption-at-rest-components"></a>Az Azure Rest-összetevők titkosítása

Az előzőekben leírtaknak titkosítás inaktív állapotban célja, hogy, hogy a rendszer megőrzi a lemezen lévő adatok titkosítva van-e a titkos kulccsal. A cél biztonságos kulcs létrehozása érdekében, tárolási, hozzáférés-vezérlés és a titkosítási főkulcsok kezelési meg kell adni. A részletek eltérőek lehetnek, de az alábbi ábrán szemléltetett használati Rest-implementációk az Azure szolgáltatások titkosítási is ismerteti.

![Összetevők](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A titkosítási kulcsokat és a hozzáférés-vezérlést az ezeknek a kulcsoknak tárolási helye a következő központi egy titkosítás a rest-modellben. A kulcsok kell lennie a védett, de még kezelhető a megadott felhasználók és magas rendelkezésre állású adott szolgáltatásokhoz. Az Azure-szolgáltatásokhoz az Azure Key Vault kulcstároló ajánlott megoldás, és egy közös megoldást biztosít a szolgáltatások. Kulcsok tárolása és kezelése a kulcstartók, és a key vaulthoz való hozzáférés adható meg a felhasználók és szolgáltatások számára. Az Azure Key Vault használata az ügyfél által felügyelt titkosítás főbb forgatókönyvek megvalósítását támogatja kulcsok létrehozását vásárlói vagy ügyfél kulcsok importálása.

### <a name="azure-active-directory"></a>Azure Active Directory

Az Azure Active Directory-fiókok adhatók engedélyek kezeléséhez vagy inaktív adatok titkosítása és a visszafejtés, titkosítás azok eléréséhez az Azure Key vaultban tárolt kulcsok használatával. 

### <a name="key-hierarchy"></a>Kulcshierarchia

Egynél több titkosítási kulcs használatban van egy rest-implementáció titkosítása. Az aszimmetrikus titkosítási hasznos létrehozásáról, a megbízhatóság és a hitelesítési kulcs hozzáférési és kezelési szükséges. Szimmetrikus titkosítási hatékony a tömeges titkosítási és visszafejtési, így erősebb titkosításra és a jobb teljesítmény érdekében. A kockázat, hogy a kulcs biztonsága sérült a rendszer és az újbóli titkosítására költségét korlátozza az egyetlen titkosítási kulcs használatát csökkenti, amikor egy kulcsot kell helyettesíteni. A rest-modellek Azure titkosítások használatára egy kulcs hierarchia áll a következő típusú kulcsok használatát:

- **Adatok titkosítási kulcsa (Adattitkosítási)** – AES256 partíció vagy letiltása az adatok titkosításához használt szimmetrikus kulcs.  Előfordulhat, hogy egy erőforrás sok partíció, és számos titkosítási kulcsokat. Egy másik kulcsot minden adatblokk titkosítására nehezebbé titkosítási elemzési támadásokkal szemben. Az erőforrás-szolgáltató vagy az alkalmazás példányhoz, van titkosítása és visszafejtése az egy adott letiltása a DEKs hozzáférést van szükség. Ha egy Adattitkosítási helyére egy új kulcsot csak az adatokat a társított blokkban újra titkosítani az új kulccsal kell lennie.
- **Kulcs titkosítási kulcscserekulcs (KEK)** – az adatok titkosítási kulcsok titkosítására használt aszimmetrikus titkosítási kulcsot. A Kulcsalapú titkosítás kulcsa használata lehetővé teszi a titkosítást és ellenőrzött magukat az adattitkosítási kulcsokat. Az entitás, amely hozzáfér a KEK nem feltétlenül azonos az entitást, amely az adattitkosítási kulcs szükséges. Egy entitás előfordulhat, hogy közvetítse, korlátozhatja az egyes egy megadott partícióra adattitkosítási kulcsot az adattitkosítási kulcsot a hozzáférést. Mivel a DEKs visszafejtéséhez szükség van a KEK, a KEK lényegében amellyel DEKs hatékonyan törölhetők, a KEK törlésre által hibaérzékeny pont.

Az adattitkosítási kulcsokat, a kulcs titkosítási kulcsok titkosítva tárolódnak, és csak egy entitás kulcstitkosítási kulcs hozzáférést kaphat ezzel a kulccsal titkosított adatok titkosítási kulcs. Kulcstároló különböző modellek támogatottak. Minden modell a következő szakasz későbbi részében részletesebben ismertetjük.

## <a name="data-encryption-models"></a>Adatmodell-titkosítás

A különböző titkosítási modellek és azok előnyeiről és hátrányairól megértése alapvető fontosságú megértésében, hogy a különböző erőforrás-szolgáltatók az Azure-ban megvalósítása a titkosítás inaktív állapotban. Ezeket a definíciókat meg vannak osztva az összes erőforrás-szolgáltatók az Azure-ban közös nyelvi és besorolás. 

Nincsenek kiszolgálóoldali titkosítás három forgatókönyv:

- Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsok használata
    - Az Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre
    - A Microsoft kezeli a kulcsok
    - Teljes körű felhőalapú funkciókkal

- Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsok használata az Azure Key Vaultban
    - Az Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre
    - Ügyfél szabályozza, kulcsok Azure Key Vault-n keresztül
    - Teljes körű felhőalapú funkciókkal

- Felhasználó által kezelt kulcsok használata az ügyfél által felügyelt hardveres kiszolgálóoldali titkosítás
    - Az Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre
    - Ügyfél szabályozza az ügyfél által felügyelt hardveres kulcsok
    - Teljes körű felhőalapú funkciókkal

Ügyféloldali titkosítás vegye figyelembe a következőket:

- Azure-szolgáltatások visszafejtett adatait nem látja.
- Ügyfelek kezelése és a helyszíni kulcsok tárolására (vagy más biztonságos tárolók). Kulcsok nem érhetők el az Azure-szolgáltatások
- Csökkentett felhőalapú funkciókkal

A támogatott titkosítási modellek az Azure-ban felosztása két fő csoportok: "Ügyfél titkosítás" és "kiszolgálóoldali titkosítás" mint azt korábban említettük. A titkosítás a rest-modellben használt, Azure-szolgáltatások mindig független egy biztonságos átvitel, például a TLS vagy HTTPS használatát javasoljuk. Ezért a titkosítás az átvitel az átviteli protokoll által beavatkozást igényel, és tényező rest modellt használja, mely titkosítási meghatározása nem lehet.

### <a name="client-encryption-model"></a>Ügyféloldali titkosítás modell

Ügyféloldali titkosítás modell titkosítás a szolgáltatás vagy a hívó alkalmazás által az erőforrás-szolgáltató vagy az Azure-on kívül végrehajtott műveletek hivatkozik. A titkosítás az szolgáltatás az Azure-ban, vagy az ügyfél az adatközpontban futó alkalmazások által hajtható végre. Mindkét esetben, ha a titkosítás Ez a modell az Azure erőforrás-szolgáltató kap egy titkosított adatok nélkül teszi lehetővé bármilyen módon az adatok visszafejtéséhez, vagy a hozzáférése a titkosítási kulcsok-blobba. Ebben a modellben a kulcskezelés végzi el a hívó szolgáltatás vagy alkalmazás, és nem átlátszó, az Azure-szolgáltatás.

![Ügyfél](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Server-side encryption model

Kiszolgálóoldali titkosítás modellek tekintse meg az Azure-szolgáltatás által végrehajtott műveletek titkosítást. Az ehhez a modellhez az erőforrás-szolgáltató titkosítása és visszafejtése műveleteket hajtja végre. Azure Storage például előfordulhat, hogy fogadni az adatokat egyszerű szöveges műveletek, és hajtja végre a titkosítási és visszafejtési belső használatra. Az erőforrás-szolgáltató titkosítási kulcsok a megadott konfigurációtól függően az ügyfél vagy a Microsoft által felügyelt lehet, hogy használja. 

![Kiszolgáló](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Kiszolgálóoldali titkosítás kulcskezelés modellek

A kiszolgálóoldali titkosítás a rest-modellek mindegyikében azt jelenti, hogy kulcskezelés megkülönböztető jellemzőit. Ez magában foglalja, hogyan titkosítási kulcsok létrehozása, és a hozzáférés-modellek és a kulcsrotálás eljárások és tárolása. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsok használata

Sok ügyfél az alapvető követelmény, hogy győződjön meg arról, hogy az adatok titkosítva van, amikor az aktívan nem. Kiszolgálóoldali titkosítás használatával a szolgáltatás által kezelt kulcsok lehetővé teszi, hogy ez a modell lehetővé teszi az ügyfelek számára, hogy az adott erőforrás (Storage-fiók, az SQL DB, stb.) jelölje meg a titkosításhoz, és minden kulcskezelés foglalkoznak, mint a kulcsok, rotálását és a biztonsági mentés és a Microsoft által . Titkosítás inaktív állapotban általában támogató a legtöbb Azure-szolgáltatások felügyeletét, a titkosítási kulcsokat az Azure-kiürítés, ez a modell támogatja. Az Azure erőforrás-szolgáltató hozza létre a kulcsokat, elhelyezi a biztonságos tárolás és lekéri őket, amikor szükséges. Ez azt jelenti, hogy a szolgáltatás a kulcsok teljes körű hozzáféréssel rendelkezik, és a szolgáltatás a hitelesítő adatok életciklus-felügyelet alatt teljes körű vezérléssel rendelkezik.

![managed](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsok használata ezért gyorsan címek az ügyfélnek alacsony többletterhelést az inaktív titkosítási van szükség. Elérhető ügyfél általában nyitja meg a célként megadott előfizetés-és erőforrás-szolgáltató az Azure Portalon, és ellenőrzi, hogy egy mező, amely azt jelzi, ha szeretnék az adatok titkosításához. Néhány erőforrás-kezelőt a szolgáltatás által kezelt kulcsokkal kiszolgálóoldali titkosítás alapértelmezés szerint be van.

A kiszolgálóoldali titkosítást a Microsoft által felügyelt kulcsokkal jelenti azt, a szolgáltatás tárolhatja és kezelheti a kulcsok teljes körű hozzáféréssel rendelkezik. Egyes ügyfeleknek érdemes kezelhetik a kulcsokat, mert azok úgy gondolja, a nagyobb biztonság kapnak, amíg a költségek és a egy egyéni kulcs tárolómegoldás kockázatának kell tekinteni a modell kiértékelése során. Sok esetben egy szervezet meghatározhatják, hogy korlátozott erőforrások és a egy helyszíni megoldás kockázatok lehet nagyobb, mint a titkosítás a rest-kulcsok felhőfelügyeleti kockázatát.  Azonban ez a modell nem feltétlenül elégségesek szabályozására, a létrehozás vagy a titkosítási kulcsok életciklusát, vagy szeretné, hogy a szolgáltatás (azaz elkülönítése kezelésére, mint egy szolgáltatás titkosítási kulcsok kezelése másik csoporthoz követelményekkel rendelkező szervezetek számára Kulcskezelés a szolgáltatás a teljes felügyeleti modellből).

##### <a name="key-access"></a>Hozzáférés a kulcshoz

Kiszolgálóoldali titkosítás a szolgáltatás által kezelt kulcsok használata esetén a kulcs létrehozása, tárolási és szolgáltatás-hozzáférés az összes kezeli a szolgáltatás. Általában az eligazodást az Azure erőforrás-szolgáltatók, amely az adatok közel van a tárolóban fogja tárolni a titkosítási kulcsokat, és gyorsan hozzáférhető, miközben a kulcs titkosítási kulcsok egy biztonságos belső tároló vannak tárolva.

**Előnyök**

- Egyszerű telepítés
- A kulcsforgatás, a backup és a redundanciát a Microsoft felügyeli
- Ügyfél nem rendelkezik végrehajtási vagy egy egyéni kulcskezelés séma kockázatát kapcsolódó költségek.

**Disadvantages**

- Nincs ügyfél maga szabályozhatja a titkosítási kulcsokat (kulcs életciklus, visszavonás, stb.)
- A szolgáltatás általános felügyeleti modellből kulcskezelés elkülönítse lehetősége nélkül

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kiszolgálóoldali titkosítás ügyfél által kezelt kulcsok használata az Azure Key Vaultban 

Az olyan forgatókönyvekben, ahol a követelmény az inaktív adatok titkosítása és a titkosítási kulcsok ügyfelek használhatják a kiszolgálóoldali titkosítás ügyfél által kezelt kulcsok használata a Key Vaultban. Egyes szolgáltatások előfordulhat, hogy csak a legfelső szintű kulcstitkosítási kulcs tárolása az Azure Key Vaultban, és tárolja a titkosított adatok titkosítási kulcsát egy belső Önhöz legközelebb eső helyet az adatok. Az adott forgatókönyv ügyfelek a saját kulcsok használata a Key Vault (BYOK – Bring Your Own Key), vagy hozzon létre újakat, és használatával titkosítja a kívánt erőforrást. Az erőforrás-szolgáltató titkosítási és visszafejtési műveleteket hajtja végre, amíg az összes titkosítási műveleteket a legfelső szintű kulcsot a konfigurált kulcsot használja. 

##### <a name="key-access"></a>Key Access

Az ügyfél által felügyelt kulcsok Azure Key vaultban a kiszolgálóoldali titkosítás modell magában foglalja a szolgáltatás elérése a titkosításához és visszafejtéséhez szükséges kulcsokat. Titkosítás inaktív rest kulcsok elérhető-e a szolgáltatás egy hozzáférés-vezérlési házirend keresztül történik. Ez a szabályzat hozzáférési jogosultságot ad a szolgáltatás identitás kapják meg a kulcsot. Egy társított előfizetés nevében futó Azure-szolgáltatás konfigurálható az identitás, az adott előfizetésben. A szolgáltatás Azure Active Directory-hitelesítést végezni, és képes az egy hitelesítési tokent azonosítja magát, hogy az előfizetés nevében eljáró szolgáltatásként is. A jogkivonat jeleníthetők Key Vault kapott hozzáférést egy kulcs beszerzése.

A titkosítási kulcsok használatával műveletek felügyeltszolgáltatás-identitást is hozzáférést kell biztosítani a következő műveletek bármelyike: visszafejtés, titkosítás, unwrapKey, wrapKey, győződjön meg arról, jelentkezzen, beolvasása, listázása, frissítése, létrehozása, importálása, törlése, biztonsági mentése és visszaállítása.

Titkosítása vagy visszafejtése inaktív adatok a kulcs beszerzése a felügyeltszolgáltatás-identitás, amely a Resource Manager-szolgáltatás példánya fog futni, és rendelkeznie kell (a a visszafejtési kulcs lekérése) UnwrapKey és WrapKey (a kulcs beszúrása a key vault egy új kulcs létrehozása).


>[!NOTE] 
>További részleteket talál a Key Vault engedélyezési meg a biztonságos a key vaulttal oldalt a [Azure Key Vault-dokumentáció](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Előnyök**

- A kulcsok teljes körű irányítását használt – a titkosítási kulcsok kezeli az ügyfél a Key Vault az ügyfél irányítása alatt.
- Több szolgáltatás egyik fő titkosíthatók.
- A szolgáltatás általános felügyeleti modellből kulcskezelés is őket.
- Meghatározhatja, hogy a szolgáltatás és a kulcs helyét van régióban

**Disadvantages**

- Ügyfél rendelkezik teljes felelősség felügyeleti hozzáférés a kulcshoz
- Ügyfél rendelkezik teljes felelősség kulcs életciklus-felügyelete
- További beállítás és konfiguráció erőforrásokra

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás, az ügyfél által felügyelt hardveres szolgáltatás által kezelt kulcsok használata

Bizonyos Azure-szolgáltatások engedélyezése a gazdagép Your Own Key (HYOK) kulcskezelés modell. Ez a felügyeleti mód olyan esetekben hasznos, ahol az inaktív adatok titkosításához, és kezelhetik a kulcsokat a saját fejlesztésű tárházban kívül a Microsoft érdekeltségébe szükség van. Ebben a modellben a szolgáltatás egy külső helyről kell lekérni a kulcsot. Teljesítmény és rendelkezésre állási garanciák érintett, és konfigurációs összetettebb. Ezenkívül mivel a szolgáltatás van hozzáférése az adattitkosítási kulcsot a titkosítási és visszafejtési műveletek során ez a modell a teljes biztonsági garanciákat hasonlóak, amikor a kulcsokat a felhasználó által felügyelt Azure Key vaultban.  Ennek eredményeképpen Ez a modell értéke nem megfelelő a legtöbb szervezet számára, kivéve, ha adott kulcskezelés követelmények rendelkeznek. Ezek a korlátozások miatt a legtöbb Azure-szolgáltatások nem támogatják az ügyfél által felügyelt hardveres kiszolgáló által kezelt kulcsok használata kiszolgálóoldali titkosítás.

##### <a name="key-access"></a>Key Access

Szolgáltatás által kezelt kulcsok használata az ügyfél által felügyelt hardveres kiszolgálóoldali titkosítás használata esetén a kulcsokat a rendszer az ügyfél által konfigurált karbantartása. Azure-szolgáltatások, amelyek támogatják ezt a modellt biztosítanak az ügyfelek biztonságos kapcsolatot azt jelenti, hogy a megadott kulcstároló.

**Előnyök**

- A legfelső szintű kulcs teljes körűen használható – titkosítási kulcsok egy vásárló által biztosított store által felügyelt
- Több szolgáltatás egyik fő titkosíthatók.
- A szolgáltatás általános felügyeleti modellből kulcskezelés is őket.
- Meghatározhatja, hogy a szolgáltatás és a kulcs helyét van régióban

**Disadvantages**

- Kulcs tárolása, biztonsági, teljesítmény és rendelkezésre állás teljes felelősség
- Hozzáférés a kulcshoz felügyeleti teljes felelősség
- A kulcs életciklusához kapcsolódó felügyeleti teljes felelősség
- Jelentős telepítése, konfigurálása és a folyamatos karbantartási költségek
- Nagyobb függőség a hálózat rendelkezésre állásának az ügyfél adatközpontja és az Azure-adatközpontok között.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Titkosítás inaktív állapotban a Microsoft cloud services

A Microsoft Cloud services három felhőmodellekre használt: IaaS, PaaS, SaaS. Az alábbi példákat, hogy azok elférjenek az egyes rendelkezik:

- Szoftver kiszolgálóként vagy alkalmazást a felhőbe, például az Office 365 által biztosított SaaS szoftveres szolgáltatások, neve.
- A platformszolgáltatások, mely ügyfelek használhatja a felhőt az alkalmazásaikat, például tárolási, elemzési és service bus-funkciót a felhő használatával.
- Infrastruktúra-szolgáltatások vagy infrastruktúra-szolgáltatás (IaaS) az, hogy melyik ügyfél telepíti az operációs rendszerek és az alkalmazásokhoz, amelyek a felhőben, és esetleg egyéb felhőalapú szolgáltatások kihasználása.

### <a name="encryption-at-rest-for-saas-customers"></a>Titkosítás inaktív állapotban SaaS-ügyfelek számára

A szoftverszolgáltatások (SaaS) ügyfelek formájában szoftver általában rendelkeznek titkosítás engedélyezve vagy nem érhető el minden egyes szolgáltatás inaktív. Office 365-nek az ügyfelek számára, győződjön meg arról, vagy engedélyezze az inaktív adatok titkosítását számos lehetőség közül választhat. Office 365-szolgáltatásokkal kapcsolatos további információkért lásd: [titkosítás az Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Titkosítás inaktív állapotban PaaS-ügyfelek számára

Platform, a Platformszolgáltatás (PaaS) felhasználói adatokat általában egy alkalmazás-végrehajtási környezetben található, és a vásárlói adatok tárolására használt bármely Azure erőforrás-szolgáltatók. Tekintse meg a többi lehetőség legyen elérhető, a titkosítása, vizsgálja meg a storage és az alkalmazás-platformokat, amelyek használja az alábbi táblázatot. Támogatott, ha a titkosítás inaktív állapotban engedélyezésével utasításokra mutató hivatkozásokat mindegyik erőforrás-szolgáltató-okat. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Titkosítás inaktív állapotban IaaS-ügyfelek számára

Infrastruktúra-szolgáltatás (IaaS) ügyfelek formájában is lehet a szolgáltatások és alkalmazások különböző használja. Az IaaS-szolgáltatások engedélyezheti a titkosítás inaktív állapotban, az Azure-ban üzemeltetett virtuális gépek és virtuális merevlemezek, az Azure Disk Encryption használatával. 

#### <a name="encrypted-storage"></a>A titkosított tároló

Mint a PaaS IaaS-megoldásokat használhatják a más Azure-szolgáltatásokkal, tárolt adatok titkosítása. Ezekben az esetekben szintén titkosítást alkalmazhat a Rest-terméktámogatási egyes igénybe vett Azure-szolgáltatás által biztosított módon. Az alábbi táblázatban enumerálása a legfontosabb tárolási, szolgáltatások, valamint alkalmazásplatformokat, és a modell a támogatott inaktív adatok titkosítását. Támogatott, ahol hivatkozások titkosítás inaktív állapotban engedélyezésével kapcsolatban állnak rendelkezésre. 

#### <a name="encrypted-compute"></a>Titkosított számítási

Rest-megoldás egy teljes körű titkosítása szükséges, hogy az adatok soha nem megőrzi-e a titkosítás nélkül. Használja a kiszolgálón, a memória, az adatok betöltése az adatok megőrizhetők helyileg számos lehetőséget kínál, beleértve a Windows oldal fájlja, egy összeomlási memóriakép és az alkalmazás végrehajthat naplózásának. Annak érdekében, hogy ezek az adatok titkosítása, IaaS-alkalmazások az Azure Disk Encryption használhatja az Azure IaaS virtuális gép (Windows vagy Linux) és a virtuális lemezt. 

#### <a name="custom-encryption-at-rest"></a>Egyéni titkosítás inaktív állapotban

Javasoljuk, hogy amikor csak lehetséges, IaaS-alkalmazások használatában az Azure Disk Encryption titkosítási felhasznált Azure-szolgáltatások által biztosított Rest-beállításokat. Bizonyos esetekben például a szabálytalan titkosítási követelményeket vagy az Azure-alapú tárolási, a fejlesztő az IaaS-alkalmazás szükségessé, a titkosítás rest-magukat. A fejlesztők az IaaS-megoldások jobban integrálható az Azure felügyeleti és a vásárlói elvárásokkal kihasználva az egyes Azure-összetevőket. A fejlesztők, az Azure Key Vault szolgáltatás a biztonságos kulcsok tárolási megoldás biztosítása, valamint adja meg az ügyfeleknek a legtöbb Azure platformszolgáltatások, amely egységes kulcskezelési beállításokat az használjon. Emellett egyéni megoldások segítségével Azure-Managed Szolgáltatásidentitások szolgáltatásfiókok eléréséhez a titkosítási kulcsok engedélyezése. Felügyelt Szolgáltatásidentitások és az Azure Key Vault fejlesztői információit tekintse meg a megfelelő SDK-k.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure-erőforrás szolgáltatókat titkosítás modell támogatása

Microsoft Azure-szolgáltatások minden egyes támogatja egy vagy több, a titkosítás a rest-modelleket. Egyes szolgáltatások esetében előfordulhat azonban, egy vagy több, a titkosítás modellek nem alkalmazható. Olyan szolgáltatásokhoz, amelyek támogatják az ügyfél által felügyelt főbb forgatókönyvek megvalósítását a kulcstípusok, amely az Azure Key Vault támogatja a kulcstitkosítási kulcs csak egy részhalmazát is támogatják. Emellett a szolgáltatások kibocsátási előfordulhat, hogy ezeket a forgatókönyveket és a különböző menetrend kulcstípusok támogatása. Ez a szakasz ismerteti a rest-terméktámogatási az fő Azure storage szolgáltatások mindegyike írásának időpontjában a titkosítást.

### <a name="azure-disk-encryption"></a>Az Azure disk encryption

Minden olyan vevői Azure infrastruktúráját használja a szolgáltatás (IaaS) funkciókat érheti el IaaS virtuális gépek és az Azure Disk Encryption használatával inaktív adatok titkosítását. Az Azure Disk encryption további információkért lásd: a [dokumentáció az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure Storage tárterület

Minden Azure Storage szolgáltatás (Blob storage, Queue storage, Table storage és az Azure Files) támogatja a kiszolgálóoldali titkosítás inaktív állapotban, néhány szolgáltatás felhasználó által kezelt kulcsokkal és ügyféloldali titkosítás támogatása.  

- Server-side: Az Azure Storage-szolgáltatások segítségével szolgáltatás által kezelt kulcsokkal, amely átlátható az alkalmazás alapértelmezés szerint a kiszolgálóoldali titkosítást. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](https://docs.microsoft.com/azure/storage/storage-service-encryption). Az Azure Blob storage és az Azure Files is támogatják az RSA 2048-bites ügyfél által felügyelt kulcsok Azure Key vaultban. További információkért lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Ügyféloldali: Az Azure-Blobok, táblák és üzenetsorok támogatja az ügyféloldali titkosítását. Ügyféloldali titkosítás használata esetén az ügyfelek titkosítja az adatokat, és töltse fel az adatokat, mint egy titkosított blobot. Kulcskezelés végzi el az ügyfél. További információkért lásd: [ügyféloldali titkosítás és a Microsoft Azure Storage for Azure Key Vault](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Az Azure SQL Database jelenleg támogatja az inaktív adatok titkosítását a Microsoft által felügyelt Szolgáltatásoldali és ügyféloldali titkosítás forgatókönyvek.

Kiszolgálói titkosítás támogatása jelenleg az SQL szolgáltatás transzparens adattitkosítás nevű keresztül biztosított. Miután az ügyfél egy Azure SQL Database lehetővé teszi a TDE kulcs automatikusan létrehozása és felügyelete a számukra. Inaktív adatok titkosítását az adatbázis és a kiszolgáló szintjén engedélyezhető. 2017 júniusától [transzparens adattitkosítási (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) az újonnan létrehozott adatbázisok alapértelmezés szerint engedélyezve van. Az Azure SQL Database támogatja a RSA 2048-bites ügyfél által felügyelt kulcsok Azure Key vaultban. További információkért lásd: [transzparens adattitkosítás Bring Your Own Key-támogatással az Azure SQL Database és a Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Ügyféloldali titkosítás az Azure SQL Database-adatok keresztül támogatott a [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funkció. Mindig titkosított kulcsot használ, és a felhasználó az ügyfél által. Ügyfelek tárolhatja a főkulcs egy Windows-tanúsítványtároló, az Azure Key Vault vagy a helyi hardveres biztonsági modul. Az SQL Server Management Studióval, a SQL-felhasználók milyen kulcs titkosítására melyik oszlop szeretnék válassza ki.

|                                  |                    | **Modell titkosítás és kulcskezelés** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Kiszolgálóoldali szolgáltatás által felügyelt kulccsal**     | **Kiszolgálóoldali használata a Key Vault az ügyfél által felügyelt**             | **Ügyféloldali használatával az ügyfél által felügyelt**      |
| **Tárolási és adatbázis**        |                    |                    |                    |                    |
| Disk (IaaS)                      | -                  | Igen, RSA 2048-bites  | -                  |
| SQL Server (IaaS)                | Igen                | Igen, RSA 2048-bites  | Igen                |
| Az Azure SQL-(adatbázis vagy adatraktár) | Igen                | Igen, RSA 2048-bites  | Igen                |
| Azure SQL (Database Managed Instance) | Igen                | Az előzetes RSA 2048-bites  | Igen                |
| Az Azure Storage (Blobok blokk vagy lap) | Igen                | Igen, RSA 2048-bites  | Igen                |
| Azure Storage (Files)            | Igen                | Igen, RSA 2048-bites  | -                  |
| Az Azure Storage (táblák, üzenetsorok)   | Igen                | -                  | Igen                |
| Cosmos DB (Document DB)          | Igen                | -                  | -                  |
| StorSimple                       | Igen                | -                  | Igen                |
| Backup                           | -                  | -                  | Igen                |
| **Intelligencia és elemzés**   |                    |                    |                    |
| Azure Data Factory               | Igen                | -                  | -                  |
| Azure Machine Learning           | -                  | Az előzetes RSA 2048-bites | -                  |
| Azure Stream Analytics           | Igen                | -                  | -                  |
| HDInsight (az Azure Blob Storage)   | Igen                | -                  | -                  |
| HDInsight (Data Lake Storage)    | Igen                | -                  | -                  |
| Apache Kafka for HDInsight       | Igen                | A minta összes RSA hossza | -                  |
| Azure Data Lake Store            | Igen                | Igen, RSA 2048-bites  | -                  |
| Azure Data Catalog               | Igen                | -                  | -                  |
| Power BI                         | Igen                | -                  | -                  |
| **IoT-szolgáltatások**                 |                    |                    |                    |
| IoT Hub                          | -                  | -                  | Igen                |
| Service Bus                      | Igen                | -                  | Igen                |
| Event Hubs                       | Igen                | -                  | -                  |
| Event Grid                       | Igen                | -                  | -                  |


## <a name="conclusion"></a>Összegzés

Az Azure-szolgáltatásokban tárolt ügyféladatok védelméről Microsoft döntő van. Az összes Azure által üzemeltetett szolgáltatások elkötelezettek a titkosítási szolgáltató Rest-beállításokat. Alapvető szolgáltatások, például az Azure Storage, Azure SQL Database- és alapvető elemzési és -szolgáltatásokkal már meg titkosítási Rest-beállításokat. Ilyen szolgáltatás támogatja a szabályozott ügyfelek kulcsok és ügyféloldali titkosítás, valamint a szolgáltatás által kezelt kulcsokkal és titkosítás. Microsoft Azure-szolgáltatások széles körben vannak kerülésről titkosítás a Rest elérhető verzió és a későbbi hónapokban új lehetőségek tervezzük a bevezetését előzetes és általánosan elérhető.
