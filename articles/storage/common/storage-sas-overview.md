---
title: Korlátozott hozzáférés megadása a megosztott hozzáférésű aláírásokkal (SAS) rendelkező adatokhoz
titleSuffix: Azure Storage
description: Ismerje meg, hogy használatával megosztott hozzáférésű aláírások (SAS) az Azure Storage-erőforrásokhoz való hozzáférés delegálásához, beleértve a blobokat, várólistákat, táblákat és fájlokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255235"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával

A megosztott hozzáférésű aláírás (SAS) biztonságos delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz anélkül, hogy veszélyeztetné az adatok biztonságát. A SAS segítségével részletesen szabályozhatja, hogy az ügyfél hogyan férhet hozzá az adatokhoz. Többek között szabályozhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkeznek ezekhez az erőforrásokhoz, és mennyi ideig érvényes a SAS.

## <a name="types-of-shared-access-signatures"></a>A közös hozzáférésű jogosultságkódok típusai

Az Azure Storage háromféle közös hozzáférésű aláírást támogat:

- **Felhasználói delegálás SAS.** A felhasználói delegálás SAS az Azure Active Directory (Azure AD) hitelesítő adatokkal és a SAS-hoz megadott engedélyekkel védett. A felhasználói delegálássas SAS csak a Blob storage-ra vonatkozik.

    A felhasználói delegálási SAS-ról további információt a [Felhasználói delegálási SAS (REST API) létrehozása című](/rest/api/storageservices/create-user-delegation-sas)témakörben talál.

- **Szolgáltatás SAS.** A szolgáltatás SAS a tárfiók kulcsa. A szolgáltatás SAS delegálja a hozzáférést egy erőforrás csak az egyik az Azure Storage-szolgáltatások: Blob storage, queue storage, table storage, vagy az Azure Files. 

    A szolgáltatásSAS-ról további információt a [Szolgáltatás SAS (REST API) létrehozása című](/rest/api/storageservices/create-service-sas)témakörben talál.

- **SAS számla.** A fiók SAS a tárfiók kulcsa. A fiókalapú SAS egy vagy több tárolószolgáltatás erőforrásaihoz nyújt hozzáférést. A szolgáltatáson vagy a felhasználói delegáláson keresztül elérhető összes művelet sas-on keresztül is elérhető egy fiókSAS-on keresztül. Emellett a fiók SAS, delegálhatja a hozzáférést a szolgáltatások szintjén érvényes műveletekhez, például a **Szolgáltatás tulajdonságainak beget/beállítása** és **a Szolgáltatásstatisztika-műveletek beszerezése/beállítása.** A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. 

    A SAS fiókkal kapcsolatos további információkért [hozzon létre egy fiókSAS-t (REST API).](/rest/api/storageservices/create-account-sas)

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait, ha lehetséges, a biztonsági ajánlott eljárás, nem pedig a fiókkulcs használata, amely könnyebben sérülhet. Ha az alkalmazás tervéhez megosztott hozzáférési jogosultságszükséges a Blob storage-hoz való hozzáféréshez, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási SAS-t, ha lehetséges a nagyobb biztonság érdekében.

A közös hozzáférésű aláírás két formája lehet:

- **Ad hoc SAS:** Amikor létrehoz egy ad hoc SAS-t, a SAS kezdési ideje, lejárati ideje és engedélyei mind meg vannak adva a SAS URI-ban (vagy hallgatólagosan, ha a kezdési időpont ki marad). Bármilyen típusú SAS lehet ad hoc SAS.
- **Szolgáltatás SAS tárolt hozzáférési házirenddel:** A tárolt hozzáférési szabályzat egy erőforrás-tárolón van definiálva, amely lehet blobtároló, tábla, várólista vagy fájlmegosztás. A tárolt hozzáférési házirend egy vagy több szolgáltatás megosztott hozzáférési aláírása kormegkötéseinek kezelésére használható. Amikor egy szolgáltatás SAS-t egy tárolt hozzáférési szabályzathoz társít, a SAS örökli a megkötéseket&mdash;a tárolt hozzáférési szabályzathoz meghatározott kezdési idő, lejárati idő és engedélyek.&mdash;

> [!NOTE]
> A felhasználói delegálási SAS-nak vagy a fiók SAS-nak ad hoc SAS-nak kell lennie. A tárolt hozzáférési házirendek nem támogatottak a felhasználói delegálási SAS vagy a fiók SAS számára.

## <a name="how-a-shared-access-signature-works"></a>A közös hozzáférésű aláírás működése

A megosztott hozzáférésű aláírás egy aláírt URI, amely egy vagy több tárolási erőforrásra mutat, és egy speciális lekérdezési paramétereket tartalmazó jogkivonatot tartalmaz. A jogkivonat azt jelzi, hogy az ügyfél hogyan érheti el az erőforrásokat. A lekérdezési paraméterek egyike, az aláírás a SAS-paraméterekből épül fel, és a SAS létrehozásához használt kulccsal van aláírva. Ezt az aláírást az Azure Storage használja a tárolási erőforráshoz való hozzáférés engedélyezéséhez.

### <a name="sas-signature"></a>SAS-aláírás

A SAS-t kétféleképpen írhatja alá:

- Az Azure Active Directory (Azure AD) hitelesítő adataival létrehozott *felhasználói delegálási* kulccsal. A felhasználói delegálási SAS a felhasználó delegálási kulccsal van aláírva.

    A felhasználói delegálási kulcs lefoglalásához és a SAS létrehozásához egy Azure AD rendszerbiztonsági taghoz kell rendelni egy szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A felhasználói delegálási kulcs lehívásához szükséges engedélyekkel rendelkező RBAC-szerepkörökről a [Felhasználói delegálási SAS (REST API) létrehozása című témakörben](/rest/api/storageservices/create-user-delegation-sas)talál részletes információt.

- A tárfiók kulcsával. A szolgáltatás SAS és egy fiók SAS alá vannak írva a tárfiók kulcsával. A fiókkulccsal aláírt SAS létrehozásához az alkalmazásnak hozzáféréssel kell rendelkeznie a fiókkulcshoz.

### <a name="sas-token"></a>SAS-token

A SAS-jogkivonat egy karakterlánc, amely az ügyféloldalon jön létre, például az Azure Storage-ügyfélkódtárak egyikének használatával. A SAS-jogkivonatot az Azure Storage semmilyen módon nem követi nyomon. Az ügyféloldalon korlátlan számú SAS-jogkivonatot hozhat létre. Miután létrehozott egy SAS-t, terjesztheti azt olyan ügyfélalkalmazások között, amelyek hozzáférést igényelnek a tárfiók erőforrásaihoz.

Amikor egy ügyfélalkalmazás egy kérelem részeként SAS-URI-t biztosít az Azure Storage-nak, a szolgáltatás ellenőrzi a SAS-paramétereket és az aláírást, hogy ellenőrizze, hogy érvényes-e a kérelem engedélyezéséhez. Ha a szolgáltatás ellenőrzi, hogy az aláírás érvényes-e, akkor a kérelem engedélyezett. Ellenkező esetben a kérelem elutasításra kerül a 403 -as (Tiltott) hibakóddal.

Íme egy példa egy szolgáltatás SAS URI, amely bemutatja az erőforrás URI és a SAS-jogkivonat:

![Szolgáltatás SAS URI-összetevői](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Mikor kell közös hozzáférésű aláírást használni?

SAS-t akkor használjon, ha biztonságos hozzáférést szeretne biztosítani a tárfiókban lévő erőforrásokhoz minden olyan ügyfél számára, aki egyébként nem rendelkezik engedélyekkel ezekhez az erőforrásokhoz.

Egy gyakori forgatókönyv, ahol a SAS hasznos egy olyan szolgáltatás, ahol a felhasználók saját adatokat olvasnak és írnak a tárfiókba. Az olyan esetekben, amikor a tárfiók felhasználói adatokat tárol, két tipikus kialakítási minta létezik:

1. Az ügyfelek egy hitelesítést végző, előtérbeli proxyszolgáltatással töltik fel- és le az adatokat. Ez az előtér-proxy szolgáltatás azzal az előnnyel jár, amely lehetővé teszi az üzleti szabályok érvényesítését, de nagy mennyiségű adat vagy nagy mennyiségű tranzakció esetén egy olyan szolgáltatás létrehozása, amely az igényeknek megfelelően skálázható, költséges vagy nehéz lehet.

   ![Esetdiagram: Előtér-proxy szolgáltatás](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Egy egyszerű szolgáltatás hitelesíti az ügyfelet, majd létrehoz egy SAS-t. Miután az ügyfélalkalmazás megkapja a SAS-t, közvetlenül hozzáférhetnek a tárfiók erőforrásaihoz a SAS által meghatározott engedélyekkel és a SAS által engedélyezett időközzel. Az SAS-szel nincs szükség az összes adat az előtérbeli proxyszolgáltatáson keresztül történő átirányítására.

   ![Esetdiagram: SAS-szolgáltatószolgáltatás](./media/storage-sas-overview/sas-storage-provider-service.png)

Számos valós szolgáltatás használhatja e két megközelítés hibridjeit. Előfordulhat például, hogy egyes adatokfeldolgozása és érvényesítése az előtér-proxyn keresztül történik, míg más adatok mentése és/vagy olvasása közvetlenül a SAS használatával történik.

Ezenkívül a SAS-nek engedélyeznie kell a forrásobjektumhoz való hozzáférést egy másolási műveletben bizonyos esetekben:

- Ha egy blobot egy másik blobba másol, amely egy másik tárfiókban található, sas-t kell használnia a forrásblobhoz való hozzáférés engedélyezéséhez. A SAS-t is használhatja a célblobhoz való hozzáférés engedélyezéséhez.
- Ha egy másik tárfiókban található fájlba másol egy fájlt, a forrásfájlhoz való hozzáférés engedélyezéséhez SAS-t kell használnia. A SAS-t a célfájlhoz való hozzáférés engedélyezéséhez is használhatja.
- Amikor egy blobot egy fájlba vagy egy blobba másol, sas-t kell használnia a forrásobjektumhoz való hozzáférés engedélyezéséhez, még akkor is, ha a forrás- és célobjektumok ugyanabban a tárfiókban vannak.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások

Ha megosztott hozzáférésű aláírásokat használ az alkalmazásokban, két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, bárki használhatja, aki beszerzi, ami potenciálisan veszélyeztetheti a tárfiók.
- Ha egy ügyfélalkalmazásnak biztosított SAS lejár, és az alkalmazás nem tud új SAS-t lekérni a szolgáltatásból, akkor az alkalmazás működése akadályokba ütközhet.

A közös hozzáférésű aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok csökkentésében:

- **Mindig https használatával** hozzon létre vagy terjesszen egy SAS.Always use HTTPS to create or distribute a SAS. Ha egy SAS http-n keresztül halad át, és elfogják, a véletlen üle-in-the-middle támadást végrehajtó támadó képes olvasni a SAS-t, majd ugyanúgy használni, mint a tervezett felhasználó, potenciálisan veszélyeztetve a bizalmas adatokat, vagy lehetővé téve a rosszindulatú felhasználó általi adatsérülés.
- **Ha lehetséges, használjon felhasználói delegálási SAS-t.** A felhasználói delegálássas SAS magasabb biztonságot nyújt egy szolgáltatás SAS-nek vagy egy fiók SAS-nak. A felhasználói delegálás SAS azure AD hitelesítő adatokkal védett, így nem kell tárolnia a fiókkulcsot a kódot.
- **SAS-hoz rendelkezik visszavonási tervvel.** Győződjön meg róla, hogy készen áll a válaszadásra, ha egy SAS sérült.
- **A szolgáltatás SAS-hoz tárolt hozzáférési szabályzat definiálása.** A tárolt hozzáférési házirendek lehetővé teszik a szolgáltatás SAS-engedélyeinek visszavonását anélkül, hogy újra kellene generálnia a tárfiók kulcsait. Állítsa be a lejárati e nagyon messze a jövőben (vagy végtelen), és győződjön meg róla, hogy rendszeresen frissítik mozgatni, hogy távolabb a jövőben.
- **Rövid távú lejárati idők használata ad hoc SAS szolgáltatás SAS-on vagy fiók SAS-on.** Ily módon, még akkor is, ha a SAS sérült, csak rövid ideig érvényes. Ez a gyakorlat különösen fontos, ha nem hivatkozhat a tárolt hozzáférési szabályzatra. A rövid távú lejárati idő is korlátozza a blobba írható adatok mennyiségét azáltal, hogy korlátozza a feltöltéshez rendelkezésre álló időt.
- **Az ügyfelek szükség esetén automatikusan megújítják a SAS-t.** Az ügyfeleknek jóval a lejárat előtt meg kell újítaniuk a SAS-t, hogy időt biztosítson az újrapróbálkozásokra, ha a SAS-t biztosító szolgáltatás nem érhető el. Ha a SAS célja, hogy használni kell egy kis számú azonnali, rövid életű műveletek, amelyek várhatóan befejeződik a lejárati időn belül, akkor ez szükségtelen lehet, mivel a SAS várhatóan nem újítható meg. Azonban, ha az ügyfél, amely rendszeresen kéri keresztül SAS, majd a lejárati lehetőség jön szóba. A legfontosabb szempont az, hogy egyensúlyt kell teremteni annak szükségességét, hogy a SAS rövid életű legyen (amint azt korábban említettük) azzal, hogy biztosítani kell, hogy az ügyfél elég korán kérje a megújítást (hogy elkerüljék a SAS sikeres megújítás a lejárata miatt ily módon történő fennakadásokat).
- **Legyen óvatos a SAS kezdési időpontjával.** Ha a SAS kezdési idejét **mosta**állítja be, akkor az óraeltérés (az aktuális idő különböző gépek szerinti különbségei) miatt időnként hibák figyelhetők meg az első néhány percben. Általában állítsa be a kezdési időt legalább 15 percre a múltban. Vagy ne állítsa be egyáltalán, ami minden esetben azonnal érvényessé teszi. Ugyanez vonatkozik a lejárati idő is - ne feledje, hogy lehet megfigyelni akár 15 perc óra ferde mindkét irányban bármilyen kérésre. A 2012-02-12 előtti REST-verziót használó ügyfelek esetében a tárolt hozzáférési szabályzatra nem hivatkozó SAS maximális időtartama 1 óra, és az ennél hosszabb időtartamú házirendek sikertelenek lesznek.
- **Legyen óvatos a SAS datetime formátumban.** Ha egy SAS kezdési idejét és/vagy lejáratát állítja be, akkor egyes segédprogramok (például az AzCopy parancssori segédprogramesetében) a datetime formátumnak "+%Y-%m-%m-%dT%H:%M:%SZ" értékre van szüksége, kifejezetten a SAS-token használatával való munkavégzéshez szükséges másodperceket.  
- **Legyen specifikus az elérendő erőforrással.** A biztonsági ajánlott eljárás az, hogy a felhasználó a minimálisan szükséges jogosultságokat. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, majd olvasási hozzáférést kell biztosítania az adott entitáshoz, és nem kell olvasnia/írnia/törölnie az összes entitáshoz való hozzáférést. Ez is segít csökkenteni a kárt, ha a SAS sérül, mert a SAS kevesebb erő a kezében a támadó.
- **Értse meg, hogy fiókját minden használatért kiszámlázunk, beleértve a SAS-t is.** Ha írási hozzáférést biztosít egy blobhoz, a felhasználó dönthet úgy, hogy feltölt egy 200 GB-os blobot. Ha olvasási hozzáférést is adott nekik, akkor dönthetnek úgy, hogy 10-szer töltik le, ami 2 TB-os kimenő költséget jelent az Ön számára. Ismét adjon meg korlátozott engedélyeket a rosszindulatú felhasználók lehetséges műveleteinek csökkentése érdekében. Rövid ideig használható SAS használatával csökkentheti ezt a fenyegetést (de vegye figyelembe az óraeltérést a befejezési időpontban).
- **SAS-sel írt adatok ellenőrzése.** Amikor egy ügyfélalkalmazás adatokat ír a tárfiókba, ne feledje, hogy problémák lehetnek az adatokkal. Ha az alkalmazás megköveteli, hogy az adatok érvényesítése vagy engedélyezése előtt készen áll a használatra, akkor ezt az ellenőrzést az adatok írása után, és mielőtt az alkalmazás által használt. Ez a gyakorlat a sérült vagy rosszindulatú adatok fiókjába történő írása ellen is védelmet nyújt, akár a SAS-t megfelelően bevásárló felhasználó, akár egy kiszivárgott SAS-t kihasználó felhasználó által.
- **Tudja, mikor ne használjon SAS-t.** Előfordulhat, hogy egy adott művelethez kapcsolódó kockázatok meghaladják a SAS használatának előnyeit. Az ilyen műveletekhez hozzon létre egy középső rétegbeli szolgáltatást, amely az üzleti szabály érvényesítése, hitelesítése és naplózása végrehajtása után ír a tárfiókba. Is, néha egyszerűbb kezelni a hozzáférést más módon. Például ha azt szeretné, hogy az összes blobok egy tárolóban nyilvánosan olvasható, a tároló nyilvános, ahelyett, hogy egy SAS minden ügyfél számára a hozzáférés.
- **Az Azure Monitor és az Azure Storage-naplók segítségével figyelheti az alkalmazást.** Az Azure Monitor és a storage analytics naplózása a SAS-szolgáltató szolgáltatás kimaradása vagy a tárolt hozzáférési szabályzat véletlen eltávolítása miatt beugró diskit. További információ: [Azure Storage-metrikák az Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) és az [Azure Storage Analytics naplózása.](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="get-started-with-sas"></a>Első lépések a SAS-szal

A megosztott hozzáférésű aláírások első lépései az egyes SAS-típusokhoz az alábbi cikkekben érhetők el.

### <a name="user-delegation-sas"></a>Felhasználói delegálás SAS

- [Felhasználói delegálássas létrehozása egy tárolóhoz vagy blobhoz a PowerShell segítségével](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Felhasználói delegálássas SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Felhasználói delegálássas létrehozása a .NET-tel rendelkező tárolóhoz vagy blobhoz](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Szolgáltatás SAS

- [Szolgáltatás SAS létrehozása a .NET-tel rendelkező tárolóhoz vagy blobhoz](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Fiók SAS

- [Fiók létrehozása SAS-szal a .NET segítségével](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>További lépések

- [Hozzáférés delegálása megosztott hozzáférésű aláírással (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Felhasználói delegálássas (REST API) létrehozása](/rest/api/storageservices/create-user-delegation-sas)
- [Szolgáltatás SAS (REST API) létrehozása](/rest/api/storageservices/create-service-sas)
- [Fiók létrehozása SAS (REST API)](/rest/api/storageservices/create-account-sas)
